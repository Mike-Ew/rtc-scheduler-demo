# 11 — Backend Design Overview (MVP)

This document frames the MVP backend for the Scheduler. It aligns with the data model, user stories, and scheduling rules to guide implementation choices.

## Goals

- Expose a clean REST API for the web front end and future integrations.
- Enforce scheduling constraints (roles, overlaps, hour caps) server-side.
- Provide auditable, secure workflows for coordinators, staff, volunteers, and admins.
- Support inbound Excel imports and outbound email/SMS notifications.
- Keep the architecture modular so post-MVP features (swaps, reporting) slot in without rewrites.

## High-Level Architecture

```
          ┌────────────┐
          │   Web App  │
          └──────┬─────┘
                 │ HTTPS / REST (JSON)
        ┌────────▼────────┐
        │ API Gateway /   │
        │ Edge Controller │
        └────────┬────────┘
                 │
     ┌───────────┼───────────────────────────┐
     │           │                           │
┌────▼────┐ ┌────▼────┐ ┌─────────┐    ┌─────▼─────┐
│Auth Svc │ │Schedule  │ │Notifier │    │ Importer  │
│         │ │ Core     │ │ Workers │    │ (Excel)   │
└────┬────┘ └────┬────┘ └────┬────┘    └─────┬─────┘
     │           │           │               │
     │     ┌─────▼─────┐    │         ┌─────▼─────┐
     │     │ Postgres  │<───┴────────▶│ Audit Log │ (append-only)
     │     └───────────┘              └───────────┘
     │
 External Identity Provider (OAuth/OpenID) or local auth
 Email/SMS provider (e.g., SendGrid/Twilio)
```

- **API Gateway / Edge Controller**: Terminate TLS, handle auth middleware, route to feature modules.
- **Schedule Core**: orchestrates shifts, assignments, availability, conflict rules.
- **Notifier Workers**: queue-based system for emails/SMS triggered by schedule events.
- **Importer**: validates and stages Excel uploads, reuses core services for persistence.
- **Audit Log store**: append-only table or dedicated write model to satisfy compliance.

## Module Breakdown

### Auth & Identity

- Token-based auth (JWT) after login; consider short-lived access tokens with refresh.
- Roles: Admin, Coordinator, Staff, Volunteer enforced via middleware + policy checks.
- Optional integration with external identity (Azure AD, Okta) in future; design claims-based.

### Scheduling Core

- Services: `ShiftService`, `AssignmentService`, `AvailabilityService`.
- Constraint engine: synchronous validations on create/update; asynchronous warnings logged for threshold-based checks (weekly hours).
- Publishes domain events (`ShiftPublished`, `AssignmentAccepted`) to event queue for notifications/audit.

### Notification Service

- Consumes domain events, builds templates, dispatches through providers.
- Provider abstraction to swap email/SMS vendors.
- Rate limiting and retry (exponential backoff) via job queue (BullMQ/Sidekiq equivalent).

### Importer

- Upload endpoint accepts Excel file → stored temporarily (S3-compatible bucket or disk).
- Parser service validates required columns (per `docs/09-Excel-Import-Template.md`).
- Transactions to ensure all-or-nothing import; partial failures reported back.

### Audit Logging

- Middleware captures actor, entity, action, request metadata.
- Domain events appended to `audit_log` table with before/after snapshots (JSONB).
- Expose read API with filters for admins.

### Admin Module

- CRUD for people, units, roles with soft-delete (status flag) to retain history.
- Role assignment updates propagate to auth cache (if used).

## Data Persistence

- **Primary DB**: PostgreSQL 15 (hosted or managed).
  - Tables align with `docs/06-Data-Model.md`.
  - Use foreign keys, `ON UPDATE CASCADE/RESTRICT` to enforce relationships.
  - JSONB columns for flexible metadata (e.g., shift notes, notification payloads).
- **Queues**: Redis-backed job queue for notifications & long-running tasks.
- **File Storage**: ephemeral disk or S3 bucket for import staging (max 24h retention).

## API Surface (MVP)

