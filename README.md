# Supply Chain Performance Analysis Report

---
## Table of Content

- [Project Background](#project-background)
- [Data Structure](#data-structure)
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

## Data Structure

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
 
Across 2,200 orders and a 24-month observation window, the business is in a financially healthy position. Gross Profit Margin stands at **34.6%**, well above the food distribution industry average of approximately 20%, and has remained broadly stable throughout the period. Financial leakages from returns is negligible in scale and do not require intervention at this time.
 
The primary concerns are operational, not financial. Two issues stand out. First, overall OTIF of **92.5%** falls below the 95% industry benchmark, with the underperformance concentrated in two High-Risk suppliers that are actively degrading service to the company's most strategically important customers. Second, warehouse inventory management shows systemic weaknesses in waste and expired inventory, driven by a structural mismatch between product shelf life and current inventory velocity in the Dairy and Bakery categories. Left unaddressed, both issues carry meaningful relationship and reputational risk even if their immediate financial footprint appears contained.

|<img width="1153" height="112" alt="image" src="https://github.com/user-attachments/assets/3fb5defb-0c07-411d-b123-4cdd26c28213" />|
|:---:|
|**Overview KPI**|
 
---
 
## Insights Deep Dive
 
---
 
### Category 1: Delivery Reliability
 
> *How reliable is the current delivery process, based on OTIF rate and late delivery status?*
 
**Insight 1: OTIF is declining, not stable.**
The aggregate OTIF of 92.5% understates the severity of recent performance. In the first half of 2024, system OTIF consistently exceeded 95%, reaching a peak of 98.8% in April 2024. By Q2–Q3 2025, the quarterly average fell to 88.9–90.0%. May 2025 recorded the single worst monthly OTIF at **82.9%** with 15 late orders in that month alone, approximately 3× the monthly average of ~5 late orders. This sustained downward trajectory signals a structural deterioration rather than isolated incidents.

<img width="843" height="297" alt="image" src="https://github.com/user-attachments/assets/230b2a56-7399-4bcc-bf3b-8bf1bc1fd9b9" />

**Insight 2: OTIF performance exhibits sharp variances across multiple supply chain dimensions.**

About suppliers, S005 (Eastern Frozen) records an on-time rate of **81.9%** (the lowest among all suppliers) and collapsed to **53.9% OTIF in Q2 2025**. S008 (Value Foods) follows at 85.3%. In contrast, S004 (Global Snacks) and S001 (Baltic Fresh) sustain 98.4% and 96.7% respectively. 

<img width="621" height="249" alt="image" src="https://github.com/user-attachments/assets/760dd69d-3b72-4c82-807e-611470657060" />

<img width="615" height="269" alt="image" src="https://github.com/user-attachments/assets/2d14598a-e301-4ef8-a16a-a3a63dff8994" />



At the category level, Frozen products (primarily S005) have the lowest on-time rate at 91.0%, while Beverages lead at 94.3%. 

Across channels, Distributor records the lowest OTIF (91.97%) and E-commerce the highest (93.12%), though channel-level differences are narrower than supplier-level differences.
 
Across all warehouses, Riga Central DC records the lowest OTIF at **89.8%** on 490 orders, the second-highest order volume in the network. The same suppliers (S005 and S008) who underperform system-wide deteriorate *further* at Riga: S008 at Riga reaches only 75.9% OTIF, versus 94.0% at Berlin Export Hub for the same supplier. This 18 percentage-point gap with identical supplier input isolates Riga's internal operations (throughput capacity, handoff protocols, and peak staffing) as an independent failure source rather than a supplier dependency.
 
---
 
### Category 2: Operational Risk (Stockout, Returns, and Waste)
 
> *Where are stockout, returns, and waste/loss occurring?*
 
**Insight 1: Dairy and Berkery are the source of expired inventory**

Total expired inventory cost across the network stands at approximately 8,000 units. Geographically, this financial leakage is heavily concentrated in three facilities: Vilnius DC (1,917), and Warsaw Hub (1,771).

<img width="572" height="250" alt="image" src="https://github.com/user-attachments/assets/edde54fa-188a-4add-91c6-4b1349e432c4" />


Upon category-level investigation, Dairy and Bakery emerge as the primary drivers of this issue. Dairy alone accounts for 80.46% of the total expired units (6,579 units), while Bakery contributes the remaining 1,598 units. This significant loss is fundamentally driven by a structural mismatch: the Days Sales of Inventory (DSI) for these specific categories significantly exceeds their inherently short shelf life.

<img width="792" height="601" alt="image" src="https://github.com/user-attachments/assets/995878bb-004b-4150-a223-2ee9185cb693" />
 
**Insight 2: Highly perishable and temperature-sensitive categories drive 96.4% of order-level waste.**

While waste occurs network-wide, it is most severe at Berlin Export Hub, Riga Central DC, and Tallinn Cross-Dock. Out of the 606 total waste units, the loss is almost entirely isolated to three categories:

- **Bakery (302 units | 49.8%):** Driven by ultra-short shelf life, pointing to slow inventory velocity and cross-docking delays.

- **Dairy (207 units | 34.2%)** & **Frozen (75 units | 12.4%)**: Driven by strict cold-chain dependencies, indicating procedural breakdowns (e.g., dock-handling delays or temperature fluctuations) at these specific hubs.

<img width="576" height="242" alt="image" src="https://github.com/user-attachments/assets/14608267-2352-4555-ada9-c1afe635f106" />

<img width="369" height="264" alt="image" src="https://github.com/user-attachments/assets/d2173ac1-2b00-4b63-9bcd-c5896704df9d" />


---
 
### Category 3: Supplier & Warehouse Performance
 
> *Which suppliers, warehouses, or distribution channels are underperforming?*
 
**Insight 1: S005 and S008 have fundamentally different root causes and require different interventions.**
Both suppliers are rated **High Risk** and together account for 580 orders (26.4% of total volume). However, their failure modes diverge completely:
 
- **S005 (Eastern Frozen, Warsaw):** Failure is quality-driven. Quality issue rate 5.1%, cold storage supplier for Frozen products only, planned lead time 11 days, reliability declared at 83%. The Q2 2025 collapse to 53.8% OTIF (from 87.5% in Q4 2024) points to a specific cold chain event between April and June 2025 requiring direct investigation of temperature logs and carrier handoff records.
- **S008 (Value Foods, Prague):** Failure is inventory-planning-driven. Stockout rate 7.9%, planned lead time 12 days, reliability declared at 80%, defect rate 3.5%. S008 supplies ambient Snacks and Beverages (categories with no cold chain dependency), meaning its underperformance is unrelated to storage conditions and instead reflects misaligned replenishment cycles and insufficient safety stock.

<img width="452" height="259" alt="image" src="https://github.com/user-attachments/assets/b1db9add-6a27-4674-96a2-224e41a66116" />

When OTIF is segmented by customer priority tier (Strategic, Standard, Low), a critical pattern emerges. S005 (Eastern Frozen) delivers to Strategic-tier customers at only **73.3% OTIF** across 15 orders, and S008 (Value Foods) at **88.9% OTIF** across 36 orders. Both figures fall well below what a strategic customer relationship should tolerate, and both suppliers are already classified as High Risk. In contrast, four suppliers (S001 Baltic Fresh, S002 Mediterranean Foods, S004 Global Snacks, and S007 Premium Beverage) achieve 100% OTIF on all Strategic customer orders, demonstrating that reliable performance at this tier is achievable within the existing network. 

<img width="1534" height="588" alt="image" src="https://github.com/user-attachments/assets/2906ace0-6a11-427a-8964-7b88e3ccc365" />
 
**Insight 2: Riga Central DC underperforms on every dimension.**

Riga records OTIF of 89.8% (490 orders), expired cost of 2,219, order-level waste of 169 units, and the second-highest stockout rate (23.91%) among warehouses. When the same suppliers are routed through different warehouses, the OTIF differential is stark: Value Foods Wholesale achieves 94.0% OTIF at Berlin and only 75.9% at Riga, an 18 pp gap. Similarly, Eastern Frozen Logistics performs worst at Riga (75.0% OTIF, 28 orders) versus 84.4% at Berlin (32 orders). Riga is not merely a low performer; it is a **risk multiplier** that degrades otherwise acceptable supplier performance.

<img width="687" height="256" alt="image" src="https://github.com/user-attachments/assets/e62775ff-b51d-4e5d-98c7-338873b5e12f" />
 
---
 
## Recommendations
 
Based on the insights and findings above, we recommend the supply chain operations, procurement, and inventory management teams consider the following actions:
 
**1. Begin supplier replacement planning for S005 (Eastern Frozen) and S008 (Value Foods).**
Both suppliers are rated High Risk and are currently delivering to Strategic customers at OTIF levels of 73.3% and 88.9% respectively — performance that poses direct relationship risk to the company's most valuable accounts. S005's failure mode is cold chain quality (5.1% quality issue rate, with a collapse to 53.8% OTIF in Q2 2025 pointing to a specific operational event requiring investigation). S008's failure mode is inventory planning (7.9% stockout rate, with 69.9% of monthly inventory snapshots falling below reorder point). Given the divergence in root causes and the concentration of risk on Strategic customers, the recommended path is to initiate parallel sourcing for both suppliers' product categories and qualify at least one alternative supplier in each case. In the interim, S005 orders destined for Strategic accounts should be capped and rerouted through channels with more flexible delivery windows, and S008's safety stock at Riga Central DC should be increased by 20 to 30% immediately while a replacement sourcing process runs in parallel.
 
**2. Restructure Dairy and Bakery inventory ordering at Vilnius DC and Warsaw Hub to address expired inventory losses.**
Expired inventory costs of approximately 8,177 units are heavily concentrated in Dairy (6,579) and Bakery (1,598), driven by a structural mismatch: Dairy carries an average shelf life of 32 days and Bakery 15 days, while both categories record among the highest Days Sales of Inventory (DSI) in the network. The fix is not demand generation but cycle adjustment. Reduce order batch sizes for Dairy and Bakery SKUs at Vilnius DC, Warsaw Hub, and Tallinn Cross-Dock by 15 to 20%, increase order frequency to match actual consumption velocity, and set expiry-based picking rules to enforce FEFO (First Expired, First Out) discipline.
 
**3. Place S003 (Nordic Dairy) and S006 (Local Bakery) on a structured performance watch program.**
Both suppliers currently sit in the mid-range of OTIF performance (S003 at 92.3%, S006 at 94.9% overall) but show sufficient variability across customer segments to warrant monitoring. S003 records a quality issue rate of 10.3% on Strategic customer orders, which is elevated. S006 records 91.4% OTIF on Strategic orders, which is borderline. Neither requires immediate replacement action, but both should be included in the monthly supplier KPI scorecard with explicit escalation triggers at 90% OTIF or 3% quality issue rate sustained for two consecutive months

---
