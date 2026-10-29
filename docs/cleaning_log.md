# Cleaning Log

Raw: 4,310 rows, 21 columns. Clean: 4,200 rows.

| # | Step | Detail | Rows affected |
|---|---|---|---|
| 1 | Remove blank rows | Entire row empty | 30 |
| 2 | Remove duplicates | Identical across all columns | 80 |
| 3 | Standardize `order_date` | 3 formats found. Confirmed day-month-year order by finding values with day > 12. Built `order_date_clean` with a nested formula; validated range 2020-01-01 to 2024-12-30 and COUNT vs COUNTA | 4,200 (ISO 3,933 / DMY 147 / text month 120) |
| 4 | Fix numeric types | Decimal point stored as text under Indonesian regional settings; converted with Text to Columns (Decimal separator ".") | all numeric columns |
| 5 | `age_clean` | Valid range 1-100. Negative, 0, 150, 999 set to blank | 25 invalid, 125 originally blank |
| 6 | `quantity_clean` | Valid range 1-10. 0, -1, 999 set to blank | 26 invalid (8 are 999), 108 originally blank |
| 7 | `days_clean` | Valid range 1-10. -1, 0, 45, 100 set to blank | 13 invalid, 97 originally blank |
| 8 | `gender (clean)` | 11 spellings mapped to Male / Female / Other by first letter | all |
| 9 | `order_status_clean` | `PROPER(TRIM())` to unify casing | all |
| 10 | `sales_flag` | `Exclude` when raw `quantity = 999` | 8 |
| 11 | Derived columns | `profit_margin`, `year`, `year_month`, `age_group`, `discount_band`, `returned`, `final_order`, `cancelled`, `order_count` | all |

## Decisions and rationale
- Invalid values are blanked, not imputed. `sales_amount` cannot be used to reconstruct `quantity` because it does not reconcile with price x quantity x (1 - discount).
- Blank `discount_pct` and `customer_satisfaction` are left blank, not set to 0.
- Ages 1-17 (about 312 rows) are not clearly invalid, so they are kept and grouped as "<18".
- Rows are never deleted for outlier reasons. They are blanked or flagged so the audit trail stays intact.
