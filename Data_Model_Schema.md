# Adventure Works — Data Model Schema

Documentation of the star schema behind the Adventure Works Sales & Customer Intelligence dashboard.

## Overview

**Schema Type:** Star schema
**Fact Tables:** 2 (Sales Data, Returns Data)
**Dimension Tables:** 6 (Calendar, Customer, Product, Product Categories, Product Subcategories, Territory)
**Parameter Tables:** 6 (drive interactivity — pricing scenarios, metric toggles, drill-downs — with no relationship to the fact tables)
**Supporting Tables:** 2 (Measure Table, Time Intelligence)
**Total Tables:** 16
**Relationships:** 9, all single-direction many-to-one (standard star schema pattern)

### Schema Diagram

```
                         ┌─────────────────────┐
                         │     Sales Data      │
                         │   (Fact Table)      │
                         └──────────┬───────────┘
                    ┌──────────────────┼──────────────────┐
                    │                │                │
            ┌─────────────┐ ┌──────────────┐ ┌──────────────┐
            │ Calendar Lookup│ │ Customer Lookup│ │Territory Lookup│
            └─────────────┘ └──────────────┘ └──────────────┘
                    │
            ┌─────────────┐
            │ Product Lookup │
            └───────┬────────┘
                     │
        ┌───────────────────┐
        │ Product Subcategories    │
        │        Lookup            │
        └─────────┬─────────────┘
                    │
        ┌───────────────────┐
        │  Product Categories      │
        │        Lookup            │
        └────────────────────┘

                         ┌─────────────────────┐
                         │    Returns Data      │
                         │   (Fact Table)       │
                         └──────────┬───────────┘
                    ┌──────────────────┼──────────────────┐
            ┌─────────────┐ ┌──────────────┐ ┌──────────────┐
            │ Calendar Lookup│ │ Product Lookup│ │Territory Lookup│
            └─────────────┘ └──────────────┘ └──────────────┘
```

---

## Fact Tables

### Sales Data

**Purpose:** One row per order line — the source for every revenue, profit, and order measure.

| Column | Type | Visible | Notes |
|---|---|---|---|
| **OrderDate** | dateTime | Hidden | Relates to Calendar Lookup[Date] |
| **StockDate** | dateTime | Visible | Date the item was stocked |
| **OrderNumber** | string | Visible | Order identifier; `Total Orders` uses `DISTINCTCOUNT` on this so multi-line orders count once |
| **ProductKey** | int64 | Hidden | Relates to Product Lookup |
| **CustomerKey** | int64 | Hidden | Relates to Customer Lookup |
| **TerritoryKey** | int64 | Hidden | Relates to Territory Lookup |
| **OrderLineItem** | int64 | Visible | Line number within the order |
| **OrderQuantity** | int64 | Visible | Units ordered — the base of every revenue/profit calculation |
| **Quantity Type** *(calculated)* | — | Visible | Supporting classification column |

### Returns Data

**Purpose:** One row per returned item — the source for all return-rate and root-cause analysis.

| Column | Type | Visible | Notes |
|---|---|---|---|
| **ReturnDate** | dateTime | Hidden | Relates to Calendar Lookup[Date] |
| **TerritoryKey** | int64 | Hidden | Relates to Territory Lookup |
| **ProductKey** | int64 | Hidden | Relates to Product Lookup |
| **ReturnQuantity** | int64 | Visible | Units returned |
| **ReturnReason** | string | Visible | Why the item was returned — powers the root-cause breakdown on the Returns page |

---

## Dimension Tables

### Calendar Lookup (13 columns)

Standard date dimension supporting every time-based comparison in the model (month-over-month, year-over-year, quarter- and year-to-date).

| Column | Type |
|---|---|
| Date | dateTime |
| Day Name | string |
| Start of Week / Start of Month / Start of Quarter / Start of Year | dateTime |
| Month Name / Month Short | string / calculated |
| Month | int64 |
| Year | int64 |
| Day of Week *(calculated)* | — |
| Weekend *(calculated)* | — |
| Quarter *(calculated: `FORMAT('Calendar Lookup'[Date], "Q")`)* | int64 |

### Customer Lookup (20 columns)

Customer demographic and profile data — drives the Customer page segmentation (occupation, income, education).

| Column | Type |
|---|---|
| CustomerKey | int64 (key) |
| Prefix / FirstName / LastName / Full Name *(calculated)* | string |
| BirthDate / Birth Year *(calculated)* | dateTime / calculated |
| MaritalStatus / Gender | string |
| EmailAddress / Domain Name *(calculated)* | string |
| AnnualIncome / Income Level *(calculated)* | int64 / calculated |
| TotalChildren / Is Parent? *(calculated)* | int64 / calculated |
| EducationLevel / Education Category *(calculated)* | string / calculated |
| Occupation | string |
| HomeOwner | string |
| Customer Priority *(calculated)* | — |

