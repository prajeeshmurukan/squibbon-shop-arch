# Squibbon Shop — Warranty, Cores & Comebacks PRD

**Type:** Product / functional requirements (requirements capture only — no technical/data/API design)
**Status:** Draft for review
**Applies to:** Squibbon Shop module (extension)

Captures the post-sale money and quality flows that competitors (Tekmetric, Shopmonkey) track but Squibbon does not — warranty coverage and claims, comebacks and pre-delivery quality control, and core charges / vendor returns. Companion: [work-order field spec](repair-shop-workorder-fields.md) · [module spec](repair-shop-module-spec.md). Mockups: [docs/mockups/warranty-cores-comebacks/](mockups/warranty-cores-comebacks/00-index.md).

> **Why this module:** every job ships with a warranty (shop, national programme, parts-manufacturer, or — in Quebec — statutory), every comeback costs unbilled labour, and every cored part ties up real money until the old unit is returned and credited. These are tracked nowhere today, so warranty exposure, rework cost, and outstanding core value are invisible. Tekmetric surfaces all three as profitability levers; Squibbon must too.

## Background / why now

Squibbon already records a job, its parts, its sublet, and its invoice (see the [work-order field spec](repair-shop-workorder-fields.md) §6 core charge, §22 comeback-of ref, §23 core handling / warranty defaults) — but only as loose fields. There is no register that answers **"is this under warranty?"** at intake, no structured **comeback** with root cause and rework cost, and no **core / RMA** ledger that says how much money is outstanding with vendors. The result:

- **Warranty exposure is unmanaged.** A covered comeback is re-keyed as a fresh paid RO; reimbursable parts/labour from a national programme (e.g. NAPA AutoCare) are never claimed.
- **Quebec statutory warranty is a compliance gap.** Quebec's *Consumer Protection Act* implies a minimum warranty on auto repairs (commonly applied as 3 months / 5,000 km), independent of any shop warranty — Squibbon cannot record or honour it.
- **Comebacks are invisible.** Rework is done "off the books" with no link to the original RO, no responsible-technician attribution, and no comeback-rate KPI, so quality problems never surface.
- **Core value leaks.** A cored part bills the customer a refundable core charge, but the old unit's return-and-credit is tracked on paper; outstanding core value owed by vendors is unknown.

This module turns those loose ends into three managed ledgers: a **warranty register & claims** book, a **comeback & QC** record, and a **cores & vendor-returns** board.

## Goals

| Goal | Measure |
|---|---|
| Know coverage at intake | "Under warranty?" lookup answered in one screen, by vehicle and by part |
| Recover warranty money | Warranty claims raised, reimbursement (parts/labour) credited, by source |
| Honour statutory obligations | 100% of jobs carry a computed warranty (incl. Quebec statutory floor) |
| Reduce rework | Comeback rate by technician and shop; no-charge rework cost trend (down) |
| Ship quality | % of deliveries with a passed pre-delivery QC checklist |
| Stop core / return leakage | Outstanding core value owed; core return & vendor-credit cycle time |

## Personas

| Persona | Cares about |
|---|---|
| **Service advisor** | Answering "is this covered?" at the counter; turning a comeback into a claim, not a free redo |
| **Technician (e.g. A. Roy)** | Their comeback rate; the QC checklist they must pass before a vehicle is delivered |
| **Shop foreman / QC** | Root-causing comebacks; enforcing the pre-delivery checklist; rework cost |
| **Parts manager** | Core return status, vendor RMAs, reconciling credits to the originating PO |
| **Shop owner / manager** | Warranty cost by source; comeback rate; outstanding core value tied up |
| **Fleet customer (e.g. Acme Fleet Ltd)** | A warranty they can rely on; covered comebacks handled at no charge |

---

## 1. Warranty register & claims

Prefix **WAR**.

