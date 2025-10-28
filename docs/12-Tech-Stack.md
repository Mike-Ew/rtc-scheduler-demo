# 12 — Recommended Tech Stack (Python Backend)

This stack keeps the product cohesive while centering backend services on Python. It supports the MVP workflows, future integrations (Gusto, Microsoft 365), and a shared dev experience across teams.

## Overview

- **Frontend**: React + TypeScript for rich scheduling UI.
- **Backend**: FastAPI services with an event-driven core in Python.
- **Data**: PostgreSQL for transactional storage, Redis for queues/cache.
- **Integrations**: Azure Active Directory, Microsoft Graph, Gusto payroll APIs.
- **DevOps**: Azure cloud hosting with GitHub Actions CI/CD, Infrastructure-as-Code.

## Frontend Stack

- **Framework**: React 18 with Vite or Next.js (app router) in TypeScript; choose based on SSR/SEO needs.
- **UI Library**: Chakra UI or Fluent UI for accessible components and Microsoft-aligned styling.
- **State / Data Fetching**: React Query (TanStack Query) + Zustand/Redux Toolkit for complex global state.
- **Form Handling**: React Hook Form with Zod schema validation to keep client/server DTOs aligned.
- **Testing**: Vitest/Jest for unit tests, Testing Library for component behavior, Playwright for end-to-end flows.
- **Build & Packaging**: Vite for fast dev + optimized builds; Storybook for component documentation.

## Backend Stack (Python)

- **Framework**: FastAPI (async, OpenAPI-by-default) for REST endpoints described in `docs/11-Backend-Design.md`.
- **App Structure**: Domain-driven modules (`auth`, `shifts`, `assignments`, `availability`, `notifications`).
- **Validation**: Pydantic models for request/response schemas; shared typings exported to the frontend via OpenAPI → TypeScript generator.
- **ORM / Persistence**: SQLAlchemy 2.0 with Alembic migrations; use async engine for high concurrency.
- **Task Queue**: Celery or Dramatiq backed by Redis for notifications, Excel import processing, hourly jobs.
- **Eventing**: Simple domain event dispatcher publishing to Redis streams or PostgreSQL NOTIFY for decoupling.
- **Authentication**: OAuth2 flows via Azure AD (MSAL library) and FastAPI dependencies enforcing role scopes.
- **Testing**: Pytest with factory fixtures, HTTPX for API tests, Scenario tests for scheduling constraints.

## Data & Storage

- **Database**: PostgreSQL 15+ (Azure Database for PostgreSQL Flexible Server).
  - Schema matches `docs/06-Data-Model.md`.
  - Use row-level security policies for staff-accessible tables if needed.
- **Cache/Queue**: Azure Cache for Redis.
- **File Storage**: Azure Blob Storage for Excel imports and generated exports (24-hour retention for staging).
- **Analytics**: Azure Application Insights logs; optional PostHog or Plausible for product analytics.

## Integrations

- **Azure Active Directory**:
  - Primary identity provider; use MSAL libraries in the frontend/back.
  - Supports SSO with Microsoft 365, Teams, SharePoint.
- **Microsoft Graph API**:
  - Calendar sync (shifts → Outlook).
  - Teams notifications (adaptive cards).
  - Excel export to OneDrive/SharePoint via Graph.
- **Gusto Payroll**:
  - OAuth2 client credentials or authorization code flow for secure connection.
  - Build dedicated integration module handling token refresh, rate limiting, and mapping assignment hours to Gusto time-off endpoints.
- **Notifications**:
  - Email: SendGrid (Azure-hosted) or Azure Communication Services.
  - SMS: Twilio or Azure Communication Services SMS.
- **Future Connectors**: Design integration layer with feature flags so additional payroll/HRIS APIs can plug in without changing core services.

## DevOps & Deployment

- **Environments**: dev, staging, prod with separate resource groups.
- **Containers**: Docker images for frontend (static export served via Azure Static Web Apps or CDN) and backend (Gunicorn/Uvicorn workers).
- **CI/CD**: GitHub Actions
  - Run linting (`ruff`, `black`, `mypy`), Pytest, frontend tests, DB migration checks.
  - Deploy via Azure CLI or Bicep templates; approvals for staging → prod.
- **Infrastructure-as-Code**: Terraform, Pulumi, or Bicep to manage App Services, Postgres, Redis, Storage, Key Vault.
- **Monitoring**: Azure Monitor + Application Insights (traces, metrics). Set alerts for queue backlog, response latency, and job failures.
- **Secrets Management**: Azure Key Vault with Managed Identities for app access.
- **Observability**: Structured logging (JSON via `structlog`), OpenTelemetry instrumentation for API/queue tracing.

## Security & Compliance

- Enforce HTTPS everywhere; HSTS via App Gateway.
- Use Azure AD Conditional Access / MFA for coordinators and admins.
- Implement role-based access control (RBAC) with policy decorators in FastAPI.
- Encrypt sensitive columns (e.g., contact info) at rest; standardize audit logging per `docs/11-Backend-Design.md`.
- Capture data retention policies (e.g., purge inactive staff after 12 months).

## Developer Experience

- Tooling:
  - `poetry` or `uv` for Python dependency management.
  - `ruff` for linting, `black` or `ruff format` for formatting, `mypy` for typing.
  - Pre-commit hooks shared across repo.
- Local Stack:
  - Docker Compose with Postgres, Redis, mailhog/twilio emulator, blob storage emulator (Azurite).
  - Seed scripts to load sample shifts/people.
- Documentation:
  - Autogenerated API docs via FastAPI `/docs`.
  - ADRs (Architecture Decision Records) for tech choices, especially integration modules.

## Roadmap Considerations

- Add GraphQL gateway or WebSocket notification service if real-time updates become critical.
- Introduce Temporal.io (via Python SDK) for complex scheduling workflows (swap approvals, compliance checks).
- Evaluate Warehouse integration (Azure Synapse or BigQuery via Fivetran) once reporting requirements grow.
- Keep optional path for microservices; initial MVP can remain monolith-with-modules until scale necessitates splits.

This stack leverages Python strength while aligning with Microsoft ecosystems and Gusto integration needs, giving the team a maintainable baseline that can expand with future scheduling and compliance features.
