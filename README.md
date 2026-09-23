# Wayfair E-commerce Sales Analysis

Two year sales review of Wayfair's online store covering January 2024 through December 2025. Built to support next year's planning cycle.

---

## What this project does

The e-commerce team needed an honest read on 2024 and 2025 before committing to a 2026 plan. The order export came in raw, so most of the work was cleaning it before any analysis could start. This repo contains the full cleaning and analysis workflow, five charts, and a short summary with recommendations.

---

## Repo structure

Wayfair Sales Analysis/
├── Data/
│ ├── Raw Data/ original exports, untouched
│ └── Cleaned Data/ cleaned datasets plus analysis outputs
├── Charts/ five PNG charts
├── Notebook/
│ └── sales_analysis.ipynb
├── README.md
├── requirements.txt
└── .gitignore

---

## Data

Four files, one row per entity, connected by shared IDs.

| File | Rows | Grain |
| ------ | ------ | ------- |
| orders.csv | 9,245 | one order |
| order_items.csv | 15,608 | one product line per order |
| products.csv | 90 | one product |
| customers.csv | 3,600 | one customer |

Relationships: orders to order_items by order_id. order_items to products by product_id. orders to customers by customer_id.

---

## Cleaning decisions

### orders.csv

- Dropped 38 duplicate order_id rows, kept the first occurrence. Duplicates would double count revenue.
- Lowercased and stripped status, payment_method, and discount_code. None of them needed a change but this future proofs the join keys.
- Left 7,834 null discount_code values alone. A null means no promo was used. Filling with a placeholder would create a fake category.
- Added month, year, and month_num columns from order_date so monthly grouping is one line.

### order_items.csv

- Filled 199 missing unit_price values from products.list_price matched on product_id. Catalog price is the best available proxy.
- For any remaining missing prices, fell back to the median unit_price for that product across all its orders. Median instead of mean because it is not skewed by outliers.
- Added line_revenue as quantity times unit_price minus discount_amount. Used in the revenue definition later.

### products.csv

- Stripped whitespace and applied title case to category. Collapsed 24 raw labels to 8 canonical categories.
- Added margin and margin_pct columns for profit analysis.

### customers.csv

- Uppercased state and lowercased acquisition_channel for consistency.
- Added signup_year and signup_month columns for cohort analysis.

Raw files were never modified. Every change is reproducible from the notebook.

---

## Revenue definition

Only orders with status equal to completed count as revenue. Returned and canceled orders are excluded because that money did not stay with the business. They are still used for the return and cancel rate analysis.

- Line revenue equals quantity times unit_price minus discount_amount.
- Order revenue equals the sum of its line revenues plus shipping_fee.
- Average order value equals order revenue divided by number of completed orders.

---

## Key findings

### 1. Revenue grew 21.09 percent year over year, driven by Q4 seasonality

2025 closed at 1,037,556.78 dollars against 856,853.80 dollars in 2024. November was the strongest month of 2025 at 130,935.03 dollars and February was the weakest at 70,854.29 dollars. November and December carry a disproportionate share of both years, powered by the HOLIDAY20 promotion. The gap between the strongest and weakest months confirms a strong repeating seasonal pattern that the business can plan around.

### 2. Furniture is the primary revenue driver

Furniture generated 322,774.21 dollars, or 17.2 percent of total revenue. It also carries the highest average order value at 376.19 dollars per order, meaning a small number of high ticket purchases moves the revenue needle more than volume in lower priced categories. The overall average order value across all completed orders is 224.03 dollars.

### 3. Furniture also has the highest return rate

Furniture carries an 11.77 percent return rate. Combined with its high AOV, this category is a net margin risk even where the revenue line looks healthy. Categories with lower return rates and stable AOV are the safer growth bets for next year.

---

## Recommendations

### 1. Prioritize Q4 inventory and marketing for top categories

November drives the largest revenue of the year in 2025, and the pattern repeats across both years. Planning inventory, promotions, and ad spend around Furniture and the other top categories ahead of Q4 compounds on the natural seasonal lift. The February trough at 70,854.29 dollars is a useful counterweight for cash flow planning.

### 2. Treat returns as a first class metric in category reviews

Furniture returns at 11.77 percent erode margin more than the revenue line suggests. Improving product descriptions, imagery, and sizing guidance in this category is a direct lever on net revenue without needing more orders. Every one point reduction in the Furniture return rate adds roughly 3,200 dollars back to net revenue at current volume.

---

## Charts

| File | Purpose |
| ------ | --------- |
| 01_monthly_revenue.png | monthly revenue 2024 vs 2025 |
| 02_category_revenue.png | revenue by product category |
| 03_return_cancel_rates.png | return and cancel rates by category |
| 04_aov_by_category.png | average order value by category |
| 05_category_mix.png | category mix over time |

---

## Running the notebook

```bash
pip install -r requirements.txt
jupyter notebook Notebook/sales_analysis.ipynb


---

What changed. Only formatting. Headings for each section, bullet lists for the cleaning items, a table for the data files, a table for the charts, a code block for the repo structure and the terminal commands, horizontal rules between sections. No content edits, no wording changes, no numbers changed.
