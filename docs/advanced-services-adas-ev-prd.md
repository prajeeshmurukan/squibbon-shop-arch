# Advanced Services — ADAS, EV & Equipment

**Type:** Product / functional requirements (requirements capture only — no technical/data/API design)
**Status:** Draft for review
**Applies to:** Squibbon Shop module (extension)

Adds the three capabilities that separate a modern repair shop from a 2015 one: **ADAS calibration**, **EV/hybrid high-voltage service**, and the **equipment register** that both depend on. Companion: [work-order field spec](repair-shop-workorder-fields.md) · [marketing & retention PRD](marketing-retention-prd.md). Mockups: [docs/mockups/advanced-services/](mockups/advanced-services/00-index.md).

> **Why this module:** an ordinary repair — a windshield, an alignment, a bumper R&R — now silently triggers a *safety-critical* calibration, and an oil change on a hybrid now means working near 400 V. The shop that doesn't detect the calibration, or that lets an uncertified tech open an HV pack, carries the liability. Squibbon makes the requirement **visible, gated, and documented**.

## Background / why now

Three 2026 forces converge on the bay:

- **ADAS is now table-stakes, and growing.** Camera/radar-driven safety systems ship on the bulk of new vehicles, and the ADAS calibration service market is growing at roughly **12% CAGR**. Coverage keeps expanding — Bosch's ADS X added 2026 model-year coverage and new calibrations in its 7.1/7.2 update — so "which calibration does *this* repair require" is a moving target a shop can't hold in its head.
- **EV/hybrid volume is in the service bay.** Hybrids and EVs in for routine work put technicians near high-voltage systems that demand **de-energization, PPE, and lockout/tagout** before a wrench turns, plus battery state-of-health diagnostics customers increasingly ask for.
- **Both carry liability that documentation mitigates.** A missed calibration or an undocumented HV procedure is a lawsuit waiting to happen. A **proof-of-calibration record** and a signed **HV safety workflow** are the shop's defence — and, for licensed/manufacturer-recognized stations, often a requirement.

Squibbon already records sublet windshield work, technician assignment, and job authorization (see the work-order spec). This module layers the *advanced-service intelligence* on top: detect what's required, gate who may do it, and produce the paperwork that proves it was done right.

## Goals

| Goal | Measure |
|---|---|
| Never miss a required calibration | % of qualifying repairs with calibration detected & dispositioned |
| Make calibration billable, not absorbed | Calibration $ captured per qualifying RO |
| No HV work by an uncertified tech | 100% of HV jobs assigned to an HV-certified technician |
| Prove the work for liability | % of calibration / HV jobs with complete documentation |
| Keep equipment in-spec | % of in-service equipment with current calibration/inspection |

## Personas

- **Service advisor** — writes the RO; needs to *know at intake* that the windshield job also needs a calibration, and to quote it.
- **ADAS / calibration technician** — runs static/dynamic calibrations, captures pre/post scans, owns the proof document.
- **EV/HV-certified technician (A. Roy, Mississauga ON)** — the only person allowed to be assigned HV work; runs the isolation/PPE/LOTO workflow.
- **Shop manager / owner** — accountable for liability, licensed-station compliance, and equipment readiness; watches the equipment-due list.

---

## 1. ADAS calibration

| ID | Requirement |
|----|-------------|
| ADAS-1 | System **flags an ADAS-equipped vehicle** (from VIN/YMM, prior history, or manual) so the advisor sees it at intake. |
| ADAS-2 | When a **qualifying repair** is added to the work order, system surfaces the **required calibration(s)** and whether each is **static**, **dynamic**, or **both**. Qualifying repairs include: windshield replacement, wheel alignment, bumper/grille R&R, suspension/steering work, and camera/radar sensor R&R. |
| ADAS-3 | Each required calibration maps to an **ADAS calibration job template** (named operation, expected systems, target/equipment needs, labour) that drops onto the RO as a job (§4 of the work-order spec). |
| ADAS-4 | Technician can **capture a pre-scan** (DTCs / systems present) before the repair and a **post-scan** after, and attach scan-tool output. |
| ADAS-5 | System records, per calibration, the **systems calibrated** (e.g. forward camera, front radar, lane-keep, blind-spot), the **method** (static/dynamic), the **targets/frames and scan tool used** (linked to the equipment register, §3), the **OEM spec / procedure reference**, and **before/after results** (pass/fail per system). |
| ADAS-6 | System produces a **proof-of-calibration document** assembling: vehicle (YMM/VIN), systems calibrated, method, targets/equipment used, OEM spec reference, pre/post results, **technician**, and **date**. Document is attachable to the RO/invoice and shareable with the customer/insurer. |
| ADAS-7 | Calibration is **billed as a line item** (labour ± fee) on the job, not absorbed into the underlying repair. |
| ADAS-8 | If a required calibration is **not performed** (declined, sublet out, or deferred), the advisor must record a **disposition** so it is never silently skipped; sublet-out calibrations link to the sublet line. |
| ADAS-9 | A calibration cannot be marked **complete** without its post-scan/result captured (proof is mandatory to close). |
| ADAS-10 | Calibration job templates, qualifying-repair rules, and the systems list are **configurable** and **bilingual (EN/FR)**. |

