# Daily Log — Product Requirements Document

## Overview

Daily Log is a feature within the MerlinAI app (accessed from the main dashboard) that gives construction managers a real-time, remote view of everything happening across all their project sites. It replaces the current tab-heavy design (Dashboard, Personnel, Summary, Deliveries, Equipment, Incident, Report) with a streamlined flow that surfaces the most important information first.

> **Context**: Daily Log is one feature within the larger MerlinAI application. Users navigate to it from the app's main dashboard. It does not have its own bottom navigation or standalone notification system — those belong to the parent app.

### Problem Statement

The current daily log interface was built without formal requirements and suffers from low adoption. Key issues:

- **Too many equal-weight tabs** — managers must navigate 7 tabs to understand a single site
- **No cross-project overview** — no way to scan all projects at a glance
- **Data-first, not insight-first** — shows raw data instead of surfacing what matters
- **Not optimized for mobile** — horizontal tabs don't work well on phone screens

### Goals

1. Manager can assess all project health in under 10 seconds from the project list
2. Exception-based attention — problems surface automatically, not buried in tabs
3. Single scrollable project detail — no tab-hopping within a project
4. Each section shows a preview with "View More" for full data
5. One-tap weekly report generation from logged data
6. Simple data entry for field supervisors with offline support

---

## User Personas

### Primary: Project/Operations Manager

- Oversees 3–15+ active construction sites
- Checks daily log remotely on mobile throughout the day
- Needs quick pulse check: "Is anything on fire?"
- Generates weekly reports for stakeholders and clients
- Approves/reviews daily logs submitted by site supervisors

### Secondary: Site Supervisor / Foreman

- Present on site daily
- Logs activities, deliveries, incidents, equipment usage
- Captures photos and notes throughout the day
- Submits daily log for manager review at end of day
- Works in areas with unreliable connectivity

---

## Screen Architecture

```
App Dashboard  -->  Daily Log (Project List)  -->  Project Detail  -->  Reports
```

Daily Log opens from the app's main dashboard. No separate bottom navigation within this feature.

---

## Screen 1: Daily Log — Project List

The landing screen answering: "Across all my sites, what needs attention?"

### Layout

#### Top Bar
- **Back arrow** — returns to main app dashboard
- **Title**: "Daily Log"
- **Report icon** — navigates to Reports screen

#### Date Filter
- Date filter pills: Today | Last 7 Days | This Month | Custom
- When longer ranges selected (month), sections and lists show summary data with "View More" to paginate

#### Search
- Search bar to filter projects by name

#### Alert Banner (conditional)
Only appears when action items exist. Examples:
- "1 incident at Skyline Tower"
- "2 deliveries delayed at Green Valley"
- "Equipment breakdown at Metro Hub"

Each alert is tappable — navigates directly to the relevant project + section.

Alert priority order:
1. Incidents (severity: major > minor > near miss)
2. Delivery delays / missed deliveries
3. Equipment breakdowns
4. Blocked tasks
5. Weather warnings

#### Project Cards (scrollable list)
Each card displays a compact project summary:

**Card header:**
| Field | Description |
|---|---|
| Project name | e.g., "Skyline Tower - Phase 2" |
| Location | City, State |
| Status indicator | Green (on track) / Yellow (needs attention) / Red (critical) |
| Incident badge | **Only shown if incidents > 0.** Red badge on card header (e.g., "1 Incident") with warning icon. Placed next to status dot for immediate visibility. |

**Stats grid (4 columns):**
| Stat | Description |
|---|---|
| Workers | Total workers logged for the period (e.g., "45") |
| Hours | Sum of all worker hours (e.g., "324") |
| Tasks | Completed / total tasks (e.g., "5/8") |
| W.Orders | Completed / total work orders (e.g., "3/4") |

**Card footer:**
| Field | Description |
|---|---|
| Weather | Auto-fetched icon + temperature |
| Last update | Relative timestamp ("25 min ago") |

**Note**: There is no manpower declaration/budget in the system. We only track actual work records — who worked, how many hours, on which project. So project cards show **actual counts** (workers, hours, tasks done, work orders done), not expected-vs-actual comparisons.

**Note on incidents**: Incidents are shown as a highlighted badge on the card header (not in the stats grid) so they immediately catch the manager's eye. Cards with no incidents show only the status dot.

**Status logic:**
- **Green**: No incidents, no missed deliveries, no blocked tasks
- **Yellow**: Minor incident OR delivery delays OR blocked tasks
- **Red**: Major incident OR equipment breakdown OR 3+ issues

Tap any card → navigates to Project Detail screen.

---

## Screen 2: Project Detail — Single Scrollable Page

Full project view as one continuous scroll with collapsible sections. No internal tabs.

