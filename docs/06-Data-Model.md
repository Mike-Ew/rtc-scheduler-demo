# 06 — Data Model (Logical, MVP)

## Person

- id, name, email, phone, role(s) [e.g., Counselor, RN, Support], status (Active/Inactive)

## Unit/Room

- id, name, location, notes

## Shift

- id, unit_id, role_required, start_at, end_at, required_headcount, status, notes

## Assignment

- id, shift_id, person_id, status (Assigned/Accepted/Declined/Reassigned), updated_by, updated_at

## Availability

- id, person_id, type (recurring/date-range), hours pattern or dates

## AuditLog

- id, actor_id, entity (Shift/Assignment/Person), entity_id, action, before/after, timestamp
