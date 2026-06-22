# Squibbon — Repair Shop Management Module

**Feature & screen specification for Notion case creation**

Reference product: [Shopmonkey](https://www.shopmonkey.io/) (auto / heavy-duty repair shop management)
Goal: extend Squibbon (currently a TMS) to also run repair shops — estimates, repair orders, inspections, parts, payments, and shop operations.

> **How to use this doc:** each numbered **Module** below is a candidate **Epic** in Notion. The **Features** bullets are candidate **stories**, the **Screens** are candidate **UI tasks**, and **Data entities** feed the backend cases. A suggested phasing roadmap is at the end.

---

## 1. How this fits Squibbon (read first)

Squibbon already gives us most of the platform plumbing. The repair-shop module is mostly **new domain entities + screens** on top of the existing foundation.

**Reuse as-is**
- **Auth, multi-tenancy, roles** — schema-per-tenant (Org ID = tenantId), `usr_roles` / `roles.name`. Repair shops are just tenants/locations; technicians/advisors are users with roles.
- **Invoicing & tax** — there is already an invoice context and a tax province/rate system (`TaxProvinceToggle`, `TaxRateModal`). Repair invoices should extend these rather than start fresh.
- **Customers/Contacts** — an existing contact list/entity can back "Customers"; we add **Vehicles** as a child.
- **List/filter/pagination pattern** — every new list screen (Customers, Orders, Inventory, POs…) follows the existing paginated+filtered list recipe.
- **Shared UI** — `CardBox`, `StatusSelect`, breadcrumbs, theme tokens (purple/teal), MUI components, FullLayout/sidebar nav.

**Key constraint to flag on every backend case**
- `spring.jpa.hibernate.ddl-auto=validate` → **no entity ships without a per-tenant DB migration**. Every new table (vehicles, orders, order_lines, inspections, parts, POs, payments, time entries…) needs a migration applied to each tenant schema. Build/agree the migration mechanism **before** the first entity case.

**New cross-cutting dimension**
- A repair tenant may have **multiple shop locations**. Decide early: location = new tenant schema, or a `location_id` column on shop entities within one tenant. (Recommend `location_id` within tenant for shared catalog + consolidated reporting.)

---

## 2. Epic summary

| # | Module (Epic) | Priority | Phase |
|---|---------------|----------|-------|
| 3 | Customers & Vehicles (CRM) | Must | MVP |
| 4 | Order lifecycle: Estimate → Repair Order → Invoice | Must | MVP |
| 5 | Workflow board (Kanban pipeline) | Must | MVP |
| 6 | Service catalog, labor & pricing matrix | Must | MVP |
| 7 | Payments & billing | Must | MVP |
| 8 | Digital Vehicle Inspections (DVI) | Should | Phase 2 |
| 9 | Parts & inventory | Should | Phase 2 |
| 10 | Purchase orders, vendors & parts procurement | Should | Phase 2 |
| 11 | Scheduling & appointments | Should | Phase 2 |
| 12 | Messaging & customer communications | Should | Phase 2 |
| 13 | Technician/team management & time tracking | Should | Phase 2 |
| 14 | Reporting & analytics | Should | Phase 2/3 |
| 15 | Settings & shop admin | Must (incremental) | MVP→ongoing |
| 16 | Customer-facing portal & mobile tech app | Could | Phase 3 |
| 17 | Integrations (VIN, labor guide, parts, accounting, payments) | Should | Phase 2/3 |

---

## 3. Customers & Vehicles (CRM)

**Purpose:** the customer + their vehicles are the root of every estimate, order, and inspection. A customer can be a person, a business, or a **fleet account**; each owns one or more vehicles with full service history.

**Features**
- Customer profiles: name, contacts (phone/email), address, type (individual / business / fleet).
- Per-customer settings: tax-exempt flag, custom labor rate, payment terms, default discount, fleet/AR account.
- Multiple **vehicles per customer**.
- Vehicle profile: VIN, license plate, year/make/model/engine/trim, color, mileage (with history), unit # (fleet), production date.
- **VIN scan / license-plate lookup** to auto-populate vehicle data (integration — see Module 17).
- Per-vehicle **service history** (all past orders, inspections, recommendations not yet done).
- Customer communication log & internal notes.
- Outstanding balance / open orders at a glance.
- Merge duplicate customers; deactivate.

**Screens**
- Customers list (search by name/phone/plate/VIN, filter by type/balance, paginated).
- Customer detail (tabs: Profile, Vehicles, Order history, Messages, Balance/AR, Notes).
- Add/Edit customer.
- Vehicle detail (specs, service history, declined/recommended work).
- Add/Edit vehicle (with VIN/plate lookup).

**Data entities:** `customer` (extend existing contact), `vehicle`, `customer_note`. Link `vehicle.customer_id`, all orders carry `customer_id` + `vehicle_id`.

---

## 4. Order lifecycle: Estimate → Repair Order → Invoice

**Purpose:** the heart of the app. A single **Order** object moves through statuses — **Estimate → Repair Order (work authorized/in progress) → Invoice (work done/billable)** — and can only be in one status at a time (this matters for reporting).

**Features**
- Create order for a customer + vehicle (capture mileage in/out, concerns/complaints).
- **Service blocks ("jobs")** that group related work; each contains line items.
- Line item types: **labor** (hours × rate), **parts** (qty × price, from inventory or ad-hoc), **sublet**, **fees**, **shop supplies**, **tires**, **discounts**.
- Add work fast from **canned services** (Module 6) and from **parts & labor guide** lookup (Module 17).
- **Pricing/labor matrix** auto-applies markup to protect margins (Module 6).
- Order-level and line-level **discounts**; **taxes** via existing tax-rate system; configurable **fees / shop supplies**.
- **Real-time profitability**: cost, gross profit, margin shown as you build.
- **Customer authorization:** send estimate via text/email; customer **e-signs** to approve, can approve/decline **per job**; instant approval notification; capture authorizer + timestamp.
- **Deposits / partial pre-payment** on an estimate.
- Convert **Estimate → Repair Order → Invoice** (one click); assign technician(s).
- **Audit trail / history** per order: authorizations, status changes, comments, messages, payments.
- Internal notes vs customer-facing notes.
- Print / PDF / email estimate & invoice; professional branded template.
- Duplicate order, recommended-work carryover from prior inspections.

**Screens**
- Order editor (the core screen) — header (customer/vehicle/mileage/status/assigned tech) + tabs: **Services/Line items**, **Inspections**, **Messages**, **History**, **Payments**.
- Add-service / canned-service picker (modal).
- Parts & labor lookup (modal — Module 17).
- Line-item editor (labor/part/fee with cost & margin).
- **Customer authorization view** (customer-facing approve/decline + e-signature).
- Invoice view + take-payment screen.
- Estimate/Invoice **PDF/print template**.
- Orders list (filter by status/date/tech/customer; this is the table backing the board).

**Data entities:** `order` (status enum, type, customer_id, vehicle_id, mileage_in/out, totals), `order_service` (job), `order_line_item` (type, qty, cost, price, labor_hours), `order_authorization` (signature, approver, ts), `order_history`/audit.

---

## 5. Workflow board (Kanban pipeline)

**Purpose:** the shop's daily command center — every open order as a card in a column, moving left→right through the shop.

**Features**
- **Kanban columns** by status/stage (default: Estimate, Repair Order, Invoice; plus shop-custom columns/labels).
- **Drag-and-drop** cards between columns (changes status / stage).
- Job cards show: customer, vehicle, assigned tech, **% complete**, promised/pickup time, labels (VIP, waiting, parts on order…), total.
- Filters: by technician, status, label, date; **"search everything"** bar across orders/customers/vehicles.
- **Automations**: auto-convert RO→Invoice on completion, auto-archive completed jobs, status-change notifications.
- At-a-glance shop view on desktop + mobile.

**Screens**
- Workflow board (Kanban).
- Card quick-view drawer (open order summary without leaving the board).
- Board configuration: columns, labels, automations (→ Settings).

**Data entities:** reuses `order`; add `order_label`, `workflow_column` config, `workflow_automation` config.

---

## 6. Service catalog, labor & pricing matrix

**Purpose:** standardize and speed up estimate building; protect margins automatically.

**Features**
- **Canned services**: reusable pre-built jobs (bundled labor + parts + standard price) — e.g. "Oil change", "Front brake pads".
- Service categories; search; one-click add to an order.
- **Labor rates**: default + per-customer + per-service overrides; effective labor rate.
- **Pricing matrix (parts):** auto-set retail from cost by margin tiers; or use manufacturer suggested retail.
- **Labor matrix:** standardized labor markups.
- Fees & shop-supply rules (flat / %, capped).

**Screens**
- Canned services list + editor.
- Pricing matrix config.
- Labor rates / labor matrix config.
- Fees & shop supplies config.

**Data entities:** `canned_service`, `canned_service_line`, `pricing_matrix`, `labor_rate`, `fee`, `shop_supply_rule`.

---

## 7. Payments & billing

**Purpose:** get paid — in person, online, or on terms — and keep AR clean for fleet accounts.

**Features**
- Take payment on an invoice: **card present, card on file, online link, cash, check**, and **Buy-Now-Pay-Later** (financing).
- **Deposits / partial payments**; split payments; tips (optional).
- **Refunds** / voids.
- Customer-facing **online payment link** (pay from phone).
- Payment history per order & per customer; receipts.
- **Fleet/AR**: invoice on terms, statements, apply payments to multiple invoices, aging.
- Integrated payment processor (see Module 17); reconcile to accounting.

**Screens**
- Take-payment screen (within invoice).
- Customer-facing online payment page.
- Refund/void flow.
- Payments/transactions list.
- Customer AR / statements.

**Data entities:** `payment`, `payment_allocation`, `refund`; extend existing invoice. (Coordinate with existing invoice/tax code.)

---

## 8. Digital Vehicle Inspections (DVI)

**Purpose:** the upsell + trust engine — techs document vehicle condition with media; customers see it and approve recommended work, raising Average Repair Order.

**Features**
- **Inspection templates** (customizable): multi-point, brakes, tires, etc.; grouped items.
- Per item: **status (green/pass, yellow/attention, red/fail)**, notes, measurements (e.g. pad mm, tread depth).
- Attach **photos & videos** per item (captured from phone/tablet).
- Fill inspection against an order; mobile-first capture.
- **Send inspection report** to customer via text/email; branded, easy to read.
- Customer views report → **approves recommended work**, which **adds jobs to the estimate**.
- Track inspections performed & **conversion** to sold work (feeds reporting).

**Screens**
- Inspection templates list + **template builder**.
- Inspection form (fill out, per order) — mobile-optimized.
- Inspection results inside the order.
- **Customer-facing inspection report** (with media + approve buttons).

**Data entities:** `inspection_template`, `inspection_template_item`, `inspection`, `inspection_item` (status/note/measurement), `inspection_media`.

---

## 9. Parts & inventory

**Purpose:** know what's on the shelf, what's committed, and what to reorder.

**Features**
- Parts/inventory items: SKU, **MPN**, manufacturer, description, cost, retail, **qty on hand**, bin/location, min/reorder point.
- **On-hand vs reserved/assigned** to open orders; auto-decrement when added to an order, auto-update on receiving.
- **Low-stock alerts** & reorder list.
- Filters by SKU / MPN / manufacturer.
- Pricing via matrix (Module 6); cost tracking for margin.
- Stock adjustments / counts; (tires: size attributes — optional).

**Screens**
- Inventory list (filter/search).
- Inventory item detail/edit.
- Low-stock / reorder view.
- Stock adjustment / count screen.

**Data entities:** `inventory_item`, `inventory_adjustment`, `inventory_reservation` (link to order_line).

---

## 10. Purchase orders, vendors & parts procurement

**Purpose:** order parts (manually or via integrations), receive them, keep inventory & accounting in sync.

**Features**
- **Vendors/suppliers** directory (terms, contact, account #).
- **Create PO** in seconds — manually, from an order's parts, or from the reorder list.
- **Receive PO** (full/partial) → updates inventory automatically.
- PO status tracking; link parts to a specific job/order; track part status from each work order.
- **Parts ordering integrations** (PartsTech / Nexpart / RepairLink): search & order across suppliers from inside the order (Module 17).
- Send PO to accounting / QuickBooks to avoid double entry.
- Cores / returns (optional, later).

**Screens**
- Vendors list + vendor detail.
- Purchase orders list.
- PO editor (create/edit).
- Receive-PO screen.
- Parts search & order modal (integration).

**Data entities:** `vendor`, `purchase_order`, `purchase_order_line`, `po_receipt`.

---

## 11. Scheduling & appointments

**Purpose:** book work, balance shop capacity, reduce no-shows.

**Features**
- Appointment **calendar**: day/week views, by technician / bay / resource.
- Create appointment linked to customer + vehicle (+ optional pre-built order/services).
- Drag to reschedule; color by status/type.
- **Automated reminders & confirmations** (text/email).
- Capacity / bay management; promised time feeds the workflow board.
- **Online booking** (customer self-schedule) — Phase 3.

**Screens**
- Calendar (day/week, resource lanes).
- Appointment create/edit modal.
- Appointment list.
- Online booking page (customer-facing, later).

**Data entities:** `appointment`, `resource`/`bay`, link to `order`.

---

## 12. Messaging & customer communications

**Purpose:** all customer conversation in one place, tied to the order/vehicle.

**Features**
- **Two-way SMS + email** per order/customer.
- **Templates**: estimate ready, work approved, vehicle done, appointment reminder, review request.
- Unified **inbox / message center**; conversation thread on the order and customer.
- **Automated notifications** on status changes, reminders, and post-service **review requests** (reputation).
- Internal team comments (not customer-visible).

**Screens**
- Message center / inbox.
- Conversation thread (embedded in order & customer detail).
- Message templates & automation config (→ Settings).

**Data entities:** `message` (channel, direction, order_id, customer_id), `message_template`.

---

## 13. Technician / team management & time tracking

**Purpose:** assign work, measure productivity, pay accurately.

**Features**
- Employees/users with **roles & permissions** (reuse Squibbon roles): advisor, technician, manager, admin.
- Assign technician(s) to orders / individual jobs.
- **Time clock**: clock in/out of a **work order** or a **specific line item/service**.
- Billed vs actual hours → **technician efficiency**.
- Technician "my jobs" view (mobile).
- Commissions / pay rules (optional, later).

**Screens**
- Employees/users list + detail.
- Roles & permissions config.
- **Time clock** screen + timesheets.
- Technician dashboard ("my jobs", clock in/out).

**Data entities:** extend existing user/role; `time_entry` (user_id, order_id, line_item_id, start/stop).

---

## 14. Reporting & analytics

**Purpose:** turn shop data into decisions; Shopmonkey ships **3 dozen+ reports**.

**Features / report set (starter list)**
- **End-of-Day report**: gross sales/profit, avg sale, avg order profit & margin, total labor cost, **effective labor rate**.
- **Sales summary & cost trend** (profit by labor efficiency / parts / supplies).
- **All Invoices**, **All Estimates**, **All Payments** (audit/track).
- **Technician / individual performance & productivity** (hours logged, efficiency, inspection conversion).
- **Inspection conversion** (inspections done → work sold).
- **Inventory & parts purchases**, outstanding balances, **commission summaries**.
- KPI **dashboard**: ARO, ELR, gross profit, car count.
- Date-range filters, by-location, **export** (CSV/PDF).

**Screens**
- Reports home / catalog.
- Individual report view (filters, date range, export).
- KPI dashboard (widgets).

**Data entities:** mostly read-model/aggregation over existing tables; consider reporting views.

---

## 15. Settings & shop admin

**Purpose:** configure the shop. Build incrementally — each module above needs its settings section.

**Features / sections**
- Shop profile: name, address, hours, logo, tax registration, currency/locale.
- **Tax rates** (reuse existing province/rate system), fees, shop supplies.
- Labor rates, **pricing & labor matrix** (Module 6).
- **Canned services** catalog (Module 6).
- **Inspection templates** (Module 8).
- **Message templates & automations** (Module 12).
- **Workflow columns, labels, automations** (Module 5).
- **Roles & permissions** (Module 13).
- **Integrations** (Module 17): payments, VIN/labor guide, parts vendors, accounting.
- Multi-location settings.

**Screens**
- Settings hub + each config sub-screen (most are listed under their owning module).

---

## 16. Customer-facing portal & mobile tech app

**Purpose:** the two "outside the back office" surfaces. Can be responsive web before native.

**Customer-facing (no login or magic-link)**
- View & **approve/decline estimate** with e-signature.
- View **inspection report** with photos/videos.
- See **repair status**; **pay online**; book appointment (later).

**Technician / mobile**
- "My jobs" + workflow board; clock in/out.
- Perform inspections, **capture photos/videos**.
- VIN/plate scan; view order details on the floor.

**Screens:** customer approval page, customer inspection page, online payment page, status page; tech mobile board, mobile inspection form, mobile time clock.

---

## 17. Integrations (third-party)

**Purpose:** the data feeds that make estimates fast and back-office clean. Squibbon already isolates integrations in the content-manager service — follow that pattern.

| Integration | Use | Notes |
|-------------|-----|-------|
| **VIN / license-plate decode** | Auto-fill vehicle specs | Provider TBD; powers Customer/Vehicle + order intake |
| **Parts & labor guide (e.g. MOTOR)** | Labor times + parts catalog inside estimate | Licensing required |
| **Parts ordering (PartsTech / Nexpart / RepairLink)** | Search/order across suppliers in-app | PartsTech aggregates 225+ suppliers |
| **Payments processor** | Card present/online, BNPL, payouts | Integrated payments; reconcile to AR |
| **Accounting (QuickBooks)** | Sync customers, sales, POs, inventory | Avoid double entry |
| **CARFAX / vehicle history** | Service history enrichment | Optional |

**Screens:** integrations settings (connect/disconnect, credentials, status), plus the in-context modals (parts search, VIN lookup) that live in their owning modules.

---

## 18. Suggested phasing roadmap

**MVP — "a shop can quote, do the work, and get paid"**
- Customers & Vehicles (3)
- Order lifecycle: Estimate → RO → Invoice (4)
- Workflow board (5)
- Service catalog + labor/pricing matrix (6)
- Payments & billing (7)
- Settings needed to support the above (15)
- Tax/invoice reuse + the per-tenant migration mechanism

**Phase 2 — "raise ARO and run operations"**
- Digital Vehicle Inspections (8)
- Parts & inventory (9)
- Purchase orders & procurement (10)
- Scheduling & appointments (11)
- Messaging & communications (12)
- Technician management & time tracking (13)
- Core reports: End-of-Day, All Invoices/Estimates/Payments (14)
- Key integrations: VIN decode, parts ordering, payments (17)

**Phase 3 — "scale & polish"**
- Customer-facing portal + mobile tech app (16)
- Full reporting suite + dashboards (14)
- Accounting & labor-guide integrations (17)
- Online booking, multi-location consolidation, BNPL, commissions

---

## Sources
- [Shopmonkey — home / overview](https://www.shopmonkey.io/)
- [Auto Repair Software](https://www.shopmonkey.io/auto-repair-software)
- [Workflow](https://www.shopmonkey.io/product/workflow)
- [Estimates](https://www.shopmonkey.io/product/estimates)
- [Inventory](https://www.shopmonkey.io/product/inventory)
- [Team management / time tracking](https://www.shopmonkey.io/product/team-management)
- [Reporting](https://www.shopmonkey.io/product/reporting)
- [Repair Order Status (help)](https://www.shopmonkey.io/help/repair-order-status)
- [Purchase Orders (help)](https://www.shopmonkey.io/help/purchase-orders)
- [Procure Parts with PartsTech (help)](https://support.shopmonkey.io/hc/en-us/articles/38744204666900-Procure-Parts-with-PartsTech)
- [Vehicle Parts & Labor Lookup (help)](https://support.shopmonkey.io/hc/en-us/articles/38743038698388-Vehicle-Parts-Labor-Lookup)
- [End of Day Report (help)](https://www.shopmonkey.io/help/end-of-day-report)