### Project Header (sticky)
- Back arrow to return to project list
- Project name + location
- Weather: current conditions (auto-fetched via site GPS coordinates)
- Quick stats bar: workers | hours | tasks | work orders
- Supervisor name + last log submission time
- Report icon — navigate to reports for this project

### Section 1: Activity Timeline
The heart of the daily log — a chronological feed of all logged entries.

- **Display**: Latest entries first, limited to recent 5 entries in collapsed view
- **"View More" button** at bottom to see full activity history
- **Entry format**: Timestamp + logger name + category icon + text + photos (if attached)
- **Filter chips**: All | Notes | Photos | Deliveries | Incidents
- **Categories**: General note, Progress update, Photo log, Delivery, Incident, Weather delay, Equipment

Each entry shows:
- Time (e.g., "2:45 PM")
- Author (e.g., "Rajesh K. — Site Supervisor")
- Category badge (e.g., `Badge.green` "Progress" or `Badge.red` "Incident")
- Description text
- Attached photos (thumbnail grid, tappable to full-screen)

### Section 2: Workers
Shows who actually worked on this project and how many hours. No expected/budgeted manpower — only actual records.

- **Summary**: Total workers count + total hours worked in the selected period
- **Worker list** (preview — shows top 5):
  - Worker name
  - Trade/role (Mason, Laborer, Electrician, Plumber, Carpenter, Helper, etc.)
  - Hours worked (e.g., "8 hrs", "6.5 hrs")
  - Overtime indicator (if hours > 8)
- **"View More" button** → full worker list with sorting/filtering
- **Breakdown by trade**: Compact summary (e.g., Masons: 10, Laborers: 18, Electricians: 4)
- **Visitor log**: Non-worker visitors (inspectors, clients, consultants) with name, purpose, time

### Section 3: Tasks & Progress
- Shows tasks for the selected date/period with status
- Preview shows recent 5 tasks, "View More" for full list
- Status options:
  - `StatusBadge.completed` — Done
  - `StatusBadge.pending` — In Progress
  - `StatusBadge.failed` — Blocked
- Each task shows:
  - Task name
  - Assigned to (worker/team)
  - % completion
  - Blocker reason (if blocked)
  - Attached photos (evidence of completion)
- Summary: X of Y tasks completed

### Section 4: Work Orders
- Work orders linked to this project for the selected period
- Preview shows recent 3 work orders, "View More" for full list
- Each work order shows:
  - Work order number + title
  - Status: `Badge.green` Completed | `Badge.blue` In Progress | `Badge.orange` Pending | `Badge.red` Overdue
  - Assigned to
  - Completion date (if done)
- Summary: X of Y work orders completed

### Section 5: Deliveries
- **Expected**: List with PO reference number, material name, supplier, expected quantity
- **Received**: Material name, quantity received, time, quality status (Accepted / Partial / Rejected)
- **Missed/Delayed**: Highlighted with `Badge.red`, expected date, reason if logged
- Preview shows recent 3 items, **"View More"** for full delivery history
- **Rejected materials**: Flagged with rejection reason and photos

### Section 6: Equipment
- **Equipment on site**: List with:
  - Equipment name + type (Excavator, Crane, Mixer, etc.)
  - Owned / Rented tag
  - Status: Active / Idle / Under Maintenance
  - Hours logged today
  - Operator name
- Preview shows top 4, **"View More"** for full equipment list
- **Equipment movement**: Any equipment transferred in or out of this site today
- **Utilization summary**: Total active hours, idle hours

### Section 7: Incidents & Safety
- **Daily safety checklist**: Status — Completed / Not Completed
  - If completed: show completion time + who completed it
  - If not: highlighted warning
- **Incidents list** (if any): Each incident shows:
  - Time of incident
  - Severity: `Badge.orange` Near Miss | `Badge.yellow` Minor | `Badge.red` Major
  - Description
  - Photos
  - Action taken
  - Reported by
- **"View More"** for full incident history in the period
- **Zero incidents message**: If no incidents, show positive confirmation "No incidents reported"

### Section 8: Weather
- **Auto-fetched** based on site GPS coordinates (no manual entry)
- **Today's conditions**: Temperature, humidity, wind speed, precipitation, general condition (Sunny/Cloudy/Rainy)
- **Weather delays**: Any logged delays with hours lost and reason
- **7-day history**: Mini chart showing temperature + conditions for past week
- **Forecast**: Next 3-day forecast to help planning

### Section Behavior
- All sections are **collapsible** — tap section header to expand/collapse
- App **remembers** user's collapse preferences per project
- Sections with alerts/issues show a **notification dot** on their header even when collapsed
- Default state: All sections expanded on first visit
- **Every section with list data has a "View More" button** — shows the first few items inline, full list on a separate view. This keeps the page scannable even when monthly or weekly date filters produce large datasets.