## 2. EV high-voltage service

| ID | Requirement |
|----|-------------|
| EV-1 | System **flags an EV or hybrid vehicle** (from VIN/YMM or manual) on the work order and vehicle record. |
| EV-2 | When **HV work** is added to an RO on a flagged vehicle, system **enforces a high-voltage safety workflow** that must be completed *before* the HV job can start. |
| EV-3 | The safety workflow is a structured checklist covering **de-energization / HV isolation steps**, a **PPE checklist** (insulated gloves, face shield, etc.), and **lockout/tagout (LOTO)**, each item explicitly confirmed. |
| EV-4 | System **requires that only an HV-certified technician** can be assigned to an HV job; assigning a non-certified technician is blocked, and the system records *which* certification gated it. |
| EV-5 | Technician can **capture battery state-of-health and HV diagnostics** (state-of-health %, pack/cell readings, isolation/insulation-resistance result, DTCs). |
| EV-6 | System **stores the required HV service documentation** (completed safety checklist with confirmations, who performed isolation, diagnostic results, technician, date) attached to the RO for liability protection. |
| EV-7 | An HV job cannot be marked **complete** until the safety workflow is fully confirmed and the assigned technician is HV-certified. |
| EV-8 | Technician **HV-certification status and expiry** are recorded so an expired certification fails the EV-4 gate. |
| EV-9 | Safety-workflow steps, PPE list, and labels are **configurable** and **bilingual (EN/FR)**. |

## 3. Shop equipment & calibration management

