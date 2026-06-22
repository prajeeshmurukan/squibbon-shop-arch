# Squibbon TMS — Maintenance Module PRD

## Compliance Tracking: Ontario DriveON, Quebec SAAQ & Canada-wide fleet maintenance compliance

**Type:** Product / functional requirements (requirements capture only)
**Status:** Draft for review
**Applies to:** Squibbon TMS — Maintenance module (extension)

> **Note on scope of this document.** This PRD captures **new compliance tracking requirements** that extend Squibbon's existing Maintenance module. It is requirements capture only — it intentionally contains **no** technical design, data design, API design, code structure, implementation steps, or naming. Existing maintenance functionality remains unchanged (see Acceptance Criteria).
>
> **Revision note.** This revision adds capabilities found in comparable Canadian fleet maintenance/compliance products and the broader Canadian regulatory regime: other-province periodic inspections (National Safety Code / CVIP), carrier-level safety ratings (Ontario CVOR, Quebec PEVL), preventive-maintenance scheduling, meter/telematics readings, equipment credential/document expiry, recall & warranty tracking, out-of-service/downtime, and audit-ready reporting. New sections are 3, 4, 6, 7, 8, 9 and 14; existing sections were extended.

---

## Overview & purpose

Squibbon TMS needs maintenance compliance features for fleets operating in **Ontario** and **Quebec**, and — for carriers that operate across Canada — in other provinces under the **National Safety Code (NSC)**. The goal is to help fleets track inspection obligations, preventive maintenance, compliance documents, defects, alerts, repair follow-up, and dispatch readiness — all from within Squibbon.

This is **compliance tracking**, not official government portal integration. The primary capability areas are referred to throughout as:

- **Ontario DriveON Compliance Tracking** (not "DriveON Integration")
- **Quebec SAAQ Compliance Tracking** (not "SAAQ Integration")
- **National Safety Code (NSC) Inspection Compliance Tracking** for other provinces (not "Integration")

## Guiding principles & scope boundaries

These principles constrain the entire feature set and must hold for every requirement below:

- This is **compliance tracking**, not official government portal integration.
- Squibbon does **not** scrape DriveON, SAAQclic, CVOR, PEVL, Transport Canada, or any other government portal.
- Squibbon does **not** store government portal usernames or passwords (any jurisdiction).
- Squibbon does **not** claim to replace official inspection systems or certify regulatory compliance.
- Squibbon helps fleets **track** compliance records, preventive maintenance, documents, alerts, defects, repairs, and dispatch readiness.
- Compliance data such as recalls, abstracts, and safety ratings is **entered manually or received only via officially authorized data sources** — never scraped.

(See the **Non-Goals** section for the full, explicit exclusion list.)

---

## 1. Ontario DriveON Compliance Tracking

**Business requirement:** Fleets operating in Ontario need a way to track commercial vehicle inspection compliance under Ontario DriveON.

**Functional requirements:**

| ID | Requirement |
|----|-------------|
| DRV-1 | User can track DriveON inspection status for trucks and trailers. |
| DRV-2 | User can record inspection date, expiry date, pass/fail status, certificate number, sticker number, inspection shop/VIC name, technician name, and notes. |
| DRV-3 | User can upload inspection documents, certificates, emails, or photos. |
| DRV-4 | System shows whether each truck/trailer is compliant, expiring soon, expired, failed, or missing required inspection information. |
| DRV-5 | System alerts users before inspection expiry. |
| DRV-6 | Alert timing is configurable by tenant. |
| DRV-7 | Failed inspections are linked to maintenance/repair follow-up. |
| DRV-8 | User can create a repair task/order from a failed inspection or inspection defect. |
| DRV-9 | User can mark the issue repaired and ready for re-inspection. |
| DRV-10 | System keeps history of all inspection records and uploaded documents. |
| DRV-11 | System supports dispatch blocking when a truck/trailer has expired or failed inspection, based on tenant settings. |
| DRV-12 | If dispatch blocking is enabled, trip creation/publishing clearly shows the compliance issue. |

