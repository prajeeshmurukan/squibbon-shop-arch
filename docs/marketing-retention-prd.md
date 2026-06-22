# Squibbon Shop — Marketing, Retention & Reputation PRD

**Type:** Product / functional requirements (requirements capture only — no technical/data/API design)
**Status:** Draft for review
**Applies to:** Squibbon Shop module (extension)

Closes the one real parity gap vs Shopmonkey (marketing) and adds reputation management. Companion: [repair-shop module spec](repair-shop-module-spec.md) · [work-order flow](repair-shop-workorder-usecases.md). Mockups: [docs/mockups/marketing-retention/](mockups/marketing-retention/00-index.md).

> **Why this module:** every adjacent module (estimates, DVI, payments) creates a customer touchpoint. Marketing & retention turn those touchpoints into repeat visits and reviews — campaigns, **service reminders**, **declined-work follow-up**, **review requests**. Shopmonkey ships this; Squibbon must too.

## 1. Overview & goals

| Goal | Measure |
|---|---|
| Bring customers back | Retention rate, visits/customer/yr, win-back conversions |
| Recover lost revenue | Declined-work $ recovered, reminder → booking rate |
| Grow reputation | Review volume, average rating, response rate |
| Do it compliantly | 100% of marketing messages CASL-compliant (consent + unsubscribe) |

## 2. Guiding principles (Canada-specific) ★

