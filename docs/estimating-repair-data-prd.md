# Squibbon Shop — Estimating Intelligence & Repair Data PRD

Wire a licensed labour/repair-data guide directly into the estimate so advisors and technicians quote and repair from manufacturer-grade data — not memory.

**Type:** Product / functional requirements (requirements capture only — no technical/data/API design)
**Status:** Draft for review
**Applies to:** Squibbon Shop module (extension)

Closes the #1 functional parity gap vs Tekmetric, Shopmonkey and Mitchell 1: every serious competitor wires a licensed labour/repair-data guide into the estimate; Squibbon does not yet. Companion: [work-order field spec](repair-shop-workorder-fields.md) · [repair-shop module spec](repair-shop-module-spec.md) · [marketing & retention PRD](marketing-retention-prd.md). Mockups: [docs/mockups/estimating-repair-data/](mockups/estimating-repair-data/00-index.md).

> **Why this module:** an estimate is only as good as the labour time and parts behind each line. Today a Squibbon advisor types hours from memory or a paper guide — slow, inconsistent, and impossible to defend to a fleet customer. Competitors pull labour times, OEM parts, fluids and factory schedules straight into the estimate from a licensed guide, then give the technician the procedure, torque spec and wiring diagram on the same screen. This module brings that intelligence to Squibbon: **estimate from data, repair from data, and show your work.**

## Background / why now

Every credible shop-management platform now treats repair data as table stakes: Tekmetric, Shopmonkey and Mitchell 1 all embed a licensed labour guide (ALLDATA, Mitchell 1 ProDemand, Identifix, MOTOR) so an advisor can look up a vehicle's labour operation and one-click it — labour time **and** OEM parts — into the estimate. Squibbon's estimate is still hand-keyed, which costs money on every quote (under-billed hours, guessed parts) and credibility on every fleet account. Just as important, Squibbon's technicians have no in-app access to OEM procedures, torque specs, wiring diagrams, TSBs or open recalls — so the quote may be priceable but the job is not yet defensibly *repairable* from the same system. Closing this gap is the single highest-leverage functional move for the Shop module, and it does so on the shop's **own** repair-data subscription (bring-your-own credentials, like Tekmetric), so Squibbon resells no data and stores no third-party content.

## Goals

| Goal | Measure |
|---|---|
| Quote from licensed data, not memory | % of estimate labour lines sourced from a guide; hours-billed accuracy |
| Make every labour line defensible | % of labour lines showing a labour-time source & value |
| Put OEM repair info on the technician's screen | Repair-info panel opens per RO; procedures attached to jobs |
| Catch safety & service gaps at intake | Open recalls surfaced per VIN; prior-service-history hit rate |
| Use the shop's own subscription | 100% of provider access via shop-entered credentials (no Squibbon-resold data) |
| Ship it bilingually & per-tenant | EN/FR coverage; each tenant configures its own providers |

## Personas

- **Service advisor (T. Hopper, Mississauga ON)** — builds estimate WO-10428 for Acme Fleet Ltd; needs accurate labour times and OEM parts fast, and needs to show a fleet customer where a number came from.
- **Technician (A. Roy)** — assigned the job on the 2019 Ford F-150 (plate ABCD 123); needs the removal procedure, torque spec and wiring diagram on the same screen, plus any DTC definitions and the codes pulled at intake.
- **Shop owner / manager** — enters and owns the shop's ALLDATA / ProDemand / Identifix / MOTOR subscription credentials; wants consistent pricing and a defensible paper trail.
- **Parts / service coordinator** — relies on OEM part numbers, fluid types and capacities pulled with the labour operation to order right the first time.

---

## 1. Labour & repair-data integration

**Business requirement:** advisors must be able to look up a vehicle's labour operations from a licensed provider and pull labour time, OEM parts, fluids and factory schedules into the estimate — using the shop's own subscription.

