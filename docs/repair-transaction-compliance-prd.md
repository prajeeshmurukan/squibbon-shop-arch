# Canadian Repair-Transaction Compliance PRD

**Type:** Product / functional requirements (requirements capture only — no technical/data/API design)
**Status:** Draft for review
**Applies to:** Squibbon Shop module (extension)

Governs the **repair sale itself** — written estimates, the legal estimate ceiling, replaced-parts return, and statutory warranty — under Ontario and Quebec consumer-protection law. Distinct from the **vehicle-inspection** moat (DriveON / SAAQ): inspections certify the vehicle; this PRD certifies the *transaction*. Companion: [repair-shop module spec](repair-shop-module-spec.md) · [work-order field spec](repair-shop-workorder-fields.md) · [marketing & retention](marketing-retention-prd.md). Mockups: [docs/mockups/repair-transaction-compliance/](mockups/repair-transaction-compliance/).

> **Why this module:** the moment a customer approves work, Ontario's *Consumer Protection Act* and Quebec's *Consumer Protection Act* impose hard rules — a written estimate, a price ceiling the final bill cannot exceed, the right to keep replaced parts, and a minimum statutory warranty. Get these wrong and the shop **cannot legally collect** the overage. No US-built tool (Shopmonkey, Tekmetric, Fullbay) models them. Paired with Squibbon's DriveON/SAAQ inspection moat, this makes Squibbon the only shop platform that is **compliant by construction** in Canada's two largest auto markets.

## Background / why now

US shop software treats the estimate as a soft quote and the invoice as whatever the work cost. Canada does not. In **Ontario**, once a consumer authorizes an estimate the shop may not charge more than **10%** above it; in **Quebec**, the final invoice may **not exceed the authorized estimate at all**, a written estimate is **mandatory when repairs will exceed $100**, and the shop may not charge for *preparing* the estimate unless the customer agreed first. Both provinces give the consumer the right to **demand the return of replaced parts**, and Quebec imposes a **mandatory minimum 3-month / 5,000 km warranty on parts and labour**. Today Squibbon shops manage this on paper, exposing them to unrecoverable charges and OPC/Consumer-Protection-Ontario complaints. This module makes the rules **enforced, auditable, and bilingual** — a compliance differentiator that complements (not duplicates) the DriveON/SAAQ vehicle-inspection workflow.

## Goals

| Goal | Measure |
|---|---|
| Make the repair sale legally collectible | % of invoices within the authorized cap at conversion; $ overage blocked until re-authorized |
| Never bill unauthorized work | % of billed work backed by a timestamped authorization; re-auth events captured before billing |
| Honour the replaced-parts right | % of ROs with a recorded parts-return election; flagged parts dispositioned before close |
| Apply the right warranty automatically | % of invoices printing statutory + shop warranty terms by province |
| Be compliant in both provinces, in both languages | 100% of estimates/invoices bilingual (EN/FR) and carrying province-correct notices |

## Personas

- **Service advisor (T. Hopper, Mississauga ON)** — builds the estimate, captures authorization, watches the cap, requests re-authorization when discovered work pushes the total up.
- **Technician** — surfaces additional/discovered work mid-repair; flags physical parts for customer return.
- **Quebec advisor / front desk** — same flow under stricter rules (0% overage, $100 written-estimate threshold, mandatory warranty), French-default documents.
- **Shop owner / manager** — configures the province rule-set per location; relies on the audit trail in a dispute.
- **Customer (Acme Fleet Ltd, 2019 Ford F-150, plate ABCD 123)** — receives a bilingual estimate, authorizes in person or remotely, elects to keep replaced parts, gets a warranty-bearing invoice.

---

## 1. Estimate & authorization (EST)

