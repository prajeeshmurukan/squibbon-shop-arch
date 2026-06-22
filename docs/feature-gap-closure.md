# Feature-gap closure — second-pass competitive scan

After reaching Shopmonkey parity (orders, DVI, parts/PO, payments, scheduling, messaging, team/pay, marketing/reputation) and adding the Canadian moat (DriveON/SAAQ compliance, fleet maintenance), a second scan went **beyond Shopmonkey** into the deeper and vertical players — **Tekmetric, Shop-Ware, Mitchell 1, Fullbay**, tire-specific systems and Canadian POS — plus **Canadian repair law**. This doc records the gaps that scan surfaced (G1–G8) and maps each to the PRD + mockups that now close it.

See also the original parity/differentiator analysis: [squibbon-feature-gaps-and-differentiators.md](squibbon-feature-gaps-and-differentiators.md) (Shopmonkey parity + Canadian/AI/operational differentiators).

## Gaps → delivered specs

| Gap | What was missing | PRD | Mockups | Status |
|-----|------------------|-----|---------|--------|
| **G1** | Licensed labor/repair-data wired into the estimate (ALLDATA / Mitchell ProDemand / Identifix / MOTOR), VIN/plate decode, open recalls, DTC/scan intake | [Estimating Intelligence & Repair Data](estimating-repair-data-prd.md) | [3 screens](mockups/estimating-repair-data/00-index.md) | ✅ specced |
| **G2** | ON/QC repair-transaction compliance — written estimate, **10% (ON) / 0% (QC) estimate cap**, replaced-parts return, QC statutory 3mo/5,000 km warranty | [Canadian Repair-Transaction Compliance](repair-transaction-compliance-prd.md) | [4 screens](mockups/repair-transaction-compliance/00-index.md) | ✅ specced |
| **G4** | Warranty register + claims; comeback/QC tracking | [Warranty, Cores & Comebacks](warranty-cores-comebacks-prd.md) | [3 screens](mockups/warranty-cores-comebacks/00-index.md) | ✅ specced |
| **G6** | Core charges + vendor RMA/credit reconciliation | (same PRD — §Cores & vendor returns) | (same set) | ✅ specced |
| **G3** | Heavy-duty / fleet mobile service: roadside dispatch, unit/component tracking, DVIR→RO, fleet portal, national-account statement billing | [Fleet & Heavy-Duty Service](fleet-heavy-duty-prd.md) | [5 screens](mockups/fleet-heavy-duty/00-index.md) | ✅ specced |
| **G7** | Accounting depth — QuickBooks/Xero/**Sage**, AR aging/statements, deposit reconciliation | (same PRD — §National-account & statement billing) | (same set) | ✅ specced |
| **G5** | Tire retail: fitment + distributor ordering, TPMS, DOT registration/recall, road-hazard, tire hotel | [Tire Retail, TPMS & Storage](tire-retail-tpms-prd.md) | [3 screens](mockups/tire-retail-tpms/00-index.md) | ✅ specced |
| **G8** | Forward-looking: ADAS calibration + proof docs, EV high-voltage service safety, equipment/calibration management | [Advanced Services — ADAS, EV & Equipment](advanced-services-adas-ev-prd.md) | [2 screens](mockups/advanced-services/00-index.md) | ✅ specced |

**Headline gaps:** G1 (technician-credibility / labor data) and G2 (a second Canadian compliance moat, software-enforceable, that no US tool has). G3 monetizes the fleet base Squibbon already owns on the TMS side.

**Out of scope (separate vertical):** collision / body-shop estimating (CCC / Audatex).

## Sources
- [Tekmetric — ALLDATA Labor Guide Integration](https://support.tekmetric.com/hc/en-us/articles/25868623867927-ALLDATA-Labor-Guide-Integration) · [ALLDATA × Tekmetric](https://www.alldata.com/us/en/tekmetric) · [Tekmetric integrations](https://www.tekmetric.com/integrations)
- [Ontario — Car repair shops: your rights (10% rule)](https://www.ontario.ca/page/car-repair-shops-your-rights) · [Steps to Justice — repair shop rules](https://stepstojustice.ca/steps/debt-and-consumer-rights/learn-about-rules-repair-shop-must-follow/) · [CLEO — motor vehicle repairs](https://www.cleo.on.ca/en/publications/vehiclerep/all)
- [Quebec OPC — Vehicle Repair](https://www.opc.gouv.qc.ca/en/consumer/good-service/vehicle/repairing/advice) · [Éducaloi — Car and Motorcycle Repairs](https://educaloi.qc.ca/en/capsules/car-and-motorcycle-repairs/)
- [Fullbay](https://www.fullbay.com/) · [Heavy-Duty Repair Shop Software 2026](https://heavydutyjournal.com/heavy-duty-repair-shop-software-2026/)
- [Tekmetric Tire Suite](https://www.tekmetric.com/feature/tire-suite) · [EliteTeQ Tire POS (Canada)](https://eliteteqpos.com/ca/industries/tire-shops/) · [Tiremetrix — TPMS & registration](https://www.tiremetrix.com/)
- [5 New ADAS Technologies Coming in 2026](https://www.bodyshopbusiness.com/5-new-adas-technologies-coming-in-2026/) · [2026 aftermarket trends (S&P Global)](https://www.spglobal.com/automotive-insights/en/blogs/2026/04/automotive-aftermarket-industry-trends)
