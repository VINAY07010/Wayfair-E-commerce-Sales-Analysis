# Wayfair E-commerce Sales Analysis (2024 to 2025)

**By: Vinay Jagtap**

A cleaning and analysis of two years of raw online order exports. The brief was to give the e-commerce team an honest read on 2024 and 2025 so they can plan next year properly. That means three questions. How did sales go. What drove it. Where should the business focus.

---

## Repo structure

Wayfair Sales Analysis/
├── Data/
│ ├── Raw Data/ original exports, untouched
│ └── Cleaned Data/ cleaned datasets plus analysis outputs
├── Charts/ five PNG charts
├── Notebook/
│ └── Wayfair_Sales_Analysis.ipynb
├── README.md
├── requirements.txt
└── .gitignore

---

## Data

Four files, roughly 29,000 rows total.

| File | Rows | Grain |
| ------ | ------ | ------- |
| orders.csv | 9,245 | one order |
| order_items.csv | 15,608 | one product line per order |
| products.csv | 90 | one product |
| customers.csv | 3,600 | one customer |

Relationships:

- orders → order_items by `order_id`
- order_items → products by `product_id`
- orders → customers by `customer_id`

Every foreign key verified. No orphans.

---

## Cleaning decisions

Every table was audited before any change. Duplicates, missing values, inconsistent labels, broken foreign keys. Each issue found is documented with the fix and the evidence behind it.

### orders.csv

- 38 fully duplicated rows with identical `order_id`, identical customer, identical timestamp, identical everything. Byte for byte copies from an export re-run, not real repeat orders. Dropped with `drop_duplicates`.
- Left the 7,834 null `discount_code` values alone. A null means no promo was used, which is not a data problem.

### products.csv

- 24 raw category labels for what should be 8 real categories. `" Kitchen"` with a leading space, `"KITCHEN"` in caps, `"kitchen"` in lowercase, all referring to one category. Stripped whitespace and applied title case. Collapsed to exactly 8 canonical categories.

### order_items.csv

- 199 line items missing `unit_price` across 78 different products. Before filling, verified that `unit_price` tracks `products.list_price` on the rows that have it. Correlation came back at 0.9994, effectively 1.0. Filled the missing values from the catalog price. No missing prices remain.
- Also verified the `discount_amount` column against the discount codes. WELCOME10 averages 10 percent, SPRING15 averages 15, HOLIDAY20 averages 20. The amounts are internally consistent with the codes, no orphan discounts.

### customers.csv

- No issues found. No duplicates, no missing values, all 35 state codes are valid two letter abbreviations.

Raw files were never modified. Every decision is reproducible from the notebook.

---

## Revenue definition

Revenue is net merchandise revenue from orders that ended in a completed sale.

- Only orders with status equal to `completed` count. Canceled orders never went through. Returned orders were unwound after the fact. Neither represents money that stayed with the business. Both are excluded from revenue, but kept for the return and cancel rate analysis.
- Line revenue equals `unit_price × quantity − discount_amount`.
- Order revenue is the sum of its line items.
- Shipping fee is excluded. It is a pass through logistics cost, not merchandise revenue.

---

## Key findings

### 1. Revenue grew 21 percent year over year and the growth was broad based

2024 closed at 850,786 dollars and 2025 at 1,030,489. Every single month of 2025 beat its 2024 counterpart. Both years share the same seasonal shape. A summer lift, a September dip, a sharp November peak that is the single strongest month by a wide margin, and a December ease off. November 2025 came in at 129,934 dollars against 100,587 in 2024, so the holiday window itself is growing faster than the rest of the year.

### 2. Furniture is the top revenue category and the biggest margin risk

Furniture generated 322,774 dollars, about 17 percent of total revenue, driven by a handful of high ticket bestsellers like the Cotton Patio Chair and Classic Dining Chair Pair. It also carries the highest average order value at 506.78 dollars per order, more than 1.6 times the next category. But it has the highest return rate at 11.77 percent, roughly three times every other category which all sit between 3.86 and 5.07 percent.

### 3. Category mix rotates seasonally rather than staying constant

Outdoor products peak in spring and summer and nearly disappear in winter. Decor and Furniture do the opposite, expanding sharply in November and December. A category's average yearly share understates how concentrated its real selling window is.

---

## Recommendations

### 1. Investigate and fix the Furniture return rate before scaling it further

Furniture is the top revenue driver and the clear outlier on returns. Even a modest improvement would help. Better size and fit information, sturdier packaging, clearer assembly instructions. Returns on bulky furniture carry higher reverse logistics cost than a returned bath mat. Every point shaved off that return rate protects a meaningful share of total revenue and cuts the logistics cost on the way back.

### 2. Plan inventory and marketing around the seasonal rotation, not the annual average

Build Outdoor inventory ahead of spring. Build Decor and Furniture inventory ahead of November. Shift acquisition spend into those windows. November is already the year's biggest opportunity and it grew faster than the baseline in 2025, so any inventory and marketing that lands in that window compounds on top of the natural lift.

---

## Charts

| File | Purpose |
| ------ | --------- |
| `01_monthly_revenue.png` | monthly revenue 2024 vs 2025 |
| `02_category_revenue.png` | revenue by product category |
| `03_return_cancel_rates.png` | return and cancel rates by category |
| `04_aov_by_category.png` | average order value by category |
| `05_category_mix.png` | category mix over time |

---

## How to run

```bash
pip install -r requirements.txt
jupyter notebook Notebook/Wayfair_Sales_Analysis.ipynb