---

## 2. Quebec SAAQ Compliance Tracking

**Business requirement:** Fleets operating in Quebec need a way to track SAAQ heavy vehicle compliance, mechanical inspection, preventive maintenance, circle checks, and defect resolution.

**Functional requirements:**

| ID | Requirement |
|----|-------------|
| SAAQ-1 | User can track Quebec heavy vehicle registration/compliance details. |
| SAAQ-2 | User can track Quebec safety rating information. |
| SAAQ-3 | User can track periodic mechanical inspection due dates. |
| SAAQ-4 | User can track mandatory maintenance due dates. |
| SAAQ-5 | User can track Preventive Maintenance Program / PMP status where applicable. |
| SAAQ-6 | User can record PMP approval number, approval date, expiry date, and status. |
| SAAQ-7 | User can upload SAAQ inspection documents, certificates, conduct records, or related compliance documents. |
| SAAQ-8 | User can record SAAQ inspection agent/shop name, inspection result, certificate/sticker details, expiry date, and notes. |
| SAAQ-9 | System shows whether each Quebec truck/trailer is compliant, expiring soon, expired, failed, or missing required information. |
| SAAQ-10 | System alerts users before SAAQ inspection, PMP, or maintenance expiry. |
| SAAQ-11 | System supports dispatch blocking when required Quebec compliance is expired, failed, or missing, based on tenant settings. |
| SAAQ-12 | User can track the Quebec compliance vignette/sticker and its validity. |
| SAAQ-13 | User can record defect-resolution obligations per Quebec rules — major defects repaired before return to operation, minor defects within a configurable deadline — and track them to closure (see Section 5). |

---

## 3. National Safety Code (NSC) Inspection Compliance Tracking — other provinces

> **Added (Canadian-market gap).** Carriers operating outside Ontario/Quebec must meet periodic commercial vehicle inspection requirements under the National Safety Code (e.g., Alberta **CVIP**, British Columbia, Saskatchewan, Manitoba and others), generally annual for trucks/trailers and semi-annual for buses. This section makes inspection tracking province-agnostic so cross-Canada fleets are covered with the same status, alert, repair-link, and dispatch behaviour as DriveON/SAAQ.

**Business requirement:** Fleets operating in provinces beyond Ontario and Quebec need to track periodic commercial vehicle safety inspection compliance under the applicable provincial program (National Safety Code).

**Functional requirements:**

| ID | Requirement |
|----|-------------|
| NSC-1 | User can track periodic commercial vehicle safety inspections for jurisdictions beyond Ontario and Quebec (e.g., Alberta CVIP, British Columbia, Saskatchewan, Manitoba). |
| NSC-2 | User can set the applicable jurisdiction(s) and inspection frequency per truck/trailer (e.g., annual for trucks/trailers, semi-annual for buses). |
| NSC-3 | User can record inspection date, expiry date, result, certificate/sticker/decal details, inspection station/facility name, inspector/technician name, and notes. |
| NSC-4 | User can upload inspection certificates, decals, or related documents. |
| NSC-5 | System shows whether each truck/trailer is compliant, expiring soon, expired, failed, or missing required inspection information for each applicable jurisdiction. |
| NSC-6 | System alerts users before inspection expiry, with tenant-configurable timing. |
| NSC-7 | Failed inspections link to repair follow-up and can block dispatch per tenant settings (consistent with DriveON/SAAQ behaviour). |
| NSC-8 | System keeps history of all inspection records and uploaded documents. |

---

## 4. Carrier / Operator-Level Compliance — Ontario CVOR & Quebec PEVL