These constrain every requirement:
- **CASL** (Canada's Anti-Spam Legislation) governs all commercial electronic messages (CEMs). Every marketing email/SMS needs **consent** (express or implied), **sender identification**, and a working **unsubscribe** honoured ≤10 days.
- **Transactional ≠ marketing.** Estimate-ready / invoice / RO-status messages are transactional and are **not** gated by marketing consent. Reminders, campaigns, review requests **are**.
- **Bilingual (EN/FR).** Templates and customer-facing pages support French (Quebec).
- Squibbon **sends** messages and tracks consent; it is **not** a replacement for the customer's own review platforms (Google) — it integrates with them.

---

## 3. Campaigns (email & SMS)

| ID | Requirement |
|----|-------------|
| CMP-1 | User can create an **email** or **SMS** campaign. |
| CMP-2 | User selects an **audience/segment** (§4) and sees the recipient count (consented only). |
| CMP-3 | User builds content from a **template** (§8) or from scratch, with **merge fields** (customer, vehicle, shop, last service). |
| CMP-4 | Email campaigns include subject, preview text, body, and an auto-inserted **CASL footer** (sender identity + unsubscribe). |
| CMP-5 | SMS campaigns show character/segment count and an auto-inserted **STOP-to-unsubscribe**. |
| CMP-6 | User can **preview** (email render / phone preview) and send a **test** to themselves. |
| CMP-7 | User can **send now** or **schedule** for a date/time. |
| CMP-8 | System excludes recipients who are **unsubscribed**, lack consent, or have hard-bounced. |
| CMP-9 | System tracks per-campaign: sent, delivered, **open**, **click**, unsubscribe, replies, and **bookings/visits attributed**. |
| CMP-10 | User can duplicate, pause, or cancel a scheduled campaign. |
| CMP-11 | Campaigns support EN/FR content variants. |

## 4. Audiences & segments

| ID | Requirement |
|----|-------------|
| SEG-1 | User can build a **segment** from filters: last visit (date range / months since), vehicle (make/model/year), service performed/due, **declined work**, total spend, location, customer type (individual/fleet), tags. |
| SEG-2 | Segment shows a live **audience size** and **consented size**. |
| SEG-3 | User can save, name, and reuse segments. |
| SEG-4 | System provides **starter segments**: lapsed (6/12 mo), due for service, declined work, new customers, top spenders, winter-tire customers. |
| SEG-5 | Segments respect consent &amp; unsubscribe status (§10). |
| SEG-6 | User can preview the customer list in a segment and exclude individuals. |

## 5. Service reminders (time & mileage)

| ID | Requirement |
|----|-------------|
| REM-1 | User can define **reminder rules** triggered by **time** (e.g. every 6 months) or **mileage** (e.g. 8,000 km) since a service. |
| REM-2 | Rules can target a service type (oil change, brake inspection, **seasonal tire swap**, PMVI/safety). |
| REM-3 | Each rule has a channel (email/SMS), template, lead time, and on/off state. |
| REM-4 | System builds a **queue of upcoming reminders** (customer, vehicle, due, channel) and sends on schedule. |
| REM-5 | Reminders link to **online booking** so the customer can self-schedule. |
| REM-6 | Reminders are suppressed if the customer has an upcoming appointment or has unsubscribed. |
| REM-7 | Mileage-based reminders use the latest known/estimated mileage (or telematics, where available). |
| REM-8 | Track reminder → **booking conversion**. |

## 6. Declined-work follow-up

| ID | Requirement |
|----|-------------|
| DEC-1 | System lists **declined/deferred jobs** (from the work-order module) with customer, vehicle, job, declined date, and value. |
| DEC-2 | User can follow up — send a targeted message/estimate or enrol in an automation. |
| DEC-3 | Follow-up content references the specific declined job and offers booking. |
| DEC-4 | Track **declined value followed-up vs recovered** (booked). |
| DEC-5 | Declined jobs auto-expire from the list after a configurable window or once performed. |

## 7. Automations (journeys)

| ID | Requirement |
|----|-------------|
| AUT-1 | User can enable **trigger-based automations** with a trigger → wait → action(channel + template). |
| AUT-2 | Built-in triggers: **service completed**, **declined work**, **N months since last visit**, **new customer (welcome)**, **vehicle/service due**, **birthday/anniversary**, **win-back (lapsed)**. |
| AUT-3 | Built-in journeys: service-completed → **review request**; declined-work → follow-up; lapsed → win-back; due → reminder. |
| AUT-4 | Each automation shows enrolled count, sent, and conversions; can be toggled on/off. |
| AUT-5 | Automations respect consent, quiet hours, and frequency caps (don't over-message). |
| AUT-6 | User can edit trigger timing, audience filters, and content per automation. |

## 8. Templates & content

| ID | Requirement |
|----|-------------|
| TPL-1 | Library of reusable **email** and **SMS** templates by purpose (reminder, follow-up, review request, promo, win-back). |
| TPL-2 | Template editor with merge fields, shop branding (logo/colours), and CASL footer. |
| TPL-3 | EN/FR variants per template. |
| TPL-4 | Starter templates provided; user can clone &amp; edit. |
| TPL-5 | Preview as email and as phone (SMS). |

## 9. Reputation & online reviews ★

| ID | Requirement |
|----|-------------|
| REV-1 | After a completed service, system can **request a review** (email/SMS) automatically (via automation) or manually. |
| REV-2 | Review request routes happy customers to **Google** (and other connected platforms); routes unhappy customers to **private feedback** first. |
| REV-3 | Dashboard shows **average rating**, review **volume**, **by source** (Google/Facebook/internal), and **rating trend**. |
| REV-4 | **Review feed** aggregates reviews from connected sources with customer, stars, text, source, date. |
| REV-5 | User can **respond** to reviews from within Squibbon (where the platform allows). |
| REV-6 | Track **review request → review** conversion and response rate. |
| REV-7 | Surface reviews/rating on the shop's customer-facing pages (optional). |
| REV-8 | Connect/disconnect **Google Business Profile** and other review sources. |

## 10. CASL consent & deliverability ★

| ID | Requirement |
|----|-------------|
| CSL-1 | System records **consent** per customer per channel: **express** (explicit opt-in), **implied** (existing business relationship, time-limited), or **unsubscribed**, with source &amp; date. |
| CSL-2 | Capture consent at intake, online booking, and via opt-in links; maintain a **consent audit trail**. |
| CSL-3 | Every marketing message includes **sender identity + physical address + unsubscribe**; unsubscribe honoured ≤10 days. |
| CSL-4 | Maintain a **suppression/unsubscribe list**; suppress across all marketing sends; never suppress **transactional** messages. |
| CSL-5 | Distinguish **transactional** (RO status, estimate, invoice — no marketing consent needed) from **commercial** messages. |
| CSL-6 | Respect **quiet hours** and provincial considerations; bilingual unsubscribe (FR). |
| CSL-7 | Deliverability: handle hard/soft bounces, spam complaints; suppress hard bounces. |

## 11. Attribution & analytics

| ID | Requirement |
|----|-------------|
| ANL-1 | Marketing dashboard: messages sent, open/click rates, unsubscribes, bookings driven, revenue attributed, reviews collected, average rating, retention rate. |
| ANL-2 | Per-campaign and per-automation performance. |
| ANL-3 | **Declined-work recovered** and **reminder → booking** funnels. |
| ANL-4 | Date-range filter, by-location, export. |

## 12. Integrations

| ID | Requirement |
|----|-------------|
| INT-1 | **Messaging** — reuse the two-way SMS/email channel from the comms module. |
| INT-2 | **Online booking** — reminders/campaigns deep-link to the booking page. |
| INT-3 | **Google Business Profile** (reviews) and optionally Facebook. |
| INT-4 | **Customers/vehicles/work orders** — segments &amp; declined-work feed from the shop data. |
| INT-5 | Email/SMS delivery provider (sending + bounce/complaint webhooks). |

## 13. Roles & permissions

| ID | Requirement |
|----|-------------|
| PERM-1 | Marketing users can create campaigns, segments, templates, automations. |
| PERM-2 | Only authorized users can **send** campaigns and edit consent/CASL settings. |
| PERM-3 | All users can view marketing performance (read-only) per permission. |
| PERM-4 | Consent records and unsubscribes cannot be deleted without elevated permission. |

## 14. Non-goals

| ID | Non-goal |
|----|----------|
| NG-1 | Squibbon does not replace the customer's review platforms — it integrates (no scraping/posting fake reviews). |
| NG-2 | No purchase of contact lists or messaging non-consented contacts. |
| NG-3 | Not a full marketing-automation suite (no landing-page builder, ad management) in v1. |
| NG-4 | Squibbon is not the legal compliance authority — it provides CASL tooling, not legal advice. |
| NG-5 | No storage of third-party platform credentials beyond authorized OAuth tokens. |

## 15. Acceptance criteria

| ID | Criterion |
|----|-----------|
| AC-1 | User can build a segment and create an email/SMS campaign to it. |
| AC-2 | Campaigns auto-include CASL footer/unsubscribe and exclude non-consented/unsubscribed. |
| AC-3 | User can schedule a campaign and see open/click/booking results. |
| AC-4 | User can configure time- and mileage-based service reminders that queue and send. |
| AC-5 | Declined jobs are followed up and recovery is tracked. |
| AC-6 | Automations (service-done → review request, lapsed → win-back) run on triggers. |
| AC-7 | Review requests route happy → Google, unhappy → private feedback; reviews aggregate with rating. |
| AC-8 | Consent (express/implied/unsubscribed) is tracked per customer; transactional messages are never suppressed. |
| AC-9 | Templates and customer-facing pages support EN/FR. |
| AC-10 | Marketing dashboard reports sends, engagement, bookings, reviews, rating &amp; retention. |

## 16. Data entities (high level)

| Entity | Core fields |
|---|---|
| Campaign | name · channel · segment ref · template ref · status · schedule · stats (sent/open/click/booked) · locale |
| Segment | name · filter rules · size · consented size |
| Reminder rule | service type · trigger (time/mileage) · lead time · channel · template · on/off |
| Automation | trigger · wait · action(channel+template) · audience · on/off · stats |
| Template | name · channel · subject · body · merge fields · branding · locale |
| Review | source · stars · text · customer · vehicle/WO · date · response |
| Review request | customer · WO · channel · sent/opened · outcome (review/private) |
| Consent record | customer · channel · type (express/implied/unsubscribed) · source · date · audit |
| Suppression entry | contact · reason (unsubscribe/bounce/complaint) · date |

## 17. Screens (see mockups)

| # | Screen |
|---|--------|
| 01 | Marketing dashboard |
| 02 | Campaigns list |
| 03 | Campaign builder |
| 04 | Audiences & segment builder |
| 05 | Service reminders |
| 06 | Declined-work follow-up |
| 07 | Automations (journeys) |
| 08 | Templates library &amp; editor |
| 09 | Reputation &amp; reviews dashboard |
| 10 | Review request (customer-facing, mobile) |
| 11 | Marketing settings — consent / CASL &amp; channels |
