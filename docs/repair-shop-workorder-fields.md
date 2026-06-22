# Repair Shop — Work Order Field Spec (all UI elements / data to capture)

Field-level reference for the work-order lifecycle (intake → build in-house + sublet → execute → parts/PO → invoice → payment). Drives the screen mockups in [docs/mockups/repair-shop-workorder/](mockups/repair-shop-workorder/00-index.md).
Grounded in Shopmonkey's data model (order / part / labor / subcontract / purchase_order / inventory_part), Tekmetric, Fullbay, and BAR documentation rules. Companion: [use cases](repair-shop-workorder-usecases.md) · [module spec](repair-shop-module-spec.md).

> Money is stored in cents internally (per API) and shown as currency in UI. `ref` = link to another entity. **Req** = required at the stage noted.

---

## 1. Work order (order header)

| Field | Type | Req | Notes |
|---|---|---|---|
| WO number | auto string | ✓ | Natural-sort; per location |
| Customer PO / external ref | text | | Customer's own PO (fleet) |
| Status | enum | ✓ | Estimate → Repair Order → Invoice → Archived (+ custom workflow columns) |
| Labels / tags | multi | | VIP, Waiter, Comeback, Warranty, **Sublet-out**, Parts on order |
| Customer | ref | ✓ | §2 |
| Vehicle | ref | ✓ | §3 |
| Service advisor / writer | ref(user) | ✓ | |
| Source | enum | | Walk-in, Appointment, Inspection, Online, Declined-carryover |
| Created date/time | datetime | ✓ | |
| Promised / ready by | datetime | | Drives board |
| Drop-off / pickup date-time | datetime | | |
| Waiter vs drop-off | flag | | |
| Key tag / vehicle location | text | | Lot/bay |
| Mileage in / Mileage out | integer | ✓in | Odometer; updates vehicle |
| Engine hours in/out | number | | HD equipment |
| Fuel level | enum | | Optional |
| Customer concern / complaint | text (multi) | | Drives jobs (3 Cs) |
| Internal notes | text | | Not customer-visible |
| Customer-facing notes | text | | Prints on estimate/invoice |
| Authorization | ref | | §12 |
| Location | ref | ✓ | Multi-location |
| Warranty / Insurance / Fleet flags | flag | | Affects invoicing |
| Linked WO (original/comeback) | ref | | §22 |
| Totals — labor / parts / sublet / tires / fees / shop supplies | money | auto | Per category |
| Discount total / Subtotal / Taxable subtotal / Tax / **Total** | money | auto | |
| Deposit & payments applied / **Balance due** | money | auto | §21 |
| Cost / Gross profit / Margin % | money,% | auto | Profitability |

## 2. Customer

| Field | Type | Req | Notes |
|---|---|---|---|
| Type | enum | ✓ | Individual / Business / **Fleet** |
| First & last name **or** Business name | text | ✓ | |
| Phone(s) | phone[] | ✓ | Mobile/work; primary flag |
| Email(s) | email[] | | For estimate/invoice send |
| Billing address | address | | |
| Preferred contact | enum | | Text / Email / Call |
| Tax-exempt | flag + cert# | | |
| Labor rate / price level | ref | | Per-customer override |
| Payment terms | enum | | Due on receipt / Net 15 / Net 30 (fleet) |
| Default discount | %/flat | | |
| Fleet / AR account # | text | | |
| Credit limit / on-hold | money/flag | | AR control |
| Marketing opt-in | flag | | |
| Notes | text | | |

## 3. Vehicle

| Field | Type | Req | Notes |
|---|---|---|---|
| Owner | ref(customer) | ✓ | |
| VIN | text | | VIN decode auto-fills YMM |
| License plate + province/state | text | | Plate lookup |
| Year / Make / Model / Submodel / Engine | text | ✓ | From decode or manual |
| Trim / Body / Drive / Transmission | text | | |
| Color | text | | |
| Unit # | text | | Fleet identifier |
| Production date | date | | |
| Current mileage (+ history) | integer | | |
| Engine hours | number | | HD |
| Registration / plate expiry | date | | Optional |
| Tags / Notes | text | | |