> **Added (Canadian-market gap).** DriveON tracks the *vehicle* inspection; **CVOR** (Ontario) and **PEVL** (Quebec) track the *carrier's* safety standing — certificate validity and a safety rating driven by collisions, convictions, and inspection/out-of-service results. Fleets need to track these operator-level credentials and watch ratings against thresholds. Tracking only — no portal scraping.

**Business requirement:** Fleets need to track carrier/operator-level safety credentials and ratings (Ontario CVOR, Quebec PEVL) alongside per-vehicle inspections.

**Functional requirements:**

| ID | Requirement |
|----|-------------|
| CAR-1 | User can record Ontario CVOR details — operator/registration number, certificate expiry, and uploaded CVOR documents/abstract. |
| CAR-2 | User can record carrier safety rating (Ontario CVOR rating; Quebec PEVL/SAAQ safety rating) with effective dates. |
| CAR-3 | User can record and monitor carrier safety performance indicators the fleet maintains (e.g., violation/threshold rate, out-of-service rate), with alerts as thresholds are approached. |
| CAR-4 | System alerts users before carrier credential (CVOR/PEVL) expiry. |
| CAR-5 | Carrier-level compliance status is visible to maintenance, dispatch, and admin users and on the maintenance dashboard. |
| CAR-6 | System keeps history of carrier-level compliance records and uploaded documents. |

---

## 5. Circle Check / Daily Inspection Requirements

**Functional requirements:**

| ID | Requirement |
|----|-------------|
| CC-1 | Driver or dispatcher can submit a circle check/daily inspection report. |
| CC-2 | Circle check captures vehicle, driver/person performing check, date/time, location, odometer (if available), defects, and notes. |
| CC-3 | User can classify defects as minor or major. |
| CC-4 | Major defects prevent dispatch until resolved, if tenant setting requires blocking. |
| CC-5 | Minor defects are tracked for maintenance follow-up. |
| CC-6 | Defects are linkable to repair tasks/orders. |
| CC-7 | User can upload photos or documents with circle check defects. |
| CC-8 | System keeps history of circle checks by vehicle. |
| CC-9 | *(Added)* User can designate a circle check as pre-trip or post-trip. |
| CC-10 | *(Added)* System tracks defect-resolution deadlines — major defects before return to operation; minor defects within a configurable window (e.g., 48 hours) per tenant/jurisdiction rules — and flags overdue resolutions. |
| CC-11 | *(Added)* An authorized user (carrier/mechanic) can certify that reported defects were repaired; the driver can acknowledge the inspection. |
| CC-12 | *(Added)* "No defect" circle checks are also recorded and retained. |

---

## 6. Preventive Maintenance (PM) Program & Scheduling

> **Added (Canadian-market gap).** Comparable products (Fleetio, AMCS/Dossier, Whip Around, RTA) center on **preventive-maintenance scheduling driven by meters (odometer/engine hours) and time intervals**, with reminders and PM history. This also underpins Quebec's recognized PMP, which can replace periodic inspection. These requirements may **extend Squibbon's existing maintenance templates/scheduling** rather than introduce a parallel mechanism.

**Business requirement:** Fleets need recurring preventive-maintenance schedules so required service is tracked, due/overdue, and tied to compliance and dispatch readiness.

**Functional requirements:**

| ID | Requirement |
|----|-------------|
| PM-1 | User can define preventive-maintenance service schedules per equipment or equipment type. |
| PM-2 | Schedules can be triggered by time interval, odometer, and/or engine hours (whichever comes first). |
| PM-3 | System shows PM items as upcoming, due, or overdue, and alerts before they come due. |
| PM-4 | PM schedules can represent Quebec PMP obligations where a recognized PMP replaces periodic inspection. |
| PM-5 | Completing a PM updates its due status and records completion against the schedule. |
| PM-6 | System keeps PM history per equipment. |
| PM-7 | Overdue mandatory PM contributes to compliance status and, per tenant settings, to dispatch readiness. |

---

## 7. Meter & Telematics Readings