| ID | Requirement |
|----|-------------|
| WAR-1 | User can **record warranty coverage** on a job and/or a specific part line, with **source/type**: shop, national programme (e.g. **NAPA AutoCare** — 24 mo / 40,000 km), parts-manufacturer, or **Quebec statutory** (3 mo / 5,000 km). |
| WAR-2 | Coverage **basis** is time and/or distance: a start **date** and start **odometer**, a **time term** (months) and/or **distance term** (km), with the **expiry** date/odometer **computed** from the in-service start. |
| WAR-3 | Coverage records its **scope** — parts, labour, or both — and whether it is **transferable** to a subsequent owner. |
| WAR-4 | Default warranty terms apply automatically from settings (per shop, and a Quebec statutory **floor**) when a job is invoiced; the user can override per job or part. |
| WAR-5 | An **"is this under warranty?" lookup** at intake answers by **vehicle** (all covered jobs/parts on this vehicle with remaining time/distance) and by **part** (this part number / position), showing source, expiry, and scope. |
| WAR-6 | The lookup flags coverage as **active**, **expiring soon**, or **expired** against the vehicle's current odometer and date. |
| WAR-7 | User can **create a warranty CLAIM from a covered comeback** (§2), carrying over the original RO, job, vehicle, covered part(s)/labour, and coverage source. |
| WAR-8 | A claim tracks **status**: open → approved → denied → reimbursed, with status notes and dates. |
| WAR-9 | A claim tracks **reimbursement**: expected vs received **parts credit** and **labour credit**, claim/authorization reference, and the covering programme or manufacturer. |
| WAR-10 | **Warranty-cost reporting by source** — labour and parts cost absorbed under warranty, grouped by shop / national programme / manufacturer / statutory, over a date range. |
| WAR-11 | A vehicle's coverage and any claims appear on its **service history** and on the RO. |
| WAR-12 | Warranty statements and coverage details render in **EN/FR**; Quebec statutory coverage is labelled as such to the customer. |

## 2. Comebacks & quality control

Prefix **CMB**.

| ID | Requirement |
|----|-------------|
| CMB-1 | User can **flag an RO as a comeback (rework)**, **linked to the original RO and job** it reworks. |
| CMB-2 | A comeback captures a **root-cause category** (e.g. part failure, faulty installation / workmanship, misdiagnosis, customer-supplied part, could-not-duplicate, related-not-caused) and the **responsible technician**. |
| CMB-3 | A comeback is marked **no-charge** vs **chargeable**, and captures the **rework cost** (labour + parts absorbed) regardless of what the customer is billed. |
| CMB-4 | A **configurable pre-delivery QUALITY-CONTROL checklist** (e.g. lug **re-torque** to spec, fluid levels, **test drive**, lights / signals, cleanliness, road-test sign-off) must be completed and **pass before delivery**. |
| CMB-5 | QC checklist templates are configurable per shop (items, pass/fail/NA, required vs optional) and can be required as a **gate** before an RO can be marked delivered. |
| CMB-6 | A **comeback-RATE KPI** is reported by **technician** and **shop** (comebacks ÷ ROs over a period), with trend. |
| CMB-7 | User can **attach DVI / photo evidence** to a comeback and to QC checklist items. |
| CMB-8 | A comeback with an underlying covered part/labour can spawn a **warranty claim** (§WAR-7) so rework cost can be recovered. |
| CMB-9 | Comeback flag, root cause, and responsible technician are visible on the original RO and the vehicle's **service history**. |
| CMB-10 | QC checklist and comeback reasons render in **EN/FR**. |

## 3. Cores & vendor returns

Prefix **COR**.