---

## Screen 3: Reports

Accessible from project list header or project detail.

### Report Types
| Type | Description |
|---|---|
| **Daily Report** | Auto-generated from today's logged data for one project |
| **Weekly Report** | 7-day summary with trends and charts for one project |
| **Custom Range** | User picks start/end date for any project |

### Report Content (auto-compiled from logged data)

1. **Executive Summary** — AI-generated 3-4 line summary of the period
2. **Work Progress** — Tasks completed, work orders completed, milestones achieved, blockers encountered
3. **Workforce Summary** — Total unique workers, total hours logged, trade-wise breakdown, daily worker count trend (bar chart), overtime totals
4. **Work Orders** — Work orders completed vs pending, overdue work orders, completion rate
5. **Material & Deliveries** — Items received, pending, rejected, PO reference summary
6. **Equipment Utilization** — Usage hours by equipment, idle time, maintenance events, equipment movement between sites
7. **Incidents & Safety** — Incident count by severity, incident descriptions, safety checklist compliance rate, corrective actions taken
8. **Weather Impact** — Conditions summary, delay hours attributed to weather, days affected
9. **Photo Highlights** — Selected photos from the period (auto-curated or manager-picked)
10. **Site Activity Summary** — Key log entries, important notes from supervisors
11. **Comparison vs Previous Period** — Side-by-side metrics comparison (this week vs last week)
12. **Next Period Plan** — Editable section for manager to add planned activities

### Report Output Options
- **View in-app** — scrollable report within the app
- **Export as PDF** — branded PDF with MerlinAI header
- **Share link** — generates a shareable URL (for client-facing reports, manager can choose which sections to include)

### Report History
- Previously generated reports are saved and listed with:
  - Report type + date range
  - Project name
  - Generated date
  - Quick actions: View | Share | Download

---

## Data Entry — Supervisor's Interface

### Quick Log (Floating Action Button)
A persistent FAB on the supervisor's screen with quick-add options:
- Add Note (text + optional photo)
- Log Worker Hours
- Log Delivery Received
- Log Incident
- Log Equipment Usage
- Add Weather Delay

Each option opens a short, focused form designed for one-hand use.

### Forms Specifications

#### Add Note
- Text field (multiline, max 500 chars)
- Photo capture (camera + gallery, max 5 photos)
- Category dropdown: General | Progress | Issue | Observation
- Auto-tags: current time, logged-in user

#### Log Worker Hours
- Worker selection (from project roster or search)
- Hours worked (numeric input)
- Trade/role (pre-filled from worker profile)
- Date (defaults to today)
- Overtime flag (auto-set if hours > 8)
- Bulk entry mode: assign same hours to multiple workers

#### Log Delivery
- PO reference (dropdown from open POs)
- Material name (auto-filled from PO)
- Quantity received
- Quality status: Accepted / Partial / Rejected
- Rejection reason (if rejected)
- Photos (delivery challan, material condition)
- Delivery time (auto-fills current time)

#### Log Incident
- Severity: Near Miss / Minor / Major
- Description (text field)
- Persons involved (multi-select from worker list)
- Photos (max 10)
- Immediate action taken
- Location on site (text description)
- Time of incident

#### Log Equipment
- Equipment (dropdown from site equipment list)
- Status: Active / Idle / Breakdown
- Hours used today
- Operator (dropdown from worker list)
- Fuel consumption (optional)
- Notes