> **Added (Canadian-market gap).** Meter-based inspection/PM due dates require current odometer and engine-hour values. Squibbon already integrates telematics/ELD providers (e.g., Samsara, Isaac, Geotab); where available, readings can be sourced automatically, otherwise entered manually. (Telematics is not a government portal — this does not affect the no-scraping principle.)

**Functional requirements:**

| ID | Requirement |
|----|-------------|
| MTR-1 | User can record odometer and engine-hour readings for each truck/trailer. |
| MTR-2 | Where Squibbon already integrates telematics/ELD, readings may be captured automatically; otherwise they are entered manually. |
| MTR-3 | Latest readings are used to calculate meter-based inspection and PM due status. |
| MTR-4 | System keeps a history of meter readings. |
| MTR-5 | *(Optional, where supported)* Diagnostic fault codes from telematics can raise a maintenance item/defect for follow-up. |

---

## 8. Equipment Credentials & Document Expiry Tracking

> **Added (Canadian-market gap).** Beyond safety inspections, fleets must keep registration, insurance, and permits current; comparable products track these "license/permit renewals" with reminders.

**Functional requirements:**

| ID | Requirement |
|----|-------------|
| DOC-1 | User can track expiry of equipment credentials beyond inspections — registration/plate, insurance, operating permits (e.g., apportioned/IRP, fuel/IFTA decal, oversize/special), and annual sticker/decal. |
| DOC-2 | User can upload supporting documents for each credential. |
| DOC-3 | System shows compliant / expiring-soon / expired / missing status per credential. |
| DOC-4 | System alerts before credential expiry, with configurable warning days. |
| DOC-5 | Per tenant settings, an expired required credential (e.g., registration or insurance) can contribute to dispatch blocking. |
| DOC-6 | System keeps history of credential records and documents. |

---

## 9. Recall & Warranty Tracking

> **Added (Canadian-market gap).** Comparable products track manufacturer **recalls/campaigns** and **warranty** coverage. Recalls are a safety/compliance item; warranty supports repair decisions. Recall data is recorded manually or via authorized sources — no portal scraping.

**Functional requirements:**

| ID | Requirement |
|----|-------------|
| REC-1 | User can record manufacturer or Transport Canada safety recalls/campaigns per truck/trailer (recall number, description, issue date, status). |
| REC-2 | User can upload recall notices/documents. |
| REC-3 | Open recalls are visible on the equipment profile and dashboard. |
| REC-4 | User can link a recall to repair follow-up and mark it resolved/closed. |
| REC-5 | System keeps recall history. |
| WAR-1 | *(Optional, where applicable)* User can record equipment or component warranty coverage and expiry (by time and/or meter). |
| WAR-2 | *(Optional)* When creating repair follow-up, system can flag items that may be warranty-eligible. |
| WAR-3 | *(Optional)* Warranty records are visible on the equipment profile. |

---

## 10. Compliance Status and Alerts

**Functional requirements:**

| ID | Requirement |
|----|-------------|
| STAT-1 | Each truck/trailer has a clear maintenance compliance status. |
| STAT-2 | Status is visible from the equipment list, equipment profile, maintenance dashboard, and trip creation/publish flow. |
| STAT-3 | System identifies: compliant equipment, expiring soon, expired compliance, failed inspection, unresolved major defect, and missing required compliance record. |
| STAT-4 | System generates alerts for upcoming expiry. |
| STAT-5 | Alerts support configurable warning days. |
| STAT-6 | Alerts are visible to maintenance, dispatch, and admin users. |
| STAT-7 | System supports email or in-app notification requirements if already supported by Squibbon. |
| STAT-8 | *(Added)* Compliance status also reflects overdue mandatory PM, expired required credentials, open safety recalls, and out-of-service status. |

---

## 11. Dispatch Compliance Validation

**Functional requirements:**

