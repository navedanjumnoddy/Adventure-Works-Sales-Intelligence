# Adventure Works - Data Model Schema

Comprehensive documentation of the star schema data model powering the Adventure Works Sales & Customer Intelligence Power BI dashboard.

## Data Model Overview

**Schema Type:** Star Schema  
**Pattern:** Fact-Dimension architecture  
**Fact Tables:** 2 (Sales Orders, Returns)  
**Dimension Tables:** 5 (Date, Product, Customer, SalesPerson, Territory)  
**Total Records:** 500K+ transactions  
**Architecture Optimization:** Power BI Import mode with relationships  

### Schema Diagram
```
                    ┌──────────────────┐
                    │  SalesOrderDetail│
                    │    (Main Fact)   │
                    └──────────────────┘
                            │
        ┌───────────────┬────┼────┬──────────────┐
        │               │    │    │              │
    ┌────────┐    ┌──────────┐ ┌─────────┐  ┌──────────┐
    │  Date  │    │ Product  │ │Customer │  │SalesPerson
    └────────┘    └──────────┘ └─────────┘  └──────────┘
        │               │            │            │
        │               └────────────┴────────────┤
        │                                        │
        └────────────────────────────────────────┤
                         ┌─────────────────┐     │
                         │   Territory     │◄────┘
                         └─────────────────┘

                    ┌─────────────────┐
                    │   Returns       │
                    │   (Fact Table)  │
                    └─────────────────┘
                            │
                    ┌───────┴───────┐
                    │               │
              ┌──────────┐    ┌────────────┐
              │ Product  │    │   Date     │
              └──────────┘    └────────────┘
```

---

## Fact Tables

### SalesOrderDetail (Primary Fact Table)

**Purpose:** Core sales transaction data  
**Granularity:** One row per line item  
**Record Count:** 500,000+  
**Update Frequency:** Daily  

#### Columns:

| Column | Type | Description | Business Use |
|---|---|---|---|
| **SalesOrderID** | Integer | PK, Order reference | Transaction grouping |
| **SalesOrderDetailID** | Integer | PK, Line item ID | Unique identifier |
| **ProductID** | Integer | FK to Product | Product linking |
| **CustomerID** | Integer | FK to Customer | Customer linking |
| **SalesPersonID** | Integer | FK to SalesPerson | Rep assignment |
| **OrderDateID** | Integer | FK to Date | Transaction date |
| **ShipDateID** | Integer | FK to Date | Fulfillment tracking |
| **OrderQty** | Integer | Quantity sold | Volume metric |
| **UnitPrice** | Decimal | Price per unit | Revenue component |
| **ProductStandardCost** | Decimal | Cost per unit | COGS calculation |
| **UnitPriceDiscount** | Decimal | Discount rate | Discount tracking |
| **LineTotal** | Decimal | Extended amount | Order total |
| **Status** | Text | Order status | Fulfillment tracking |
| **OrderStatus** | Text | Complete/Pending | Status categorization |
| **SalesChannel** | Text | Online/Reseller | Channel analysis |

#### Key Relationships:
- SalesOrderDetail.ProductID → Product.ProductID (M:1)
- SalesOrderDetail.CustomerID → Customer.CustomerID (M:1)
- SalesOrderDetail.SalesPersonID → SalesPerson.SalesPersonID (M:1)
- SalesOrderDetail.OrderDateID → Date.DateID (M:1)
- SalesOrderDetail.ShipDateID → Date.DateID (M:1)

#### Aggregations:
- Sum: OrderQty, UnitPrice, ProductStandardCost, LineTotal
- Count: SalesOrderID (distinct for order count)

---

### Returns (Secondary Fact Table)

**Purpose:** Product returns and refund tracking  
**Granularity:** One row per returned item  
**Record Count:** 50,000+  
**Update Frequency:** Daily  

#### Columns:

| Column | Type | Description |
|---|---|---|
| **ReturnID** | Integer | PK, Return identifier |
| **ProductID** | Integer | FK to Product |
| **ReturnQty** | Integer | Quantity returned |
| **ProductPrice** | Decimal | Item refund amount |
| **ReturnAmount** | Decimal | Total refund |
| **ReturnDateID** | Integer | FK to Date |
| **ReturnReasonCode** | Text | Return reason |
| **ReturnStatus** | Text | Processed/Pending |
| **RestockingFee** | Decimal | Fee charged |

#### Relationships:
- Returns.ProductID → Product.ProductID (M:1)
- Returns.ReturnDateID → Date.DateID (M:1)

---

## Dimension Tables

### Date (Time Dimension)

