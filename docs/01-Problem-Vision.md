# 01 — Problem & Vision (RTC Scheduling)

**Org type:** Residential Treatment Center (RTC)  
**Today:** Excel-based schedules → conflicts, late updates, no central visibility.  
**Core problem:** Coordinators spend hours fixing spreadsheets; staff/volunteers miss changes.  
**Vision (MVP):** A simple, secure web app to create/adjust shifts in real time, notify people, prevent conflicts, and show daily coverage by unit/room.

**Success (first 4–6 weeks):**

- 50% less coordinator time on scheduling.
- Zero double-booked shifts in pilot unit.
- Same-day coverage view for all units.

## 09 — Excel Import Template

**Purpose:** Import existing schedules from Excel into the web app easily and accurately.

## Required Columns

To ensure proper import, the Excel file must have the following columns with these exact headers:

| Header      | Description                                  |
| ----------- | -------------------------------------------- |
| Date        | Date of the shift (YYYY-MM-DD)               |
| Unit        | Unit or location where the shift takes place |
| Room        | Specific room or area within the unit        |
| Shift Start | Shift start time (HH:MM, 24-hour format)     |
| Shift End   | Shift end time (HH:MM, 24-hour format)       |
| Role        | Role or position required for the shift      |
| Assigned To | Name of the staff or volunteer assigned      |
| Notes       | Optional notes or special instructions       |

## Sample Data

| Date       | Unit   | Room   | Shift Start | Shift End | Role      | Assigned To | Notes              |
| ---------- | ------ | ------ | ----------- | --------- | --------- | ----------- | ------------------ |
| 2024-06-01 | Unit A | Room 1 | 08:00       | 12:00     | Nurse     | Jane Doe    | Morning medication |
| 2024-06-01 | Unit A | Room 2 | 12:00       | 16:00     | Volunteer | John Smith  | Activity support   |

Ensure all times and dates are consistent and formatted correctly to avoid import errors.