| ID | Requirement |
|----|-------------|
| DISP-1 | During trip creation or trip publishing, system validates selected truck/trailer compliance. |
| DISP-2 | If equipment is not compliant, system shows the reason clearly. |
| DISP-3 | Tenant can configure whether non-compliant equipment blocks dispatch. |
| DISP-4 | Blocking conditions may include: expired inspection, failed inspection, unresolved major defect, missing required compliance record, and expired PMP/maintenance record for Quebec. |
| DISP-5 | If override is allowed, only authorized users can override. |
| DISP-6 | Override must require a reason. |
| DISP-7 | Override must be visible in audit/history. |
| DISP-8 | *(Added)* Equipment in an out-of-service state is treated as not dispatch-ready. |
| DISP-9 | *(Added)* Per tenant settings, an overdue periodic inspection in any tracked jurisdiction (DriveON/SAAQ/NSC) or an overdue mandatory PM may block dispatch. |
| DISP-10 | *(Added)* Per tenant settings, an expired required credential (e.g., registration or insurance) may block dispatch. |

---

## 12. Repair and Maintenance Follow-up

**Functional requirements:**

| ID | Requirement |
|----|-------------|
| REP-1 | Failed inspections and circle check defects create or link to repair work. |
| REP-2 | Repair work shows the original compliance source. |
| REP-3 | User can track repair status. |
| REP-4 | Once repair is completed, the compliance issue updates accordingly. |
| REP-5 | Some issues may require re-inspection before equipment becomes compliant. |
| REP-6 | Repair and compliance history remains visible on the equipment profile. |
| REP-7 | *(Added)* User can record repair work details — assigned shop/vendor (internal or external), work performed, parts used (as a reference, not full inventory management), labour, cost, and dates. |
| REP-8 | *(Added)* Equipment can be placed out-of-service — manually, or automatically from a failed inspection or unresolved major defect — and is flagged as not dispatch-ready while out-of-service. |
| REP-9 | *(Added)* User can record a return-to-service date; system tracks downtime duration. |
| REP-10 | *(Added)* Repairs can be flagged as warranty-eligible where warranty coverage applies. |

---

## 13. Maintenance Dashboard Requirements

The maintenance dashboard adds the following compliance views:

| ID | Requirement |
|----|-------------|
| DASH-1 | Compliance summary by province/jurisdiction. |
| DASH-2 | Expiring soon inspections. |
| DASH-3 | Expired inspections. |
| DASH-4 | Failed inspections. |
| DASH-5 | Open major defects. |
| DASH-6 | Open minor defects. |
| DASH-7 | Equipment blocked from dispatch. |
| DASH-8 | Upcoming PMP/maintenance due items for Quebec. |
| DASH-9 | Recently completed compliance repairs. |
| DASH-10 | *(Added)* Upcoming and overdue preventive maintenance (PM) items. |
| DASH-11 | *(Added)* Expiring/expired equipment credentials (registration, insurance, permits). |
| DASH-12 | *(Added)* Open safety recalls. |
| DASH-13 | *(Added)* Equipment currently out-of-service (with downtime). |
| DASH-14 | *(Added)* Carrier-level safety credential/rating status (CVOR, PEVL). |

---

## 14. Compliance Reporting & Audit Readiness

> **Added (Canadian-market gap).** Carriers undergo MTO (Ontario), SAAQ (Quebec), and other provincial facility audits and must produce inspection/maintenance/defect records on demand. Comparable products make "pulling records for an audit" a first-class capability.

**Functional requirements:**

| ID | Requirement |
|----|-------------|
| RPT-1 | User can generate/export a compliance record package for a truck/trailer or for the fleet over a selected date range, suitable for an MTO, SAAQ, or other provincial facility audit. |
| RPT-2 | The package includes inspection records, PM/maintenance history, circle checks/defects, repairs, credentials, and uploaded documents. |
| RPT-3 | User can filter compliance records by jurisdiction, equipment, status, and date range. |
| RPT-4 | Exported/printed records retain references to who recorded, changed, or overrode each record (traceability). |

