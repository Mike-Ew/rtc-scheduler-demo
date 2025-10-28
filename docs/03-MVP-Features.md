# 03 — MVP Feature List (Must/Should/Could)

## MUST (v1)

1. **Shift creation & assignment** by unit/room, role, start/end, required headcount.
2. **Conflict detection** (double-book, overlapping shifts, role mismatch, overtime threshold).
3. **Self-service portal** (view schedule, accept/decline, set availability/blackout dates).
4. **Notifications** (email + optional SMS) for new/changed/cancelled shifts; daily reminder.
5. **Daily coverage view** (per unit: who’s on, gaps, status). Printable roster.
6. **Excel import (one-time)**: seed people, roles, and initial schedule from a template.
7. **Basic audit log**: who changed what, when.
8. **Auth & roles** (Admin, Coordinator, Staff, Volunteer).

## SHOULD (post-MVP, quick win)

- Shift templates & cloning — create recurring patterns or duplicate shifts.
- Bulk edit & publish — change multiple shifts at once and notify affected staff.
- Time-off request workflow — staff request → coordinator review/approve.
- Availability-based suggestions — recommend staff who match role and availability.
- Customizable notifications — allow opt-in/opt-out and configurable reminder timing.
- Simple exports & reports — CSV/Excel for coverage, hours, and exceptions.
- Basic qualifications tracking — record required certifications with expiry alerts.
- Partial-day availability & blackout granularity — support shifts within days.
- Automated conflict resolution hints — suggest alternatives when conflicts detected.
- Permission scoping — more granular coordinator permissions (unit/role).

- **Swap workflow** (request → coordinator approval).
- **iCal/ICS feed** per person (works with Google/Outlook).
- **Hours summary** per person/week for payroll handoff (no payroll integration yet).

## COULD (later)

- Coverage forecasting, compliance exports, multi-location routing, kiosk sign-in, mobile app.
