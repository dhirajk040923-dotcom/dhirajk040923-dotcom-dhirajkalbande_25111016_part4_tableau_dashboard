# Chart Selection Justification — Retail Executive Dashboard

This document explains the chart type chosen for each of the 7 sheets in the executive dashboard, the reasoning behind each choice, the fields used, the design principles applied, and the mistakes deliberately avoided.

---

## Sheet 1 — Sales Trend Over Time (Line Chart)

**Question answered:**
How are total sales changing over time? Are we growing month over month and year over year?

**Chart type chosen:** Line Chart

**Why this chart type:**
A line chart is the standard and most appropriate choice for showing a continuous numeric measure (Sales) changing over a time dimension (Order Date by month). The human eye naturally reads a line as movement through time — rises and falls in the line instantly communicate growth or decline without any explanation. No other chart type communicates temporal trend as clearly.

**Fields used:**
- Columns: `Order Date` (aggregated to Month)
- Rows: `SUM(Sales)`
- The date is drilled to month level to show seasonal patterns while still being readable

**Design principle applied:**
*Preattentive processing* — the slope of the line communicates trend direction before the viewer consciously reads any numbers. The chart uses a single line without unnecessary colour variation, keeping attention on the trend rather than on visual decoration.

**Mistake avoided:**
A bar chart was not used here. While bar charts can show sales by time period, they emphasise individual period comparisons rather than the overall trend direction. Using a bar chart would make it harder to spot seasonal patterns or multi-month momentum. A pie chart would have been completely inappropriate — pie charts cannot show change over time at all.

---

## Sheet 2 — Regional Performance (Geographic Map)

**Question answered:**
Which regions and states are generating the most sales? Where is the business geographically concentrated?

**Chart type chosen:** Filled Map (Choropleth)

**Why this chart type:**
A geographic map is the only chart type that communicates spatial distribution — it shows not just *which* regions perform well, but *where* they are relative to each other. Leadership teams making decisions about sales territories, logistics, or regional expansion benefit from seeing performance anchored to actual geography. The map uses Tableau's auto-generated Latitude and Longitude fields plotted against State data.

**Fields used:**
- Columns: `Longitude (generated)`
- Rows: `Latitude (generated)`
- Geographic dimension: `State`
- Color encoding: `SUM(Sales)` — darker shade indicates higher sales
- Filter interaction: clicking a state filters all other sheets via the Action filter

**Design principle applied:**
*Spatial encoding* — position on the map is inherently meaningful. Color intensity (a sequential single-hue ramp from light to dark) encodes sales magnitude without introducing confusing multi-color complexity. This follows the principle of using one visual channel per variable.

**Mistake avoided:**
A plain bar chart of regions could show the same sales figures, but it would strip out the geographic context that makes the insight actionable. Decision-makers planning regional sales staffing or distribution need to see *where* the South is, not just that it leads. Additionally, using multiple unrelated colors per region (treating regions as categorical) was avoided — the continuous color ramp correctly reflects the continuous nature of the sales measure.

---

## Sheet 3 — Category and Sub-Category Profitability (Horizontal Bar Chart)

**Question answered:**
Which product categories and sub-categories are the most and least profitable? Where are we making money and where are we losing it?

**Chart type chosen:** Horizontal Bar Chart with Category / Sub-Category hierarchy on rows

**Why this chart type:**
A bar chart is ideal for comparing a single numeric measure (Profit) across multiple discrete categories. Horizontal orientation was chosen specifically because sub-category names are text labels that are too long to display cleanly on a vertical axis — rotating them would make them unreadable. The two-level hierarchy (Category → Sub-Category) on the row shelf allows the viewer to immediately see both the parent category summary and the individual sub-category breakdown in one view.

**Fields used:**
- Rows: `Category` / `Sub-Category` (hierarchical, both on rows shelf)
- Columns: `SUM(Profit)`
- Color: `SUM(Profit)` — positive profit bars appear in one color, negative in a contrasting color to immediately flag loss-making sub-categories
- Sorted: descending by profit so highest performers appear at the top