---

## 15. Equipment Profile Requirements

On a truck/trailer profile, the user should see:

| ID | Requirement |
|----|-------------|
| EQP-1 | Current compliance status. |
| EQP-2 | Latest Ontario DriveON inspection, if applicable. |
| EQP-3 | Latest Quebec SAAQ inspection, if applicable. |
| EQP-4 | PMP status, if applicable. |
| EQP-5 | Circle check history. |
| EQP-6 | Open defects. |
| EQP-7 | Repair history. |
| EQP-8 | Uploaded certificates/documents. |
| EQP-9 | Upcoming due dates. |
| EQP-10 | Compliance alerts. |
| EQP-11 | *(Added)* Periodic inspection status for any other applicable jurisdiction (NSC/CVIP). |
| EQP-12 | *(Added)* Preventive-maintenance schedule and PM history. |
| EQP-13 | *(Added)* Latest meter readings (odometer / engine hours). |
| EQP-14 | *(Added)* Equipment credentials and expiry (registration, insurance, permits). |
| EQP-15 | *(Added)* Open safety recalls and recall history. |
| EQP-16 | *(Added)* Out-of-service status and downtime history. |
| EQP-17 | *(Added, optional)* Warranty information, where applicable. |

---

## 16. User Roles / Permissions Requirements

Product-level permission requirements:

| ID | Requirement |
|----|-------------|
| PERM-1 | Maintenance users can create and update compliance records. |
| PERM-2 | Dispatch users can view compliance status during planning and publishing. |
| PERM-3 | Drivers can submit circle checks if the mobile flow supports it. |
| PERM-4 | Admin users can configure compliance settings. |
| PERM-5 | Only authorized users can override dispatch blocking. |
| PERM-6 | Compliance history cannot be deleted without proper permission. |
| PERM-7 | *(Added)* Only authorized users can place equipment out-of-service or return it to service. |
| PERM-8 | *(Added)* Only authorized users can certify defect repairs or close recalls. |
| PERM-9 | *(Added)* Admin users can configure PM schedules, credential types, alert thresholds, and jurisdiction settings. |

---

## 17. Non-Goals

The following are explicitly **out of scope** for this work:

| ID | Non-Goal |
|----|----------|
| NG-1 | No direct DriveON API integration unless official access is confirmed. |
| NG-2 | No direct SAAQclic integration unless official access is confirmed. |
| NG-3 | No portal scraping. |
| NG-4 | No automated login to government portals. |
| NG-5 | No storage of government portal credentials. |
| NG-6 | No claim that Squibbon replaces official Ontario or Quebec inspection systems. |
| NG-7 | No legal/compliance certification claim by Squibbon. |
| NG-8 | Squibbon only helps track compliance records, documents, alerts, defects, repairs, and dispatch readiness. |
| NG-9 | *(Added)* No scraping of, or automated login to, MTO, SAAQ/SAAQclic, CVOR, PEVL, Transport Canada, or other provincial systems for recalls, abstracts, or safety ratings; such data is entered manually or received only via officially authorized data sources. |
| NG-10 | *(Added)* No storage of government portal credentials for any jurisdiction (extends NG-5 Canada-wide). |
| NG-11 | *(Added)* Driver Hours-of-Service (HOS) / ELD logbook compliance is out of scope of this maintenance-compliance PRD (separate driver-compliance domain). |
| NG-12 | *(Added)* Full parts inventory/procurement, tire lifecycle management, and fuel/IFTA tax reporting are out of scope of this PRD (may be separate maintenance/TMS modules); only references needed for the repair record are captured here. |
| NG-13 | *(Added)* Squibbon does not certify or guarantee regulatory compliance for any jurisdiction; it provides tracking, documentation, alerts, and dispatch-readiness support. |

---

