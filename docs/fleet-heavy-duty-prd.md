# Squibbon Shop — Fleet & Heavy-Duty Service PRD

**Type:** Product / functional requirements (requirements capture only — no technical/data/API design)
**Status:** Draft for review
**Applies to:** Squibbon Shop module (extension) — fleet & heavy-duty service

Turns Squibbon's existing fleet/TMS DNA into a first-class **heavy-duty and fleet-service** offering: mobile/roadside calls, unit-and-component repair history, DVIR-driven work, a fleet-customer portal, and national-account statement billing. Companion: [maintenance-compliance PRD](maintenance-compliance-prd.md) (DOT/NSC/PMVI, circle checks, dispatch readiness — cross-referenced, not duplicated here) · [work-order flow](repair-shop-workorder-usecases.md) · [marketing & retention PRD](marketing-retention-prd.md). Mockups: [docs/mockups/fleet-heavy-duty/](mockups/fleet-heavy-duty/00-index.md).

> **Why this module:** Squibbon is already a TMS with fleet customers — it knows units, meters, dispatch and compliance before a repair-shop competitor writes its first work order. **Fullbay** leads heavy-duty shop management precisely because it speaks fleet (units, components, DVIR, national accounts); Squibbon can match it without leaving its home turf. This PRD captures the fleet-and-heavy-duty service capabilities that close that gap and make Squibbon the obvious choice for a shop that services **fleets** — including its own.

## Background / why now

Light-duty repair shops and heavy-duty/fleet shops look similar on the surface and diverge sharply underneath. A fleet shop doesn't service a walk-in "vehicle" — it services a **unit** (a numbered tractor, trailer, reefer, straight-truck or bus) with **components** (engine, transmission, reefer unit, axles, brakes) that each accumulate their own repair history and warranty. Work doesn't start at the counter — it starts with a **driver vehicle inspection report (DVIR)** defect, a **breakdown on the 401**, or a **preventive-maintenance interval** coming due on odometer or engine hours. The customer isn't an individual paying at pickup — it's a **fleet account** on Net 30 that wants one **monthly statement** across forty work orders, with purchase-order authorization and a self-service portal so it stops phoning for status.

Squibbon's unfair advantage is that it already models most of this on the TMS side — units, meters, telematics/ELD, dispatch and compliance. This module extends that fleet DNA *into the shop*, so a Squibbon-running shop can win heavy-duty and mixed-fleet work that light-duty tools (and even some heavy-duty tools without TMS roots) can't service end-to-end. Continuity example used throughout: **Acme Fleet Ltd** (Net 30 fleet account, **Northstar Logistics** parent, **Mississauga ON**) bringing a **2019 Ford F-150** and heavier units in for service.

## Goals

| Goal | Measure |
|---|---|
| Service fleets where the work happens | Share of work orders opened from roadside/mobile calls and DVIR defects |
| Repair the *unit*, not just the vehicle | % of work orders linked to a unit + component; component-level history coverage |
| Cut status-call volume | Portal adoption per fleet account; status calls/week per account; portal-approved estimates |
| Bill fleets the way they buy | Accounts on monthly statements; PO-authorized spend; AR days-to-pay (target Net 30) |
| Reuse fleet DNA, don't rebuild | Units/meters/telematics reused from TMS; compliance handled by the maintenance module |

## Personas

