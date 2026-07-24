# Adventure Works — DAX Measures Reference

This is a reference for the 150 DAX measures behind the Adventure Works Sales & Customer Intelligence dashboard, organized the same way they're organized in the model: by display folder, so a measure needed for a specific chart or KPI is easy to find rather than searching through a flat list.

Not every measure is documented line-by-line here — many exist purely to drive chart formatting (axis scaling, conditional colors, dynamic titles) rather than to report a business number. Those are grouped and explained collectively. The measures that answer an actual business question are documented individually with their formula and purpose.

---

## All Revenue Measures (23 measures)

Core revenue tracking: current performance, comparisons to prior periods, and dynamic targets.

### Total Revenue
```dax
Total Revenue =
SUMX(
    'Sales Data',
    'Sales Data'[OrderQuantity] * RELATED('Product Lookup'[ProductPrice])
)
```
**Purpose:** The base revenue number every other revenue measure is built on — quantity sold × price, summed across every order line.

### Revenue Previous Month / Revenue Previous Year
```dax
Revenue Previous Month =
CALCULATE([Total Revenue], DATEADD('Calendar Lookup'[Date], -1, MONTH))

Revenue Previous Year =
CALCULATE([Total Revenue], DATEADD('Calendar Lookup'[Date], -1, YEAR))
```
**Purpose:** The comparison points behind every month-over-month and year-over-year view.

### Revenue MoM % / Revenue YoY %
```dax
'Revenue MoM %' =
VAR _revenueMoM = DIVIDE([Total Revenue] - [Revenue Previous Month], [Revenue Previous Month], BLANK())
RETURN IF(ISBLANK(_revenueMoM), BLANK(), _revenueMoM)
```
**Purpose:** Percentage change vs. last month / last year. Drives the trend arrows on the Executive page.

### Revenue Target / Revenue Target Gap
```dax
Revenue Target = [Revenue Previous Month] * 1.1

Revenue Target Gap = [Total Revenue] - [Revenue Target]
```
**Purpose:** A rolling target set at 10% above last month's actual, and how far current revenue is from hitting it — an early-warning signal rather than a fixed annual number.

### Adjusted Revenue (pricing scenario)
```dax
Adjusted Revenue =
SUMX(
    'Sales Data',
    'Sales Data'[OrderQuantity] *
    (1 + 'Price Adjustment (%)'[Price Adjustment (%) Value] / 100) *
    RELATED('Product Lookup'[ProductPrice])
)
```
**Purpose:** Recalculates revenue at a hypothetical price point, driven by the Price Adjustment slider (0% to +50%). Lets a pricing decision be tested before it's made.

### Revenue QTD / Revenue LTD
```dax
Revenue QTD = CALCULATE([Total Revenue], DATESQTD('Calendar Lookup'[Date]))
Revenue LTD = TOTALYTD([Total Revenue], 'Calendar Lookup'[Date])
```
**Purpose:** Quarter-to-date and year-to-date revenue for cumulative tracking.

### Avg Revenue / Customer
```dax
'Avg Revenue / Customer' = DIVIDE([Total Revenue], [Active Customers])
```
**Purpose:** Revenue efficiency per customer — useful for spotting whether growth is coming from more customers or more spend per customer.

**Other measures in this folder** (chart support — territory bar-chart titles/details, trend high/low markers, axis scaling): `Revenue Target Gap with Arrow`, `Revenue by Territory Bar Chart Title/Detail/Max X Axis/Total Orders`, `Revenue Trending High and Low Value/Label`, `KPI Chart Revenue Max Axis`, `Max X Axis Revenue Trending`, `Space Fill Revenue Trending`, `Revenue YoY`.

---

## All Profit Measures (10 measures)

