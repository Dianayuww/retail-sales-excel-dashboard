# Retail Sales & Return Rate Analysis (Excel Dashboard)

An end-to-end Excel project: cleaning a messy retail dataset, building PivotTables, and delivering an interactive dashboard with slicers. The goal is to understand **what sells, what is actually profitable, and where returns and cancellations hurt the business**.

![Dashboard](images/dashboard.png)

> **Tools:** Microsoft Excel (formulas, Text to Columns, PivotTables, Calculated Fields, PivotCharts, Slicers)
> **Dataset:** 4,310 raw rows, 21 columns, order dates 2020-01 to 2024-12 (see [Data source](#data-source))

---

## Business questions

1. What are total sales and profit (Delivered orders)?
2. How do sales trend by year and by month?
3. How do categories compare on sales **and margin**?
4. Which payment method is used most?
5. How high is the return rate, and what is it associated with?
6. How do discounts relate to profit margin?
7. Which products and customer segments are most profitable?
8. Is delivery time related to customer satisfaction?

## Key results

All sales and profit figures use **Delivered orders** and exclude 8 invalid rows (`quantity = 999`, see Data cleaning).

| KPI | Value |
|---|---|
| Total sales | 153,515,205 |
| Total profit | 28,709,810 |
| Profit margin | 18.7% |
| Orders | 2,725 |
| Average order value | 56,336 (median is only ~9,585: a few large Electronics orders pull the mean up) |
| Return rate | 18.3% |
| Cancellation rate | 8.2% |

## Findings

**1. Sales grew until 2022, then flattened.**
Yearly sales: 24.5M (2020), 29.9M (2021), 33.0M (2022), 32.6M (2023), 33.6M (2024). Margin stayed stable at 18-19%.

**2. High sales does not mean high profit.**
Electronics is 58% of sales but only 37% of profit (margin 11.9%). Furniture is 26% of sales but 39% of profit (margin 28.4%). Groceries has the lowest margin (7.9%); Beauty has the highest (45.0%).
Headphones, Laptop, Smartwatch and Camera are top-10 products by profit but have margins of only ~12%, while Furniture products earn 27-30%.

**3. Sports is the only category that declined in 2024.**
Sports sales fell 40.6% (3.83M to 2.27M). Both order volume (91 to 68) and average order value (42,069 to 33,454) dropped, and total incoming orders for Sports fell about 34%, so this is not caused by more returns or cancellations. Dumbbells and Yoga Mat account for roughly 70% of the drop, but Dumbbells in 2023 was unusually high.

**4. Return rate is uniform, but the money lost is concentrated.**
Return rate is 17.0-20.3% across categories (differences are not statistically significant). However, returned orders are worth ~32.4M (17.4% of finalized order value) and **Electronics makes up 62% of it**, because of its high ticket size. Returned orders also receive much lower satisfaction (2.46 vs 3.13 for Delivered). This is an association: the data cannot show which came first.

**5. Payment methods are evenly split.**
UPI is the most used (18.2% of orders) and Debit Card the least (15.9%), but the gap is small and not statistically significant. No method clearly dominates.

**6. Discount depth shows no visible effect on margin.**
Margin is flat at 18.4-18.8% across discount bands, and order counts per band are similar. See limitations: `sales_amount` does not reconcile with price x quantity x (1 - discount), so the data may not model discounts in the usual way.

**7. Female customers show a higher margin.**
Margin is 20.8% for Female vs 17.6% (Male) and 18.2% (Other); the difference in per-order margin is statistically significant. Note that "Other" is about one third of records.

**8. Delivery time is unrelated to satisfaction.**
Average satisfaction stays around 3.0-3.3 for 1-10 shipping days (correlation about -0.01).

## Recommendations (hypotheses to validate with the business)

- Review pricing and discounting in **Electronics**: it drives revenue but with thin margin, and it accounts for most of the returned value.
- Prioritize **Furniture** promotion and availability, since it converts sales to profit most efficiently among the large categories.
- Investigate the **Sports** drop in 2024 (pricing, stock, competition, seasonality). The dataset cannot explain the cause.
- **Record return reasons** at the source. Without them, the analysis can only describe where returns happen, not why.

## Data cleaning

Raw: 4,310 rows -> clean: 4,200 rows. Full log in [`docs/cleaning_log.md`](docs/cleaning_log.md).

| Issue | Treatment |
|---|---|
| 30 fully blank rows | Removed |
| 80 duplicate rows (same `order_id`, identical values) | Removed (Remove Duplicates, all columns) |
| 3 date formats (`2024-08-15`, `15/08/2024`, `August 15, 2024`) | Parsed with a nested formula after confirming DMY order; validated with MIN/MAX/COUNT |
| `age` outliers (negative, 0, 150, 999): 25 rows | Set to blank in `age_clean` (rows kept) |
| `quantity` outside 1-10 (0, -1, 999): 26 rows | Blank in `quantity_clean` |
| `quantity = 999`: 8 rows | **Excluded from sales/profit analysis** (`sales_flag`), because `sales_amount` is scaled from 999 units |
| `days_to_ship` outside 1-10: 13 rows | Blank in `days_clean` |
| 11 gender spellings (`M`, `male`, `FEMALE`, ...) | Standardized to Male / Female / Other |
| Inconsistent status casing | Standardized (`order_status_clean`) |
| Numbers stored as text (decimal point vs regional comma) | Converted with Text to Columns |

Raw columns are kept next to the cleaned ones for auditability.

## KPI definitions

Details in [`docs/kpi_definitions.md`](docs/kpi_definitions.md).

- **Sales / Profit:** sum of `sales_amount` / `profit` for `order_status_clean = Delivered` and `sales_flag = Include`
- **Margin:** total profit / total sales (not the average of row-level margins)
- **Return rate:** Returned / (Delivered + Returned)
- **Cancellation rate:** Cancelled / all orders

## Dashboard

[`dashboard/retail_sales_dashboard.xlsx`](dashboard/) contains the PivotTables and the dashboard sheet: KPI cards, monthly trend, sales and margin by category, payment methods, return rate by category and returned value by category. Slicers filter by year and category.

## Limitations

- No cost column, so profit cannot be recomputed or explained.
- `sales_amount` does not reconcile with price x quantity x (1 - discount): fewer than 3% of rows match. Values are used as given.
- No return reason column, so causes of returns are unknown.
- Almost every customer appears once (4,112 unique IDs in 4,200 rows), so retention/RFM analysis is not possible.
- `customer_satisfaction` is filled even for Cancelled and Pending orders, which should not be rateable.
- Currency is not specified in the dataset. The data looks synthetic.
- Several patterns (payment, return factors, shipping time) are small and not statistically significant, so they are reported as "no clear pattern".

## Repository structure

```
retail-sales-excel-dashboard/
├── README.md
├── data/
│   ├── raw/          # original dataset (add if license allows)
│   └── cleaned/      # cleaned workbook
├── dashboard/        # final Excel dashboard
├── images/           # dashboard screenshot
└── docs/
    ├── cleaning_log.md
    ├── data_dictionary.md
    └── kpi_definitions.md
```

## Data source

TODO: add the dataset name, link and license here.

## Author

TODO: your name, LinkedIn.
