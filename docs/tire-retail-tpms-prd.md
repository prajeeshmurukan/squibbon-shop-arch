# Squibbon Shop — Tire Retail, TPMS & Storage PRD

**Type:** Product / functional requirements (requirements capture only — no technical/data/API design)
**Status:** Draft for review
**Applies to:** Squibbon Shop module (extension)

Turns the shop into a full-service tire retailer: fitment-aware quoting, live distributor ordering, TPMS service, DOT registration & recall checks, road-hazard warranties, and a seasonal **tire hotel**. Companion: [work-order field spec](repair-shop-workorder-fields.md) · [marketing & retention PRD](marketing-retention-prd.md). Mockups: [docs/mockups/tire-retail-tpms/](mockups/tire-retail-tpms/00-index.md).

> **Why this module:** the work-order spec already carries a tire line, a TPMS-aware inspection, and tire eco-fees — but a Canadian shop *lives* on tires. They are the single largest recurring, seasonal-volume category here, and the **tire hotel + provincial winter-tire law** are a structural advantage no US-first competitor can match. This module makes Squibbon the system of record for the whole tire lifecycle: sell → register → service → store → swap → sell again.

## Background / why now

Tires are not a side line in Canada — for most general-repair and tire-specialty shops they are the **highest-volume, most repeatable** category, and uniquely **seasonal**. Two provincial realities create a home-field advantage Squibbon should own:

- **Quebec mandates winter tires Dec 1 – Mar 15** on every passenger vehicle registered in the province (SAAQ). That law forces two predictable, province-wide swap surges per year — a fall fitting rush and a spring changeover — and a customer base that *must* store one set of tires somewhere for six months.
- **Ontario** has no mandate but strong seasonal demand, plus an insurance discount for winter tires that drives adoption — so the same fall/spring swap pattern holds in Mississauga and across the GTA.

That seasonality is the wedge. A **tire hotel** (off-season storage) turns a one-time tire sale into a recurring storage subscription *and* a guaranteed twice-a-year service visit — the customer comes back because we have their tires. Pair storage with seasonal **swap reminders** keyed to the Dec 1 – Mar 15 window and the shop captures the changeover before the customer shops around. The rest of the module — fitment catalog, live distributor pricing, TPMS, DOT registration & recall checks, road-hazard warranties — is table stakes that the leading tire suites (Tekmetric, Tire Power, EliteTeQ) already ship; Squibbon needs them to compete, and needs the hotel to win.

This builds directly on the existing tire line, TPMS inspection prompts, and provincial tire eco-fee settings; it adds the catalog, distributor connectivity, registration, warranty products, and storage that turn those fields into a sellable tire business.

## Goals

| Goal | Measure |
|---|---|
| Win the tire sale at the counter | Tire quotes → orders, attach rate of tires to ROs |
| Fit the *right* tire, fast | Time to a priced quote; % quotes built from fitment catalog (vs manual) |
| Capture recurring storage revenue | Tire-hotel sets stored, storage subscriptions active, storage $/season |
| Own the seasonal swap | Swap reminders → booked changeovers; repeat swap rate per stored set |
| Stay compliant & safe | % tires DOT-registered at sale; recall checks run before sale; eco-fees applied correctly |
| Add margin with protection products | Road-hazard attach rate; warranty $ sold |

## Personas

| Persona | Needs from this module |
|---|---|
| **Service advisor / counter** | Look up a fitment, pull live distributor price/availability, build a tire quote, add it to the RO, register the tire, sell road-hazard. |
| **Tire technician** | See the approved fitment & TPMS procedure, record DOT codes and tread depths, do the relearn, check stored sets in/out. |
| **Storage / yard coordinator** | Check tire sets in and out, assign bin/rack locations, run the seasonal swap-reminder queue, manage capacity. |
| **Shop owner / manager** | Storage capacity & revenue, distributor margins, recall/registration compliance, eco-fee configuration. |
| **Customer (Acme Fleet Ltd, 2019 Ford F-150)** | A clear winter-tire quote, knowing their stored set is safe and where it is, and a timely reminder before the Dec 1 deadline. |

---

## 1. Tire sales & fitment

