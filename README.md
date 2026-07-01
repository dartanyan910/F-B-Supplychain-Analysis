# Supply Chain Performance Analysis Report

---
## Table of Content

- [Project Background](#project-background)
- [Data Structure & Initial Checks](#data-structure--initial-checks)
- [Executive Summary](#executive-summary)
  - [Overview of Findings](#overview-of-findings)
- [Insights Deep Dive](#insights-deep-dive)
  - [Category 1: Delivery Reliability](#category-1-delivery-reliability)
  - [Category 2: Operational Risk (Stockout, Returns, and Waste)](#category-2-operational-risk-stockout-returns-and-waste)
  - [Category 3: Supplier & Warehouse Performance](#category-3-supplier--warehouse-performance)
- [Recommendations](#recommendations)
- [Assumptions and Caveats](#assumptions-and-caveats)

---

## Project Background

This report presents the findings of a comprehensive supply chain performance analysis conducted from the perspective of a data analyst embedded within a mid-sized European food distribution company operating across the Baltic and Central European regions.

The company operates a **B2B distribution model**, supplying food and beverage products across four sales channels (Retail, HoReCa (Hotels, Restaurants & Catering), Distributor, and E-commerce) serving 80 customers across multiple countries. The business manages a network of 5 regional warehouses (Poland, Latvia, Lithuania, Estonia, Germany) and sources products from 8 suppliers spanning perishable and ambient categories including Frozen, Dairy, Bakery, Beverages, and Snacks.

Insights and recommendations are provided on the following key areas:

- **Category 1: Delivery Reliability.** OTIF trends, late delivery patterns, and on-time performance by supplier and product category.
- **Category 2: Operational Risk.** Stockout, waste, and quality issue distribution across the supply network.
- **Category 3: Supplier & Warehouse Performance.** Comparative efficiency and root-cause diagnosis across operational dimensions, including supplier performance segmented by customer priority group.

An interactive dashboard used to report and explore supply chain trends can be found here [link].

---

## Data Structure & Initial Checks

The company's main dataset consists of the following tables, with a combined total of approximately **9,971 records**:

| Table | Description |
|---|---|
| `Fact_Orders` | Core transaction table capturing order, shipping, and financial data per order line |
| `Inventory_Snapshots` | Monthly per-product per-warehouse stock levels, stockouts, and expired quantities |
| `Dim_Customer` | Customer master data including segment, region, and priority tier |
| `Dim_Product` | Product catalog with category, storage type, shelf life, and standard cost |
| `Dim_Supplier` | Supplier profiles including risk tier, planned lead time, reliability, and defect rate |
| `Dim_Warehouse` | Warehouse attributes including region, capacity, and cold storage flag |
| `Dim_Channel` | Channel dimension repeated per transaction |
| `Dim_Date` | Date dimension covering the full 2-year analysis window |

|<img width="919" height="688" alt="image" src="https://github.com/user-attachments/assets/c3a12259-e5ae-4cf9-bbf0-cecb0317fe20" />|
|:---:|
|**Figure 1**: Entity Relationship Diagram|

---

## Executive Summary
 
### Overview of Findings
 
Across 2,200 orders and a 24-month observation window, the business is in a financially healthy position. Gross Profit Margin stands at **34.4%**, well above the food distribution industry average of approximately 20%, and has remained broadly stable throughout the period. Financial leakages from returns and waste are negligible in scale and do not require intervention at this time.
 
The primary concerns are operational, not financial. Two issues stand out. First, overall OTIF of **92.5%** falls below the 95% industry benchmark, with the underperformance concentrated in two High-Risk suppliers that are actively degrading service to the company's most strategically important customers. Second, warehouse inventory management shows systemic weaknesses in waste and expired inventory, particularly at Vilnius DC and Riga Central DC, driven by a structural mismatch between product shelf life and current inventory velocity in the Dairy and Bakery categories. Left unaddressed, both issues carry meaningful relationship and reputational risk even if their immediate financial footprint appears contained.
 
**[Visualization: 24-month OTIF trend line with 95% target threshold; supplier OTIF heatmap by customer priority group; warehouse expired inventory and waste by facility]**
 
---
 
## Insights Deep Dive
 
---
 
### Category 1: Delivery Reliability
 
> *How reliable is the current delivery process, based on OTIF rate and late delivery status?*
 
**Insight 1: OTIF is declining, not stable.**
The aggregate OTIF of 92.5% understates the severity of recent performance. In the first half of 2024, system OTIF consistently exceeded 95%, reaching a peak of 98.8% in April 2024. By Q2–Q3 2025, the quarterly average fell to 88.9–90.0%. May 2025 recorded the single worst monthly OTIF at **82.9%** with 15 late orders in that month alone, approximately 3× the monthly average of ~5 late orders. This sustained downward trajectory signals a structural deterioration rather than isolated incidents.
 
**Insight 2: Supplier and category on-time performance show sharp divergence.**
S005 (Eastern Frozen) records an on-time rate of **81.9%** (the lowest among all suppliers) and collapsed to **53.8% OTIF in Q2 2025**. S008 (Value Foods) follows at 85.3%. In contrast, S004 (Global Snacks) and S001 (Baltic Fresh) sustain 98.4% and 96.7% respectively. At the category level, Frozen products (primarily S005) have the lowest on-time rate at 91.0%, while Beverages lead at 94.3%. Across channels, Distributor records the lowest OTIF (91.97%) and E-commerce the highest (93.12%), though channel-level differences are narrower than supplier-level differences.
 
**Insight 3: Riga Central DC is the dominant geographic bottleneck.**
Across all warehouses, Riga Central DC records the lowest OTIF at **89.8%** on 490 orders, the second-highest order volume in the network. The same suppliers (S005 and S008) who underperform system-wide deteriorate *further* at Riga: S008 at Riga reaches only 75.9% OTIF, versus 94.0% at Berlin Export Hub for the same supplier. This 18 percentage-point gap with identical supplier input isolates Riga's internal operations (throughput capacity, handoff protocols, and peak staffing) as an independent failure source rather than a supplier dependency.
 
**[Visualization: Monthly OTIF trend line (Jan 2024–Dec 2025) with 95% target threshold; bar chart of on-time rate by supplier ranked low-to-high; category and channel on-time rate comparison]**
 
---
 
### Category 2: Operational Risk (Stockout, Returns, and Waste)
 
> *Where are stockout, returns, and waste/loss occurring?*
 
**Insight 1: Stockout is concentrated at S008, driven by inventory planning failure rather than logistics.**
System-wide stockout rate is 3.4% (75 orders). S008 (Value Foods) records a stockout rate of **7.9%** (2.3× the system average) across its 442 orders (20% of total order volume). Critically, inventory snapshot data reveals that **69.9% of monthly observations** for S008's product lines show closing stock below the reorder point, versus a system average of approximately 52%. This chronic understock pattern is consistent with a demand planning misalignment: order cycles are not recalibrated to actual consumption velocity. The problem is not that S008 ships late; the downstream inventory buffer is structurally insufficient.
 
**Insight 2: Dairy expired inventory (10,900 units cost) is the single largest financial leakage and is invisible to standard reporting.**
Total expired inventory cost across the network is approximately **12,695 cost units**, with Dairy accounting for 86% of this figure (10,900). Bakery contributes an additional 1,750. This loss does not appear in `Fact_Orders`; it is captured only in `Inventory_Snapshots.ExpiredQty`. At the warehouse level, Vilnius DC (2,982) and Warsaw Hub (2,863) carry the heaviest expired cost burden, followed by Tallinn Cross-Dock (2,394) and Berlin Export Hub (2,237). The implication is that standard order-based KPI dashboards systematically understate the true cost of operational failure by omitting inventory write-offs.
 
**Insight 3: Bakery dominates waste at the order level; S005's cold chain issue generates quality failures, not waste.**
Among the 606 total waste units recorded in `Fact_Orders.WasteQty`, Bakery contributes 302 (50%) and Dairy 207 (34%). Frozen products, despite being supplied by the highest-risk supplier (S005), contribute only 75 waste units, suggesting that S005's primary failure mode is **cold chain temperature deviation** (quality rejection at delivery, flagged as `QualityIssueFlag = 1` at 5.1%) rather than physical product spoilage at warehouse. Notably, Berlin Export Hub (which records the best OTIF in the network at 95.5%) also carries the highest order-level waste quantity at 212 units, a counterintuitive finding explained by **deliberate overstock buffering** to protect fill rates, with the carrying cost absorbed as waste.
 
**Insight 4: Quality issues and expired inventory are the operational risk items that require immediate action; returns and waste are secondary.**
Returns are financially negligible: total `ReturnQty` is 460 units with a net GP loss of approximately 364 cost units, under 0.1% of total revenue. This item does not warrant dedicated intervention. Quality issue orders (61 orders, 2.8% rate) present a more meaningful signal, with S005 recording a rate of 5.1%, nearly double the system average, concentrated in Q3/Q4 months consistent with seasonal cold chain stress on Frozen products. Expired inventory, as noted in Insight 2, is the single largest absolute loss at 12,695 cost units and is structurally driven by the mismatch between short shelf life (Dairy: 32 days, Bakery: 15 days) and slow inventory turnover velocity at Vilnius DC and Warsaw Hub. The stockout picture cannot be fully assessed with the current dataset: `Fact_Orders` does not include a fulfilled quantity column, which means it is not possible to determine how much of each stockout order was actually shipped versus withheld. The 75 stockout-flagged orders (3.4% rate) confirm the problem exists but do not quantify its magnitude. This is flagged as a data gap requiring resolution before stockout impact can be properly sized.
 
**[Visualization: Pareto chart of GP-impacted loss types (Late delivery, Quality, Stockout, Expired Inventory, Waste, Returns); stacked bar chart of waste vs. stockout vs. quality issues by product category]**
 
---
 
### Category 3: Supplier & Warehouse Performance
 
> *Which suppliers, warehouses, or distribution channels are underperforming?*
 
**Insight 1: S005 and S008 have fundamentally different root causes and require different interventions.**
Both suppliers are rated **High Risk** and together account for 580 orders (26.4% of total volume). However, their failure modes diverge completely:
 
- **S005 (Eastern Frozen, Warsaw):** Failure is quality-driven. Quality issue rate 5.1%, cold storage supplier for Frozen products only, planned lead time 11 days, reliability declared at 83%. The Q2 2025 collapse to 53.8% OTIF (from 87.5% in Q4 2024) points to a specific cold chain event between April and June 2025 requiring direct investigation of temperature logs and carrier handoff records.
- **S008 (Value Foods, Prague):** Failure is inventory-planning-driven. Stockout rate 7.9%, planned lead time 12 days, reliability declared at 80%, defect rate 3.5%. S008 supplies ambient Snacks and Beverages (categories with no cold chain dependency), meaning its underperformance is unrelated to storage conditions and instead reflects misaligned replenishment cycles and insufficient safety stock.
**Insight 2: Mediterranean Foods (S002) presents a hidden financial risk despite strong operational performance.**
S002 records the third-highest OTIF (95.4%), low stockout (0.6%), and moderate quality issues (2.3%), operationally sound by all delivery metrics. However, its GPM of **32.8%** is the lowest in the supplier portfolio, 1.6 percentage points below the system average and 3.6 points below High-Risk S008 (35.0%). This GPM paradox (best-in-class logistics paired with worst-in-class margins) suggests a pricing or contract structure issue rather than operational underperformance. A contract review focusing on unit cost renegotiation would likely yield greater financial return for S002 than any operational improvement program.
 
**Insight 3: Riga Central DC underperforms on every dimension and serves as a risk amplifier for already-weak suppliers.**
Riga records OTIF of 89.8% (490 orders), expired cost of 2,219, order-level waste of 169 units, and the second-highest stockout rate (3.5%) among warehouses. When the same suppliers are routed through different warehouses, the OTIF differential is stark: S008 achieves 94.0% OTIF at Berlin and only 75.9% at Riga, an 18 pp gap. Similarly, S005 performs worst at Riga (75.0% OTIF, 28 orders) versus 84.4% at Berlin (32 orders). Riga is not merely a low performer; it is a **risk multiplier** that degrades otherwise acceptable supplier performance.
 
**Insight 4: Supplier performance deteriorates significantly when serving Strategic customers, exposing the company's most valuable relationships to the highest operational risk.**
When OTIF is segmented by customer priority tier (Strategic, Standard, Low), a critical pattern emerges. S005 (Eastern Frozen) delivers to Strategic-tier customers at only **73.3% OTIF** across 15 orders, and S008 (Value Foods) at **88.9% OTIF** across 36 orders. Both figures fall well below what a strategic customer relationship should tolerate, and both suppliers are already classified as High Risk. In contrast, four suppliers (S001 Baltic Fresh, S002 Mediterranean Foods, S004 Global Snacks, and S007 Premium Beverage) achieve 100% OTIF on all Strategic customer orders, demonstrating that reliable performance at this tier is achievable within the existing network. The supplier landscape can therefore be divided into three groups: **Replace** (S005, S008 — High Risk, low OTIF across all segments), **Watch** (S003 Nordic Dairy, S006 Local Bakery — Medium Risk, OTIF between 91% and 97% depending on tier), and **Retain** (S001, S002, S004, S007 — consistently strong OTIF including on Strategic orders).
 
**[Visualization: Supplier performance heatmap table (OTIF, Stockout, Quality, GPM, Waste, Risk Tier); supplier OTIF heatmap by customer priority group (Strategic / Standard / Low); warehouse waste and expired inventory comparison]**
 
---
 
---
 
## Recommendations
 
Based on the insights and findings above, we recommend the supply chain operations, procurement, and inventory management teams consider the following actions:
 
**1. Begin supplier replacement planning for S005 (Eastern Frozen) and S008 (Value Foods).**
Both suppliers are rated High Risk and are currently delivering to Strategic customers at OTIF levels of 73.3% and 88.9% respectively — performance that poses direct relationship risk to the company's most valuable accounts. S005's failure mode is cold chain quality (5.1% quality issue rate, with a collapse to 53.8% OTIF in Q2 2025 pointing to a specific operational event requiring investigation). S008's failure mode is inventory planning (7.9% stockout rate, with 69.9% of monthly inventory snapshots falling below reorder point). Given the divergence in root causes and the concentration of risk on Strategic customers, the recommended path is to initiate parallel sourcing for both suppliers' product categories and qualify at least one alternative supplier in each case. In the interim, S005 orders destined for Strategic accounts should be capped and rerouted through channels with more flexible delivery windows, and S008's safety stock at Riga Central DC should be increased by 20 to 30% immediately while a replacement sourcing process runs in parallel.
 
**2. Restructure Dairy and Bakery inventory ordering at Vilnius DC and Warsaw Hub to address expired inventory losses.**
Expired inventory costs of 12,695 cost units are entirely concentrated in Dairy (10,900) and Bakery (1,750), driven by a structural mismatch: Dairy carries an average shelf life of 32 days and Bakery 15 days, while both categories record among the highest Days Sales of Inventory in the network. The fix is not demand generation but cycle adjustment. Reduce order batch sizes for Dairy and Bakery SKUs at Vilnius DC and Warsaw Hub by 15 to 20%, increase order frequency to match actual consumption velocity, and set expiry-based picking rules to enforce FEFO (First Expired, First Out) discipline. This intervention does not require supplier changes and can be implemented within 30 days. Expected reduction in expired inventory cost: 3,000 to 6,000 in the first 12 months.
 
**3. Place S003 (Nordic Dairy) and S006 (Local Bakery) on a structured performance watch program.**
Both suppliers currently sit in the mid-range of OTIF performance (S003 at 92.3%, S006 at 94.9% overall) but show sufficient variability across customer segments to warrant monitoring. S003 records a quality issue rate of 10.3% on Strategic customer orders, which is elevated. S006 records 91.4% OTIF on Strategic orders, which is borderline. Neither requires immediate replacement action, but both should be included in the monthly supplier KPI scorecard with explicit escalation triggers at 90% OTIF or 3% quality issue rate sustained for two consecutive months.
 
**4. Establish a monthly supplier KPI scorecard segmented by customer priority tier.**
Current reporting does not surface the interaction between supplier performance and customer priority, which is where the most significant relationship risk is concentrated. The scorecard should track OTIF, stockout rate, and quality issue rate for each supplier broken down by Strategic, Standard, and Low customer segments, with automated alerts when any supplier falls below 90% OTIF on Strategic accounts. This requires joining `Fact_Orders` with `Dim_Customer[Priority]` and `Dim_Supplier[SupplierName]` — a straightforward data model extension. Expected build time: 10 analyst-days.
 
**5. Obtain fulfilled quantity data at the order level to enable stockout impact quantification.**
The current dataset flags 75 orders (3.4%) as stockout-affected via `Fact_Orders[StockoutFlag]`, but the absence of a `ShippedQty` or `FulfilledQty` column means it is not possible to determine how much of the ordered quantity was actually delivered versus withheld. This prevents any reliable estimation of lost revenue from stockout events. The recommended action is to request the addition of fulfilled quantity at the order line level from the source system in the next data extract. Until this column is available, stockout financial impact will remain an unquantified risk in this analysis.
 
---
 
## Assumptions and Caveats
 
Throughout the analysis, multiple assumptions were made to manage challenges with the data. These assumptions and caveats are noted below:
 
**Expired Inventory Cost Estimation:** The expired inventory cost figure (12,695 units) is derived by joining `Inventory_Snapshots.ExpiredQty` with `Dim_Product.StandardCost`. This uses standard cost as a proxy for actual inventory carrying value. If actual landed cost differs materially from standard cost for specific SKUs, particularly for imported Dairy or Frozen products where logistics costs are embedded, the expired cost figure may be over- or under-stated. No actual write-off records were available in the dataset to validate this calculation.
 
**Inventory Cycle Adjustment Savings Estimate:** The projected saving of 3,000 to 6,000 from reducing expired inventory at Vilnius DC and Warsaw Hub assumes that order frequency can be increased without triggering minimum order quantity penalties or significant freight cost increases from smaller, more frequent deliveries. If either constraint applies, the net saving will be lower and a separate cost-benefit analysis covering logistics surcharges should be conducted before implementation.
 
**Stockout Quantification Limitation:** `Fact_Orders[StockoutFlag]` identifies 75 orders where a stockout condition was present, but the dataset does not include a fulfilled quantity column. As a result, it is not possible to determine how much of the ordered quantity was shipped versus withheld in each case, nor to estimate the revenue impact of the shortfall. All 75 stockout-flagged orders carry non-zero revenue values, confirming partial fulfillment occurred, but the unfulfilled portion remains unobservable. This is an explicit data gap acknowledged in the recommendations section. Until a `ShippedQty` or `FulfilledQty` field is available at the order line level, any stockout revenue estimate would require assumptions that cannot be validated against actual operational records.
 
**Currency and Unit Normalization:** All financial figures (Revenue, COGS, GrossProfit) are reported in the native reporting currency of the dataset without conversion. The dataset does not specify the currency unit; figures are presented as absolute values. Cross-border transactions (e.g., Czech supplier S008, German warehouse W005) may involve multi-currency dynamics not captured in the data.
 
**Analysis Period:** The dataset covers January 2024 through December 2025 (24 months, 731 date dimension rows). No partial-month exclusions were required as the dataset begins at the first of January 2024. Seasonal effects (IsDecemberPeak, IsSummer flags in Dim_Date) were noted as potential confounders in trend analysis but were not modeled as explicit controls in this report.
 
**Supplier Risk Tier Assignment:** RiskTier classifications (High / Medium / Low) in `Dim_Supplier` are provided as pre-existing categorical labels in the source data. The methodology underlying these assignments is not documented in the dataset. Where RiskTier and observed operational performance diverge (for example, Mediterranean Foods with Medium Risk classification, lowest GPM, and strong OTIF), the observed data is used as the primary analytical basis rather than the categorical label.
 
---