| Persona | Needs from this module |
|---|---|
| **Service advisor / shop manager** | Open work orders from calls and DVIRs; see unit history and PM due before quoting; manage the dispatch board. |
| **Fleet manager** (customer side) | One portal for every unit's status, remote estimate approval, history/documents, and a monthly statement — not forty phone calls. |
| **Mobile / road technician** | Take a service call on a phone, capture location, travel and on-site labour, work from service-truck stock, convert to a work order — offline-tolerant. |
| **Dispatcher** (shop side) | Match service calls to the right service truck / mobile tech, watch statuses and locations, balance load. |
| **Parts / inventory** | Stock service trucks, replenish from on-site usage, see what left on which call. |
| **A/R & accounting** | Run monthly statements per account, enforce credit limits and PO authorization, age receivables, reconcile deposits, export to QuickBooks/Xero/Sage. |
| **Driver** (fleet's employee) | Submit DVIR defects (or have telematics submit them) and see them turn into certified repairs. |

---

## 1. Mobile & roadside service

**Business requirement:** Heavy-duty work frequently happens **on-site or roadside**, not in a bay. The shop needs to intake emergency requests, dispatch a service truck or mobile technician, capture where the work happened and how long it took, and convert the call into a billable work order.

| ID | Requirement |
|----|-------------|
| MOB-1 | User can create a **service call** for work performed on-site/roadside rather than in a bay, against a fleet account and (where known) a specific unit. |
| MOB-2 | User can intake **emergency requests** by phone, text, or the fleet portal, and via **fleet emergency/breakdown networks** (e.g., FleetNet), capturing the requesting party, reference/case number, complaint, and location. |
| MOB-3 | User can **dispatch** a call to a specific **service truck** or **mobile technician**. |
| MOB-4 | Call captures **location / GPS**, **travel time**, and **on-site labour** (and arrival/departure times). |
| MOB-5 | Call moves through statuses: **requested → dispatched → en route → on site → complete** (with cancelled/unable-to-complete as terminal states). |
| MOB-6 | Mobile technician can view assigned calls, see the location/route, and update status from the field (mobile, offline-tolerant). |
| MOB-7 | Technician can record parts used **from service-truck stock** on the call, and add labour/notes/photos. |
| MOB-8 | User can **convert a service call into a work order** (carrying customer, unit, complaint, location, travel, labour, and parts used), or attach the call to an existing work order. |
| MOB-9 | Travel and on-site time can be billed (e.g., trip/mileage and call-out charges) on the resulting work order per account rate rules (§5). |
| MOB-10 | A roadside defect or failed roadside repair can be flagged for compliance/dispatch follow-up — handled by the maintenance-compliance module, not duplicated here. |

---

## 2. Units, components & PM

**Business requirement:** Fleet assets are **units**, each composed of **components** with their own meters and repair history. The shop needs a multi-unit roster, component-level history, and per-unit preventive-maintenance schedules with due/overdue tracking — reusing the units, meters and telematics Squibbon's TMS already has.

| ID | Requirement |
|----|-------------|
| UNIT-1 | User can model a fleet asset as a **unit** with a **unit number**, **type** (tractor / trailer / reefer / straight-truck / bus), VIN/plate, make/model/year, and owning fleet account. |
| UNIT-2 | A unit has **components** (e.g., engine, transmission, reefer unit, axles, brakes), each with identifying details (make/model/serial) where known. |
| UNIT-3 | A unit (and where applicable a component, e.g., reefer unit) tracks **meters** — **odometer** and **engine-hours**. |
| UNIT-4 | Meter readings can come from manual entry or, where Squibbon already integrates telematics/ELD, automatically (no new mechanism — reuse the maintenance module's meter/telematics capability). |
| UNIT-5 | The shop can see **component-level repair history** — what was repaired/replaced on which component, when, on which work order, at what meter. |
| UNIT-6 | User can define **per-unit preventive-maintenance schedules** (by time, odometer, and/or engine-hours) and see items as **upcoming, due, or overdue**. |
| UNIT-7 | PM and inspection scheduling, due/overdue logic, and DOT/NSC/PMVI compliance are owned by the **maintenance-compliance module** — this module **surfaces and links to** that status from the unit and at quoting time, and does not re-implement it. |
| UNIT-8 | User can view a **multi-unit fleet roster** for an account (units, type, meters, PM/compliance status, open work) with search and filter. |
| UNIT-9 | When opening or quoting a work order, the advisor sees the unit's current meters, PM-due items, open defects, and compliance status (from the maintenance module) to bundle due work. |
| UNIT-10 | Light-duty vehicles (e.g., the **2019 Ford F-150**) and heavy units coexist on the same account roster; unit type drives which fields/components apply. |

---

## 3. DVIR → work order

**Business requirement:** Fleet work originates from **driver vehicle inspection report (DVIR)** defects. The shop needs to import those defects, raise work orders from them, map each defect to a job, and track the defect through repair to **certified resolution**, linked to compliance records.

| ID | Requirement |
|----|-------------|
| DVIR-1 | System can **import DVIRs / reported defects** from drivers or telematics into a shop-side **DVIR inbox** (unit, driver, date/time, defect list, severity, location, meter). |
| DVIR-2 | User can **raise a work order** from one or more reported defects (single defect, or batch several defects on a unit into one work order). |
| DVIR-3 | User can **map a defect to a job** on the work order, so the repair is traceable to the reported defect. |
| DVIR-4 | System tracks each defect through **defect → repair → certified resolution**, including who certified the repair. |
| DVIR-5 | Defect severity (e.g., major/minor) is visible, and major/unresolved defects surface for dispatch-readiness handling **in the maintenance-compliance module** (not duplicated here). |
| DVIR-6 | Defects and their certified resolutions **link to DOT/NSC compliance records** owned by the maintenance module (circle-check/inspection defect closure), with no duplication of that workflow. |
| DVIR-7 | A defect can be marked **deferred/declined** by the fleet (e.g., monitor) and is eligible for declined-work follow-up via the marketing & retention module. |
| DVIR-8 | The DVIR inbox supports filtering by unit, account, severity, status, and date, and shows which defects have/haven't become work orders. |

---

## 4. Fleet customer portal

**Business requirement:** Fleet managers want to **self-serve** — see each unit's repair status, approve estimates remotely, and pull history and documents — so they stop phoning for status. This portal extends the customer-facing approval/estimate experience to a **multi-unit fleet account**.

| ID | Requirement |
|----|-------------|
| PORT-1 | A fleet manager can sign in to a **self-service portal** scoped to their fleet account(s) and units. |
| PORT-2 | Portal shows a **multi-unit dashboard** — each unit's current **repair status** (in shop / awaiting approval / in progress / ready / roadside call open), with the open work order/estimate. |
| PORT-3 | Fleet manager can **review and approve (or decline) estimates remotely**, including line-item authorization where the shop enables it, with the approval recorded against the work order. |
| PORT-4 | Portal exposes per-unit and per-account **service history and documents** (work orders, invoices, inspection/PM certificates, photos) for download. |
| PORT-5 | Portal reflects **statement/AR** visibility for the account where enabled (open balance, statements, PO references) — see §5. |
| PORT-6 | Approvals/declines and document access in the portal are tied to **portal user permissions** for the account (e.g., who may authorize spend). |
| PORT-7 | Portal is **bilingual (EN/FR)** and consistent with Squibbon's customer-facing styling. |
| PORT-8 | Portal activity (status changes, ready-for-pickup, approval requests) can trigger notifications via the existing comms/marketing channels — transactional, not gated by marketing consent. |

---

## 5. National-account & statement billing

**Business requirement:** Fleets buy on **accounts**, not per-visit. The shop needs **consolidated monthly statements** (many work orders → one statement), **PO authorization and spend limits**, **credit limits and Net-30 terms**, **AR aging and statements**, **negotiated rate sheets**, **accounting exports**, and **end-of-day deposit reconciliation**.

| ID | Requirement |
|----|-------------|
| BILL-1 | User can configure a **fleet account** with **payment terms** (e.g., **Net 30**), **credit limit**, billing contact, and statement preferences (e.g., monthly cycle). |
| BILL-2 | System can produce a **consolidated monthly statement** per account that rolls up **many work orders into one statement** for the period. |
| BILL-3 | User can require and capture **purchase-order (PO) authorization** at the **per-unit or per-account** level, with **spend limits** that warn/block work or invoicing beyond the authorized amount per account settings. |
| BILL-4 | System tracks the account's **credit limit** and outstanding balance, and warns/blocks new charges that would exceed it per settings. |
| BILL-5 | User can maintain **negotiated rate sheets per account** — labour rates, parts pricing/markup, and call-out/travel rates — applied automatically to that account's work orders and calls (§1). |
| BILL-6 | System provides **accounts-receivable aging** (e.g., current / 30 / 60 / 90+) by account and overall, and can issue **statements** to fleet accounts. |
| BILL-7 | User can **export** invoices/statements and the associated ledger to **QuickBooks**, **Xero**, and **Sage**. |
| BILL-8 | User can perform **end-of-day deposit reconciliation** — reconcile the day's payments/deposits against recorded transactions before export. |
| BILL-9 | A unit-level or account-level **PO reference** flows onto each work order and appears on the consolidated statement so the fleet can match charges to authorizations. |
| BILL-10 | Statement, AR, PO, and rate-sheet data are **bilingual (EN/FR)** on customer-facing output and visible to the fleet via the portal (§4) where enabled. |

---

## 6. Data entities (conceptual)

> Conceptual only — no schema/field-level design here. New persisted entities follow the platform's tenancy rules (see Data & tenancy).

| Entity | Core idea |
|---|---|
| Service call | roadside/mobile job · account · unit · complaint · source (phone/text/portal/network) · location/GPS · status (requested→…→complete) · dispatched-to (truck/tech) · travel time · on-site labour · parts used · resulting work order |
| Service truck | mobile resource · assigned technician · current location · stock list |
| Service-truck stock | parts/quantities carried on a truck · replenishment from on-site usage |
| Unit | unit number · type (tractor/trailer/reefer/straight-truck/bus) · VIN/plate · make/model/year · fleet account · meters |
| Component | belongs to unit · type (engine/transmission/reefer/axles/brakes…) · identifiers · repair history · (optional) warranty |
| Meter reading | unit/component · odometer / engine-hours · value · source (manual/telematics) · timestamp |
| PM schedule (ref) | per-unit preventive-maintenance interval & due/overdue — **owned by maintenance module**, referenced here |
| DVIR / defect | unit · driver · date · defects · severity · location · meter · status (defect→repair→certified) · linked job/work order · compliance link |
| Fleet account | account · parent (e.g., Northstar Logistics) · terms (Net 30) · credit limit · billing contact · statement cycle · portal users |
| Portal user | fleet-side login · account scope · permissions (view/approve/authorize spend) |
| PO authorization | account or unit scope · PO number · authorized amount/spend limit · status |
| Rate sheet | per account · labour rates · parts pricing/markup · call-out/travel rates |
| Statement | account · period · rolled-up work orders/invoices · balance · issued date |
| AR aging record | account · buckets (current/30/60/90+) · outstanding |
| Deposit reconciliation | day · payments/deposits · reconciled vs recorded · export status |

---

## 7. Integrations

| ID | Requirement |
|----|-------------|
| INT-1 | **Work orders / estimates / invoices** — service calls and DVIR defects open work orders; statements roll up invoices (reuse the repair-shop work-order module). |
| INT-2 | **Units, meters & telematics/ELD** — reuse the TMS/maintenance units, odometer/engine-hour readings, and existing telematics/ELD integrations (e.g., Samsara, Isaac, Geotab); no new telematics mechanism. |
| INT-3 | **Maintenance-compliance module** — PM scheduling, DVIR/circle-check defect closure, DOT/NSC/PMVI, and dispatch readiness are owned there and surfaced/linked here. |
| INT-4 | **Fleet emergency/breakdown networks** (e.g., FleetNet) — intake of emergency service requests/case references. |
| INT-5 | **Accounting** — export invoices/statements/ledger to **QuickBooks**, **Xero**, and **Sage**. |
| INT-6 | **Payments** — reuse the shop payment/AR capability; end-of-day deposit reconciliation feeds export. |
| INT-7 | **Comms / portal notifications** — reuse the messaging channel for transactional status/approval notifications; declined fleet work feeds the marketing & retention module. |
| INT-8 | **Mapping / location** — capture GPS/location for service calls and show the dispatch board (location display). |

---

## 8. Roles & permissions

| ID | Requirement |
|----|-------------|
| PERM-1 | **Service advisors / managers** can create service calls and work orders, view unit/component history, and manage the dispatch board. |
| PERM-2 | **Dispatchers** can assign calls to service trucks/mobile techs and update call status. |
| PERM-3 | **Mobile technicians** can view and update only their assigned calls and record on-site labour, parts, and status from the field. |
| PERM-4 | **A/R / accounting** users can configure account terms, run statements/AR, manage rate sheets and PO authorizations, reconcile deposits, and run accounting exports. |
| PERM-5 | Only authorized users can **override** PO/credit-limit/spend-limit warnings or blocks; overrides require a reason and are traceable. |
| PERM-6 | **Fleet portal users** see only their own account(s)/units; **approving estimates or authorizing spend** is gated by portal-user permission (§4). |
| PERM-7 | **Drivers** can submit DVIR defects where the mobile flow supports it (defect submission only). |
| PERM-8 | Statement, AR, and rate-sheet configuration cannot be changed without elevated permission; financial history is not deletable without proper permission. |

---

## 9. Non-goals

| ID | Non-goal |
|----|----------|
| NG-1 | This module does **not** re-implement PM scheduling, inspections, circle checks, DOT/NSC/PMVI, or dispatch-readiness logic — those live in the **maintenance-compliance PRD** and are referenced, not duplicated. |
| NG-2 | Not a **telematics/ELD platform** — it reuses Squibbon's existing telematics integrations; no new device/provider mechanism. |
| NG-3 | Not a **dispatch/routing optimization** engine — the dispatch board assigns and tracks calls; it does not auto-optimize routes or load-plan. |
| NG-4 | Not a **TMS trip/load** redesign — fleet *operations* (trips, loads, ELD HOS) remain in the TMS; this is fleet **service**. |
| NG-5 | Not a **full accounting/GL** system — Squibbon bills, ages AR, and **exports** to QuickBooks/Xero/Sage; it is not the books of record. |
| NG-6 | Not a **parts procurement/inventory** overhaul — service-truck stock is tracked at the level needed for calls; deep inventory/procurement remains the shop inventory module's concern. |
| NG-7 | Squibbon is **not the legal compliance authority** — compliance tooling and claims are bounded by the maintenance-compliance PRD's non-goals. |
| NG-8 | No marketing messaging without consent — fleet declined-work follow-up runs through the marketing module under its CASL rules; portal/status notifications are transactional. |

---

## 10. Acceptance criteria

| ID | Criterion |
|----|-----------|
| AC-1 | User can create a roadside/mobile **service call**, intake it (phone/text/portal/network), dispatch it to a service truck or mobile tech, and watch it move requested → dispatched → en route → on site → complete. |
| AC-2 | A mobile technician can capture **location/GPS, travel time, on-site labour, and service-truck parts** on a call from the field. |
| AC-3 | User can **convert a service call into a work order** carrying customer, unit, complaint, location, travel, labour, and parts. |
| AC-4 | User can model a **unit** with type, **components**, and **meters** (odometer/engine-hours), and view **component-level repair history**. |
| AC-5 | User sees **per-unit PM due/overdue** and compliance status (sourced from the maintenance module) when quoting, and a **multi-unit roster** per account. |
| AC-6 | System can **import a DVIR**, **raise a work order** from its defects, **map a defect to a job**, and track **defect → repair → certified resolution** linked to compliance. |
| AC-7 | A fleet manager can sign in to the **portal**, see a **multi-unit status dashboard**, and **approve an estimate remotely**, with the approval recorded on the work order. |
| AC-8 | Fleet manager can pull **per-unit history and documents** from the portal. |
| AC-9 | User can configure a **Net-30 fleet account** with **credit limit** and produce a **consolidated monthly statement** rolling up many work orders. |
| AC-10 | System enforces **PO authorization / spend limits** (per unit or account) and **credit limit**, with authorized overrides traceable. |
| AC-11 | **Negotiated rate sheets** apply automatically to an account's work orders and calls. |
| AC-12 | System shows **AR aging**, issues statements, supports **end-of-day deposit reconciliation**, and **exports to QuickBooks/Xero/Sage**. |
| AC-13 | Customer-facing portal, statements, and rate output are available in **EN/FR**. |
| AC-14 | No capability in this module **duplicates** the maintenance-compliance module; PM/inspection/dispatch-readiness is referenced and linked. |

---

## Data & tenancy

Squibbon is **schema-per-tenant** (one schema per fleet/shop tenant), with **BIGINT** primary keys and JPA running **`ddl-auto=validate`**. Consequence for this PRD: any **new persisted entity** introduced here (e.g., service call, service truck/stock, rate sheet, statement, PO authorization, portal user) requires a **per-tenant database migration** before use — entities are never auto-created at runtime. UI is **Next.js + Flowbite-React (Spike)** in the Squibbon palette (purple **#c479c2** / teal **#16CDC7**, **Plus Jakarta Sans**), **bilingual EN/FR**. This is noted once here and is the only platform/implementation note in this document; everything above is requirements capture only.

---

## Screens

Mockups live in `docs/mockups/fleet-heavy-duty/`.

| # | File | Screen |
|---|------|--------|
| 01 | `01-mobile-roadside-dispatch.svg` | Dispatch board: service calls vs service trucks/mobile techs, statuses, locations |
| 02 | `02-fleet-unit-component-history.svg` | Fleet unit detail: components, meters (odometer/engine hrs), PM due, repair history |
| 03 | `03-dvir-to-workorder.svg` | DVIR inbox: driver-reported defects → raise work order |
| 04 | `04-fleet-portal.svg` | Customer-facing fleet portal: unit statuses + remote estimate approval |
| 05 | `05-national-account-billing.svg` | Fleet account statement billing + AR aging + accounting export |

---

## Sources

- [Fullbay](https://www.fullbay.com/)
- [Fullbay — service orders](https://www.fullbay.com/products/service-orders/)
- [Heavy-Duty Repair Shop Software 2026 — Heavy Duty Journal](https://heavydutyjournal.com/heavy-duty-repair-shop-software-2026/)