#### Weather Delay
- Delay reason: Rain / Storm / Extreme Heat / Flooding / Other
- Hours lost
- Activities affected (multi-select from today's tasks)
- Notes
- Auto-attaches current weather data

### Daily Sign-Off
At end of day, supervisor reviews all entries and taps "Submit Daily Log". This:
- Locks the day's entries from further editing
- Sends push notification to the manager
- Marks the project's daily log as "Submitted" with timestamp

---

## Notifications (Push)

Handled by the parent MerlinAI app's notification system. Daily Log triggers these events:

| Event | Timing | Priority |
|---|---|---|
| Major incident reported | Immediate | Critical |
| Minor incident reported | Immediate | High |
| Delivery missed/delayed | When expected time passes | Medium |
| Equipment breakdown | Immediate | Medium |
| Daily log submitted by supervisor | End of day | Normal |
| Weather warning for site location | When forecast changes | Medium |
| Weekly report ready | Monday morning | Low |

---

## Smart Features

### Auto-Weather
- Fetched automatically using site GPS coordinates via weather API
- Updated every 2 hours during work hours
- No manual entry required from supervisor
- Historical data stored for reporting

### Auto-Report Generation
- One tap generates report from logged data
- AI summarizes key highlights and concerns
- Manager can edit before sharing
- Branded PDF output with MerlinAI design

### Remembered Preferences
- Collapsed/expanded section states per project
- Favorite projects pinned to top of project list
- Default date range preference
- Last viewed project (quick resume)

### Offline Support
- Supervisors can log all entries without connectivity
- Data queues locally and syncs when connection restores
- Sync status indicator on each entry
- Conflict resolution: last-write-wins with audit trail

---

## Component Mapping (Production Flutter)

| UI Element | Production Component |
|---|---|
| Submit / Generate Report buttons | `PrimaryButton.filled` (`.btn-primary.filled`) |
| Cancel / Back buttons | `NaturalButton.outlined` (`.btn-natural.outlined`) |
| Delete / Reject buttons | `ErrorButton.filled` (`.btn-error.filled`) |
| Project status (on track) | `StatusBadge.completed` with `.status-light` |
| Project status (attention) | `StatusBadge.pending` with `.status-light` |
| Project status (critical) | `StatusBadge.failed` with `.status-light` |
| Severity: Near Miss | `Badge.orange.lighter` |
| Severity: Minor | `Badge.yellow.lighter` |
| Severity: Major | `Badge.red.filled` |
| Delivery: Accepted | `Badge.green.lighter` |
| Delivery: Partial | `Badge.orange.lighter` |
| Delivery: Rejected | `Badge.red.lighter` |
| Equipment: Active | `Badge.green.dot` |
| Equipment: Idle | `Badge.gray.dot` |
| Equipment: Breakdown | `Badge.red.dot` |
| Work Order: Completed | `Badge.green.lighter` |
| Work Order: In Progress | `Badge.blue.lighter` |
| Work Order: Pending | `Badge.orange.lighter` |
| Work Order: Overdue | `Badge.red.lighter` |
| All form inputs | `MerlinTextField` (`.merlin-text-field`) |
| All dropdowns | `MerlinDropdown` (`.merlin-dropdown`) |
| Date inputs | `InputDateField` (`.input-date-field`) |
| Date range inputs | `InputDateRangeField` (`.input-date-range-field`) |
| Filter chips | `GroupButton` (`.group-button`) |
| AI Summary button | `AskAIButton` (`.btn-ask-ai`) |
| View More links | `PrimaryButton.ghost` (`.btn-primary.ghost`) |

---

## Data Model (Key Entities)

### DailyLog
- `id`, `project_id`, `date`, `status` (draft/submitted/approved), `submitted_by`, `submitted_at`, `approved_by`, `approved_at`

### LogEntry
- `id`, `daily_log_id`, `type` (note/delivery/incident/equipment/weather_delay), `content`, `photos[]`, `logged_by`, `logged_at`, `category`

### WorkerLog
- `id`, `daily_log_id`, `worker_id`, `worker_name`, `trade`, `hours_worked`, `overtime_hours`, `contractor_id`, `date`

### WorkOrder
- `id`, `project_id`, `title`, `status` (pending/in_progress/completed/overdue), `assigned_to`, `due_date`, `completed_date`

### DeliveryLog
- `id`, `daily_log_id`, `po_id`, `material_name`, `quantity_expected`, `quantity_received`, `quality_status`, `rejection_reason`, `photos[]`, `received_at`

### IncidentLog
- `id`, `daily_log_id`, `severity` (near_miss/minor/major), `description`, `persons_involved[]`, `photos[]`, `action_taken`, `location`, `incident_time`, `reported_by`

### EquipmentLog
- `id`, `daily_log_id`, `equipment_id`, `status` (active/idle/breakdown), `hours_used`, `operator_id`, `fuel_consumption`, `notes`

### WeatherData
- `id`, `project_id`, `date`, `temperature`, `humidity`, `wind_speed`, `precipitation`, `condition`, `fetched_at`

### WeatherDelay
- `id`, `daily_log_id`, `reason`, `hours_lost`, `tasks_affected[]`, `notes`

### Report
- `id`, `project_id`, `type` (daily/weekly/custom), `date_from`, `date_to`, `generated_by`, `generated_at`, `content_json`, `pdf_url`, `share_url`

---

## Success Metrics

| Metric | Target |
|---|---|
| Time to assess all projects (manager) | < 10 seconds |
| Daily log submission rate (supervisor) | > 90% of working days |
| Feature adoption (active users / total users) | > 70% within 3 months |
| Report generation frequency | >= 1 weekly report per project |
| Incident reporting time (from event to logged) | < 15 minutes |

---

## Out of Scope (v1)

- Financial data (costs, invoices, budgets)
- Manpower planning / budgeted headcount per project
- Document management and file storage
- In-app chat or messaging
- Detailed scheduling / Gantt charts
- GPS-based automatic attendance (future enhancement)
- Video capture (photos only in v1)
- Multi-language support
