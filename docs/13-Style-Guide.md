# 13 — UI Style Guide (MVP)

This guide codifies the visual language for the Scheduler MVP so designers and engineers can create mockups and production UI with a consistent look and feel.

## Design Principles

- **Clarity over decoration**: emphasize information hierarchy, keep surfaces clean.
- **Actionable states**: every status change should pair color with iconography/text.
- **Accessible by default**: follow WCAG 2.1 AA for color contrast and interaction.
- **Responsive rhythm**: elements adapt gracefully from mobile to desktop while preserving hierarchy.
- **Modular components**: reusable patterns (cards, chips, drawers) drive consistency across modules.

## Design Tokens

| Token             | Value                 | Notes                                   |
|-------------------|-----------------------|-----------------------------------------|
| `color.primary`   | `#2563EB`             | Buttons, accents, active states         |
| `color.primaryDark` | `#1E40AF`          | Hover/pressed                           |
| `color.secondary` | `#0EA5E9`             | Optional tertiary highlights            |
| `color.success`   | `#16A34A`             | Successful checks, filled status        |
| `color.warning`   | `#F59E0B`             | Partial coverage, availability warnings |
| `color.error`     | `#DC2626`             | Conflicts, blocking errors              |
| `color.neutral900`| `#111827`             | Headings                                |
| `color.neutral700`| `#374151`             | Body text                               |
| `color.neutral500`| `#6B7280`             | Secondary text, labels                  |
| `color.neutral200`| `#E5E7EB`             | Dividers, borders                       |
| `color.background`| `#F8FAFC`             | App background                          |
| `radius.sm`       | `4px`                 | Chips, badges                           |
| `radius.md`       | `8px`                 | Cards, dialogs                          |
| `shadow.sm`       | `0 1px 2px rgba(15, 23, 42, 0.08)` | Card elevation                 |
| `spacing.xs`      | `4px`                 | Icon gaps                               |
| `spacing.sm`      | `8px`                 | Chip padding                            |
| `spacing.md`      | `16px`                | Standard gutters                        |
| `spacing.lg`      | `24px`                | Section padding                         |
| `spacing.xl`      | `32px`                | Page margins                            |

## Typography

- **Primary Typeface**: Inter (or system sans fallback `-apple-system`, `BlinkMacSystemFont`, `Segoe UI`).
- **Scale**:
  - `Heading 1` (page titles): 24px, semibold, line-height 32px.
  - `Heading 2` (section titles): 20px, semibold, line-height 28px.
  - `Heading 3` (card headers): 18px, medium, line-height 24px.
  - `Body` (default text): 16px, regular, line-height 24px.
  - `Small` (metadata, labels): 14px, medium, line-height 20px.
- **Usage**:
  - Limit to two levels of headings per screen.
  - Use uppercase only for compact badge labels (e.g., `FILLED`, `DRAFT`) to reduce noise.

## Layout & Spacing

- Base grid: 8px spacing increments.
- Page layout:
  - Top app bar height 64px desktop / 56px mobile.
  - Context bar 56px, pinned below the app bar.
  - Content max width 1280px on desktop with `spacing.xl` side padding.
- Cards: `spacing.md` internal padding; align card content left; actions pinned to bottom on mobile.
- Lists/tables: 48px row height minimum; zebra striping optional with `color.background` tint.

## Color Usage & States

- Primary buttons: `color.primary` background, white text; hover darkens to `color.primaryDark`.
- Secondary buttons: outline style with `color.primary` border; hover fill `rgba(37, 99, 235, 0.08)`.
- Status badges:
  - `Filled`: `color.success` background 12% opacity + icon `✓`.
  - `Partial`: `color.warning` background 16% opacity + icon `⚠`.
  - `Gap`: `color.error` background 16% opacity + icon `✱`.
- Conflict chips: border `color.error`, text `color.error`, background transparent.
- Disabled state: reduce opacity to 40%, keep text contrast ≥ 4.5:1.

## Iconography

- Icon set: Heroicons outline/solid or Fluent icons for Microsoft alignment.
- Size: default 20px; pair with 16px text for alignment.
- Always accompany color-coded icons with text labels/tooltips.
- Provide accessible labels via `aria-label` or visually hidden text for standalone icons.

## Components

- **App Bar**: Solid white background, subtle bottom border using `color.neutral200`.
- **Navigation Tabs**: Underline active tab with `color.primary`; inactive text `color.neutral500`.
- **Buttons**:
  - Primary: filled, medium radius, drop shadow none.
  - Destructive: use `color.error`; confirm dialogs for destructive actions.
- **Cards**: Soft shadow (`shadow.sm`), radius `radius.md`, header area 48px; optional status badge top-right.
- **Drawer / Modal**: Full-height on desktop (width 400–480px), full-screen on mobile. Include sticky footer with primary action button.
- **Form Inputs**: Outline style; focus ring `color.primary` 2px; error text in `color.error`.
- **Table**: Use column headers with uppercase label + `color.neutral500`. Sticky header on desktop; collapse into cards on mobile.
- **Chips/Tags**: Use `radius.sm`, background `rgba(37, 99, 235, 0.12)` for neutral chips; status-specific colors as above.

## Interaction States

- Focus: use 2px outline `color.primary` with 4px corner radius; ensure keyboard navigation highlights actionable elements.
- Hover: lighten background or add underline for links; maintain accessible color contrast.
- Loading: skeleton placeholders for key cards and tables; spinner for blocking actions inside buttons (left-aligned).
- Feedback:
  - Toasts appear bottom-right (desktop) / bottom-center (mobile); success = `color.success`, error = `color.error`.
  - Inline validation messages below inputs; include icon + text.

## Responsive Guidelines

- Breakpoints (matching frontend design doc):
  - Mobile: <600px
  - Tablet: 600–1024px
  - Desktop: >1024px
- Mobile adjustments:
  - Collapse navigation into hamburger menu; use bottom sheet for drawers.
  - Increase touch targets to 48x48px minimum.
  - Stack content vertically with `spacing.md` between sections.
- Tablet:
  - Two-column layouts where possible (filters + content).
  - Maintain 16px body text; avoid shrinking icon sizes.
- Desktop:
  - Use multi-column grids for coverage board; keep max width to avoid overly long lines.

## Accessibility Checklist

- Contrast: verify using tooling (e.g., Stark, Axe) to maintain AA compliance.
- Keyboard: ensure tab order matches visual flow; trap focus in modals/drawers; ESC closes overlays.
- Screen readers: provide descriptive labels for buttons, shift cards, status badges (`aria-live` region for updates).
- Motion: keep transitions subtle (<150ms). Offer motion-reduced variants where possible.

## Asset & Collaboration Notes

- Maintain shared design tokens in Figma and code (CSS variables or Tailwind config).
- Create a Storybook catalog covering:
  - Buttons (primary/secondary/destructive/loading).
  - Status badges, chips, person tokens.
  - Cards (shift, person), drawers, forms.
- Version control design assets alongside code releases to keep parity.

This style guide is the baseline for mockups and implementation. Update it as new components emerge or branding evolves.
