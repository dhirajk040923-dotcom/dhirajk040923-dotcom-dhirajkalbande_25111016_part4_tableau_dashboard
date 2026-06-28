# Retail Executive Dashboard — Part 4 Tableau Dashboard

## Repository Structure

```
part4_tableau_dashboard/
├── data/
│   └── dashboard_sales_data.xlsx
├── tableau/
│   └── executive_dashboard.twbx
├── outputs/
│   ├── dashboard_story.md
│   ├── business_insights.md
│   └── chart_selection_justification.md
├── screenshots/
│   ├── full_dashboard.png
│   ├── sales_trend_view.png
│   ├── regional_performance_view.png
│   ├── category_profitability_view.png
│   └── filter_interaction_view.png
└── README.md
```

---

## Business Problem Summary

The retail leadership team required a single executive dashboard to monitor and diagnose sales performance across the business. The core business questions driving this project were:

- Is the business growing profitably, or is revenue growth masking margin erosion?
- Which regions, product categories, and customer segments are the strongest and weakest performers?
- Are discounting practices helping or hurting the bottom line?
- Where are return rates highest, and what is the financial impact?
- Which shipping modes are causing delivery delays, and how widespread is the problem?
- Which marketing channels are delivering the best returns?

The dashboard was designed to answer all of these questions in a single view, allowing leadership to identify business risks and opportunities without running separate reports.

---

## Dataset Description

**File:** `data/dashboard_sales_data.xlsx`
**Records:** 4,200 orders
**Period:** 1 January 2024 – 31 December 2025

| Field | Type | Description |
|---|---|---|
| `order_id` | Text | Unique order identifier |
| `order_date` | Date | Date the order was placed |
| `ship_date` | Date | Date the order was shipped |
| `customer_id` | Text | Unique customer identifier |
| `customer_segment` | Text | Consumer, Corporate, or Home Office |
| `region` | Text | North, South, East, or West |
| `state` | Text | US state |
| `city` | Text | City of delivery |
| `category` | Text | Furniture, Office Supplies, or Technology |
| `sub_category` | Text | Product sub-category (13 total) |
| `product_name` | Text | Name of the product |
| `ship_mode` | Text | Same Day, First Class, Second Class, Standard Class |
| `sales` | Number | Order revenue in USD |
| `quantity` | Number | Units ordered |
| `discount` | Number | Discount applied (0.0 to 0.5) |
| `profit` | Number | Profit earned on the order in USD |
| `return_flag` | Binary | 1 = order was returned, 0 = not returned |
| `delivery_days` | Number | Days between order date and ship date |
| `customer_rating` | Number | Customer satisfaction rating |
| `campaign_channel` | Text | Organic, Paid, Email, Social, Referral |

**Key summary statistics:**
- Total Sales: $217,017,652
- Total Profit: $33,306,313
- Overall Profit Margin: 15.35%
- Average Discount: 13.73%
- Overall Return Rate: 4.55%

---

## Tableau Workbook Description

**File:** `tableau/executive_dashboard.twbx`

The packaged workbook (`.twbx`) contains the data source and all sheets bundled into a single file. It can be opened directly in Tableau Public or Tableau Desktop without needing to reconnect to the data.

The workbook contains:
- 7 individual worksheet views (Sheets 1–7)
- 1 executive dashboard (Dashboard 1) combining all views
- 5 calculated fields
- 3 interactive filters
- 1 action filter (click-to-filter via the regional map)

---

## Calculated Fields

Five calculated fields were created in Tableau to support the analysis:

| Calculated Field | Formula | Purpose |
|---|---|---|
| **Profit Margin** | `SUM([profit]) / SUM([sales])` | Shows the percentage of sales that converts to profit; used in KPI cards and color encoding |
| **Cost** | `SUM([sales]) - SUM([profit])` | Derives cost of goods since it is not directly in the dataset |
| **Average Order Value** | `SUM([sales]) / COUNTD([order_id])` | Measures average revenue per unique order; used in segment comparison |
| **Return Rate** | `SUM([return_flag]) / COUNT([order_id])` | Calculates the proportion of orders returned; used in return analysis sheet |
| **Shipping Delay Bucket** | `IF [delivery_days] <= 2 THEN "Fast" ELSEIF [delivery_days] <= 5 THEN "Standard" ELSE "Delayed" END` | Groups delivery speed into three meaningful business categories for the shipping performance view |

---

## Dashboard Components

The executive dashboard (Dashboard 1) contains the following components:

### KPI Cards (Summary Metrics)
Three large-number summary tiles at the top of the dashboard:
- **Total Sales** — $217M overall
- **Total Profit** — $33.3M overall
- **Profit Margin** — 15.35% overall

### Sheet 1 — Sales Trend (Line Chart)
Shows how total sales change over time, aggregated by month. Reveals seasonality (July–September peak) and year-over-year growth trajectory from 2024 to 2025.

### Sheet 2 — Regional Performance (Geographic Map)
A filled choropleth map showing sales by state, color-coded by sales magnitude. Doubles as a filter source — clicking any state filters all other sheets on the dashboard to that geography.