| ID | Requirement |
|----|-------------|
| COR-1 | A part's **core charge** is represented as a **distinct line** with its **core value**, separate from the part's retail price. |
| COR-2 | The line tracks **core return state**: pending → returned → credited, with dates. |
| COR-3 | User can **raise a vendor RMA** for a **returned core**, a **warranty** part, or a **defective** part, with reason, originating part, and vendor. |
| COR-4 | An RMA / return tracks **status** (requested → authorized → shipped → credited / rejected) and the vendor RMA reference. |
| COR-5 | A vendor **credit is reconciled back to the originating PO / part line** (see [work-order field spec](repair-shop-workorder-fields.md) §16–17), so the credit is matched, not lost. |
| COR-6 | A **core / returns AGING view** shows **outstanding core value owed** (cores billed but not yet returned/credited) and open RMAs, bucketed by age, by vendor. |
| COR-7 | Default core handling applies from settings (which parts carry a core, default core value) and flows onto the part line automatically. |
| COR-8 | Core and return status are visible on the part line, the PO, and the cores board. |
| COR-9 | A core return or RMA can be **linked to a warranty claim** (§WAR) or **comeback** (§CMB) when the underlying part failed. |

---

## Data entities (conceptual)

> **Data & tenancy:** Squibbon is **schema-per-tenant** with **BIGINT** primary keys and JPA **`ddl-auto=validate`** — so each new entity below requires a **per-tenant migration** before it can be used; no entity is auto-created at runtime. (Stated once; not repeated per entity.)

| Entity | Core fields (conceptual) |
|---|---|
| **Warranty coverage** | subject (job ref / part-line ref) · source/type (shop / national programme / manufacturer / Quebec statutory) · basis (time and/or distance) · start date · start odometer · time term · distance term · computed expiry (date / odometer) · scope (parts / labour / both) · transferable · vehicle ref · locale |
| **Warranty claim** | originating RO ref · original job/part ref · coverage ref · source/programme · status (open / approved / denied / reimbursed) · expected vs received parts credit · expected vs received labour credit · claim/auth reference · dates · notes |
| **Comeback** | RO ref · **original RO / job ref** (comeback-of) · root-cause category · responsible technician ref · no-charge vs chargeable · rework cost (labour + parts) · linked claim ref · evidence (DVI / photos) · date |
| **QC checklist (template + result)** | template name · items (label · pass/fail/NA · required) · per-RO result · passed flag · gate-before-delivery · completed by · completed date · locale |
| **Core charge line** | part-line ref · core value · return state (pending / returned / credited) · dates |
| **Vendor return / RMA** | reason (core / warranty / defective) · originating part-line ref · originating PO ref · vendor ref · status (requested / authorized / shipped / credited / rejected) · vendor RMA reference · expected vs received credit · linked claim/comeback ref · dates |
| **Warranty / core settings** | default shop warranty terms · Quebec statutory floor · core-carrying parts & default core values · QC checklist templates |

## Integrations