**Purpose:** Enable time intelligence and temporal analysis  
**Granularity:** Daily  
**Date Range:** 2015-01-01 to 2025-12-31  
**Record Count:** ~4,000 rows  

#### Columns:

| Column | Type | Description | Example |
|---|---|---|---|
| **DateID** | Integer | PK | 20260626 |
| **Date** | Date | Full date | 2026-06-26 |
| **Year** | Integer | Calendar year | 2026 |
| **Quarter** | Integer | Quarter (1-4) | 2 |
| **Month** | Integer | Month (1-12) | 6 |
| **MonthName** | Text | Month name | June |
| **Week** | Integer | Week of year | 26 |
| **DayOfWeek** | Integer | Weekday (1-7) | 5 |
| **DayName** | Text | Day name | Friday |
| **IsWeekend** | Boolean | Weekend flag | FALSE |
| **FiscalYear** | Integer | Fiscal year | 2026 |
| **QuarterName** | Text | Quarter label | Q2 2026 |

#### Usage:
- YTD/MTD/QTD calculations
- Time intelligence functions
- Fiscal period grouping
- Trend analysis

---

### Product (Product Dimension)

**Purpose:** Product master data and hierarchy  
**Granularity:** Individual SKU  
**Record Count:** 500+ products  

#### Columns:

| Column | Type | Description | Example |
|---|---|---|---|
| **ProductID** | Integer | PK | 1001 |
| **ProductName** | Text | Product title | "Road-150" |
| **ProductNumber** | Text | SKU | "BK-R205-60" |
| **Category** | Text | Main category | "Bikes" |
| **Subcategory** | Text | Sub-category | "Road Bikes" |
| **Brand** | Text | Manufacturer | "AdventureWorks" |
| **Color** | Text | Product color | "Red" |
| **Size** | Text | Product size | "58" |
| **ListPrice** | Decimal | MSRP | 1431.50 |
| **ProductCost** | Decimal | Standard cost | 350.00 |
| **ProductLine** | Text | Product line | "M" (Mountain/Road) |
| **Class** | Text | Product class | "L" (Low/Mid/High) |
| **Style** | Text | Design style | "Unisex" |
| **ModelYear** | Integer | Year introduced | 2025 |
| **ProductStatus** | Text | Active/Obsolete | "Active" |
| **StockLevel** | Integer | Current inventory | 150 |
| **SafetyStockLevel** | Integer | Minimum inventory | 20 |
| **DiscontinuedDate** | Date | End of life | NULL |

#### Hierarchy:
```
Category → Subcategory → Brand → Product Line → Product
```

#### Usage:
- Product-level analysis
- Portfolio management
- Profitability tracking
- Inventory monitoring

---

### Customer (Customer Dimension)

**Purpose:** Customer master data and segmentation  
**Granularity:** Individual customer  
**Record Count:** 20,000+ customers  

#### Columns:

| Column | Type | Description | Example |
|---|---|---|---|
| **CustomerID** | Integer | PK | 29485 |
| **CustomerName** | Text | Full name | "John Smith" |
| **EmailAddress** | Text | Email | "john@email.com" |
| **City** | Text | City | "San Francisco" |
| **StateProvinceName** | Text | State | "California" |
| **CountryRegionName** | Text | Country | "United States" |
| **PostalCode** | Text | Zip code | "94105" |
| **CustomerType** | Text | Individual/Store | "Individual" |
| **FirstPurchaseDate** | Date | Initial purchase | 2015-03-15 |
| **LastPurchaseDate** | Date | Most recent | 2026-06-15 |
| **AnnualIncome** | Decimal | Income bracket | 75000 |
| **TotalSpent** | Decimal | Lifetime value | 5250 |
| **TotalOrders** | Integer | Order count | 12 |
| **AverageOrderValue** | Decimal | AOV | 437.50 |
| **SegmentRank** | Integer | RFM segment | 1 (Top) |

#### Customer Segments:
- **Premium:** High frequency, high value, recent purchase
- **Gold:** Medium-high value, regular purchases
- **Silver:** Medium value, occasional purchases
- **Bronze:** Low value or inactive

#### Usage:
- Customer analytics
- RFM segmentation
- Churn prediction
- Targeted marketing

---

### SalesPerson (Sales Rep Dimension)

**Purpose:** Sales team structure and performance tracking  
**Granularity:** Individual salesperson  
**Record Count:** 300+ representatives  

#### Columns:

| Column | Type | Description | Example |
|---|---|---|---|
| **SalesPersonID** | Integer | PK | 275 |
| **FirstName** | Text | First name | "Michael" |
| **LastName** | Text | Last name | "Blythe" |
| **FullName** | Text | Full name | "Michael Blythe" |
| **JobTitle** | Text | Position | "Sales Representative" |
| **ManagerID** | Integer | Direct manager | 268 |
| **ManagerName** | Text | Manager name | "Jillian Carson" |
| **TerritoryID** | Integer | Primary territory | 1 |
| **CommissionPct** | Decimal | Commission rate | 1.5 |
| **Bonus** | Decimal | Annual bonus | 5000 |
| **YTDSales** | Decimal | Year-to-date revenue | 125000 |
| **LastYearSales** | Decimal | Previous year | 150000 |
| **HireDate** | Date | Hire date | 2011-05-12 |
| **TerminationDate** | Date | End date | NULL |
| **RepStatus** | Text | Active/Inactive | "Active" |

#### Hierarchy:
```
Manager → Sales Rep → Territory → Customers
```

#### Usage:
- Sales rep performance
- Commission tracking
- Hierarchical reporting
- Territory management

---

### Territory (Geographic Dimension)

**Purpose:** Geographic sales regions and territories  
**Granularity:** Sales territory  
**Record Count:** 50+ territories  

#### Columns:

| Column | Type | Description | Example |
|---|---|---|---|
| **TerritoryID** | Integer | PK | 1 |
| **TerritoryName** | Text | Territory name | "Northeast" |
| **Region** | Text | Large region | "North America" |
| **SubRegion** | Text | Sub-region | "Northeast Coast" |
| **CountryRegion** | Text | Country | "United States" |
| **SalesYTD** | Decimal | Year-to-date sales | 3000000 |
| **SalesLastYear** | Decimal | Previous year | 2800000 |
| **TerritoryManager** | Text | Manager name | "Steven Kannan" |
| **GrowthRate** | Decimal | YoY growth | 7.1% |

#### Hierarchy:
```
Region → SubRegion → Territory
```

#### Usage:
- Geographic performance
- Regional trends
- Territory assignments
- Growth analysis

---

## Data Quality & Governance

### Data Integrity Checks
✅ **Referential Integrity:** All FKs have matching PKs  
✅ **No Orphans:** No fact rows with missing dimension keys  
✅ **Date Coverage:** Continuous dates, no gaps  
✅ **Cardinality:** Verified M:1 relationships  
✅ **Duplicates:** Checked and removed  

### Quality Metrics
- **Fact Table Rows:** 500K verified
- **Date Range:** 2015-2025 complete years
- **Customer Dedup:** 20K unique customers
- **Product Count:** 500+ active SKUs
- **Data Freshness:** Daily refresh

---

## Performance Optimization

### Indexes & Keys
- All PKs indexed for lookup performance
- All FKs indexed for relationship traversal
- Date table optimized for time intelligence
- Fact table partitioned by year

### Best Practices Applied
✅ Star schema (vs. snowflake) for simplicity  
✅ Columnar compression in Power BI  
✅ Aggregation tables for large measures  
✅ Role-playing dimensions (Date used twice)  
✅ Deactivated relationships for flexibility  

### Query Performance Tips
1. Filter by Date first (reduces fact table volume)
2. Use CALCULATE with specific dimensions
3. Leverage aggregations for large datasets
4. Avoid unnecessary FILTER functions
5. Use CONTAINS for multi-column lookups

---

## Relationships Summary

| Relationship | Type | Cardinality | Active |
|---|---|---|---|
| SalesOrderDetail → Product | FK | M:1 | Yes |
| SalesOrderDetail → Customer | FK | M:1 | Yes |
| SalesOrderDetail → SalesPerson | FK | M:1 | Yes |
| SalesOrderDetail → Date (Order) | FK | M:1 | Yes |
| SalesOrderDetail → Date (Ship) | FK | M:1 | No |
| Returns → Product | FK | M:1 | Yes |
| Returns → Date | FK | M:1 | Yes |
| SalesPerson → Territory | FK | M:1 | Yes |
| Customer → Territory | FK | M:1 | No |

**Note:** Deactivated relationships available for advanced analysis via USERELATIONSHIP()

---

## Data Dictionary

**Dimensions:** 5 tables, 85+ columns  
**Facts:** 2 tables, 20+ measures  
**Total Tables:** 7  
**Total Columns:** 100+  
**Estimated Size:** 200MB-500MB in Power BI  
**Last Updated:** June 2026  

---

**Schema Version:** 2.0  
**Optimization Level:** Production-Ready  
**Design Pattern:** Star Schema (Dimensional Model)