| ID | Requirement |
|----|-------------|
| EQP-1 | System maintains an **equipment asset register** — lifts, ADAS calibration targets/frames, scan tools, alignment racks, EV isolation/PPE kit — with identity (name, type, make/model, serial/asset #), location, and **in-service status**. |
| EQP-2 | Each asset can carry a **calibration / inspection schedule** with a **next-due date** (e.g. annual lift inspection, scan-tool/target calibration). |
| EQP-3 | System surfaces an **equipment-due list and reminders** ahead of due dates, with an **overdue** state. |
| EQP-4 | The **specific equipment used on a job** is linked to that job/calibration (supports the ADAS proof document, §1, and licensed-station record-keeping). |
| EQP-5 | System tracks **equipment downtime / out-of-service** periods (reason, from/to) so an out-of-service asset isn't shown as usable. |
| EQP-6 | An asset that is **out-of-service or overdue** is flagged when a technician tries to link it to a calibration/HV job. |
| EQP-7 | Calibration/inspection records per asset are retained as a **history** (date, who, result, certificate/document). |
| EQP-8 | Equipment types, schedules, and labels are **bilingual (EN/FR)**. |

## Data entities (conceptual)

> **Data & tenancy:** Squibbon is schema-per-tenant with BIGINT PKs and JPA `ddl-auto=validate`; any new entity below means a **per-tenant migration** before it is live. Stated once here — not repeated per entity.

| Entity | Core fields (conceptual) |
|---|---|
| ADAS vehicle profile | vehicle ref · ADAS-equipped flag · systems present · source (VIN/manual) |
| Calibration requirement | RO/job ref · trigger repair · system(s) · method (static/dynamic/both) · disposition |
| Calibration job template | name · systems · method · target/equipment needs · labour · locale |
| Calibration record | job ref · systems calibrated · method · pre-scan · post-scan · equipment used (ref) · OEM spec ref · before/after results · technician · date |
| Proof-of-calibration document | calibration ref · assembled fields · file · shared-with |
| EV/HV vehicle flag | vehicle ref · EV/hybrid flag · HV system notes |
| HV safety workflow | RO/job ref · isolation steps · PPE checklist · LOTO confirmations · performed-by · date |
| HV diagnostic record | job ref · state-of-health % · pack/cell readings · isolation result · DTCs · technician · date |
| Technician certification | technician ref · type (HV/ADAS) · status · issued/expiry |
| Equipment asset | name · type · make/model · serial/asset # · location · status |
| Equipment calibration/inspection | asset ref · schedule · next-due · history (date · result · document) |
| Equipment downtime | asset ref · reason · from/to |
| Equipment usage link | asset ref · job/calibration ref · date |

## Integrations

| ID | Requirement |
|----|-------------|
| INT-1 | **Work orders / jobs** — calibration and HV jobs are jobs on the RO; calibration billing, sublet-out, and authorization reuse the work-order module. |
| INT-2 | **Vehicles** — ADAS-equipped and EV/hybrid flags read from VIN decode / YMM on the vehicle record. |
| INT-3 | **Scan / diagnostic tools** — capture pre/post scans, DTCs, and HV diagnostics (e.g. Bosch ADS X-class tooling); attach output to the record. |
| INT-4 | **Equipment register** — calibration jobs and HV jobs link the specific asset used (§3). |
| INT-5 | **Technician records / certifications** — the HV-certified gate (EV-4) and ADAS competency read from technician certifications. |
| INT-6 | **Documents** — proof-of-calibration and HV documentation stored with the RO and shareable with customer/insurer. |

## Roles & permissions

| ID | Requirement |
|----|-------------|
| PERM-1 | Advisors can add calibration/HV jobs and record dispositions; technicians capture scans, results, and HV workflow confirmations. |
| PERM-2 | Only an **HV-certified technician** may be assigned to and confirm an HV job (EV-4). |
| PERM-3 | Only authorized users can **edit certifications**, qualifying-repair rules, calibration templates, and equipment schedules. |
| PERM-4 | Completed calibration/HV documentation and certification records cannot be deleted without elevated permission. |
| PERM-5 | All users can **view** proof documents and the equipment-due list (read-only) per permission. |

## Non-goals

| ID | Non-goal |
|----|----------|
| NG-1 | Squibbon does not *perform* calibrations or read scan tools itself — it captures, structures, and documents what the tooling/technician produces. |
| NG-2 | Not the OEM repair-procedure authority — it references the spec used; it is not a labour-guide/procedure database. |
| NG-3 | Not a substitute for technician HV training or certification — it gates on certification status, it does not grant it. |
| NG-4 | No full EAM/CMMS asset-management suite (depreciation, work-order maintenance for equipment) in v1 — register + calibration-due + downtime only. |
| NG-5 | Squibbon is not the legal/licensing authority — it provides documentation tooling, not compliance certification. |

## Acceptance criteria

| ID | Criterion |
|----|-----------|
| AC-1 | Adding a qualifying repair (e.g. windshield, alignment) to an ADAS-flagged vehicle surfaces the required calibration(s) with static/dynamic method. |
| AC-2 | A calibration job can capture pre/post scans and systems-calibrated results, and is billed as a line item. |
| AC-3 | A proof-of-calibration document is produced with vehicle, systems, method, equipment used, OEM spec, before/after, technician, and date — and is mandatory to close the calibration. |
| AC-4 | A required calibration that isn't performed records a disposition (declined/sublet/deferred) and is never silently skipped. |
| AC-5 | HV work on an EV/hybrid forces the isolation/PPE/LOTO safety workflow before the job can start. |
| AC-6 | Assigning a non-HV-certified technician (or one with an expired certification) to an HV job is blocked. |
| AC-7 | Battery state-of-health/diagnostics and the completed HV safety documentation are stored on the RO. |
| AC-8 | The equipment register shows calibration/inspection due dates with reminders and an overdue state. |
| AC-9 | The specific equipment used is linked to a calibration/HV job and appears on the proof document; an out-of-service/overdue asset is flagged. |
| AC-10 | Templates, workflows, equipment, and documents support EN/FR. |

## Screens

Mockups in [docs/mockups/advanced-services/](mockups/advanced-services/00-index.md).

| # | File | Screen |
|---|------|--------|
| 01 | 01-adas-calibration-job.svg | ADAS calibration job: required calibrations, pre/post scan, targets/equipment used, proof-of-calibration document |
| 02 | 02-ev-hv-service-safety.svg | EV high-voltage service: isolation/PPE/LOTO safety checklist, HV-certified-tech gate, battery state-of-health |

## Sources

- [5 New ADAS Technologies Coming in 2026 — BodyShop Business](https://www.bodyshopbusiness.com/5-new-adas-technologies-coming-in-2026/)
- [2026 automotive aftermarket industry trends — S&P Global](https://www.spglobal.com/automotive-insights/en/blogs/2026/04/automotive-aftermarket-industry-trends)
- [Inside today's most advanced auto shops (EV/ADAS diagnostics) — Autoblog](https://www.autoblog.com/news/inside-todays-most-advanced-auto-shops-3d-printers-cloud-tools-and-ev-diagnostics)
- [Bosch Diagnostics ADS X software 7.1/7.2 — 2026 coverage & expanded ADAS calibrations — BodyShop Business](https://www.bodyshopbusiness.com/bosch-diagnostics-releases-ads-x-software-updates-7-1-7-2-adding-2026-model-year-coverage-and-expanded-adas-calibrations/)
