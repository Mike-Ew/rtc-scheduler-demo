# 05 — Scheduling Rules (MVP)

## Constraints

- No overlapping shifts for the same person.
- Person must match the **required role** for a shift.
- Max daily/weekly hours thresholds (configurable) → warn on assign.
- Enforce minimum rest period between shifts (configurable).
- Unit/room must meet minimum headcount and skill mix (if specified).

## Statuses

- Draft → Published → Filled/Partial → Completed.
- Person status per shift: Assigned, Accepted, Declined, Reassigned, No-Show.

## Availability

- Person may set recurring availability and blackout dates.
- Requests for Time Off (simple block) — coordinator override allowed with warning.
