# MerlinAI - Construction ERP

## Project Overview

MerlinAI is a SaaS ERP platform built for the construction industry. This repository is used for creating prototypes of various modules and features before they go into production.

## Domain

Construction business management - covering the full lifecycle from lead acquisition through project execution and material procurement.

## Core Modules

### Operations
- **Process Flow** - Define and manage construction workflows and approval chains
- **Work Orders** - Create, assign, and track work orders for construction activities
- **Tasks** - Task management with assignment, tracking, and status updates
- **Site Activity** - Log and monitor on-site activities, progress, and daily reports

### Supply Chain
- **Materials** - Material catalog, inventory tracking, and stock management
- **Purchase Orders** - Procurement workflow from requisition to delivery

### Sales & CRM
- **Leads** - Lead capture, qualification, and pipeline management
- **CRM** - Customer relationship management and communication tracking
- **Sales** - Quotations, proposals, and deal management

## Purpose of This Repository

This repo contains **prototypes and PRDs** for MerlinAI modules. Each prototype explores UI flows, data models, and feature specifications before production implementation.

## Folder Structure

Organize all work under these top-level directories, grouped by module category (`operations`, `supply-chain`, `sales-crm`).

```
merlinai/
├── prd/                          # Product Requirement Documents
│   ├── operations/               # process-flow.md, work-orders.md, tasks.md, site-activity.md
│   ├── supply-chain/             # materials.md, purchase-orders.md
│   └── sales-crm/               # leads.md, crm.md, sales.md
│
├── prototypes/                   # Interactive prototypes (HTML/CSS/JS)
│   ├── operations/               # process-flow/, work-orders/, tasks/, site-activity/
│   ├── supply-chain/             # materials/, purchase-orders/
│   └── sales-crm/               # leads/, crm/, sales/
│
├── pages/                        # Individual page/screen designs
│   ├── shared/                   # Common pages (login, dashboard, settings)
│   ├── operations/
│   ├── supply-chain/
│   └── sales-crm/
│
├── features/                     # Cross-module feature specs (notifications, RBAC, reports, etc.)
│
└── assets/                       # Shared assets across prototypes
    ├── css/
    ├── js/
    └── images/
```

### Rules

- **Module categories**: Use `operations`, `supply-chain`, `sales-crm` as sub-folders under `prd/`, `prototypes/`, and `pages/`.
- **Naming**: Use `kebab-case` for all folder and file names (e.g., `process-flow`, `purchase-orders`).
- **PRDs**: One `.md` file per module inside `prd/<category>/`.
- **Prototypes**: Each module prototype gets its own folder with `index.html` as the entry point.
- **Pages**: Individual screen/page designs go here. Use `pages/shared/` for screens common across modules (login, dashboard, settings).
- **Features**: Cross-cutting feature specs (not tied to a single module) go directly under `features/` as `.md` files.
- **Assets**: Shared CSS, JS, and images go under `assets/`. Do not duplicate assets inside individual prototype folders.

## Theme

The app uses a custom design system with **Inter** font and a detailed color palette. Full reference with all color tokens, semantic mappings (light/dark), and typography specs is in [`assets/theme-reference.md`](assets/theme-reference.md).

**When building prototypes, always:**
- Use colors and typography from the theme reference - do not use arbitrary values.
- Include the Inter font from Google Fonts.
- Use CSS custom properties (variables) for theme tokens.
- Default to **light mode** unless specified otherwise.

## Component Library (Production Widgets)

The production Flutter app has a reusable component library. **When building prototypes, replicate these components visually in HTML/CSS so designs map 1:1 to production code.** Use the same class names (as CSS classes), visual specs, and behavior described below.

### Buttons

Use CSS class `.btn` combined with type and style modifiers. All buttons have `border-radius: 4px`, `font-weight: 500` (labelSmall), and center-aligned content.

**Sizes:**
| Class | Height |
|---|---|
| `.btn-md` | 44px (default) |
| `.btn-sm` | 40px |
| `.btn-xs` | 36px |
| `.btn-xxs` | 32px |

**Types and when to use:**

| Class | Use for | Filled bg | Text/outline color |
|---|---|---|---|
| `.btn-primary` | Main CTA — save, submit, confirm | `--color-primary-base` | white |
| `.btn-natural` | Secondary — cancel, back, filters | `--color-bg-surface-800` | `--color-text-sub-600`, border: `--color-stroke-soft-200` |
| `.btn-error` | Destructive — delete, remove, reject | `--color-error-base` | white |

