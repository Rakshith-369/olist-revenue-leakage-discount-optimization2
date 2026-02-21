# Metrics Dictionary (Order Item Grain)

**Grain:** 1 row per `order_item_sk`

## Revenue Metrics
- **Gross Revenue**  
  Definition: [REPLACE: e.g., SUM(price) or SUM(price + freight_value)]  
  Notes: Must match Tableau measure exactly.

- **Discount Amount**  
  Source: `discounts_one` (deduped)  
  Definition: SUM(discount_amount)

- **Refund Amount (Fixed)**  
  Source: `refunds_fixed`  
  Definition: Refund modeled as net paid (prevents over-refunding).

- **Revenue Leakage ($)**  
  Definition: `Discount Amount + Refund Amount`

- **Net Revenue**  
  Definition: `MAX(0, Gross Revenue - Revenue Leakage)`

- **Leakage %**  
  Definition: `Revenue Leakage / Gross Revenue`

## Validation Checks
- Grain safety: `COUNT(*) = COUNT(DISTINCT order_item_sk)`
- Net revenue floor: no negative net revenue allowed