| ID | Requirement |
|----|-------------|
| EST-1 | User can produce a **written estimate** from the RO (work order) itemizing parts and labour, with a single **authorized total**. |
| EST-2 | When the location is **Quebec** and the estimate total will **exceed $100**, a written estimate is **mandatory** before work begins; the system requires one and blocks authorization without it. |
| EST-3 | System captures customer **authorization** with **timestamp**, **authorizer name**, and **method**. |
| EST-4 | Authorization method is **in-person e-signature** (signature captured on device) **or remote approval** via a one-tap **SMS/email link**; remote approvals record the channel and approval timestamp. |
| EST-5 | The estimate is rendered as a **bilingual (EN/FR)** document; language defaults from the province rule-set (§6) and can be switched. |
| EST-6 | System records whether the customer **agreed in advance to a charge for preparing the estimate** (Quebec: no such charge unless agreed); default is **no charge**. |
| EST-7 | On authorization, the system stores the **authorized amount as the baseline** for cap enforcement (§2). |
| EST-8 | The estimate states the **work to be done, parts, labour, and the total**, and (where applicable) that the customer may **request return of replaced parts** (§3) — surfaced *at* authorization, not after. |
| EST-9 | Authorization is immutable once recorded; changes create a **new authorization version** with its own timestamp, author, and amount (full audit trail). |

## 2. Estimate-cap enforcement (CAP)

| ID | Requirement |
|----|-------------|
| CAP-1 | System enforces the **legal ceiling** by province: **Ontario — final invoice may not exceed the authorized estimate by more than 10%**; **Quebec — may not exceed the authorized estimate at all (0%)**. |
| CAP-2 | The RO editor shows a **running authorized total vs actual** and the **remaining headroom** (cap − actual) at all times. |
| CAP-3 | System **warns** as the actual total **approaches** the cap (configurable threshold, e.g. ≥90% of headroom consumed). |
| CAP-4 | System **blocks converting the RO to an invoice** when the total would exceed the cap, until a **new authorization** is recorded. |
| CAP-5 | Any **additional or discovered work** requires a **fresh incremental authorization** — captured exactly like EST (timestamp, authorizer, method, new amount) — **before it can be billed**. |
| CAP-6 | Each re-authorization **raises the baseline**; the cap is recomputed from the **latest authorized total**, and the headroom display updates. |
| CAP-7 | The cap check evaluates the **customer-payable** total; warranty- and insurance-covered portions are handled per their own rules and do not consume customer headroom. |
| CAP-8 | The full **authorization history** (each baseline, who approved, when, by what method) is retained and printable for dispute defence. |

## 3. Replaced-parts return (PRT)

| ID | Requirement |
|----|-------------|
| PRT-1 | At authorization, the system captures the customer's **election to have replaced parts returned** (yes / no), recorded against the RO. |
| PRT-2 | When the election is **yes**, affected part lines on the RO are **flagged for return**. |
| PRT-3 | System **excludes** parts that are **warranty-exchange, core-return, or never charged to the customer** — these need not be returned (ON/QC) and are not flagged. |
| PRT-4 | Each flagged part is tracked to a **disposition**: **returned to customer**, **declined by customer**, or **kept by shop**. |
| PRT-5 | The RO cannot be **closed** until every flagged part has a recorded disposition. |
| PRT-6 | The customer's parts-return **election and final dispositions** are **printed on the invoice**. |
| PRT-7 | The election is **bilingual** on customer-facing documents. |

## 4. Statutory & shop warranty (SWR)

| ID | Requirement |
|----|-------------|
| SWR-1 | For **Quebec** locations, the system **auto-applies the mandatory minimum 3-month / 5,000 km warranty** on **both parts and labour** to qualifying repairs. |
| SWR-2 | Warranty start basis is recorded (e.g. invoice/pickup date and mileage-out) so **coverage start and expiry** are computable. |
| SWR-3 | User can configure an **additional shop warranty** or a **national-program warranty** layered on top of the statutory minimum (cross-reference the [Warranty PRD](warranty-prd.md)). |
| SWR-4 | **Warranty terms** — coverage scope (parts/labour), **start**, and **expiry basis** (time and/or mileage) — are **printed on every invoice**. |
| SWR-5 | Warranty terms are rendered **bilingually (EN/FR)** by the location's default language. |
| SWR-6 | The statutory warranty is applied **automatically by province** via the rule-set (§6); the advisor sees that it is in effect and cannot silently remove it. |

## 5. Mandatory disclosures & invoice (DISC)