### Total Profit / Total Cost
```dax
Total Cost =
SUMX('Sales Data', 'Sales Data'[OrderQuantity] * RELATED('Product Lookup'[ProductCost]))

Total Profit = [Total Revenue] - [Total Cost]
```
**Purpose:** Straightforward margin tracking — revenue minus cost, calculated at the same order-line grain as revenue itself.

### Previous Month Profit / Profit Target / Profit Target Gap
```dax
'Previous Month Profit' = CALCULATE([Total Profit], DATEADD('Calendar Lookup'[Date], -1, MONTH))
'Profit Target' = [Previous Month Profit] * 1.1
'Profit Target Gap' = [Total Profit] - [Profit Target]
```
**Purpose:** Same rolling-target logic as revenue, applied to profit.

### Adjusted Profit / Profit vs Adjusted Profit Variance
```dax
'Adjusted Profit' = [Adjusted Revenue] - [Total Cost]
```
**Purpose:** Shows what profit would look like under the pricing scenario, and how far that is from actual profit today.

**Other measures in this folder** (chart support): `Profit Target Gap with Arrow`, `KPI Chart Profit Max Axis`, `Max Axis Adjusted Profit`, `Profit Adjustment Change`.

---

## All Order Measures (7 measures)

### Total Orders
```dax
'Total Orders' = DISTINCTCOUNT('Sales Data'[OrderNumber])
```
**Purpose:** Order volume, counted by unique order number rather than order line — so a multi-item order counts once, not per product.

### Previous Month Orders / Order Target / Order Target Gap / Orders MoM %
Same rolling-target and comparison pattern as revenue and profit, applied to order count.

**Other measures in this folder** (chart support): `Order Target Gap with Arrow`, `KPI Chart Orders Max Axis`.

---

## All Return Measures (19 measures)

### Quantity Sold / Quantity Returned / Total Returns
```dax
'Quantity Sold' = SUM('Sales Data'[OrderQuantity])
'Quantity Returned' = SUM('Returns Data'[ReturnQuantity])
'Total Returns' = SUM('Returns Data'[ReturnQuantity])
```

### Return Rate / All Time Return Rate
```dax
'Return Rate' = DIVIDE([Quantity Returned], [Quantity Sold], 0)

'All Time Return Rate' = CALCULATE([Return Rate], REMOVEFILTERS('Returns Data'))
```
**Purpose:** Returned units as a share of units sold — the headline quality metric, plus an unfiltered baseline to compare any filtered view against.

### Return Revenue Impact / Avg Return Value
```dax
'Return Revenue Impact' =
SUMX('Returns Data', 'Returns Data'[ReturnQuantity] * RELATED('Product Lookup'[ProductPrice]))

'Avg Return Value' = DIVIDE([Return Revenue Impact], [Total Returns], 0)
```
**Purpose:** Puts a dollar figure on returns — not just "how many," but "how much revenue did that cost."

### Avg Returns / Month
```dax
'Avg Returns / Month' =
DIVIDE([Total Returns], COUNTROWS(VALUES('Calendar Lookup'[Start of Month])), 0)
```
**Purpose:** Smooths return volume into a monthly average for trend comparison.

### Previous Month Returns / Previous Month Return Rate / Return MoM %
Same month-over-month comparison pattern used for revenue, applied to returns.

**Other measures in this folder** (chart support): `KPI Chart Return Rate Max Axis` (V1 and V2), `KPI Chart Total Returns Max Axis`, `KPI Chart Revenue Impact Max Axis`, `KPI Chart AVG Return Value Max Axis`, `KPI Chart AVG Returns / Month Max Axis`, `Max X Axis Return Rate Trend`, `Space Fill Return Rate Trend`, `Return Rate Trend High and Low Value`.

---

## Top Customer Measures (19 measures)

Drives the dynamic top-10 customer ranking, which can be sorted by Revenue, Orders, or Profit via the `TopCustomerBy` parameter.

