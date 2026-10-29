# KPI Definitions

| KPI | Definition | Filters |
|---|---|---|
| Total sales | Sum of `sales_amount` | `order_status_clean = Delivered`, `sales_flag = Include` |
| Total profit | Sum of `profit` | same |
| Profit margin | Total profit / total sales | same |
| Orders | Count of `order_id` | same |
| Average order value | Total sales / orders | same |
| Return rate | Returned / (Delivered + Returned) | no status filter. Cancelled, Pending and Shipped are excluded from the denominator because their outcome is not final |
| Cancellation rate | Cancelled / all orders | no filter |
| Returned value | Sum of `sales_amount` where status = Returned | `sales_flag = Include` |

## Why Delivered only?
Delivered orders are completed sales. Returned and Cancelled orders are not revenue, and Pending/Shipped orders may still fail.

## Why `sales_flag`?
Eight rows have `quantity = 999`. Their `sales_amount` is scaled from 999 units (for example one tablet order of 17.4M), which would distort every total. They are flagged `Exclude`, not deleted.

## Margin
Always computed as total profit / total sales for the group shown (Calculated Field in the PivotTable), never as an average of row-level `profit_margin`.