### Sheet 3 — Category and Sub-Category Profitability (Horizontal Bar Chart)
Shows profit by Category and Sub-Category in a two-level hierarchy. Color encodes positive vs negative profit to immediately flag underperforming sub-categories.

### Sheet 4 — Customer Segment Performance (Bar Chart)
Compares sales and profit across the three customer segments (Consumer, Corporate, Home Office). Bar length encodes sales; color encodes profit margin.

### Sheet 5 — Shipping Performance (Bar Chart)
Crosses Ship Mode against the Shipping Delay Bucket calculated field to show what proportion of orders in each shipping mode arrive fast, on time, or delayed.

### Sheet 6 — Discount vs Profit (Scatter Plot)
Plots discount level against profit to reveal the inverse relationship between discounting and profitability. Color-coded by Category.

### Sheet 7 — Return Analysis (Bar Chart)
Shows return rates by Category and Customer Segment, with a reference line for the overall average return rate.

---

## Filters and Interactions

### Interactive Filters (visible on dashboard)
| Filter | Field | Applies To |
|---|---|---|
| Region filter | `region` | All sheets |
| Category filter | `category` | All sheets |
| Customer Segment filter | `customer_segment` | All sheets |

### Action Filter (click interaction)
- **Source:** Sheet 2 (Regional Map)
- **Trigger:** Click on any state or region
- **Effect:** Filters all other dashboard sheets to show data for the selected geography only
- **Reset:** Click an empty area of the map to clear the selection

### Additional Sheet-Level Filters
- All sheets are connected via the shared data source, so global filters cascade across the entire dashboard automatically.

---

## Key Business Insights

1. **Revenue grew 4.3% YoY but profit grew only 2.2%** — margin slipped from 15.5% (2024) to 15.2% (2025), signalling that growth is coming at a cost.
2. **Technology drives 84.2% of all profit** at an 18.2% margin — the business has a dangerous single-category dependency.
3. **Furniture earns only a 6.9% margin** and has a 7.7% return rate — the combination makes it the highest-risk category.
4. **75.6% of orders carry a discount**, and orders with discounts earn 13.1% margin vs 20.6% for undiscounted orders — a recoverable gap.
5. **Home Office is the top-revenue segment** ($74.5M) but has the highest return rate (5.7%), suggesting a product-fit issue worth investigating.
6. **26.9% of Standard Class orders arrive late** (over 5 days), affecting the majority of order volume.
7. **The South region leads in sales** ($64.7M) but all regions operate at nearly identical margins — a sign of consistent operational execution.
8. **Referral channel has the highest margin** of all acquisition channels (15.7%) but is the smallest by volume — a clear underinvestment opportunity.

Full analysis with data evidence and recommended actions is in `outputs/business_insights.md`.

---

## Dashboard Story Summary

The dashboard tells a story of a business that is growing on the surface but facing structural pressure on profitability. Technology is carrying an outsized share of profit, discounting is eroding margin across three-quarters of all orders, and Furniture is consuming operational resources for returns that barely justify the volume it generates. Meanwhile, three opportunities — a structured referral programme, a discount policy cap, and a peak-season demand playbook — are available to recover margin and accelerate growth without requiring new customers or new products.

The full narrative written for a leadership audience is in `outputs/dashboard_story.md`.

---

## Assumptions and Limitations

- **`return_flag` is treated as binary (0/1)** — the dataset does not include return reasons, return costs, or whether returned items were resold. Return rate calculations assume all flagged orders were fully returned.
- **`delivery_days` is assumed to represent order-to-ship time**, not order-to-delivery time. The Shipping Delay Bucket thresholds (≤2 days = Fast, ≤5 days = Standard, >5 days = Delayed) are business-defined for this analysis.
- **Profit figures are taken as given** — the dataset does not break out cost of goods, shipping costs, or overhead allocation separately. Profit is calculated as `sales - cost` at the order level as recorded.
- **Campaign channel has some null values** — a small number of orders had no channel recorded and are excluded from channel-level analysis.
- **The dataset covers exactly 2 years (2024–2025)** — seasonal comparisons and trend analysis are based on this window only. Longer historical data would increase confidence in trend conclusions.
- **Geographic analysis is limited to region and state** — city-level performance was not included in the dashboard due to the large number of cities reducing readability at dashboard scale.

---

## Screenshots Included

| File | Contents |
|---|---|
| `screenshots/full_dashboard.png` | Complete executive dashboard showing all charts, KPI cards, and filters |
| `screenshots/sales_trend_view.png` | Sheet 1 — Sales trend line chart over 2024–2025 |
| `screenshots/regional_performance_view.png` | Sheet 2 — Geographic map of sales by state/region |
| `screenshots/category_profitability_view.png` | Sheet 3 — Category and sub-category profit bar chart |
| `screenshots/filter_interaction_view.png` | Dashboard with a region selected via the map action filter, showing filtered state across all charts |

---

## Output Files

| File | Description |
|---|---|
| `outputs/business_insights.md` | 8 business insights with data evidence, interpretation, and recommended actions |
| `outputs/dashboard_story.md` | Executive narrative covering performance, risks, opportunities, recommended actions, limitations, and next steps |
| `outputs/chart_selection_justification.md` | Justification for each chart type used, including fields, design principles, and mistakes avoided |