- **LAB-1** — Squibbon integrates third-party labour/repair-data providers: **ALLDATA**, **Mitchell 1 ProDemand**, **Identifix Direct-Hit** and **MOTOR**.
- **LAB-2** — Provider access uses the **shop's own subscription credentials** entered in settings (bring-your-own subscription, like Tekmetric); Squibbon resells no repair data.
- **LAB-3** — From inside the estimate editor, the advisor opens a **labour-guide side panel** and looks up a vehicle's labour operations.
- **LAB-4** — Guide results return **labour times**, **OEM parts**, **fluids & capacities**, and **factory maintenance schedules** for the decoded vehicle.
- **LAB-5** — **One-click transfer** of a guide line (labour **and** its associated parts) into the estimate as a job/labour line + part lines.
- **LAB-6** — Each transferred labour line shows the **labour-time source** (provider) and the **labour-time value** on the line.
- **LAB-7** — Support **flat-rate** (guide book time) vs **measured/actual** time, and let the advisor choose or override per line (override is flagged).
- **LAB-8** — A tenant sets a **default provider** with **fallback** to another configured provider when the primary returns no result.
- **LAB-9** — Look-up requires the vehicle's **VIN to be decoded first** (see §3); the guide query keys off the decoded year/make/model/engine.
- **LAB-10** — Advisor can search the guide by **operation/keyword** (e.g. "front brake pads", "water pump") and browse the returned operations before transferring.
- **LAB-11** — Pulled **OEM parts** carry part number, description and (where provided) fluid/capacity, ready for the parts coordinator to order or price (§ part lines in the work-order field spec).
- **LAB-12** — Factory **maintenance schedule** results can be transferred as a recommended service (e.g. a mileage-due interval) onto the estimate.
- **LAB-13** — Settings page lets an authorized user enter, test, enable/disable and remove each provider's credentials per tenant.
- **LAB-14** — Guide look-up and transferred labels are available in **EN/FR**; provider content is shown as the provider returns it.
- **LAB-15** — If no provider is configured or none returns a result, the advisor can still add a **manual** labour line (no guide source shown).

## 2. Repair information viewer

**Business requirement:** technicians need OEM repair information on the same screen as the job — procedures, specs, diagrams and trouble-code definitions — and must be able to attach what they used to the RO.

- **INFO-1** — A **repair-information side panel** is openable from any job/RO line.
- **INFO-2** — Panel surfaces **OEM repair / removal & installation procedures** for the operation.
- **INFO-3** — Panel surfaces **torque specifications** for the operation/fasteners.
- **INFO-4** — Panel surfaces relevant **Technical Service Bulletins (TSBs)**.
- **INFO-5** — Panel surfaces **DTC (diagnostic trouble-code) definitions**.
- **INFO-6** — Panel surfaces **interactive wiring diagrams**.
- **INFO-7** — Panel surfaces an **ADAS quick-reference** (calibration triggers / requirements summary).
- **INFO-8** — Panel surfaces **fluid & capacity specifications**.
- **INFO-9** — Technician can **attach a procedure or reference** to the RO or job so it travels with the work and is visible to whoever picks it up.
- **INFO-10** — A DTC can be **deep-linked to its procedure** (open the DTC definition, jump straight to the related repair procedure).
- **INFO-11** — Repair-information look-up uses the same **provider credentials** and **default/fallback** configuration as §1; VIN must be decoded first.
- **INFO-12** — Panel labels and chrome are bilingual **EN/FR**; provider content is shown as returned.

## 3. VIN / plate decode & intake

**Business requirement:** intake must populate an accurate vehicle record and pull prior history so the estimate and repair data key off the right vehicle.

