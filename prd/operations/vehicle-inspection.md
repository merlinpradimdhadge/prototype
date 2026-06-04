# Vehicle Inspection (DVIR) — Product Requirements Document

## Overview

Vehicle Inspection is a MerlinAI feature for drivers to record the federally required Daily Vehicle Inspection Report (DVIR) at the start and end of every trip. It replaces the current paper sheet ("Vehicle Safety Inspection Sheet — Daily") with a mobile-first digital form, automatic history, and search.

> **Context**: Vehicle Inspection is one feature within the larger MerlinAI Operations module. Drivers navigate to it from the app's main dashboard. The feature does not have its own bottom navigation or notification system — those belong to the parent app.

### Problem Statement

Drivers currently use a printed two-column sheet (Pre-Trip | Post-Trip) on a clipboard. Key issues with the paper workflow:

- **Lost or illegible reports** — paper gets damaged, misplaced, or hard to read after a long day on site.
- **No search or history** — managers cannot quickly look up the last 90 days of inspections for a vehicle (FMCSA 49 CFR 396.11(c)(2) requires 3-month retention).
- **Defects don't reach the right people** — a defect noted on paper at 5 PM may not be communicated to the mechanic until the next morning.
- **No photo evidence** — defects are described in text only.
- **Multi-day trips break the form** — pre-trip and post-trip on a single sheet doesn't work cleanly when a trip spans multiple days.
- **Compliance risk** — DOT roadside inspections expect the prior DVIR to be in the cab; missing or unsigned reports cause violations.

### Goals

1. Driver can complete a full pre-trip or post-trip inspection in under 90 seconds for a vehicle with no defects.
2. Pre-Trip and Post-Trip are recorded against a single DVIR record, with independent timestamps so multi-day trips are supported.
3. Defects automatically prompt for a photo and comment, and surface to managers without paper hand-off.
4. The driver can sign on the device (no separate signature workflow).
5. Any inspection is retrievable from history by date, truck number, driver, or status.
6. Mechanic certification of a previously reported defect is captured before the next pre-trip can be marked safe.

---

## User Personas

### Primary: Driver

- Operates a single truck per shift; may swap vehicles across days.
- Performs pre-trip inspection at yard before leaving, post-trip at end of shift.
- Frequently works in low-signal environments and gloves.
- Wants the inspection to be fast and forgiving — large tap targets, default values that fit the common "everything OK" case.

### Secondary: Fleet / Safety Manager

- Reviews DVIRs to schedule repairs and ensure compliance.
- Pulls inspection history by truck for audits and DOT inspections.
- Wants to be alerted immediately when a driver marks a vehicle "Unsafe to operate".

### Tertiary: Mechanic

- Reviews defects logged by drivers.
- Signs off on repairs and marks the vehicle safe before the next trip.

---

## Screen Architecture

```
App Dashboard
   │
   ▼
Screen 1: Today's Inspections (landing list)
   │   ├─[History icon] ─▶ Screen 3: Inspection History
   │   │                          │
   │   │                          └─[tap row] ─▶ Screen 4: Inspection Detail
   │   │
   │   ├─[+ New Inspection]  ─▶ Screen 2: Form (fresh draft)
   │   └─[tap a card]        ─▶ Screen 2: Form (loads that DVIR)
   │
   ▼
Screen 2: Inspection Form (Pre-Trip / Post-Trip tabs)
   │   ├─[Submit Pre-Trip]  ─▶ tab auto-switches to Post-Trip;
   │   │                       returns to Screen 1 with the card updated
   │   │                       to "Pre-trip done · Post-trip pending"
   │   │
   │   └─[Submit Post-Trip] ─▶ returns to Screen 1 with the card marked
   │                           Complete (Safe / Defects / Unsafe)
```

No separate bottom navigation within this feature — back arrow on Screen 1 returns to the parent dashboard; back arrow on Screen 2 returns to Screen 1.

### Business rules