## 4. Service / Job (groups the work the customer approves)

| Field | Type | Req | Notes |
|---|---|---|---|
| Name / title | text | ✓ | e.g. "Front brake service" |
| Category | enum | | Brakes, Maintenance, Diagnostics… |
| Source | enum | | Manual / Canned / Inspection / Declined-carryover |
| Authorization status | enum | | Recommended / Approved / Declined / Deferred |
| Technician(s) | ref[] | | Assigned to job |
| Concern / Cause / Correction | text | | 3 Cs (BAR) |
| Labor lines | §5[] | | |
| Part lines | §6[] | | |
| **Sublet lines** | §7[] | | In-house *and* outside on the same job allowed |
| Fee lines | §9[] | | |
| Job note (internal / public) | text | | |
| Job status | enum | | Not started / In progress / On hold / Complete |
| Warranty (parts / labor period) | text | | |
| Subtotal / Cost / Margin | money | auto | |
| Ordinal | int | | Display order |

## 5. Labor line

| Field | Type | Req | Notes |
|---|---|---|---|
| Description / operation | text | ✓ | From labor guide or manual |
| Labor-guide / op code | ref | | MOTOR/ProDemand skill code |
| Billed hours | number | ✓ | |
| Actual hours | number | auto | From time tracking (§13) |
| Rate / hr | money | ✓ | Customer rate or labor matrix |
| Rate source | enum | | Customer / Matrix / Override |
| Technician | ref | | |
| Amount | money | auto | hours × rate |
| Discount | %/flat | | |
| Taxable | flag | | Labor taxability varies by jurisdiction |
| Internal labor cost | money | | For margin (pay rate) |
| Skill level / pay type | enum | | For tech pay |
| Note / Ordinal | text/int | | |

## 6. Part line (in-house: stock, special-order, customer-supplied)

| Field | Type | Req | Notes |
|---|---|---|---|
| Part name / description | text | ✓ | |
| Part number (MPN) / SKU | text | | |
| Manufacturer / Brand | text | | |
| Quantity | number | ✓ | |
| Unit cost (wholesale) | money | | Margin basis |
| Unit retail / sell | money | ✓ | |
| Price source | enum | | Parts **matrix** / Manual / List |
| Markup applied | %/auto | | |
| Extended price | money | auto | qty × retail |
| **Core charge** (core cost + core sale) | money | | + core return status |
| Discount / Taxable | %/flag | | |
| Vendor | ref | | §15 |
| Inventory part | ref | | Stock link (§19) |
| Source | enum | ✓ | Stock / Special-order / Customer-supplied |
| Status | enum | | Needed / Ordered / Received / Installed / Returned / Backordered |
| PO | ref | | If special-order (§16) |
| Warranty / Position / Note | text | | e.g. front/rear |

## 7. Sublet / outside-repair line ★ (record every outside repair)

| Field | Type | Req | Notes |
|---|---|---|---|
| Description / service | text | ✓ | e.g. "Windshield replacement" |
| Sublet type | enum | ✓ | **Full sublet** vs **Partial** (in-house R&R + vendor specialty) |
| Vendor | ref | ✓ | Sublet vendor (§15) |
| **Vendor cost** (shop pays) | money | ✓ | From quote / vendor invoice |
| **Customer price** (sell) | money | ✓ | Cost + markup |
| Markup | %/flat | | Sublet markup matrix or manual |
| Margin | money/% | auto | |
| Taxable | flag | | |
| Sublet order / vendor PO | ref | | §16 (kind = sublet) |
| Vendor invoice # | text | | Captured on return |
| Status | enum | ✓ | Quoted / Sent-out / In-progress / Returned / Billed |
| Sent date / Returned date | datetime | | Gates completion |
| Vendor warranty terms | text | | Passed through to customer |
| Documents | file[] | | Vendor invoice, photos, certs |
| Note / Ordinal | text/int | | |

