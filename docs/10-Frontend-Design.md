# 10 — Front-End Design Overview (MVP)

This document sketches a simple, component-driven front-end for the SureServe Scheduler MVP. It focuses on clarity for coordinators, a lightweight staff portal, and a layout that can grow with post-MVP features.

## Design Goals

- Prioritize day-to-day scheduling tasks: see gaps fast, fill shifts, notify people.
- Keep the coordinator mental model close to the current spreadsheet workflow, but with better conflict signals.
- Give staff/volunteers a frictionless self-service space focused on “what’s next for me?”
- Use a consistent visual language (badges, chips, icons) to signal status and conflicts.
- Design mobile-first for staff screens; coordinators can rely on desktop/tablet but must remain usable at 1024px.

## Information Architecture & Navigation

### Global Layout

```text
+--------------------------------------------------------------+
| SureServe Scheduler            Daily Coverage | Shifts | ⋯   |
+--------------------------------------------------------------+
| Context Bar (Unit ▼  | Date ⇄ | Publish ⚑ | Filter ⚙︎)       |
+--------------------------------------------------------------+
| Primary content panel (varies by module)                     |
+--------------------------------------------------------------+
| Inline toasts / footer actions when needed                   |
+--------------------------------------------------------------+
```

- **Top app bar**: app name, quick module tabs (Daily Coverage, Shifts, People, Settings), user avatar menu (profile, sign out).
- **Context bar** (visible in coordinator/admin modules): scoping controls (unit picker, date range) + key actions (publish, export, filters). Keeps critical filters in one predictable row.
- **Primary content**: card/grid layouts that respond between three breakpoints (mobile <600px, tablet 600–1024px, desktop >1024px).
- **Staff portal**: simplified shell with a single “My Schedule” focus and a compact nav drawer that surfaces availability + notifications.

### Navigation by Role

- **Coordinator/Admin**: top tabs + quick-access shortcuts (e.g., `Shift + N` triggers “New Shift” modal).
- **Staff/Volunteer**: hamburger menu (mobile) or narrow left rail (desktop) with `My Schedule`, `Availability`, `Notifications`.
- **Authentication**: shared login page → routes users to appropriate shell based on role.

## Key Screens (MVP)

### 1. Daily Coverage Board (Coordinator Default)

```text
| Unit: Sunrise West ─── Date: Mon Jun 3 |

+------------+-------------------------------+
| 08:00-12:00| RN (2)                        |
|            | [✓ Emily S.]  [⚠ 1 spot open] |
|            | Notes: Medication cart        |
+------------+-------------------------------+
| 12:00-16:00| Counselor (1)                 |
|            | [✱ Assign] [Suggest matches]  |
+------------+-------------------------------+
```

- Column = time blocks; rows grouped by unit/room.
- **Status badges**: green “✓ Filled”, amber “⚠ Partial”, red “✱ Gap”.
- Hover/click opens shift drawer on the right with details, conflict alerts, assignment history.

### 2. Shifts Workspace

- Split layout with **calendar/list toggle**.
- Left: filters (unit, role, status, date range).
- Right: table or calendar. Each row card shows role, headcount, assignments, conflict chips.
- “New shift” floating action button (FAB) on mobile; persistent button on desktop.

### 3. Shift Editor Drawer (Shared Component)

Slides over from the right; supports create/edit.

- Sections: `Basics` (unit, role, timing), `Assignments`, `Checks`.
- Inline validation surfaces conflicts (“Max hours exceeded”, “Role mismatch”) with icons and short guidance.
- Footer with `Save Draft`, `Publish`, `Notify` buttons (notify disabled until published).

### 4. People Directory & Availability (Coordinator)

- Grid of person cards with quick stats (upcoming shifts count, recent declines).
- Detail modal shows contact info, role eligibility, availability calendar, audit trail.
- Availability tab uses segmented control (`Recurring`, `Blackout Dates`) with compact timeline chips.

### 5. Staff / Volunteer Portal

```text
┌───────────────────────────────┐
│ Hello, Maya!                  │
│ Next shift: Tue Jun 4, 12-4pm │
├───────────────┬───────────────┤
│ My Schedule   │ Availability  │
└───────────────┴───────────────┘

Upcoming Shift Card:
--------------------
Counselor — Sunrise West
Tue Jun 4 · 12:00–16:00
[ Accept ]   [ Decline ]
```

- Cards stack vertically on mobile; two-column grid on desktop.
- Banner for urgent actions (e.g., “Update availability for next week”).
- Decline flow captures reason (optional) and confirms coordinator will be notified.

### 6. Admin Settings

- Simple tabbed form: `People`, `Units`, `Roles`, `Audit Log`.
- Tables with inline edit for quick tweaks; modals for add/edit.
- Audit log = sortable table with filters by entity/date/user.

## Component & Pattern Library (MVP)

- **Shift Card**: variant for board, list row, mobile tile. Props: role, time, headcount, status, assigned people, conflicts.
- **Status Badge**: `filled` (green), `partial` (amber), `gap` (red), `draft` (gray), `published` (blue). Paired with iconography.
- **Conflict Chip**: pill with red outline + icon, tooltip text (e.g., “Overlaps with Wed 08–12 shift”).
- **Person Token**: avatar circle with initials + role tag; status dot for accepted/declined.
- **Availability Block**: calendar heat map (coordinator) and simple list chips (staff).
- **Modal/Drawer**: consistent header with title + secondary actions (duplicate, delete), sticky footer CTA.
- **Toasts**: appear bottom-right (desktop) / bottom-center (mobile) for success/errors.

## Visual Styling (Starter Palette)

- Primary: `#2563EB` (indigo-500) for actions and highlights.
- Success: `#16A34A`; Warning: `#F59E0B`; Error: `#DC2626`; Neutral text `#1F2937`; Background `#F8FAFC`.
- Typography: Inter or system sans. Use 16px base, 24px section headers, 18px subheads.
- Use light elevation on cards (shadow-sm) and 8px corner radius to soften tables.

## Responsive & Accessibility Notes

- **Breakpoints**: stack navigation into hamburger under 768px; convert tables into cards with collapsible detail.
- Ensure 44px touch targets for buttons on mobile.
- Contrast ratio ≥ 4.5:1 for text; use icons + text together for status (color alone ≠ meaning).
- Keyboard: focus-visible styling, modal trap, ESC to close drawers, space/enter to activate primary actions.
- Provide ARIA live region for toast notifications and schedule updates.

## Interaction Flows (Highlights)

1. **Create & publish shift**
   - Coordinator clicks `New Shift` → drawer opens.
   - Fill basics → add assignee(s) via typeahead (shows availability badges).
   - Save as draft or publish. On publish, optional notify toggle appears.
2. **Staff accepts shift**
   - Email/SMS deep links to `My Schedule`.
   - Card shows action buttons → accept = instant confirmation toast; decline = capture reason.
3. **Conflict resolution**
   - Conflict chip on coverage board → clicking opens drawer focused on `Checks` tab with suggestions (people alphabetically sorted with availability).

## Implementation Guidance

- Use a component framework (e.g., React + Tailwind or Chakra) to speed styling; keep tokens centralized.
- Favor server-driven data for status chips to keep business logic in the API.
- Start with skeleton states for slow loads; optimistic updates only after audit logging is reliable.
- Instrument analytics to learn which filters/actions coordinators use most (helps prioritize enhancements).

This structure should let you build a clean MVP quickly while leaving room for future features like swap workflows and reporting without major layout changes.