### Product Lookup (14 columns)

Product master data — powers the Product page and every revenue/cost calculation (via `RELATED`).

| Column | Type |
|---|---|
| ProductKey | int64 (key) |
| ProductSubcategoryKey | int64 (hidden, FK) |
| ProductSKU / ProductName / ModelName / ProductDescription | string |
| ProductColor / ProductSize / ProductStyle | string |
| **ProductCost** | decimal — used in `Total Cost` |
| **ProductPrice** | decimal — used in `Total Revenue` and `Adjusted Revenue` |
| SKU Type | string |
| Discounted Price | decimal |
| SKU Category *(calculated)* | — |

### Product Categories Lookup / Product Subcategories Lookup

Two-level category hierarchy feeding the Product page's category and subcategory breakdowns.

| Table | Columns |
|---|---|
| Product Categories Lookup | ProductCategoryKey (hidden), CategoryName |
| Product Subcategories Lookup | ProductSubcategoryKey (hidden), SubcategoryName, ProductCategoryKey (hidden, FK) |

### Territory Lookup (4 columns)

Geographic dimension driving the Map page and the Continent → Country → Region drill.

| Column | Type |
|---|---|
| SalesTerritoryKey | int64 (key) |
| Region / Country / Continent | string |

---

## Parameter Tables

These have **no relationship** to the fact tables — they exist purely to drive interactivity, and measures reference their selected value directly via `SELECTEDVALUE`.

| Table | Drives | Values |
|---|---|---|
| **Price Adjustment (%)** | The pricing scenario slider on the Executive/Product pages | 0% to +50% |
| **Product Metric Selection** | The Product page trend-metric switcher | Orders, Revenue, Profit, Returns, Return % |
| **Customer Metric Selection** | The Customer page view toggle | Active Customers, Avg Revenue per Customer |
| **TopCustomerBy** | The top-10 ranking sort order | Revenue, Orders, Profit |
| **Territory Parameter** | The Map page drill toggle | Continent, Country, Region |
| **Dates Parameter** | The revenue trend granularity switcher | Year, Month, Week, Day |

---

## Supporting Tables

### Measure Table
A hidden table that houses all 150 DAX measures, organized into 8 display folders for fast discovery. See [`DAX_Measures.md`](DAX_Measures.md) for the full reference.

### Time Intelligence (calculation group)
A single calculation group with 10 reusable items — Revenue (base), LM, MoM, MoM %, LY, YoY, YoY %, QTD, YTD, MTD — that apply to whichever base measure is placed in the Executive page's period-comparison matrix, rather than requiring a separate measure per metric per period.

---

## Relationships

| From | To | Type |
|---|---|---|
| Sales Data[TerritoryKey] | Territory Lookup[SalesTerritoryKey] | Many-to-one |
| Sales Data[OrderDate] | Calendar Lookup[Date] | Many-to-one |
| Sales Data[CustomerKey] | Customer Lookup[CustomerKey] | Many-to-one |
| Sales Data[ProductKey] | Product Lookup[ProductKey] | Many-to-one |
| Product Subcategories Lookup[ProductCategoryKey] | Product Categories Lookup[ProductCategoryKey] | Many-to-one |
| Product Lookup[ProductSubcategoryKey] | Product Subcategories Lookup[ProductSubcategoryKey] | Many-to-one |
| Returns Data[TerritoryKey] | Territory Lookup[SalesTerritoryKey] | Many-to-one |
| Returns Data[ProductKey] | Product Lookup[ProductKey] | Many-to-one |
| Returns Data[ReturnDate] | Calendar Lookup[Date] | Many-to-one |

All relationships filter in a single direction, from dimension to fact — the standard star schema pattern that keeps query performance predictable as the model grows.

---

## Why This Design Works

- **Parameter tables stay disconnected** from the sales data, so pricing scenarios, metric toggles, and drill-downs don't risk double-counting or slowing down the core model
- **Two fact tables share the same dimensions** (Calendar, Product, Territory), so returns can always be analyzed in the same terms as sales
- **A two-level product hierarchy** (Category → Subcategory → Product) supports drill-down without flattening everything into one wide table
- **One calculation group replaces what would otherwise be dozens of duplicate measures** — every metric gets last-month, year-over-year, and to-date comparisons without writing that logic more than once

---

**Total Tables:** 16
**Total Relationships:** 9
**Last Updated:** July 2026