**Design principle applied:**
*Ranking and sorting* — bars are sorted from highest to lowest profit, making it immediately obvious which sub-categories are at the top and bottom without the viewer needing to scan the full chart. This follows Tufte's principle of ordering data meaningfully rather than alphabetically.

**Mistake avoided:**
A pie chart or treemap was deliberately avoided. While a treemap can show proportional contribution to a total, it makes it very difficult to compare precise values across categories — especially when profit differences are small. Pie charts cannot show negative values at all, which would hide any loss-making sub-categories. The bar chart handles negative profit values naturally by extending the bar to the left of the zero line.

---

## Sheet 4 — Customer Segment Performance (Bar Chart)

**Question answered:**
How do the three customer segments (Consumer, Corporate, Home Office) compare on sales and profitability? Which segment is the most valuable?

**Chart type chosen:** Horizontal Bar Chart

**Why this chart type:**
With only three discrete segments to compare, a bar chart is the clearest possible choice. It allows an immediate side-by-side comparison of sales values across segments. The low number of categories (3) means there is no risk of the chart becoming cluttered, and each bar can be clearly labelled.

**Fields used:**
- Rows: `Customer Segment`
- Columns: `SUM(Sales)`
- Color: `SUM(Profit)` — color encodes profitability independently of the bar length (sales), allowing two measures to be read simultaneously
- Labels: values shown on bars for precise reading

**Design principle applied:**
*Dual encoding* — bar length encodes one measure (Sales) while color encodes a second related measure (Profit). This allows leadership to spot at a glance whether a high-sales segment is also high-profit, or whether sales volume is coming at the cost of margin. This is more efficient than using two separate charts.

**Mistake avoided:**
A pie chart showing segment share of sales was avoided. While it would show relative proportions, it cannot simultaneously encode profitability, and humans are poor at judging the difference between similar-sized pie slices. The three segments have relatively similar sales ($70–75M each), a difference that is clear on a bar chart but nearly impossible to discern on a pie chart.

---

## Sheet 5 — Shipping Performance (Bar Chart / Matrix)

**Question answered:**
How does each shipping mode perform in terms of delivery speed? How many orders fall into fast, standard, or delayed delivery buckets for each shipping mode?

**Chart type chosen:** Bar Chart (Ship Mode on rows, Shipping Delay Bucket on columns — acting as a grouped/stacked comparison)

**Why this chart type:**
The shipping performance view crosses two categorical dimensions — Ship Mode (4 categories: First Class, Same Day, Second Class, Standard Class) and the calculated Shipping Delay Bucket (Fast / Standard / Delayed). A bar or matrix layout is the clearest way to show how these two dimensions interact. The Shipping Delay Bucket is a calculated field created specifically for this analysis:
```
IF [delivery_days] <= 2 THEN "Fast"
ELSEIF [delivery_days] <= 5 THEN "Standard"
ELSE "Delayed" END
```

**Fields used:**
- Rows: `Ship Mode`
- Columns: `Shipping Delay Bucket` (calculated field)
- Size or Label: count of orders per combination
- The Action filter from the map (Sheet 2) connects to this sheet, so clicking a state shows shipping performance for that region only

**Design principle applied:**
*Small multiples / cross-tabulation* — breaking down each shipping mode by delay category allows leadership to see not just which mode is fastest on average, but how consistent it is. A mode with mostly "Fast" deliveries is more reliable than one with a mix of Fast and Delayed.

**Mistake avoided:**
Using only average delivery days per shipping mode (a single bar per mode) was avoided. Averages hide the distribution — a shipping mode could have an acceptable average but many extreme delays. The bucket breakdown reveals the full picture. A line chart was also not used here since neither axis is a time series.

---

## Sheet 6 — Discount vs Profit (Scatter Plot)

**Question answered:**
Is there a relationship between the discount offered and the profit generated? At what discount level does profitability drop critically?

**Chart type chosen:** Scatter Plot

**Why this chart type:**
A scatter plot is the correct chart for showing the relationship between two continuous numeric variables — Discount (X axis) and Profit (Y axis). It reveals correlation, clusters, and outliers that no other chart type can show. In this dataset, the scatter plot makes the inverse relationship between discount and profit immediately visible: as the X axis (discount) increases, the Y axis (profit) trends downward.