| ID | Requirement |
|----|-------------|
| TIRE-1 | System provides a **fitment catalog** mapping **year / make / model / trim** → factory (OE) tire **size**, plus the approved **optional sizes** (plus-sizing) for that vehicle. |
| TIRE-2 | Advisor can resolve a fitment from the **vehicle on the RO** (VIN/YMM decode) or by selecting year/make/model/trim manually. |
| TIRE-3 | Advisor can **search tires by size** (e.g. 265/70R17) **or by brand/model**, filtered by season (all-season / winter / all-weather), load/speed rating, and run-flat. |
| TIRE-4 | System shows **live distributor availability and pricing** across connected distributors (e.g. PartsTech, 40+ Canadian/US distributors), including stock quantity, location/warehouse, cost, and ETA. |
| TIRE-5 | Advisor can **order tires online** from a distributor against the RO (single or multiple distributors), and track order/line status (ordered / shipped / received). |
| TIRE-6 | System supports **staggered fitments** — different front and rear sizes/quantities on the same vehicle — and keeps front/rear positions distinct through quote, order, and install. |
| TIRE-7 | Advisor can **build a tire quote** (set of 1–4+ tires with brand, model, size, qty, cost, retail) and add it to a **job on the RO** as tire lines. |
| TIRE-8 | Quote can include **mounting, balancing, valve/TPMS service, and disposal** as standard add-on lines (canned tire package). |
| TIRE-9 | System **auto-applies provincial tire eco-fees** (cross-referenced from settings — e.g. Quebec tire fee / provincial stewardship fee) per tire, by ship-to / shop province. |
| TIRE-10 | Quote shows margin (cost vs retail) per tire and for the package, using the parts/tire matrix where configured. |
| TIRE-11 | Advisor can compare two or three tire options side by side (good/better/best) for the same fitment before adding to the RO. |
| TIRE-12 | Quotes and tire lines display **bilingual (EN/FR)** labels and print bilingually on the estimate/invoice. |

## 2. TPMS service

| ID | Requirement |
|----|-------------|
| TPMS-1 | System provides a **TPMS sensor catalog** — OE and aftermarket/programmable sensors — looked up by vehicle (YMM) or sensor part number. |
| TPMS-2 | System provides **per-vehicle relearn / reset procedures** (e.g. auto-relearn / OBD relearn / stationary) so the tech knows how to reset the system for that vehicle. |
| TPMS-3 | When tires or wheels are removed on an RO (tire sale, swap, rotation, or storage check-in/out), the system **prompts for TPMS service** (inspect / service kit / sensor health). |
| TPMS-4 | Tech can **record sensor health** per wheel position (OK / weak battery / faulty / missing) and flag sensors that need replacement, converting them to recommended part lines. |
| TPMS-5 | Advisor can add **TPMS sensors and service-pack/valve-kit** lines to the RO from the catalog, with availability/pricing where the distributor supplies them. |
| TPMS-6 | System records that the **relearn was completed** for the visit and the procedure used. |

## 3. DOT registration & recalls

| ID | Requirement |
|----|-------------|
| REG-1 | System **captures each tire's DOT code** (Tire Identification Number) at point of sale, per tire/position, manually or by scan. |
| REG-2 | System **validates** the DOT code format/structure and flags codes that fail validation before they are saved or registered. |
| REG-3 | Before sale, system **checks the tire against known recalls** by DOT/brand/model and warns the advisor if a recall applies. |
| REG-4 | At point of sale, system **electronically registers** the tires with the manufacturer (tire registration), recording the customer, vehicle, DOT codes, and date — meeting the legal/safety **traceability** requirement. |
| REG-5 | System records **registration status** per tire (registered / pending / failed) and lets the advisor retry a failed registration. |
| REG-6 | Registered DOT codes attach to the **vehicle's service history** so a later recall can be traced to the customer and set. |

## 4. Road-hazard & tire warranty

| ID | Requirement |
|----|-------------|
| HAZ-1 | System offers a **road-hazard warranty product** the advisor can sell with a tire set, with a defined **term**, **coverage** (repair/replace), and **proration** rules. |
| HAZ-2 | System supports **mileage / treadwear warranties** (e.g. treadlife mileage rating) recorded against the tire set. |
| HAZ-3 | Road-hazard / warranty is added as a sellable **line on the RO**, priced per tire or per set, taxable per jurisdiction. |
| HAZ-4 | System records the **coverage details** (start date, term, mileage, covered set with DOT codes) for later reference. |
| HAZ-5 | Advisor can **create a claim** against an active road-hazard or treadwear warranty, recording the reason, affected tire(s), proration/credit, and outcome. |
| HAZ-6 | A warranty claim can generate a **credit or replacement line** on a new RO (tie to the original covered set). |

## 5. Tire hotel / seasonal storage