## 18. Acceptance Criteria

| ID | Acceptance Criterion |
|----|----------------------|
| AC-1 | User can record Ontario DriveON inspection information for a truck/trailer. |
| AC-2 | User can upload Ontario inspection certificate/document. |
| AC-3 | User can see Ontario inspection status and expiry. |
| AC-4 | System alerts before Ontario inspection expiry. |
| AC-5 | System can block dispatch for expired/failed Ontario inspection if enabled. |
| AC-6 | User can record Quebec SAAQ inspection information. |
| AC-7 | User can track Quebec PMP/maintenance compliance. |
| AC-8 | User can submit circle check with minor/major defects. |
| AC-9 | Major defect can block dispatch if enabled. |
| AC-10 | Failed inspection or defect can create repair follow-up. |
| AC-11 | Repair completion updates the compliance issue status. |
| AC-12 | Equipment profile shows full compliance and repair history. |
| AC-13 | Dashboard shows expiring, expired, failed, and blocked equipment. |
| AC-14 | All compliance changes and overrides are traceable. |
| AC-15 | Existing maintenance functionality remains unchanged. |
| AC-16 | *(Added)* User can track periodic safety inspections (NSC/CVIP) for provinces beyond Ontario and Quebec. |
| AC-17 | *(Added)* User can record carrier-level safety credentials and ratings (Ontario CVOR, Quebec PEVL) and be alerted before expiry. |
| AC-18 | *(Added)* User can define preventive-maintenance schedules by time, odometer, and/or engine hours and see due/overdue PM. |
| AC-19 | *(Added)* User can record odometer/engine-hour readings (manually or from existing telematics integration) that drive due calculations. |
| AC-20 | *(Added)* User can track expiry of registration, insurance, and permits with alerts. |
| AC-21 | *(Added)* User can record safety recalls and link them to repair follow-up. |
| AC-22 | *(Added)* Equipment can be placed out-of-service and is blocked from dispatch while out-of-service (per settings). |
| AC-23 | *(Added)* User can export an audit-ready compliance record package by equipment or fleet for a date range. |
| AC-24 | *(Added)* Compliance status reflects inspections, PM, credentials, recalls, and out-of-service state. |

---

## Appendix — Considered but intentionally excluded

To bound scope, the following capabilities seen in some Canadian products were reviewed and **left out** of this PRD (candidates for separate modules):

- **Driver Hours-of-Service / ELD logbook compliance** — separate driver-compliance domain.
- **Full parts inventory & procurement / auto-reordering** — separate maintenance module; here only "parts used" is referenced on the repair record.
- **Tire lifecycle management** (rotation, tread, retread programs).
- **Fuel, MPG, and IFTA fuel-tax reporting.**
- **Total-cost-of-ownership / cost-per-km analytics** — basic repair cost is captured (REP-7); deeper cost analytics deferred.
- **VMRS coding standards** — could be adopted later for standardized repair coding.

## Appendix — Benchmark references

Regulatory:
- [Ontario DriveON — commercial vehicle safety/emissions inspection](https://www.ontario.ca/page/get-safety-or-emissions-inspection-commercial-vehicle) · [Ontario CVOR](https://www.ontario.ca/page/commercial-vehicle-safety-requirements)
- [Quebec SAAQ — Preventive Maintenance Program (PMP/PEP)](https://saaq.gouv.qc.ca/en/transportation-goods/preventative-maintenance-program) · [SAAQ — owners & operators (PEVL)](https://saaq.gouv.qc.ca/en/transportation-goods/owners-operators)
- [Alberta CVIP — Commercial Vehicle Inspection Program (National Safety Code)](https://www.alberta.ca/vehicle-inspection-program-commercial-vehicles)

Product benchmarks (feature comparison): Fleetio, AMCS Fleet Maintenance (formerly Dossier), Whip Around, RTA Fleet, Samsara Connected Maintenance.
