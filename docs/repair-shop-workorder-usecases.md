# Repair Shop — Work Order, Execution, Invoicing & Payment Use Cases

**Scope:** the operational core of the repair-shop module — create a work order, build it (in-house **and** outside/sublet work), execute it, consume inventory, invoice, and collect payment.
Reference systems: [Shopmonkey](https://www.shopmonkey.io/), [Tekmetric](https://www.tekmetric.com/), [Fullbay](https://www.fullbay.com/) (heavy-duty), Shop-Ware, Mitchell 1, Protractor.
Companion doc: [repair-shop-module-spec.md](repair-shop-module-spec.md) (this drills into Module 4 + parts/inventory + payments).

> **Use-case format:** each row is a candidate Notion case — `ID · Use case · Actor(s) · Notes`. Group headings are candidate epics.

---

## Order lifecycle (the spine)

```
Estimate ──auth──▶ Repair Order (work authorized / in progress) ──complete──▶ Invoice ──pay──▶ Paid / Closed
   │                        │                                                     │
   └─ decline/defer         ├─ in-house jobs  (your labour + your parts)          └─ deposit can be taken earlier
                            └─ sublet jobs    (outside vendor does the work)
```

A single **order** carries customer, vehicle, service advisor, status, and the money (labour, parts, sublet, tires, fees, discounts, tax, total, paid, balance). One order can contain **both** in-house and sublet jobs.

**Actors:** Service Advisor (writer), Technician, Parts/Inventory clerk, Shop Manager/Admin, Customer, Outside Vendor (sublet/parts), Accounting.

**Key distinction this module must model (per requirement — record *all* repairs):**
| Line type | Who performs | Cost source | Billed to customer |
|---|---|---|---|
| In-house labour | Your technician | Tech time / labour rate | Hours × rate (+ labour matrix) |
| Stock part | — | Your inventory (on-hand) | Part price (+ parts matrix) |
| Special-order part | — | Vendor PO (per-WO buy) | Part price (+ matrix) |
| **Sublet labour/service** | **Outside vendor** | **Vendor invoice/bill** | **Vendor cost + markup** |
| **Sublet w/ in-house R&R** | **Vendor + your tech** | Vendor cost + your labour | Both lines |

---

## 1. Work order creation & intake

| ID | Use case | Actor | Notes |
|----|----------|-------|-------|
| WO-1 | Create a work order from scratch (walk-in) | Advisor | Select/create customer + vehicle |
| WO-2 | Create a work order from a booked appointment | Advisor | Pre-fills customer/vehicle/requested services |
| WO-3 | Create a work order from inspection-recommended work | Advisor | Carries recommended jobs onto the WO |
| WO-4 | Create a work order from prior declined/deferred work | Advisor | Pulls forward previously declined jobs |
| WO-5 | Capture intake details | Advisor | Mileage-in, customer concerns/complaints, VIN/plate lookup, drop-off photos |
| WO-6 | Assign service advisor & promised/ready time | Advisor | Drives the workflow board |
| WO-7 | Record waiter vs drop-off, key tag / location | Advisor | |
| WO-8 | Work-order numbering & status lifecycle | System | Estimate → Repair Order → Invoice; customizable workflow columns |
| WO-9 | One order with many jobs, or many orders per visit | Advisor | Model decision: jobs within one WO |

## 2. Build the work order — in-house jobs (labour + parts)

| ID | Use case | Actor | Notes |
|----|----------|-------|-------|
| JOB-1 | Add a service/job (groups labour + parts) | Advisor | The unit a customer approves/declines |
| JOB-2 | Add a canned service/job (pre-built labour+parts+price) | Advisor | Speeds estimating; standardizes pricing |
| JOB-3 | Add a labour line (operation, hours, rate) | Advisor/Tech | From labour guide or manual; apply labour matrix |
| JOB-4 | Add a stock part line from inventory | Advisor/Parts | Shows on-hand; price via parts matrix |
| JOB-5 | Add an ad-hoc / non-stock part line | Advisor | Manual part not in catalogue |
| JOB-6 | Parts & labour guide lookup inside the job | Advisor | MOTOR / PartsTech integration |
| JOB-7 | Add fees (shop supplies, hazmat/disposal, environmental) | Advisor/System | Flat or %, capped |
| JOB-8 | Add tire / tire-specific lines | Advisor | Where applicable |
| JOB-9 | Apply discounts (line and order level), coupons | Advisor | Permissioned |
| JOB-10 | Apply taxes per rules | System | Parts vs labour taxability; tax-exempt customers |
| JOB-11 | Mark a job recommended/optional vs approved; reorder jobs | Advisor | |
| JOB-12 | Show real-time job & order profitability | System | Cost vs price, margin |
| JOB-13 | Internal notes vs customer-facing notes per job/line | Advisor/Tech | |
| JOB-14 | Assign technician(s) to a job/line | Advisor | |

## 3. Outside / sublet repairs (record every outside repair)

| ID | Use case | Actor | Notes |
|----|----------|-------|-------|
| SUB-1 | Add a sublet job/line (outside vendor performs the work) | Advisor | First-class line type on the WO |
| SUB-2 | Select the sublet vendor & capture vendor cost/quote | Advisor | From vendor directory; canned sublets supported |
| SUB-3 | Choose sublet type: full sublet vs partial (in-house R&R + vendor specialty) | Advisor | e.g. windshield (full) vs turn rotors (partial) |
| SUB-4 | Record vendor cost + customer price with markup | Advisor | Sublet markup matrix or manual; track margin |
| SUB-5 | Raise a vendor purchase order / sublet order for the outside work | Advisor/Parts | Sent to vendor; linked to WO |
| SUB-6 | Receive completed sublet work + vendor invoice/bill | Parts/Accounting | Marks sublet returned |
| SUB-7 | Reconcile vendor invoice to estimated sublet cost (variance) | Accounting | Adjust margin if cost differs |
| SUB-8 | Track sublet status (sent out / in progress / returned) | Advisor/Tech | Prevents premature completion/invoicing |
| SUB-9 | Record outside-purchased parts for an in-house job | Parts | Distinct from sublet *labour* (part bought, your tech installs) |
| SUB-10 | Capture sublet/vendor warranty & pass-through to customer | Advisor | Vendor warranty terms recorded |
| SUB-11 | Attach sublet documents (vendor invoice, photos, certs) | Advisor | |
| SUB-12 | Report sublet spend by vendor & sublet margin | Manager | |

## 4. Parts procurement & inventory consumption

| ID | Use case | Actor | Notes |
|----|----------|-------|-------|
| PART-1 | Reserve/commit inventory when a stock part is added | System | Decrement available-to-promise |
| PART-2 | Consume/deduct inventory when used / WO closed | System | Adjust on-hand; cost captured for margin |
| PART-3 | Create a special-order / purchase order for non-stock parts, linked to WO | Parts | "Buy for this job" |
| PART-4 | Order parts via integrated supplier from the WO | Parts | PartsTech / Nexpart / RepairLink |
| PART-5 | Receive parts against a PO (full/partial); update inventory & cost | Parts | |
| PART-6 | Track part status per WO (ordered / received / installed / backordered) | Parts/Tech | Gates job progress |
| PART-7 | Return unused/wrong parts to vendor for credit; remove from WO | Parts | |
| PART-8 | Core charge & core return tracking | Parts | Charge core, refund on return |
| PART-9 | Landed cost / freight adjustments affecting margin | Parts | |
| PART-10 | Low-stock alert / auto-reorder triggered by consumption | System | |
| PART-11 | Move/unassign a part between WOs | Parts | |
| PART-12 | Warranty part replacement (no-charge part) | Parts | Links to warranty claim |

## 5. Estimate, approval & authorization

| ID | Use case | Actor | Notes |
|----|----------|-------|-------|
| AUTH-1 | Generate estimate from WO and send (text/email) | Advisor | Branded; itemized or summary |
| AUTH-2 | Customer authorizes estimate with e-signature | Customer | Capture authorizer, timestamp |
| AUTH-3 | Per-job approve/decline | Customer | Approves some jobs, declines others |
| AUTH-4 | Record verbal/in-person authorization | Advisor | Who, when, amount |
| AUTH-5 | Re-authorization when scope/total changes beyond threshold | Advisor/Customer | Additional work found mid-repair |
| AUTH-6 | Track declined/deferred jobs for follow-up | System | Feeds future estimates (WO-4) |
| AUTH-7 | Take a deposit/pre-payment at authorization | Advisor | Esp. for special-order parts/sublet |
| AUTH-8 | Authorization audit trail (estimate versions) | System | Every change retained |

## 6. Execution

| ID | Use case | Actor | Notes |
|----|----------|-------|-------|
| EXE-1 | Convert estimate → repair order (authorized) | Advisor | Work may begin |
| EXE-2 | Dispatch jobs to technicians; tech workload board | Advisor | |
| EXE-3 | Technician clocks in/out of WO or specific job/line | Technician | Labour time tracking |
| EXE-4 | Track job/line status (not started / in progress / on hold / sublet out / complete) | Technician | |
| EXE-5 | Capture actual vs billed labour hours | System | Technician efficiency |
| EXE-6 | Perform inspection during the job → recommend new jobs | Technician | Triggers re-authorization (AUTH-5) |
| EXE-7 | Add additional work/parts discovered mid-repair (change order) | Technician/Advisor | Notify advisor/customer |
| EXE-8 | Put WO/job on hold with reason (waiting parts/sublet/approval) | Technician | |
| EXE-9 | Record test/QC results, technician notes, photos/videos | Technician | |
| EXE-10 | Move WO across the workflow board (drag status) | Advisor | |
| EXE-11 | Reassign / split a job across technicians | Advisor | |
| EXE-12 | Track WO progress % / completion | System | |

## 7. Completion & QC

| ID | Use case | Actor | Notes |
|----|----------|-------|-------|
| DONE-1 | Mark all jobs complete; final QC / road-test sign-off | Technician | |
| DONE-2 | Capture mileage-out; update vehicle service history | Advisor | |
| DONE-3 | Verify parts installed & sublet returned before invoicing | Advisor | Gate to invoice |
| DONE-4 | Notify customer vehicle is ready | Advisor/System | |

## 8. Invoicing

| ID | Use case | Actor | Notes |
|----|----------|-------|-------|
| INV-1 | Convert repair order → invoice (bill only approved/completed jobs) | Advisor | |
| INV-2 | Recalculate totals: labour, parts, **sublet**, tires, fees, shop supplies, discounts, tax | System | |
| INV-3 | Apply final discounts / price adjustments / write-offs | Advisor/Manager | Reason + permission |
| INV-4 | Split invoice (customer-pay vs warranty vs insurance portions) | Advisor | |
| INV-5 | Warranty / no-charge (internal) lines + claim tracking | Advisor | |
| INV-6 | Fleet/account billing on terms (net 15/30), customer PO reference | Advisor | |
| INV-7 | Apply prior deposit/pre-payment to the invoice | System | |
| INV-8 | Generate branded invoice PDF (itemized vs summary) | System | |
| INV-9 | Send invoice via text/email; print | Advisor | |
| INV-10 | Edit / void / credit an invoice | Manager | Audit + permission |
| INV-11 | Multi-vehicle / consolidated invoice for a fleet customer | Advisor | |
| INV-12 | Show invoice profitability (labour/parts/sublet cost vs revenue) | Manager | |

## 9. Payments

| ID | Use case | Actor | Notes |
|----|----------|-------|-------|
| PAY-1 | Take payment: card present, card on file, cash, check | Advisor | |
| PAY-2 | Online payment via text-to-pay / payment link | Customer | |
| PAY-3 | Buy-now-pay-later / financing | Customer | |
| PAY-4 | Deposit / partial payment; track balance due | Advisor | |
| PAY-5 | Split payment across multiple methods | Advisor | |
| PAY-6 | Apply a payment across multiple invoices (fleet/account) | Accounting | |
| PAY-7 | Refund / void / chargeback handling | Manager | |
| PAY-8 | Record tip (optional) | Advisor | |
| PAY-9 | Surcharge / processing-fee handling (where allowed) | System | |
| PAY-10 | Mark invoice paid / partially paid / unpaid; issue receipt | System | |
| PAY-11 | Fleet AR: statements, aging, apply payments, write-offs | Accounting | |
| PAY-12 | Reconcile integrated-payments payouts; post to accounting | Accounting | |

## 10. Post-service & accounting

| ID | Use case | Actor | Notes |
|----|----------|-------|-------|
| POST-1 | Update vehicle service history with completed WO | System | Jobs, parts, mileage |
| POST-2 | Warranty tracking on work performed (labour/part period); lookup on return | System | Includes sublet warranty (SUB-10) |
| POST-3 | Comeback/rework WO linked to the original (no-charge/warranty); track comeback rate | Advisor | |
| POST-4 | Profitability reporting per WO / advisor / technician | Manager | ARO, effective labour rate, gross profit |
| POST-5 | Sync invoices, payments, POs, inventory to accounting (QuickBooks) | Accounting | |
| POST-6 | Request customer review / follow-up after service | System | |

## 11. Internal / non-customer & edge cases

| ID | Use case | Actor | Notes |
|----|----------|-------|-------|
| X-1 | Internal work order (shop's own/loaner vehicle) — no customer charge, cost tracked | Manager | |
| X-2 | Cancel/abandon WO (customer declines all) — capture diagnostic/teardown fee | Advisor | |
| X-3 | Estimate-only, no work done — diagnostic fee or no charge | Advisor | |
| X-4 | Transfer parts/jobs between work orders | Parts/Advisor | |
| X-5 | Reopen a closed/invoiced WO for correction | Manager | Audit trail |
| X-6 | Multi-location: WO at a location, parts from that location's inventory | System | |
| X-7 | Roles/permissions on WO actions (discount, void, price override, close) | Admin | |
| X-8 | Hazardous/regulated disposal recording (oil, tires, batteries) | System | |
| X-9 | Convert declined/deferred work into a future appointment/estimate | Advisor | |
| X-10 | Print/track parts pick-list & sublet send-out list per WO | Parts | |

---

## Primary happy path (in-house + sublet on one order)

1. Advisor creates WO for customer + vehicle, captures concern & mileage-in `(WO-1, WO-5)`.
2. Advisor builds jobs: an **in-house** brake job (labour + stock pads) `(JOB-1…4)` and a **sublet** windshield job to an outside vendor `(SUB-1…5)`.
3. Estimate sent; customer e-signs, approves both jobs, leaves a deposit `(AUTH-1, AUTH-2, AUTH-7)`.
4. RO created; tech clocks into the brake job; windshield sent to vendor and tracked `(EXE-1…4, SUB-8)`.
5. Stock pads consumed from inventory; vendor returns glass + sends bill, reconciled `(PART-2, SUB-6, SUB-7)`.
6. QC sign-off, mileage-out, ready notification `(DONE-1…4)`.
7. RO → invoice: labour + parts + sublet (cost+markup) + fees + tax, deposit applied `(INV-1, INV-2, INV-7)`.
8. Customer pays balance (card / text-to-pay); invoice closed; service history + warranty recorded; posted to accounting `(PAY-1…10, POST-1, POST-2, POST-5)`.

---

## Sources
- [Shopmonkey — Repair Order Status](https://support.shopmonkey.io/hc/en-us/articles/38743969407124-Repair-Order-Status) · [Workflow Views](https://support.shopmonkey.io/hc/en-us/articles/38743858598676-Workflow-Views) · [Send Estimates & Invoices](https://support.shopmonkey.io/hc/en-us/articles/38743964800276-Send-Estimates-Invoices)
- [Tekmetric — shop management](https://www.tekmetric.com/) (canned vendor sublets, parts matrices, purchase orders, return orders, integrated payments)
- [Fullbay — Subletting Repair Service](https://www.fullbay.com/blog/sublet-service/) (sublet process & markup)
- [Tekmetric — inventory & stock management best practices](https://www.tekmetric.com/post/inventory-and-stock-management-best-practices-for-auto-repair-shops)
- [Protractor — work orders](https://protractorsoftware.com/work-orders/)