| ID | Requirement |
|----|-------------|
| DISC-1 | The invoice is **itemized**, **separating parts and labour** (and sublet/fees/supplies, per the work-order field spec). |
| DISC-2 | The invoice shows the **shop's legal identity** (registered name, address, contact). |
| DISC-3 | The invoice includes an **estimate-vs-final comparison** — authorized total, final total, and variance (within cap). |
| DISC-4 | The invoice carries the **statutory consumer notices** required by **Ontario's Consumer Protection Act** and **Quebec's Consumer Protection Act**, selected by the location's province (§6). |
| DISC-5 | Both the **estimate and the invoice** are available **bilingually (EN/FR)**. |
| DISC-6 | The replaced-parts election (§3) and warranty terms (§4) appear on the invoice alongside the disclosures, so a single document satisfies the provincial requirements. |

## 6. Province configuration (CFG)

| ID | Requirement |
|----|-------------|
| CFG-1 | System provides a **per-location province rule-set** with at least **Ontario** and **Quebec** profiles. |
| CFG-2 | Each rule-set controls: **cap percentage** (ON 10% / QC 0%), **written-estimate threshold** (QC > $100), **required statutory notices**, **statutory warranty** (QC 3 mo / 5,000 km), and **default language** (ON English / QC French). |
| CFG-3 | The rule-set is **applied automatically by shop location** — an RO inherits the province profile of the location it belongs to. |
| CFG-4 | The settings screen **shows which rules are active** for each location (cap %, threshold, notices, warranty, language) in a readable summary. |
| CFG-5 | Only authorized roles (§ Roles) may **edit** a province rule-set; changes are audit-logged. |
| CFG-6 | Rule-sets are **versioned** so historic ROs/invoices reflect the rules in force when they were authorized. |

## Data entities (conceptual)

| Entity | Core concept |
|---|---|
| Estimate | The written, itemized quote (parts + labour) with an authorized total; bilingual; per RO |
| Authorization (baseline) | A timestamped approval: authorizer name, method (e-sign / remote SMS-email), amount; versioned, immutable |
| Re-authorization event | An incremental approval for additional/discovered work; raises the cap baseline |
| Estimate-prep-charge consent | Customer's advance agreement (or not) to be charged for producing the estimate (QC) |
| Replaced-parts election | Per-RO yes/no choice to have replaced parts returned |
| Flagged part disposition | Per-part outcome: returned / declined / kept-by-shop (excludes warranty-exchange, core, uncharged) |
| Statutory warranty | Auto-applied QC 3-month / 5,000 km coverage on parts + labour; start/expiry basis |
| Shop / program warranty | Optional additional coverage layered above the statutory minimum |
| Province rule-set | Per-location profile: cap %, estimate threshold, notices, statutory warranty, default language; versioned |
| Compliance disclosure set | The province-correct statutory notices printed on estimate/invoice |

> **Data & tenancy.** Squibbon is **schema-per-tenant** with BIGINT primary keys, and JPA runs `ddl-auto=validate` — so every **new persistent entity** above (authorization baselines, parts-return elections/dispositions, province rule-sets, etc.) requires a **per-tenant migration** before it can be persisted. Capturing them here is requirements-only; provisioning is handled at build time, not by the ORM.

## Integrations

| ID | Requirement |
|----|-------------|
| INT-1 | **Work order / RO** — estimate, authorized total, part lines, and invoice totals come from the work-order module (field spec §1–§12, §20). |
| INT-2 | **Messaging** — remote authorization links and approvals reuse the two-way **SMS/email** channel; these are **transactional** (not gated by marketing consent, per the marketing PRD). |
| INT-3 | **E-signature capture** — in-person signature pad for authorization. |
| INT-4 | **Warranty module** — shop/national-program warranties layered on the statutory minimum (cross-reference Warranty PRD). |
| INT-5 | **Locations / settings** — province rule-set binds to the shop location record. |
| INT-6 | **Document/PDF rendering** — bilingual estimate and invoice generation. |

## Roles & permissions

| ID | Requirement |
|----|-------------|
| PERM-1 | Service advisors can **produce estimates** and **capture authorizations / re-authorizations**. |
| PERM-2 | Converting an RO to invoice **beyond the cap** is **blocked for everyone** until a new authorization exists — no role can override the cap without recording authorization. |
| PERM-3 | Only authorized roles may **edit province rule-sets** (cap %, threshold, notices, warranty, language). |
| PERM-4 | Authorization records, re-authorization events, and parts-return dispositions **cannot be deleted**; corrections create new versions (audit-preserving). |
| PERM-5 | Recording a **parts disposition** (returned / declined / kept) is permitted to advisors and technicians per shop policy. |

