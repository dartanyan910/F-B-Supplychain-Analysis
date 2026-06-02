# F&B Supply Chain Performance Analysis Report
---

## Project Background

This report presents the findings of a comprehensive supply chain performance analysis conducted from the perspective of a data analyst embedded within a mid-sized European food distribution company operating across the Baltic and Central European regions.

The company operates a **B2B distribution model**, supplying food and beverage products across four sales channels (Retail, HoReCa (Hotels, Restaurants & Catering), Distributor, and E-commerce) serving 80 customers across multiple countries. The business manages a network of 5 regional warehouses (Poland, Latvia, Lithuania, Estonia, Germany) and sources products from 8 suppliers spanning perishable and ambient categories including Frozen, Dairy, Bakery, Beverages, and Snacks.

Key business metrics tracked across the 2024–2025 period include:

- **Total Revenue:** 605,600 (reporting currency units)
- **Gross Profit Margin (GPM):** 34.4%
- **OTIF (On-Time In-Full) Rate:** 92.5% across 2,200 orders

Insights and recommendations are provided on the following key areas:

- **Category 1: Delivery Reliability.** OTIF trends, late delivery patterns, and on-time performance by supplier and product category.
- **Category 2: Operational Risk.** Stockout, waste, returns, and quality issue distribution across the supply network.
- **Category 3: Supplier & Warehouse Performance.** Comparative efficiency and root-cause diagnosis across operational dimensions.
- **Category 4: Financial Impact of Operational Failures.** Revenue and gross profit effects of operational issues, and prioritized improvement opportunities.

The Python code used to inspect and clean the data for this analysis can be found here [link].
Targeted SQL queries regarding various business questions can be found here [link].
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

|**Figure 1: Entity Relationship Diagram**|
|:---:|
|<img width="678" height="637" alt="image" src="https://github.com/user-attachments/assets/91ba9109-7723-4046-a9f3-0606a6d96f8c" />|

Initial data quality checks confirmed no critical integrity issues. All foreign keys in `Fact_Orders` resolve cleanly to their respective dimension tables. Financial metrics (`Revenue`, `COGS`, `GrossProfit`) were verified as internally consistent: `GrossProfit = Revenue − COGS` holds across all 2,200 rows. Operational flags (`OTIF_Flag`, `StockoutFlag`, `QualityIssueFlag`) are binary and complete with no nulls.

---

## Executive Summary

### Overview of Findings

Across 2,200 orders and a 24-month observation window, the supply chain achieves a headline OTIF rate of **92.5%** and a Gross Profit Margin of **34.4%**, both within acceptable ranges. However, these aggregate figures mask a deteriorating trend: OTIF declined from a peak of ~98.8% in April 2024 to a low of **82.9% in May 2025**, driven disproportionately by two High-Risk suppliers (S005 and S008) and concentrated warehouse failures at **Riga Central DC**. Simultaneously, **expired inventory costs of 12,695 units** (the single largest financial leakage) remain invisible to standard order-level reporting, while the Retail channel silently erodes margin at 33.1% GPM versus HoReCa's 35.9%. Correcting these four structural issues (S005/S008 OTIF, Retail margin, expired inventory, Riga DC) represents a **+23.4K gross profit opportunity, equivalent to +11.2% of current GP**.

**[Visualization: 24-month OTIF trend line with 95% target threshold + quarterly GPM vs. problem rate dual-axis chart]**

---

## Insights Deep Dive

---

### Category 1: Delivery Reliability

> *How reliable is the current delivery process, based on OTIF rate and late delivery status?*

**Insight 1: OTIF is declining, not stable.**
The aggregate OTIF of 92.5% understates the severity of recent performance. In the first half of 2024, system OTIF consistently exceeded 95%, reaching a peak of 98.8% in April 2024. By Q2–Q3 2025, the quarterly average fell to 88.9–90.0%. May 2025 recorded the single worst monthly OTIF at **82.9%** with 15 late orders in that month alone, approximately 3× the monthly average of ~5 late orders. This sustained downward trajectory signals a structural deterioration rather than isolated incidents.