- **Multiple active DVIRs allowed** — a driver can have several DVIRs in progress at the same time, one per vehicle. Starting a new inspection for a different truck does **not** require finishing the current one. The `+ New Inspection` button is always enabled.
- **One DVIR per vehicle at a time** — a single vehicle cannot have two active (post-trip pending) DVIRs simultaneously. When the driver taps `+ New Inspection`, the truck dropdown defaults to the next vehicle that does **not** already have an active DVIR.
- **Multi-day trips** — a DVIR is "active" until its post-trip is submitted, regardless of how many days have passed since pre-trip. If pre-trip was submitted yesterday (or earlier), the DVIR stays at the top of Screen 1 with a **"Multi-day"** tag and a relative-date stamp like *"Pre-trip · Yesterday 6:30 AM"*. The driver taps the card to open the form on the Post-Trip tab and submit the post-trip whenever the trip ends.
- **Multiple vehicles per day** — supported. Screen 1 shows one card per vehicle the driver is responsible for inspecting, sorted active-first.

---

## Screen 1: Today's Inspections

The landing screen. Shows every DVIR the driver has touched today, plus any DVIR from a prior day that's still awaiting its post-trip (multi-day routes).

### Layout

#### Top Bar
- **Back arrow** — returns to main app dashboard.
- **Title**: "Vehicle Inspection".
- **History icon** (clock-arrow) — navigates to Screen 3.

#### Header strip
- Date: "Today · Wed, Jun 03".
- Summary chip: e.g. *"1 active · 1 complete"* or *"No inspections yet"*.

#### List of relevant DVIRs
Includes (a) any DVIR with post-trip still pending — regardless of when pre-trip was submitted — and (b) any DVIR completed today.

Each card shows:
- **Truck/Unit #** and trailer (if any). If the pre-trip was submitted on a previous day, a **"Multi-day"** tag is shown next to the trailer.
- **Driver name**.
- Two columns: **Pre-Trip** (timestamp, or *"Yesterday · 6:30 AM"* for multi-day) and **Post-Trip** (timestamp or *"Pending"*).
- **Status badge** on the right:
  - `In progress` — blue with pulsing dot. Indicates pre-trip done, post-trip pending. Cards in this state are sorted **to the top** with a soft accent border.
  - `Safe` — green (completed, no defects)
  - `Defects` — orange (completed, defect items recorded but vehicle marked safe)
  - `Unsafe` — red (completed, vehicle marked unsafe to operate)
- Tap → opens Screen 2 with that DVIR loaded. The form opens on the Pre-Trip tab for fresh DVIRs and on the Post-Trip tab for active (pre-done) DVIRs.

Empty state: "No inspections today. Tap **New Inspection** below to start your pre-trip."

#### Action button (sticky bottom)
- **`+ New Inspection`** — primary button, always enabled.
- Tapping it opens Screen 2 with a fresh DVIR. The truck dropdown defaults to the next fleet vehicle that doesn't already have an active DVIR.

---

## Screen 2: Inspection Form

Reached from Screen 1 via `+ New Inspection` (fresh draft) or by tapping a card (loads that DVIR). The form is the same regardless of entry path.

### Layout

