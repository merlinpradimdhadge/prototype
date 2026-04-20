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