**Insight 2: Late deliveries are concentrated in short delays, pointing to a scheduling problem rather than a logistics breakdown.**
Of the 164 late orders recorded over the full period, **86 (52%)** were delayed by exactly 1 day, and **40 (24%)** by 2 days. This means that 93% of all late orders are within a 2-day delay band. The average late delay across all instances is 1.9 days. This distribution is characteristic of last-mile scheduling failures (insufficient buffer time in order promising or route optimization gaps) rather than systemic supply disruption. The implication is that operational fixes (scheduling discipline, improved carrier SLAs) could eliminate the majority of late events without major structural investment.

**Insight 3: Supplier and category on-time performance show sharp divergence.**
S005 (Eastern Frozen) records an on-time rate of **81.9%** (the lowest among all suppliers) and collapsed to **53.8% OTIF in Q2 2025**. S008 (Value Foods) follows at 85.3%. In contrast, S004 (Global Snacks) and S001 (Baltic Fresh) sustain 98.4% and 96.7% respectively. At the category level, Frozen products (primarily S005) have the lowest on-time rate at 91.0%, while Beverages lead at 94.3%. Across channels, Distributor records the lowest OTIF (91.97%) and E-commerce the highest (93.12%), though channel-level differences are narrower than supplier-level differences.

**Insight 4: Riga Central DC is the dominant geographic bottleneck.**
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

**Insight 4: Returns represent a minor financial risk; quality issues are the true margin threat.**
Total `ReturnQty` is 460 units, with a net GP loss from returns of approximately 364 cost units, less than 0.2% of total gross profit. This makes returns a low-priority operational issue in financial terms. However, quality issue orders (61 orders, 2.8% rate) carry a GPM of 33.9% versus 34.5% for clean orders, a gap that compounds across high-volume suppliers. S005 quality issue rate (5.1%) is nearly double the system average. E-commerce channel records the lowest quality issue rate (1.7%), while Dairy and Frozen categories are above average at 3.2% and quality-issue distribution is highest in Q3/Q4 months, potentially linked to seasonal temperature stress on cold chain logistics.

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

**Insight 4: The Retail channel erodes margin more than any other channel, and the gap is widening.**
Retail accounts for 939 orders (42.7% of total volume) and 253,000 in revenue, the largest channel by both volume and revenue. However, its GPM of **33.1%** is the lowest across all four channels, 2.84 percentage points below HoReCa's 35.9%. Problem rate in Retail is 20%, the highest of any channel. The GPM penalty combined with the revenue scale creates a disproportionate financial drag: closing the Retail-HoReCa margin gap entirely would generate an estimated **+7,179 in additional gross profit**, the single largest addressable improvement opportunity in the dataset. The Retail channel also records the highest return quantity (200 units) and above-average stockout rates (3.4%).

**[Visualization: Supplier performance heatmap table (OTIF, Stockout, Quality, GPM, Waste, Risk Tier); side-by-side warehouse OTIF vs. waste bar chart; channel GPM ranked bar chart with GP-at-risk overlay]**

---

### Category 4: Financial Impact of Operational Failures

> *How are operational issues impacting revenue and gross profit?*

**Insight 1: The GPM gap between clean and problem orders is small in absolute terms but structurally widening over time.**
Orders with at least one operational issue (late, stockout, quality, waste, or return) represent 19.4% of all orders (426 of 2,200). These problem orders carry a GPM of **34.1%** versus **34.5%** for clean orders, a delta of 0.42 percentage points. While this seems modest, the trend is concerning: the proportion of problem orders has grown from 16.1% in Q1 2024 to a peak of **24.2% in Q3 2025**, a 50% relative increase in 18 months. If this trajectory continues and the problem rate reaches 30%, the margin drag will compound materially against a growing base.

**Insight 2: Late delivery creates the largest revenue exposure; expired inventory creates the largest invisible cash loss.**
By revenue exposure, late delivery affects the most orders (164) and the highest aggregate revenue pool (47,415 in associated revenue). However, by direct profit destruction (cash that is entirely lost with no offsetting revenue), **expired inventory at 12,695 cost units** is the leading source. This figure does not appear in any standard order-level P&L and is only recoverable through an inventory management intervention. The full loss hierarchy is: Late delivery GP impact (~2,200), Quality issues (~1,600), Stockout (~1,500), Expired inventory (~12,695 absolute cost, not margin delta), Order-level waste (878), and Returns (364).

