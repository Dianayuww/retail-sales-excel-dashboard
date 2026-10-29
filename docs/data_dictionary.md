# Data Dictionary

## Original columns

| Column | Description |
|---|---|
| order_id | Order identifier |
| order_date | Order date (mixed formats in raw data) |
| customer_id, customer_name | Customer identifiers |
| age | Customer age (contains invalid values) |
| gender | Customer gender (mixed spellings) |
| region, city | Customer location (5 regions, 20 cities) |
| product_category | 7 categories: Electronics, Furniture, Sports, Clothing, Beauty, Books, Groceries |
| product_name | Product (42 products) |
| quantity | Units ordered (valid 1-10) |
| unit_price | Price per unit (currency not specified) |
| discount_pct | Discount rate, 0-0.40 |
| sales_amount | Order sales value, used as given |
| profit | Order profit, used as given |
| shipping_cost | Shipping cost |
| payment_method | UPI, Credit Card, Debit Card, EMI, Net Banking, Cash on Delivery |
| customer_satisfaction | Rating 1-5 |
| return_flag | TRUE if the order was returned |
| order_status | Delivered, Returned, Cancelled, Shipped, Pending |
| days_to_ship | Days to ship (valid 1-10) |

## Added columns

| Column | Formula idea |
|---|---|
| order_date_clean | Nested IF/DATE parsing of the 3 date formats |
| age_clean, quantity_clean, days_clean | Blank if outside the valid range |
| gender (clean) | Male / Female / Other |
| order_status_clean | PROPER(TRIM(order_status)) |
| profit_margin | profit / sales_amount |
| year, year_month | From order_date_clean |
| age_group | <18, 18-25, 26-35, 36-45, 46-60, 60+, Unknown |
| discount_band | 0%, 1-10%, 11-20%, 21-30%, 31-40%, No data |
| sales_flag | Exclude if quantity = 999, else Include |
| returned, final_order | Helper columns for return rate |
| cancelled, order_count | Helper columns for cancellation rate |