## 8. Tire line (optional — tire shops)

| Field | Type | Notes |
|---|---|---|
| Brand / Model / Size / Load-speed rating | text | |
| Quantity / Cost / Retail | money | Like part |
| DOT # | text | |
| FET (federal excise tax) | money | |
| Old-tire / disposal fee | money | §9 |
| Inventory tire | ref | Stock link |

## 9. Fee / shop supply / hazmat line

| Field | Type | Notes |
|---|---|---|
| Name | text | Shop supplies / Hazmat / Environmental / Disposal / **Diagnostic fee** |
| Kind | enum | Percentage / Flat |
| Rate or amount | number/money | |
| Basis | enum | Labor / Parts / Order |
| Cap (max) | money | |
| Taxable | flag | |

## 10. Discount  ·  11. Tax (config applied to order)

| Field | Type | Notes |
|---|---|---|
| Discount scope / type / amount / reason / code | enum/money | Line / Job / Order; %/flat; coupon; applied-by (permission) |
| Tax config / jurisdiction | ref | Province/state |
| Parts tax rate / Labor tax rate | % | Often different |
| Taxable subtotal / Tax amount | money | auto |
| Custom-tax override | flag+money | |

## 12. Authorization

| Field | Type | Req | Notes |
|---|---|---|---|
| Status | enum | ✓ | Pending / Authorized / Partial / Declined |
| Method | enum | ✓ | E-signature / Verbal / In-person / Online |
| Authorized by (name) | text | ✓ | |
| Signature image | file | | E-sign |
| Authorized amount | money | ✓ | |
| Authorized date/time | datetime | ✓ | |
| IP / device | text | | Online auth |
| Per-job approvals | map(job→enum) | | Approve some / decline others |
| Re-authorization events | list | | When total changes beyond threshold |
| Notes / version history | text | | Full audit |

## 13. Technician time entry

| Field | Type | Notes |
|---|---|---|
| Technician | ref | |
| Work order / Job / Line | ref | Clock into specific line |
| Clock-in / Clock-out | datetime | |
| Duration | auto | |
| Type | enum | Billable / Non-billable / Break |
| Status | enum | Open / Closed |
| Note | text | |

## 14. Inspection finding (links to inspection module)

| Field | Type | Notes |
|---|---|---|
| Item / status / measurement / note / media | mixed | Green/Yellow/Red; converts to recommended job (§4) |

## 15. Vendor

| Field | Type | Notes |
|---|---|---|
| Name | text | |
| Type | enum | Parts supplier / **Sublet vendor** / Both |
| Contact (phone, email, rep) | mixed | |
| Account # / Store # | text | |
| Address / Tax ID | mixed | |
| Terms | enum | |
| Integration provider | enum | PartsTech / Nexpart / Epicor / Worldpac / ATD / RepairLink |
| Default markup | % | Parts or sublet |
| Notes | text | |

## 16. Purchase order (header)  ·  parts PO **and** sublet order

| Field | Type | Notes |
|---|---|---|
| Number / External number | string | |
| Kind | enum | Parts PO / **Sublet order** |
| Status | enum | Draft / Ordered / Received / Fulfilled / Cancelled |
| Vendor | ref | §15 |
| Linked order (WO) | ref | `orderId` |
| Provider | enum | PartsTech / Nexpart / Epicor / Worldpac / ATD / RepairLink |
| Ordered / Expected / Fulfilled dates | datetime | |
| Vendor invoice # | text | On receipt |
| Total cost / Tax / Shipping-freight | money | |
| Ship-to location | ref | |
| Note | text | |

## 17. Purchase-order line