**Insight 3: The OTIF-GPM relationship reveals a counterintuitive risk structure that masks supplier risk in profitability reports.**
A scatter analysis of OTIF versus GPM by supplier produces a non-linear, segmented pattern. High-Risk suppliers S005 and S008 both record GPM of **35.0%**, above the system average of 34.4% and significantly above Mediterranean Foods (95.4% OTIF, 32.8% GPM). This means that standard supplier profitability reports will rank S005 and S008 as financially attractive partners despite their operational fragility. The explanation is that these suppliers compensate for operational underperformance through lower input costs, effectively transferring their operational risk discount to the buyer's margin. This arrangement is financially stable only as long as service failures remain below a threshold where lost sales, SLA penalties, and customer churn costs exceed the cost advantage, a threshold that current trend lines suggest is approaching.

**Insight 4: Four targeted interventions represent a combined +23,400 gross profit opportunity (+11.2% of current GP).**
Quantified improvement scenarios, modeled against actual order and inventory data:

| Scenario | Mechanism | GP Uplift Estimate |
|---|---|---|
| S1: Fix S005 + S008 OTIF to 95% | Reduce problem orders from 26.4% to system average | +9,883 |
| S2: Lift Retail GPM to HoReCa level | Reduce problem rate + improve product/discount mix | +7,179 |
| S3: Reduce expired inventory by 50% | Improve demand planning for Dairy and Bakery SKUs | +6,348 |
| S4: Improve Riga DC to Berlin OTIF standard | Process and capacity intervention at Riga | +4,865 |
| **Total combined opportunity** | | **+28,275 (+13.6%)** |

Implementation sequencing matters: Scenarios S1 and S4 share a dependency on Riga DC operations, meaning they should be addressed as a unified program. S3 is operationally independent and can begin immediately with a demand planning review of the Dairy category.

**[Visualization: Scatter plot OTIF vs GPM by supplier (bubble = order volume); waterfall chart showing GP baseline + each uplift scenario; quarterly GPM line vs. problem rate percentage dual-axis]**

---

## Recommendations

Based on the insights and findings above, we recommend the supply chain operations, commercial, and procurement teams consider the following actions:

**1. Initiate a cold chain audit for S005 (Eastern Frozen) with immediate channel reallocation.**
S005's quality issue rate of 5.1% and collapse to 53.8% OTIF in Q2 2025 indicate a cold chain integrity failure, not a scheduling problem. Recommended action: conduct a temperature log review and carrier handoff audit for all S005 shipments between April and June 2025 to identify the root event. Concurrently, cap S005's Retail channel allocation at below 15% of its total volume (currently receiving 52 orders in Retail at 71.2% OTIF) and redirect to HoReCa (93.0% OTIF), where delivery time requirements are more flexible. Expected timeline: 35 to 40 days. Expected OTIF uplift: S005 total from 81.9% to above 88%.

**2. Increase safety stock for S008 at Riga Central DC and trigger a contract renegotiation.**
With 69.9% of monthly snapshots below reorder point and a 7.9% stockout rate, S008's replenishment policy at Riga is structurally undersized. Increase safety stock by 20–30% for Snacks and Beverages SKUs at Riga, and set automated alerts when closing stock falls below 1.5× reorder point. In parallel, engage S008 on contract renegotiation to include a 95% OTIF SLA with a 2% invoice penalty per breach; the supplier's current 35.0% GPM provides headroom to absorb this. Expected timeline: 30 days for stock adjustment and 60 to 90 days for contract finalization. Expected GP recovery: +4,500 to +9,900.

**3. Launch a Retail channel margin improvement program as a 90-day commercial priority.**
With a 2.84 pp GPM gap versus HoReCa on 253,000 in revenue, Retail represents the highest-leverage single addressable opportunity (+7,179 GP). Three complementary levers: (a) reduce Retail problem rate from 20% to below 12% through stricter supplier SLA enforcement for Retail-destined orders; (b) shift product mix toward Beverages (GPM 36.2%) and away from Bakery (GPM 33.7%) within Retail assortment; (c) review Retail discount policy, which appears structurally higher than other channels. Expected timeline: 90–120 days for full impact.