**Fields used:**
- Columns: `SUM(Discount)` (or average discount per order)
- Rows: `SUM(Profit)`
- Color: `Category` — different colors for Furniture, Office Supplies, and Technology allow the viewer to see whether the discount-profit relationship differs by product type
- Each mark represents an aggregated order or product group

**Design principle applied:**
*Correlation visualisation* — the scatter plot is specifically designed to communicate relationships between variables. Adding a trend line (if enabled) reinforces the negative correlation visually. Color encoding by Category adds a third dimension of information without adding a third axis, following the principle of maximising data density without clutter.

**Mistake avoided:**
A bar chart comparing average profit at different discount levels was considered but rejected. While it would show the trend, it would lose individual data point variation and make it harder to spot outliers — for example, high-discount orders that are still profitable, or low-discount orders that lose money. The scatter plot preserves this granularity. A line chart was also inappropriate here since Discount is not a time variable — connecting points with a line would imply a sequence that does not exist.

---

## Sheet 7 — Return Analysis (Bar Chart)

**Question answered:**
Which customer segments, categories, or regions have the highest return rates? Where is the return problem most concentrated?

**Chart type chosen:** Horizontal Bar Chart

**Why this chart type:**
The return analysis compares the return rate (a calculated field: returned orders divided by total orders) across discrete categories such as customer segment or product category. A bar chart directly encodes the magnitude of the return rate as bar length, making comparisons instant and precise. The horizontal orientation accommodates category name labels without rotation.

**Fields used:**
- Rows: `Category` (or `Customer Segment` depending on view)
- Columns: `Return Rate` (calculated field: `SUM([return_flag]) / COUNT([order_id])`)
- Color: `Return Rate` — a color ramp from low (cool) to high (warm/red) adds a second visual signal that high bars are problematic, reinforcing the message without requiring the viewer to read the axis value
- Reference line: an average return rate reference line added to show which categories are above vs below average

**Design principle applied:**
*Redundant encoding* — both bar length and color encode return rate. This is intentional: redundant encoding ensures the message (high return rate = problem) is communicated even if a viewer only glances at the color and misses the bar length. It also makes the chart accessible to viewers with color vision differences, since the length cue is independent of the color cue.

**Mistake avoided:**
A pie chart showing return share by category was avoided — it would show proportional contribution to total returns, but it would obscure the actual rate. A category with 5% of all returns might have a 10% return rate (a serious problem) or a 1% return rate (acceptable), depending on its order volume. The bar chart shows rate, not share, which is the operationally relevant metric for identifying problem areas.

---

## Dashboard-Level Design Decisions

**KPI Cards (Summary Metrics)**
Three KPI cards at the top of the dashboard display Total Sales, Total Profit, and Overall Profit Margin as large single numbers. These are implemented as text-mark sheets with a single aggregated measure displayed in large font. KPI cards give leadership an immediate "state of the business" snapshot before they engage with the individual charts.

**Filter Design**
Interactive filters for Region, Category, Customer Segment, Ship Mode, and Campaign Channel are placed consistently on the right side of the dashboard. This follows standard dashboard convention — filters are a utility element, not a visual element, and positioning them consistently means users always know where to find them without hunting.

**Action Filters (Click Interactions)**
The map (Sheet 2) is configured as a filter source — clicking a state or region on the map filters all other sheets on the dashboard to show data for that selection only. This supports exploratory analysis: a regional sales director can click their territory and immediately see how all other metrics (category performance, segment breakdown, shipping delays) look specifically for their region.

**Color Consistency**
A consistent color palette is maintained across sheets — the same color represents the same dimension across charts. For example, if Technology is blue in the category profitability chart, it remains blue in the discount scatter plot. This reduces cognitive load: viewers do not need to re-learn the color legend for each chart.

**Sorting**
All bar charts are sorted in descending order by the primary measure (profit, sales, or return rate). Alphabetical sorting was deliberately avoided — it would require the viewer to find and compare items rather than reading the ranking instantly from top to bottom.

---

*Document prepared for Part 4 Tableau Dashboard Assignment*