| Field | Type | Notes |
|---|---|---|
| Name (description) | text | |
| Number (vendor part #) | text | |
| Quantity (ordered) | number | |
| Received quantity | number | Partial receipts |
| Unit cost / Extended cost | money | |
| Inventory part / tire | ref | Stock mapping |
| Linked WO part line | ref | Special-order tie-back |
| Core | money | |
| Status / Ordinal | enum/int | Ordered / Received / Backordered |

## 18. Goods receipt (receiving)

| Field | Type | Notes |
|---|---|---|
| PO | ref | |
| Received date / Received by | datetime/ref | |
| Per-line received qty (this receipt) | number | Full / partial |
| Condition / discrepancy | text | |
| Vendor packing slip / invoice # | text | |
| Landed cost / freight allocation | money | Updates cost |
| → updates inventory on-hand & cost | auto | |

## 19. Inventory part (catalog)

| Field | Type | Notes |
|---|---|---|
| Name | text | |
| Part number / SKU / MPN / Manufacturer | text | |
| Category | enum | |
| Default vendor | ref | |
| Quantity on-hand / Reserved / **Available** | number | Reserved = committed to WOs |
| Bin / location | text | |
| Cost (wholesale) / Retail | money | |
| Pricing matrix | ref | |
| Min / Reorder point / Reorder qty | number | Low-stock alert |
| Core (has-core, core value) | flag/money | |
| Taxable / Unit of measure / Barcode | mixed | |
| Status (Active / Reorder) / Total value | enum/money | |

## 20. Invoice (header / summary)

| Field | Type | Notes |
|---|---|---|
| Invoice number | string | |
| Linked WO / RO | ref | |
| Invoice date / Due date | date | |
| Bill-to (customer / fleet account) + customer PO # | ref/text | |
| Status | enum | Unpaid / Partial / Paid / Void |
| Line summary (labor / parts / **sublet** / tires / fees / supplies / discounts) | money | |
| Subtotal / Taxable / Tax / **Total** | money | |
| Deposits & payments applied / **Balance due** | money | |
| Split allocations | list | Customer-pay / Warranty / Insurance portions |
| Terms / disclaimers / warranty statement | text | |
| Cost / GP / Margin | money | Profit |
| Sent (channels) / Printed | flag | |

## 21. Payment

| Field | Type | Notes |
|---|---|---|
| Payment ref / number | string | |
| Linked invoice(s) | ref[] | Apply across invoices (fleet) |
| Date / time | datetime | |
| Amount | money | |
| Type | enum | **Deposit** / Partial / Full / Refund |
| Method | enum | Card present / Card on file / Cash / Check / Online link / **BNPL/Financing** / Account (AR) |
| Processor / Transaction ID / Last4 / Auth code | text | Integrated payments |
| Tip / Surcharge / Change given | money | |
| Received by | ref | |
| Status | enum | Authorized / Captured / Settled / Refunded / Voided |
| Note | text | |

## 22. Service-history record (per vehicle)  ·  23. Settings (config)

| Area | Fields |
|---|---|
| **Service history** | Date · WO# · mileage · jobs performed · parts · sublet · total · advisor · warranty · documents · comeback-of (ref) |
| **Labor rates** | Default rate · rate levels / per-customer · labor matrix |
| **Parts matrix** | Cost tiers → markup % (protect margin) |
| **Sublet matrix** | Cost tiers → markup % |
| **Canned services** | Reusable jobs (labor + parts + price) |
| **Fees / shop supplies** | Name · kind · rate · cap · basis · taxable |
| **Tax** | Parts/labor rates by jurisdiction · custom-tax |
| **Core handling** | Core charge & return rules |
| **Payments** | Methods · processor · surcharge rules |
| **Roles / permissions** | Discount cap · void · price override · close WO · refund · reopen |
| **Numbering / warranty defaults** | WO/PO/Invoice schemes · default warranty periods |
