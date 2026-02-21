# Revenue Leakage & Discount Optimization (Olist) — SQL Server + Tableau Public

**Dashboard (Tableau Public):** (https://public.tableau.com/views/olistinprogress/Dashboard1?:language=en-GB&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)  

## Executive Summary
This project builds a grain-safe revenue model at the **order-item level** to quantify revenue leakage from **discounts + refunds**, then visualizes trends and category drivers in Tableau.

Current results (from the exported dataset used in the dashboard):
- **Gross Revenue:** ~128,526  
- **Revenue Leakage ($):** ~18,531  
- **Net Revenue:** ~109,995  
- **Leakage %:** ~14.42%

## Problem
Revenue reporting gets distorted when discounts/refunds are duplicated across joins or modeled at inconsistent grain.  
Goal: create a clean, auditable model that answers:
- How much revenue is leaking to discounts and refunds?
- Is leakage trending up/down over time?
- Which product categories contribute most to leakage ($ and %)?

## Dataset
Brazilian E-Commerce Public Dataset by Olist (Kaggle).

## Metric Definitions (order_item_sk grain)
All metrics are computed at **1 row per order_item_sk**.

- **Gross Revenue:** sum of item-level revenue used as baseline  
- **Discount Amount:** deduped discounts from `discounts_one`  
- **Refund Amount (Fixed):** refunds modeled as **net paid** (prevents over-refunds / negative nets)  
- **Revenue Leakage ($):** `discount + refund`  
- **Net Revenue:** `gross - leakage` (floored at 0 in `vw_net_revenue`)  
- **Leakage %:** `leakage / gross`

## SQL Pipeline (built in SQL Server)
Created objects:
1. `order_items_clean` — 1 row per `order_item_sk`
2. `discounts_one` — deduped discounts
3. `refunds_fixed` — refund = net paid
4. `vw_refunds_agg`
5. `vw_net_revenue` — clean grain, no negative net revenue
6. `vw_net_revenue_enriched` — adds purchase timestamp/date/month + geolocation_state

### Grain Safety Validation
Verified:
- `COUNT(*) = COUNT(DISTINCT order_item_sk)`  
to prevent fanout and duplicated leakage.

## Tableau Dashboard (Executive View)
Included views:
- KPI cards: Gross Revenue, Total Leakage ($), Net Revenue, Leakage %
- Monthly leakage trend
- Discount vs Refund split by product category
- Top leakage categories ($)
- Top leakage categories (%), with minimum gross revenue filter (LOD) to avoid misleading small categories