## Non-goals

| ID | Non-goal |
|----|----------|
| NG-1 | This module does **not** cover DriveON / SAAQ **vehicle inspections** — that is the separate inspection moat; here we govern the repair **transaction**. |
| NG-2 | Squibbon is **not** the legal compliance authority — it operationalizes the statutory rules; it does not give legal advice. |
| NG-3 | No coverage of provinces **other than Ontario and Quebec** in v1 (rule-set is extensible later). |
| NG-4 | Not a payments/AR redesign — cap enforcement gates **invoice conversion**, not payment processing (see work-order §21). |
| NG-5 | Does **not** define warranty-claim handling beyond printing terms — claim workflows live in the Warranty PRD. |

## Acceptance criteria

| ID | Criterion |
|----|-----------|
| AC-1 | An advisor can produce a **bilingual written estimate** and capture authorization with **timestamp, authorizer name, and method** (e-sign or remote SMS/email). |
| AC-2 | For a **Quebec** location, the system **requires a written estimate** when the total exceeds **$100** and blocks work without one. |
| AC-3 | The authorized total is stored as the **baseline**; the RO shows **running total vs cap and remaining headroom**. |
| AC-4 | The system **warns** near the cap and **blocks invoice conversion** beyond it (ON 10% / QC 0%) until a **new authorization** is recorded. |
| AC-5 | Additional/discovered work requires a **fresh incremental authorization** before it can be billed; the cap baseline updates. |
| AC-6 | The customer's **replaced-parts election** is captured at authorization; flagged parts are dispositioned (returned/declined/kept) and the election prints on the invoice; warranty-exchange/core/uncharged parts are excluded. |
| AC-7 | A **Quebec** invoice auto-applies and prints the **3-month / 5,000 km** statutory warranty on parts and labour; an optional shop warranty can be layered on. |
| AC-8 | The invoice is **itemized (parts vs labour)**, shows **shop legal identity**, an **estimate-vs-final comparison**, and the **province-correct statutory notices**. |
| AC-9 | A **province rule-set** (ON 10% / QC 0%, $100 threshold, notices, statutory warranty, language) applies **automatically by location** and the settings screen **shows the active rules**. |
| AC-10 | Estimate and invoice render correctly in **both EN and FR**. |

## Screens

Mockups in [docs/mockups/repair-transaction-compliance/](mockups/repair-transaction-compliance/). Continuity: WO-10428 · Acme Fleet Ltd · 2019 Ford F-150, plate ABCD 123 · advisor T. Hopper · Mississauga ON.

| # | File | Screen |
|---|------|--------|
| 01 | `01-estimate-authorization-esign.svg` | Bilingual estimate with authorized total, customer e-signature pad / "approved via SMS" state, replaced-parts-return checkbox |
| 02 | `02-estimate-cap-reauthorization.svg` | RO editor showing authorized cap + running total + headroom bar, with a blocking re-authorization modal when added work would exceed the 10% (ON) ceiling |
| 03 | `03-invoice-parts-return-warranty.svg` | Final invoice: itemized parts/labour, estimate-vs-final comparison, replaced-parts-return status, statutory + shop warranty terms (QC 3 mo / 5,000 km) |
| 04 | `04-compliance-province-settings.svg` | Settings: province rule-sets (ON 10% / QC 0%, estimate threshold, statutory warranty, language) per location |

## Sources

- Ontario — [Car repair shops: your rights](https://www.ontario.ca/page/car-repair-shops-your-rights)
- Steps to Justice — [rules a repair shop must follow](https://stepstojustice.ca/steps/debt-and-consumer-rights/learn-about-rules-repair-shop-must-follow/)
- CLEO — [motor vehicle repairs](https://www.cleo.on.ca/en/publications/vehiclerep/all)
- Quebec OPC — [Vehicle Repair](https://www.opc.gouv.qc.ca/en/consumer/good-service/vehicle/repairing/advice)
- Éducaloi — [Car and Motorcycle Repairs](https://educaloi.qc.ca/en/capsules/car-and-motorcycle-repairs/)