**4. Conduct an inventory cycle review for Dairy SKUs to halt expired inventory accumulation.**
Dairy expired cost of 10,900 represents 86% of total network expired inventory cost and does not appear in any standard order-based reporting. This is a silent cash drain. Immediate action: review order frequency and batch size for Dairy products at Vilnius DC and Warsaw Hub (highest expired concentration). Reduce order batch size by 15–20% and increase replenishment frequency. Expected saving: 3,000 to 6,000 in the first 12 months depending on Dairy demand volatility.

**5. Establish a monthly supplier KPI scorecard with automated threshold alerts.**
The most critical near-term enabler is early warning capability. S005 fell from 87.5% OTIF (Q4 2024) to 53.8% (Q2 2025) without any documented detection trigger in the current reporting setup. Implement a lightweight monthly scorecard tracking OTIF, stockout rate, and quality issue rate for all 8 suppliers, with dashboard alerts at 90% and 85% thresholds. This costs approximately 10 analyst-days to build and is the foundational layer for all other recommendations. Expected timeline: 10 days to deploy.

---

## Assumptions and Caveats

Throughout the analysis, multiple assumptions were made to manage challenges with the data. These assumptions and caveats are noted below:

**Expired Inventory Cost Estimation:** The expired inventory cost figure (12,695 units) is derived by joining `Inventory_Snapshots.ExpiredQty` with `Dim_Product.StandardCost`. This uses standard cost as a proxy for actual inventory carrying value. If actual landed cost differs materially from standard cost for specific SKUs, particularly for imported Dairy or Frozen products where logistics costs are embedded, the expired cost figure may be over- or under-stated. No actual write-off records were available in the dataset to validate this calculation.

**GP Uplift Scenario Modeling:** All four improvement scenarios (S1–S4) are based on applying benchmark GPM and OTIF rates to the affected order or revenue base. These are conservative linear estimates and do not model second-order effects such as customer churn recovery from improved OTIF, volume growth from better Retail relationships, or supplier price renegotiation outcomes. Actual realized GP uplift may be higher or lower depending on implementation fidelity and market response.

**Stockout Lost Sales Estimation:** The analysis identifies stockout orders (StockoutFlag = 1) but does not attempt to quantify lost revenue from unfulfilled demand, as no demand signal or backorder data exists in the dataset. The 75 flagged stockout orders represent orders that were placed and partially or fully unfulfilled, not the full demand signal that was suppressed. True stockout cost is therefore understated.

**Currency and Unit Normalization:** All financial figures (Revenue, COGS, GrossProfit) are reported in the native reporting currency of the dataset without conversion. The dataset does not specify the currency unit; figures are presented as absolute values. Cross-border transactions (e.g., Czech supplier S008, German warehouse W005) may involve multi-currency dynamics not captured in the data.

**Analysis Period:** The dataset covers January 2024 through December 2025 (24 months, 731 date dimension rows). No partial-month exclusions were required as the dataset begins at the first of January 2024. Seasonal effects (IsDecemberPeak, IsSummer flags in Dim_Date) were noted as potential confounders in trend analysis but were not modeled as explicit controls in this report.

**Supplier Risk Tier Assignment:** RiskTier classifications (High / Medium / Low) in `Dim_Supplier` are provided as pre-existing categorical labels in the source data. The methodology underlying these assignments is not documented in the dataset. Where RiskTier and observed operational performance diverge (for example, Mediterranean Foods with Medium Risk classification, lowest GPM, and strong OTIF), the observed data is used as the primary analytical basis rather than the categorical label.

---

*Report prepared by: Data Analytics Team*
*Data sources: Fact_Orders.csv, Inventory_Snapshots.csv, Dim_Supplier.csv, Dim_Warehouse.csv, Dim_Product.csv, Dim_Customer.csv, Dim_Channel.csv, Dim_Date.csv*
*Analysis period: January 2024 – December 2025 | Total records analyzed: 9,971*