| ID | Requirement |
|----|-------------|
| HOTEL-1 | System can **store a customer's off-season tire set** as a tracked **set** (4+ tires) linked to the customer and vehicle, with a **set ID**. |
| HOTEL-2 | Each stored set has a **physical LOCATION** — bin / rack / shelf — so staff can retrieve it; locations are assignable and re-assignable. |
| HOTEL-3 | At **check-in**, staff record per-tire **tread depth** and capture **photos**, plus DOT/brand/size and any damage notes (condition baseline). |
| HOTEL-4 | At **check-out**, staff record per-tire **tread depth** and **photos** again, so wear and condition between seasons is documented. |
| HOTEL-5 | Sets are **scanned in and out** (set ID / location barcode) to move them between *stored* and *on-vehicle*, with a movement history. |
| HOTEL-6 | System runs **seasonal swap reminders** tied to **Quebec's mandatory winter-tire period (Dec 1 – Mar 15)** and Ontario seasonal demand — prompting the customer to book the changeover ahead of the deadline (reuses the reminders/booking channel from the marketing module). |
| HOTEL-7 | System supports **recurring storage billing / a storage subscription** (per season or per year), with renewal and the ability to add it to an RO or bill standalone. |
| HOTEL-8 | System provides a **storage capacity view** — occupied vs available bins/racks, sets due for swap, and sets overdue for pickup. |
| HOTEL-9 | Stored-set status is visible on the **vehicle/customer record** (which set is stored, where, since when) and on the RO when the customer comes in for a swap. |
| HOTEL-10 | Storage records, photos, and tread depths are retained for the storage term and surface bilingually (EN/FR) to the customer where shared. |

---

## Data entities (conceptual)

> **Data & tenancy note:** Squibbon is **schema-per-tenant** with **BIGINT** primary keys and JPA `ddl-auto=validate`. Every new conceptual entity below implies a **per-tenant migration** before use — there is no runtime schema creation. This PRD captures *what* is stored, not table/column design.

| Entity | Core fields (conceptual) |
|---|---|
| **Fitment** | vehicle YMM/trim ref · OE size · approved optional sizes · staggered front/rear sizes · season suitability |
| **Tire catalog item** | brand · model · size · load/speed rating · season · run-flat flag · distributor refs |
| **Distributor availability** | tire ref · distributor · warehouse/location · qty · cost · ETA · price (live) |
| **Tire order** | RO ref · distributor · lines (tire + qty + cost) · status · ordered/shipped/received dates |
| **Tire quote / line** | RO/job ref · brand/model/size · position (front/rear) · qty · cost · retail · eco-fee · margin · locale |
| **TPMS sensor** | OE/aftermarket · part number · vehicle compatibility · relearn procedure ref |
| **TPMS service record** | RO ref · per-position sensor health · relearn procedure used · completed flag |
| **DOT registration** | tire/position ref · DOT code · validation status · recall-check result · registration status · customer/vehicle · date |
| **Road-hazard / tire warranty** | product · term · coverage · proration · mileage/treadwear · covered set (DOT codes) · start date · price |
| **Warranty claim** | warranty ref · affected tire(s) · reason · proration/credit · outcome · resulting RO line |
| **Stored tire set (tire hotel)** | set ID · customer/vehicle ref · 4+ tires (DOT/brand/size) · bin/rack location · status (stored/on-vehicle) |
| **Storage condition record** | set ref · event (check-in/check-out) · per-tire tread depth · photos · damage notes · date/staff |
| **Storage subscription / billing** | set ref · term (season/year) · price · renewal · RO/invoice ref · status |
| **Swap reminder** | stored-set ref · season (winter/summer) · target window (e.g. Dec 1–Mar 15) · channel · booking link · status |

## Integrations

| ID | Integration |
|----|-------------|
| INT-1 | **Distributor / tire ordering** — live availability, pricing, and online ordering across Canadian/US distributors (e.g. **PartsTech**, 40+ distributors); reuses the vendor/PO model from the work-order spec. |
| INT-2 | **Tire registration & recall service** — electronic manufacturer registration and recall lookup at point of sale (e.g. a tire-registration provider). |
| INT-3 | **Fitment / TPMS data** — fitment catalog and per-vehicle TPMS relearn procedures & sensor catalog. |
| INT-4 | **VIN / plate decode** — resolve YMM/trim for fitment from the vehicle on the RO (reuses existing decode). |
| INT-5 | **Reminders & online booking** — seasonal swap reminders and self-scheduling reuse the marketing/comms channel and booking page. |
| INT-6 | **Work orders / invoicing / payments** — tire quotes, eco-fees, storage billing, and warranty lines flow onto the RO/invoice and through payments. |
| INT-7 | **Settings — provincial eco-fees & tax** — tire eco-fee/stewardship and tax config sourced from shop settings by province. |