#### Top Bar
- **Back arrow** — returns to Screen 1 (Today's Inspections).
- **Title**: "Pre-Trip / Post-Trip Inspection" (or the truck #).

#### Status pill (below top bar)
A single-line summary of the current DVIR state. Examples:
- `New trip — start with Pre-Trip` (gray)
- `Pre-trip submitted at 7:42 AM · Post-trip pending` (blue)
- `Both submitted — start a new trip` (green)

#### Segmented control: Pre-Trip | Post-Trip
- Default selection is the next section to be filled.
- Tapping the other tab shows that section's form (or its read-only summary if already submitted).
- Use the `.delivery-seg` pattern from `purchase-orders` (see CLAUDE.md component library).

#### Form body (scrollable)

**1. Trip Info** — collapsible section, expanded by default.

| Field | Type | Notes |
|-------|------|-------|
| Carrier Name | text | Prefilled from organization, editable |
| Driver Name | text | Prefilled from logged-in user, read-only |
| Truck/Unit # | text (free-form) | **Optional**. Internal fleet nickname (e.g. `TX-204`). When the driver types a value that matches a known fleet entry, the License Plate, Trailer #, and Make/Model fields auto-suggest from the Vehicle record. Free-form input — a fleet doesn't need to be pre-registered. |
| License Plate / Registration No. | text | **Required**. The legal identifier used in DOT/RTO roadside inspections. Auto-suggested when a known Unit # is entered; otherwise typed by the driver. |
| Trailer # | text | Optional. Auto-suggested from the Vehicle record when applicable; otherwise free-form. |
| Date | date | Defaults to today, editable |
| Time Out (pre-trip) / Time In (post-trip) | time | Defaults to current time |
| Odometer | numeric | Required |
| Start Location (pre-trip) / End Location (post-trip) | text | Required |

Use `merlin-text-field`, `merlin-dropdown`, and `input-date-field` components.

**2. Inspection Checklist** — 12 fixed FMCSA items, fresh per pre/post.

Items: Service Brakes, Parking Brake, Steering Mechanism, Lighting Devices/Reflectors, Tires, Horn, Windshield Wipers, Rear Vision Mirrors, Coupling Devices, Wheels/Rims, Emergency Equipment, Fluid Leaks.

Each row:
- Item label on the left.
- Two-button toggle on the right: `OK` (green when active) / `Defect` (red when active).
- When `Defect` is selected, the row expands to reveal a comment textarea and a photo-add tile (reuse `.qf-photo-btn` and `.tl-photo` patterns from daily-log).
- Progress hint at top of section: `8 / 12 checked`.

A convenience action `Mark all OK` button at the bottom of the checklist — sets every unselected item to OK with one tap, for the common defect-free trip.

**3. Vehicle Safe to Operate?** — segmented Yes/No (green / red).
- Required to submit.
- Auto-suggests `No` if any item is marked `Defect`.

**4. Driver Remarks / Defects Noted** — multi-line text field (textarea).

**5. Mechanic / Repair Certification** — collapsible section.
- Only shown when the previous DVIR for this vehicle reported a defect that's not yet certified fixed.
- Fields: Mechanic Name, Date, "Repairs completed and vehicle is safe to operate" checkbox.
- Banner explains: *"A defect was reported on the last DVIR for this truck. The mechanic must certify the repair before this trip can be marked safe."*

**6. Driver Signature** — drawable HTML5 `<canvas>` pad.
- Tap-and-drag to sign. Touch events supported (`touchstart`/`touchmove`/`touchend`).
- `Clear` link below the canvas.
- After signing, shows timestamp: "Signed at 7:42 AM, Jun 3".

**7. Sticky Submit button**
- Label: `Submit Pre-Trip` or `Submit Post-Trip`.
- Disabled until required fields are valid.
- Uses `PrimaryButton.filled` style.

#### After submit
- **Pre-Trip submit**: success toast, segmented control auto-switches to Post-Trip, pre-trip section becomes a collapsed read-only summary at the top. Returning to Screen 1 shows the DVIR card with status `In progress`.
- **Post-Trip submit**: success toast, the screen returns to Screen 1 with the DVIR card now marked `Complete · Safe` / `Defects` / `Unsafe`. The `+ New Inspection` button on Screen 1 becomes enabled again for the driver's next vehicle.

---

## Screen 3: Inspection History

### Layout

#### Top Bar
- **Back arrow** — returns to Screen 1.
- **Title**: "Inspection History".
- **Filter icon** — opens date-range picker (stub in prototype).

#### Search
- Search input — placeholder: "Search by truck #, driver, or date".

#### Filter chips
`All | Today | This Week | This Month | Defects Only | Unsafe`. Use the `.chip` pattern from daily-log.

#### List
Scrollable list of past DVIRs. Each row card shows:
- **Date** with day-of-week badge (e.g. "Jun 03 · Wed").
- **Truck/Unit #** and **Driver name**.
- Two small chips: `Pre-trip ✓` and `Post-trip ✓` (green when submitted, gray/dashed when pending).
- **Status badge** on the right:
  - `Safe` — `StatusBadge.completed` (green)
  - `Defects` — `StatusBadge.pending` (orange)
  - `Unsafe` — `StatusBadge.failed` (red)
  - `Open` — `StatusBadge.info` (blue, post-trip still pending)
- Tap → Screen 3.

Empty state: "No inspections match your filters."

---

## Screen 4: Inspection Detail (read-only)

Reached by tapping a row in Screen 3. Read-only view of the full DVIR.

### Layout

#### Top Bar
- **Back arrow** — returns to Screen 2.
- **Title**: "DVIR · Jun 03, 2026".
- **Overflow menu (•••)** — placeholder, not wired in prototype.

#### Header card
- Date, Truck #, Driver name.
- Overall status badge (Safe / Defects / Unsafe / Open).

#### Pre-Trip section (collapsible, expanded by default)
- Trip Info as a key-value list (carrier, driver, truck #, trailer #, time out, odometer, start location).
- Inspection items as a compact list — items OK show a green check; items with defects show a red dot, the comment, and photo thumbnails.
- Driver signature image.

#### Post-Trip section (collapsible, expanded by default)
- Same structure as Pre-Trip, with end-of-trip fields (time in, end location).
- If post-trip not yet submitted, section shows: *"Post-trip inspection pending — driver has not yet completed this DVIR."*

#### Driver Remarks block — full text.

#### Mechanic Certification block — shown only if filled.

---

## Vehicle Identification

A fleet vehicle has three identifiers; the system stores all three and uses them in different contexts:

| Identifier | Purpose | Where it's surfaced |
|---|---|---|
| **System UUID** | Internal database primary key. Never shown to users. | DB only. |
| **Truck/Unit #** (e.g. `TX-204`) | Internal fleet nickname — short, human-friendly. **Optional**, free-form text. | Form, today's cards, history rows, detail. Driver's quick reference. |
| **License Plate / Registration Number** (e.g. `MD 7B2391`) | Legal identifier required by DOT (US) / RTO (India) for roadside inspections. **Required**. Auto-suggested from the fleet record when a known Unit # is entered; otherwise typed manually. | Form, today's cards (small plate-styled chip), history rows, detail header. **Searchable** in history. |

The `Make / Model / Year` is shown as a hint under the License Plate field so the driver can visually confirm they're inspecting the correct vehicle.

When the driver changes the Truck/Unit # in the dropdown, the License Plate, Trailer #, and Make/Model fields auto-update from the Vehicle record. Only the Truck/Unit # is user-selected; the rest is derived.

## Data Model

```
Vehicle {
  id: UUID                    // system primary key, never shown
  unitNumber: string          // e.g. "TX-204" — fleet nickname
  licensePlate: string        // e.g. "MD 7B2391" — legal ID
  defaultTrailer: string | null
  make, model, year
  vin: string                 // optional, captured for compliance, not shown in UI
}

DVIR {
  id: string                  // e.g. "DVIR-2026-0612"
  vehicleId: UUID             // FK to Vehicle
  vehicleUnit: string         // snapshot of Truck/Unit # at time of inspection
  vehiclePlate: string        // snapshot of License Plate at time of inspection
  trailer: string | null
  carrier: string
  driver: { id, name }
  preTrip:  InspectionHalf
  postTrip: InspectionHalf | null
  vehicleSafeToOperate: "yes" | "no" | null
  mechanicCertification: { name, date, note } | null
  status: "draft" | "pre-trip-done" | "completed"
  createdAt: timestamp
  updatedAt: timestamp
}

InspectionHalf {
  date: date
  time: time
  odometer: number
  location: string
  items: InspectionItem[12]
  remarks: string
  signature: dataURL
  signedAt: timestamp
}

InspectionItem {
  name: string                // one of the 12 FMCSA items
  state: "ok" | "defect" | null
  comment: string
  photos: string[]            // attachment URLs
}
```

---

## Future Work

- **Org-configurable checklist** — fleet manager can add custom items (e.g. "Lift gate" for box trucks, "Refrigeration unit" for reefers).
- **Offline support** — draft submissions queue locally when offline, sync when connectivity returns. Critical for long-haul.
- **PDF export & email** — driver and safety manager can export a DVIR as PDF for DOT inspections and recordkeeping.
- **Manager review queue** — surface DVIRs with defects to fleet/safety managers for triage.
- **Mechanic mobile flow** — mechanic certifies repairs from their own device, not on the driver's form.
- **Auto-populate previous defects** — on the next pre-trip, show what was flagged in the prior post-trip so the driver explicitly confirms the issue persists or has been fixed.