| Method | Endpoint                         | Purpose                               | Auth Scope             |
|--------|----------------------------------|---------------------------------------|------------------------|
| POST   | `/auth/login`                    | Authenticate to receive JWT           | Public                 |
| GET    | `/me`                            | Fetch current user profile/roles      | Authenticated          |
| GET    | `/units`                         | List units/rooms                      | Coordinator/Admin      |
| GET    | `/people`                        | Directory + filters                   | Coordinator/Admin      |
| POST   | `/people`                        | Create staff/volunteer                | Admin                  |
| GET    | `/shifts`                        | List/filter shifts                    | Coordinator/Admin      |
| POST   | `/shifts`                        | Create shift                          | Coordinator/Admin      |
| PATCH  | `/shifts/{id}`                   | Update shift                          | Coordinator/Admin      |
| POST   | `/shifts/{id}/publish`           | Transition draft→published            | Coordinator/Admin      |
| POST   | `/shifts/{id}/assign`            | Assign person(s)                      | Coordinator/Admin      |
| POST   | `/assignments/{id}/status`       | Accept/decline/reassign               | Coordinator/Staff/V    |
| GET    | `/availability/me`               | Staff view of own availability        | Staff/Volunteer        |
| PUT    | `/availability/me`               | Update availability/blackouts         | Staff/Volunteer        |
| POST   | `/import/shifts`                 | Upload Excel schedule                 | Admin/Coordinator      |
| GET    | `/audit-logs`                    | Filtered audit trail                  | Admin                  |

- All endpoints respond with JSON; use ISO 8601 for timestamps, snake_case keys.
- Pagination via `?page` + `?pageSize`; filtering via query params.
- Error format: `{ "error": "ValidationError", "message": "...", "details": { ... } }`.

## Domain Workflows

1. **Create & Publish Shift**
   - Coordinator POST `/shifts` → shift stored as Draft.
   - PATCH updates allowed until publish.
   - POST `/shifts/{id}/publish` triggers constraint check; on success, `ShiftPublished` event enqueued.
   - Notifier listens → send email/SMS to assigned staff if notify flag true.

2. **Staff Accept/Decline**
   - Staff portal calls POST `/assignments/{id}/status` with `Accepted`/`Declined`.
   - Service validates (shift still active, user is assignee).
   - Event `AssignmentAccepted` or `AssignmentDeclined` logged + notifications to coordinator.

3. **Excel Import**
   - File uploaded → importer parses rows into DTOs.
   - Validate units/roles exist; mismatches flagged with row references.
   - On success, within transaction create shifts + assignments; events emitted for notifications.

4. **Conflict Detection**
   - Before commit, services run queries to detect overlaps, headcount, role mismatches.
   - Blocking conflicts produce 422 with details; warnings (e.g., weekly hours) logged but can be overridden via `force=true` for admins.

## Background Jobs & Scheduling

- Nightly job recalculates weekly-hour warnings and sends summary digest to coordinators.
- Queue worker processes notifications; separate retry dead-letter queue for manual review.
- Optional: scheduled task to purge stale drafts and temporary import files.

## Non-Functional Requirements

- **Security**: enforce TLS, password hashing (Argon2/BCrypt), rate limit auth endpoints, implement audit log for sensitive changes.
- **Availability**: target ≥99.5% uptime for MVP; use health checks and readiness endpoints for deployment.
- **Scalability**: horizontal scaling of API workers behind load balancer; queue workers scaled independently.
- **Observability**: structured logging (JSON), request IDs, metrics (Prometheus/Grafana), alerting for failed jobs.
- **Testing**: unit tests for services, integration tests for constraint scenarios, contract tests for frontend integration.

## Deployment & Environments

- Environments: `dev`, `staging`, `prod`.
- CI pipeline runs tests, linting, migration dry-run.
- Database migrations via tool (Prisma, Knex, Flyway).
- Infrastructure-as-code (Terraform/Pulumi) recommended once stable.

## Future Considerations

- Event sourcing or CQRS for complex reporting.
- Webhooks or GraphQL subscriptions for real-time updates.
- Expand notification preferences per user (quiet hours, channel opt-outs).
- Integrate with workforce/payroll systems once API contracts established.

This backend plan keeps the MVP manageable while laying foundations for post-MVP workflows like swaps, advanced compliance checks, and richer reporting.