### Top Customer Key (By Revenue)
```dax
'Top Customer Key (By Revenue)' =
VAR _customerTable = ALLSELECTED('Customer Lookup'[CustomerKey])
VAR _topCustomer =
    TOPN(1, _customerTable, [Total Revenue], DESC, [Total Orders], DESC, 'Customer Lookup'[CustomerKey], ASC)
RETURN
MAXX(_topCustomer, 'Customer Lookup'[CustomerKey])
```
**Purpose:** Identifies the single top customer within whatever filter context is active (a territory, a time period, etc.), with order count and customer key as tie-breakers.

### Supporting detail measures
`Top Customer Name`, `Top Customer Initials`, `Top Customer Occupation | Country`, `Top Customer Orders`, `Top Customer Revenue`, `Top Customer Rank`, `Top Customer Previous Orders/Revenue/Rank` (for period-over-period rank movement), `Top 10 Customer by Metric`, `Top 10 Customers Title`, `Year for Visual Title`.

**Chart/UI support:** `Top Customer Orders Formatting`, `Top Customer Revenue Formatting`, `Top Customer Visual Title`, `Customer Toggle Color`, `Toggle Circle`, `Customer Toggle Chart Title`.

---

## HomePage Measures (12 measures)

Summary statistics shown on the landing page — dataset scope at a glance before drilling into any page.

```dax
'Product Count' = COUNTROWS(VALUES('Product Lookup'[ProductKey])) & " Products"
'All Customers' = COUNTROWS('Customer Lookup')
'Unique Territories' = COUNTROWS(VALUES('Territory Lookup'[Continent]))
```

**Other measures in this folder:** `Dateset Information` (date range label), `Description`, `Revenue/Orders/Customers/Revenue per Customer with Label Landing Page`, `Global Revenue Landing Page`, `Territories Landing Page`, `ViewPage`.

---

## Other Measures (17 measures)

Utility measures that support the model but aren't themselves a reported business number — things like `Active Customers` (distinct customer count), `Filter Flag` (tracks active filter state), and a set of axis-scaling and space-fill measures (`Max Y Axis (Tooltip Chart)`, `Max Axis Profit Trend`, `Max Axis Customers`, etc.) that keep charts scaled correctly as the underlying data changes. Also includes `Customer Standings Dynamic`, `Category Name (Tooltip)`, `Product Trend High and Low Labels/Values`, `Top 5 Returned Products`, and `Product Metric Custom Number Formatting`.

---

## Visual Formatting (40 measures)

The largest folder, and entirely in service of presentation rather than new business logic: conditional trend-arrow colors (`Revenue MoM Formatting`, `Orders MoM Formatting`, `Returns MoM Formatting`), dynamic axis maximums so bar charts scale correctly (`MaxRevenueFormatting`, `MaxProfitFormatting`, `MaxOrdersFormatting`, `MinReturnRateFormatting`), and chart-specific labels and bar details for the category, occupation, income, territory, and return-reason breakdown charts.

**Example — a percentage-of-total bar chart label:**
```dax
'Return Reason Breakdown Bar Chart Detail' =
VAR _category = SELECTEDVALUE('Returns Data'[ReturnReason])
VAR _val = [Total Returns]
VAR _allReturns = CALCULATE([Total Returns], REMOVEFILTERS('Returns Data'[ReturnReason]))
RETURN
REPT(UNICHAR(160), 100) & FORMAT(DIVIDE(_val, _allReturns, 0), "0%")
```
This pattern repeats across the category, occupation, income, and territory breakdown charts — each reason/category shown as a share of its total.

---

## A Note on the Remaining Measures

A small number of measures (`Revenue MoM`, `Revenue Performance Subtitle`, `Scatter Chart Title`) are newer additions not yet filed into a display folder. They support the Executive trend card and the Map page's revenue-vs-return-rate scatter chart.

---

**Total Measures:** 150
**Organization:** 8 display folders + a small set of recently added, not-yet-filed measures
**Last Updated:** July 2026