## Roles & permissions

| ID | Requirement |
|----|-------------|
| PERM-1 | Advisors can look up fitments, build tire quotes, order from distributors, and add tire/TPMS/warranty lines to an RO. |
| PERM-2 | Tire/storage staff can record DOT codes, tread depths, photos, TPMS health, and check sets in/out. |
| PERM-3 | Only authorized users can **configure** distributor connections, eco-fee/tax settings, road-hazard products, and storage pricing. |
| PERM-4 | Only authorized users can **process a warranty claim** that issues a credit/replacement. |
| PERM-5 | Tire-registration and storage condition records (DOT codes, check-in/out photos & tread depths) are auditable and cannot be deleted without elevated permission. |

## Non-goals

| ID | Non-goal |
|----|----------|
| NG-1 | Squibbon does not replace the distributor's own catalog/pricing — it integrates and orders. |
| NG-2 | Not a full tire-wholesale / distribution platform; the shop is a retailer, not a warehouse jobber. |
| NG-3 | Squibbon does not adjudicate manufacturer warranty claims — it records and submits; the manufacturer/registration provider decides. |
| NG-4 | No mounting-machine / balancer hardware control or wheel-imaging in v1. |
| NG-5 | Squibbon is not the legal authority on provincial winter-tire law or eco-fees — it provides tooling and reminders, not legal/tax advice. |
| NG-6 | Storage logistics beyond the shop (off-site warehouses, courier of tire sets) are out of scope for v1. |

## Acceptance criteria

| ID | Criterion |
|----|-----------|
| AC-1 | From a vehicle on an RO, advisor resolves the OE/optional fitment, searches by size or brand, and sees live distributor availability and price. |
| AC-2 | Advisor builds a tire quote (including a **staggered** front/rear set), adds it to the RO with mounting/balancing/disposal, and the correct **provincial tire eco-fee** auto-applies. |
| AC-3 | Advisor orders the tires online from a distributor against the RO and tracks order status. |
| AC-4 | Removing tires/wheels on an RO **prompts TPMS service**; tech looks up the sensor and relearn procedure, records sensor health, and marks the relearn complete. |
| AC-5 | At sale, each tire's **DOT code is captured and validated**, a **recall check** runs and warns if applicable, and the tires are **electronically registered** with status recorded. |
| AC-6 | Advisor sells a **road-hazard warranty** with the set and can later **create a claim** against it that produces a credit/replacement line. |
| AC-7 | Staff **check a tire set into the hotel** with a bin/rack location, set ID, per-tire tread depths and check-in photos, and can **check it out** with closing tread depths/photos. |
| AC-8 | System generates **seasonal swap reminders** tied to the **Dec 1 – Mar 15** Quebec window (and Ontario demand) that deep-link to booking. |
| AC-9 | A stored set is billed via a **recurring storage subscription**, and the **capacity view** shows occupied vs available bins and sets due/overdue for swap. |
| AC-10 | Tire quotes, registration prompts, storage records, and reminders are available in **EN/FR**. |

## Screens

See [docs/mockups/tire-retail-tpms/](mockups/tire-retail-tpms/00-index.md).

| # | File | Screen |
|---|------|--------|
| 01 | `01-tire-sale-fitment.svg` | Tire sale: fitment catalog + size results + live distributor availability/price + add to RO |
| 02 | `02-tpms-dot-registration.svg` | TPMS service + DOT code capture + recall check + electronic registration |
| 03 | `03-tire-hotel-storage.svg` | Tire hotel: stored sets, bin locations, tread depths in/out, seasonal reminders, storage billing |

## Sources

- [Tekmetric — Tire Suite](https://www.tekmetric.com/feature/tire-suite)
- [EliteTeQ — Tire Shop POS (Canada)](https://eliteteqpos.com/ca/industries/tire-shops/)
- [Tiremetrix — TPMS Manager & tire registration](https://www.tiremetrix.com/)
- [Tire Power by TCS Technologies — Tire retail software](https://tcstire.com/tire-retail-software)
- [SAAQ — Winter tires](https://saaq.gouv.qc.ca/en/road-safety/behaviours/winter-tires)