- **VIN-1** — Decode a **VIN** to **year, make, model, engine, trim and drivetrain**.
- **VIN-2** — **Ontario** and **Quebec** licence-plate → vehicle lookup (plate to vehicle, where the data source allows).
- **VIN-3** — Decoded specs **auto-prefill** the vehicle record at intake (advisor can correct before saving).
- **VIN-4** — At intake, pull **prior service history** — **internal** (this shop's past ROs for the VIN) and **optional CARFAX Service History** where the shop is connected.
- **VIN-5** — **Flag odometer-rollback anomalies** when the captured odometer is lower than a prior recorded reading for the same VIN.
- **VIN-6** — A successful VIN decode is the **prerequisite** that unlocks the labour guide (§1) and repair-info viewer (§2) for that vehicle.
- **VIN-7** — Intake supports manual entry/correction of any decoded field (decode assists, never blocks).
- **VIN-8** — Prior-history view shows date, mileage, services performed and source (internal vs CARFAX), and links to the originating RO where internal.

## 4. Open recalls & TSBs

**Business requirement:** the shop must see open safety recalls and relevant bulletins per vehicle at intake and on the estimate — without ever implying Squibbon performs the OEM recall remedy.

- **RCL-1** — Show **open safety recalls** per VIN from **Transport Canada** and **NHTSA**, at **intake** and on the **estimate**.
- **RCL-2** — Surface **relevant TSBs** for the vehicle alongside recalls.
- **RCL-3** — **Flag the vehicle** when one or more open recalls exist.
- **RCL-4** — **One-tap customer note** to add the recall information to the customer-facing estimate/communication.
- **RCL-5** — Advisor can mark a recall **"handled here"**, **"refer to dealer"**, or **"declined"**, and the disposition is recorded on the RO.
- **RCL-6** — Squibbon **never represents itself as performing the OEM recall remedy**; recall items are informational and dispositional, not billable OEM-remedy claims.
- **RCL-7** — Recall and TSB labels/notes are available in **EN/FR**.

## 5. Diagnostics / scan-tool intake

**Business requirement:** trouble codes from a scan tool (or entered manually) must attach to the customer concern, link to repair info, and be retained pre- and post-repair on the RO.

- **DTC-1** — **Import a diagnostic scan report** or **enter DTCs manually** (sources include **Bosch / Autel / Snap-on**, or manual entry).
- **DTC-2** — **Attach codes to the customer concern** so the complaint, cause and correction stay linked.
- **DTC-3** — **Link a DTC to suggested repair info** (§2) and a **recommended job** (so a code becomes a quotable line).
- **DTC-4** — **Store pre- and post-repair scans** with the RO for verification and warranty defence.
- **DTC-5** — A DTC on the concern can **deep-link to its definition and procedure** in the repair-info viewer (§2 INFO-10).
- **DTC-6** — Manual DTC entry validates the code format and pulls its **definition** where the provider has one.
- **DTC-7** — Scan attachments and code lists are visible to the assigned technician on the job.

---

## Data entities (conceptual)

| Entity | What it captures (conceptual) |
|---|---|
| Repair-data provider config | provider (ALLDATA / ProDemand / Identifix / MOTOR) · shop credentials reference · enabled · default/fallback order · per tenant |
| Labour-guide lookup result | decoded vehicle · operation/op code · labour time (value + flat-rate/measured) · source provider · associated OEM parts · fluids/capacities · maintenance interval |
| Estimate labour line (extended) | labour-time **source** + **value** · flat-rate vs measured · override flag (extends existing labour line in the work-order field spec) |
| Repair-info reference | type (procedure / torque / TSB / DTC definition / wiring diagram / ADAS / fluid-capacity) · source provider · link · attached-to RO/job |
| Vehicle decode record | VIN · year/make/model/engine/trim/drivetrain · decode source · plate + province (ON/QC) |
| Service-history record | VIN · date · mileage · services · source (internal RO / CARFAX) · odometer-anomaly flag |
| Recall / TSB item | VIN · source (Transport Canada / NHTSA / provider) · type (recall/TSB) · title · open status · disposition (handled here / refer to dealer / declined) · customer-note flag |
| Diagnostic scan / DTC | source (Bosch / Autel / Snap-on / manual) · code(s) + definition · pre/post-repair · linked concern · linked repair-info · recommended job |

## Integrations

| ID | Integration |
|----|-------------|
| INT-1 | **Labour/repair-data providers** — ALLDATA, Mitchell 1 ProDemand, Identifix Direct-Hit, MOTOR — via the **shop's own** subscription credentials (bring-your-own). |
| INT-2 | **VIN decode** — VIN → year/make/model/engine/trim/drivetrain. |
| INT-3 | **Plate lookup** — Ontario & Quebec licence-plate → vehicle (where the data source permits). |
| INT-4 | **CARFAX Service History** — optional prior-service history at intake, per shop connection. |
| INT-5 | **Recalls** — Transport Canada and NHTSA open safety recalls per VIN. |
| INT-6 | **Scan tools** — diagnostic scan import / DTC entry (Bosch, Autel, Snap-on, manual). |
| INT-7 | **Estimate / work order** — guide lines transfer into the existing estimate; repair-info attaches to ROs/jobs. |

## Roles & permissions

| ID | Requirement |
|----|-------------|
| PERM-1 | Only an authorized user (owner/manager) can **enter, test, edit or remove** provider credentials and set the default/fallback provider. |
| PERM-2 | Advisors can **run guide look-ups** and **transfer** guide lines into estimates. |
| PERM-3 | Technicians can **open the repair-info viewer**, attach procedures/references, and view scans/DTCs on their jobs. |
| PERM-4 | Advisors can set a recall **disposition** and add the **customer note**; recall items are never billable as an OEM-remedy claim (§RCL-6). |
| PERM-5 | Provider credentials are visible only to authorized roles and are **never exposed** on estimates, ROs or customer-facing output. |

## Non-goals

| ID | Non-goal |
|----|----------|
| NG-1 | Squibbon does **not** resell or sublicense repair data — every look-up runs on the shop's own subscription. |
| NG-2 | Squibbon does **not** store or re-host third-party repair content; it displays provider results and stores only references/attachments. |
| NG-3 | Squibbon does **not** scrape provider, Transport Canada, NHTSA or CARFAX sites; data comes only via authorized integration. |
| NG-4 | Squibbon does **not** perform or certify the OEM recall remedy — recall items are informational/dispositional only. |
| NG-5 | Not an OEM scan tool — Squibbon imports/records scan results and DTCs; it does not actuate or program modules. |
| NG-6 | No new persistent entity is added without a per-tenant migration (see Data & tenancy). |

## Data & tenancy

Squibbon is **schema-per-tenant** (one schema per shop), uses **BIGINT** primary keys, and runs JPA with **ddl-auto=validate** — so any **new persistent entity** introduced by this module (provider config, repair-info references, vehicle-decode and recall/scan records, etc.) requires a **per-tenant migration** before it can be used. Provider credentials are configured **per tenant**, and provider results, attachments and dispositions are scoped to the owning tenant's schema. No cross-tenant sharing of credentials or pulled content. Frontend is **Next.js + Flowbite-React (Spike)**, theme **purple #c479c2 / teal #16CDC7**, **Plus Jakarta Sans**; all advisor/technician-facing labels are bilingual **EN/FR** (Quebec Bill 96).

## Acceptance criteria

| ID | Criterion |
|----|-----------|
| AC-1 | An authorized user can enter ALLDATA / ProDemand / Identifix / MOTOR credentials in settings, test them, and set a default with fallback. |
| AC-2 | After a VIN is decoded, the advisor can open the labour-guide panel inside the estimate and look up an operation. |
| AC-3 | One click transfers a guide line (labour + OEM parts) into the estimate; the labour line shows its time **source** and **value**. |
| AC-4 | A labour line can be set to flat-rate or measured/actual time, and an override is flagged. |
| AC-5 | A technician can open the repair-info viewer from a job and see a procedure, torque spec, a TSB, a wiring-diagram and a DTC definition, and attach a reference to the RO. |
| AC-6 | A DTC deep-links from its definition to the related repair procedure. |
| AC-7 | Intake decodes a VIN to year/make/model/engine/trim/drivetrain and (ON/QC) supports plate lookup, prefilling the vehicle record. |
| AC-8 | Intake pulls prior service history (internal + optional CARFAX) and flags an odometer-rollback anomaly. |
| AC-9 | Open recalls (Transport Canada + NHTSA) appear at intake and on the estimate, the vehicle is flagged, and a recall can be marked handled-here / refer-to-dealer / declined with a one-tap customer note. |
| AC-10 | A scan report can be imported or DTCs entered (Bosch/Autel/Snap-on/manual), attached to the concern, linked to repair info and a recommended job, with pre/post scans stored on the RO. |
| AC-11 | Squibbon never presents itself as performing the OEM recall remedy. |
| AC-12 | All advisor/technician-facing labels in these screens render in EN and FR. |

## Screens

Mockups in `docs/mockups/estimating-repair-data/`.

| # | Screen file | Description | Requirement IDs |
|---|---|---|---|
| 01 | `01-estimate-labor-guide.svg` | Estimate editor with labour-guide side panel (provider results: labour time, parts, fluids; "Transfer to estimate") | LAB-1…LAB-15 |
| 02 | `02-intake-vin-recalls-history.svg` | New-vehicle intake: VIN/plate decode + decoded specs + OPEN recalls + prior service history | VIN-1…VIN-8 · RCL-1…RCL-7 |
| 03 | `03-repair-info-viewer.svg` | Repair-information panel: procedure steps + torque, a TSB, wiring-diagram thumbnail, ADAS quick-reference, a DTC definition | INFO-1…INFO-12 · DTC-3 · DTC-5 |

## Sources

- [Tekmetric — ALLDATA Labor Guide Integration](https://support.tekmetric.com/hc/en-us/articles/25868623867927-ALLDATA-Labor-Guide-Integration)
- [ALLDATA × Tekmetric](https://www.alldata.com/us/en/tekmetric)
- [Tekmetric integrations](https://www.tekmetric.com/integrations)
- [Transport Canada — Motor Vehicle Safety Recalls](https://recalls-rappels.canada.ca/en)
- [NHTSA — Recalls](https://www.nhtsa.gov/recalls)