| ID | Requirement |
|----|-------------|
| INT-1 | **Work orders / jobs / parts** — coverage, comebacks, and core lines attach to existing ROs, jobs, and part lines (no parallel record of the vehicle's work). |
| INT-2 | **Vehicles & service history** — coverage, claims, and comebacks appear on the vehicle's history; the intake lookup reads current odometer. |
| INT-3 | **Vendors & purchase orders** — RMAs and core credits reconcile to the originating PO / part line and the vendor record ([field spec](repair-shop-workorder-fields.md) §15–17). |
| INT-4 | **Inspections / DVI** — comeback and QC evidence reuses DVI photos / findings. |
| INT-5 | **National warranty programmes** (e.g. NAPA AutoCare) and parts-manufacturer warranties — coverage terms and claim references captured for reimbursement; programme look-up/submission is a future integration, manual entry in v1. |
| INT-6 | **Invoicing** — warranty split (covered vs customer-pay) and core lines flow to the invoice's line summary and split allocations. |

## Roles & permissions

| ID | Requirement |
|----|-------------|
| PERM-1 | Advisors and techs can **view coverage** and the intake "under warranty?" lookup. |
| PERM-2 | Only authorized users can **edit warranty terms / settings** and the Quebec statutory floor. |
| PERM-3 | Only authorized users can **mark a comeback no-charge** and approve absorbed rework cost. |
| PERM-4 | Only authorized users can **submit / approve / mark reimbursed** a warranty claim. |
| PERM-5 | Parts/authorized users can **raise RMAs** and **reconcile vendor credits**. |
| PERM-6 | QC checklists can be **configured** only by authorized users; the **gate-before-delivery** setting is permissioned. |
| PERM-7 | All users can view warranty-cost, comeback-rate, and core-aging reports (read-only) per permission. |

## Non-goals

| ID | Non-goal |
|----|----------|
| NG-1 | Squibbon does not provide **legal advice** on warranty obligations — it computes a configurable statutory floor and surfaces it; the shop remains the compliance authority. |
| NG-2 | No **direct electronic claim submission** to programmes/manufacturers in v1 (capture + manual submission; integration later). |
| NG-3 | Not a **vendor accounting / AP** system — core and RMA credits reconcile to POs but ledgering lives in accounting. |
| NG-4 | No **extended-warranty / service-contract sales** (third-party VSC underwriting) in v1. |
| NG-5 | QC checklists are **pre-delivery quality control**, not a replacement for the safety-inspection (PMVI/SAAQ) module. |

## Acceptance criteria

| ID | Criterion |
|----|-----------|
| AC-1 | User can record coverage on a job/part with source, basis (time/distance), start date/odometer, computed expiry, scope, and transferability. |
| AC-2 | Default warranty (incl. Quebec statutory floor of 3 mo / 5,000 km) applies automatically on invoice and can be overridden. |
| AC-3 | At intake, the "is this under warranty?" lookup answers by vehicle and by part, showing source, expiry, and active/expiring/expired status. |
| AC-4 | A covered comeback can spawn a warranty claim that carries over the original RO/job and tracks status and parts/labour reimbursement. |
| AC-5 | Warranty cost is reported by source (shop / programme / manufacturer / statutory) over a date range. |
| AC-6 | An RO can be flagged a comeback linked to the original RO/job, with root cause, responsible technician, no-charge vs chargeable, and rework cost. |
| AC-7 | A configurable pre-delivery QC checklist must pass before delivery (when set as a gate), with DVI/photo evidence attachable. |
| AC-8 | Comeback rate is reported by technician and shop. |
| AC-9 | A part's core charge is a distinct line tracking pending/returned/credited; a vendor RMA can be raised for core/warranty/defective returns. |
| AC-10 | A vendor credit reconciles to the originating PO/part line; the core/returns aging view shows outstanding core value owed by vendor and age. |
| AC-11 | Warranty statements, QC checklists, and comeback reasons render in EN/FR. |

## Screens

See [docs/mockups/warranty-cores-comebacks/](mockups/warranty-cores-comebacks/00-index.md). Continuity sample: **WO-10428**, **Acme Fleet Ltd**, **2019 Ford F-150**, tech **A. Roy**, sublet vendor **ClearView Glass**, Mississauga ON.

| # | File | Screen |
|---|------|--------|
| 01 | `01-warranty-register-claim.svg` | Vehicle/customer warranty register (covered jobs/parts + expiry) + a warranty claim detail (coverage, status, reimbursement) |
| 02 | `02-comeback-qc-checklist.svg` | Comeback record (linked original RO, root cause, responsible tech, cost) + pre-delivery QC checklist; comeback-rate KPI |
| 03 | `03-cores-vendor-returns.svg` | Cores & vendor-returns board (core charges pending/returned/credited; open vendor RMAs; outstanding core value) |

## Sources

- [Tekmetric — Auto Repair & Shop Management Software](https://www.tekmetric.com/)
- [Tekmetric — three ways to maximize auto repair shop profitability](https://www.tekmetric.com/post/three-ways-to-maximize-auto-repair-shop-profitability)
- [NAPA AutoCare Peace of Mind Warranty](https://www.napaautocare.com/warranty/)