**Style variants** (append to type class):
- `.filled` — solid background, white text
- `.outlined` — transparent bg, colored border, colored text
- `.lighter` — light tinted bg (alpha-10), colored text
- `.ghost` — transparent bg, no border, colored text
- `.disabled` — `--color-bg-weak-50` bg, `--color-text-disabled-300` text

Buttons can have optional prefix/suffix icons (20x20 SVG, tinted to match text color).

**Ask AI Button** — Special gradient button with pill shape (`border-radius: 32px`), gradient `#1F3688 → #365AD9`, white text, star icon. Use class `.btn-ask-ai`.

**Segmented/Tab Controls** — `.group-button` container with `--color-bg-weak-50` background, `padding: 4px`, `border-radius: 4px`. Each tab is 36px height. Selected tab gets white background. Use `.group-button-v2` for the variant with box-shadow on the selected tab.

### Badges

**General badge** (`.badge`) — `padding: 8px 2px`, `border-radius: 4px`, inline-flex.

Colors (use as modifier class e.g. `.badge-green`):
`gray`, `orange`, `green`, `sky`, `yellow`, `teal`, `red`, `blue`, `purple`, `white`

Styles:
- `.badge-filled` — solid color bg, white text
- `.badge-light` — lighter bg shade, dark text
- `.badge-lighter` — lightest bg shade, base color text
- `.badge-outlined` — transparent bg, colored border, base color text

Variants:
- `.badge-basic` — text only
- `.badge-dot` — small 4px dot before text
- `.badge-icon-left` / `.badge-icon-right` — 12x12 icon before/after text

Sizes: `.badge-sm` (sub2XSmall text), `.badge-md` (labelXSmall text, default).

**Status badge** (`.status-badge`) — Semantic status indicator with 6px dot or 16x16 icon.

| Class | Dot/icon color | Light bg |
|---|---|---|
| `.status-completed` | `--color-success-base` | `--color-success-lighter` |
| `.status-failed` | `--color-error-base` | `--color-error-lighter` |
| `.status-pending` | `--color-warning-base` | `--color-warning-lighter` |
| `.status-info` | `--color-information-base` | `--color-information-lighter` |
| `.status-disabled` | `--color-faded-base` | `--color-faded-lighter` |

Styles: `.status-light` (colored bg) or `.status-outlined` (transparent bg, `--color-stroke-soft-200` border).

### Text Inputs

**Text field** (`.merlin-text-field`) structure:
```html
<div class="merlin-text-field">
  <label class="input-label">Label <span class="required">*</span></label>
  <div class="input-wrapper">
    <!-- optional prefix icon 20x20 -->
    <input type="text" placeholder="Placeholder" />
    <!-- optional suffix icon -->
  </div>
  <div class="hint-text">Helper text</div>
</div>
```

Specs:
- Label: `font-weight: 500` (labelSmall), color `--color-text-strong-950`
- Required asterisk: `--color-primary-base`
- Input: `padding: 12px`, `border: 1px solid --color-stroke-soft-200`, `border-radius: 4px`
- Focus: border width `1.5px`
- Error state: border color `--color-error-base`
- Placeholder: `--color-text-soft-400`
- Input text: paragraphSmall, `--color-text-strong-950`
- Helper/error text: paragraphXSmall with 20x20 info icon, `--color-text-sub-600` (helper) or `--color-error-base` (error)
- 8px gap between label and input, 6px gap between input and hint

**Date field** (`.input-date-field`) — Same structure as text field but read-only with calendar icon suffix. Placeholder: `__/__/____`. Opens native date picker on click. With time: `__/__/____ __:__ __`.

**Date range field** (`.input-date-range-field`) — Same as date field. Placeholder: `__/__/____ - __/__/____`. Format: `MM/DD/YYYY`.

### Dropdown

**Dropdown field** (`.merlin-dropdown`) — Same label/hint structure as text field.
```html
<div class="merlin-dropdown">
  <label class="input-label">Label</label>
  <select>
    <option value="" disabled selected>Select</option>
    <option value="1">Option 1</option>
  </select>
  <div class="hint-text">Helper text</div>
</div>
```

Specs: Same border, padding, and error styling as text field. Dropdown menu background: `--color-bg-weak-50`. Selected item uses `--color-text-strong-950`, unselected placeholder uses `--color-text-sub-600`.

### Prototype Rules

- **Always use these component patterns** — do not invent custom button, input, badge, or dropdown styles.
- Use the CSS class names above so the prototype visually matches production.
- All components inherit theme colors via CSS variables from `assets/theme-reference.md`.
- In PRDs, reference components by their production name: e.g., "Use `PrimaryButton.filled` for submit" or "Show `StatusBadge.completed` for done items".