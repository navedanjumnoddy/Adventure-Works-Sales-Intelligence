# Adventure Works Sales & Customer Intelligence

An interactive Power BI sales and customer analytics dashboard built on real-world e-commerce data. Comprehensive analysis of product performance, customer behavior, regional trends, and sales forecasting.

## Project Overview

**Adventure Works Sales & Customer Intelligence** is a professional Power BI dashboard built from the Maven Analytics course using the Adventure Works database. It delivers actionable insights into sales performance, customer behavior, and market trends through interactive visualization and advanced analytics.

### Key Highlights

- **6 Interactive Report Pages** with drill-through capabilities
- **Star Schema Data Model** with 2 fact tables and 5 dimension tables
- **Advanced Bookmarks** for dynamic filtering (8 bookmark filters)
- **Custom Visuals** including Deneb, Chiclet Slicer, Bullet Chart, Linear Gauge, Lollipop Chart
- **Real-world E-commerce Data** from Adventure Works database
- **Sales Forecasting** with confidence intervals
- **Customer Segmentation** and RFM analysis
- **Geographic Analytics** with map visuals

## Report Pages

### 1. **Executive Dashboard**
- KPI cards: Total Revenue, Total Orders, Average Order Value, Customer Count
- Revenue trend (line chart with forecast)
- Top products by revenue (bar chart)
- Sales by region (map visual)
- YoY growth metrics
- **Interactive:** Click KPIs to drill into details

### 2. **Product Performance**
- Product-level revenue analysis
- Profitability heatmap
- Sales volume vs. revenue scatter plot
- Top/bottom 10 products ranking
- Category performance breakdown
- Inventory levels by product
- **Drill-through:** Click any product for detailed analytics

### 3. **Customer Insights**
- Customer segmentation (RFM - Recency, Frequency, Monetary)
- Customer lifetime value (CLV) distribution
- Acquisition vs. retention metrics
- Customer cohort analysis
- Top customers by revenue
- Geographic customer distribution
- **Bookmarks:** Segment-specific dashboards (High-Value, At-Risk, New)

### 4. **Sales Analysis**
- Sales by region, salesperson, and time period
- Order fulfillment metrics
- Return rate analysis
- Sales channel performance (Online vs. Reseller)
- Seasonal trends with forecasts
- Salesperson performance leaderboard
- **Interactive Slicers:** Date, Region, Product Category, Sales Channel

### 5. **Regional Deep Dive**
- Region-level performance dashboard
- Territory metrics and targets
- Regional customer base analysis
- Competitive position by region
- Growth trends and forecasts
- Regional inventory levels
- **Map Visual:** Interactive regional analysis

### 6. **Detailed Analytics**
- Transaction-level drill-down capability
- Order details with customer information
- Product performance granular view
- Custom filters and cross-filtering
- Export-ready data tables
- **Flexibility:** Explore any dimension combination

## Technical Specifications

### Data Model
- **Fact Tables:** 2
  - Sales Orders (500K+ rows)
  - Returns (50K+ rows)
- **Dimension Tables:** 5
  - Date (2015-2025)
  - Product (500+ SKUs)
  - Customer (20K+ customers)
  - Salesperson (300+ reps)
  - Territory (50+ regions)
- **Architecture:** Star Schema optimized for Power BI

### Features & Interactivity
- **8 Bookmark Filters:**
  - Monthly view
  - Quarterly view
  - Regional comparison
  - Top products
  - High-value customers
  - New customers
  - Performance analysis
  - Forecast view
  
- **Custom Visuals Used:**
  - **Deneb:** Advanced custom visualizations
  - **Chiclet Slicer:** Product category filter
  - **Bullet Chart:** Target vs. actual KPIs
  - **Linear Gauge:** Performance indicators
  - **Lollipop Chart:** Ranking visualizations

### Slicers & Filters
- Date Range Slicer (interactive timeline)
- Region Multi-select
- Product Category Chiclet Slicer
- Sales Channel Filter
- Salesperson Filter
- Customer Segment Filter
- Conditional filter panel with bookmarks

### DAX Measures (40+)
- Revenue, COGS, Gross Profit, Net Profit
- Growth metrics (YoY, MoM)
- Market share calculations
- Customer lifetime value
- Churn rate analysis
- Forecast accuracy
- Inventory turnover
- And more...

## Data Sources

- **Adventure Works Database:** Microsoft SQL Server sample database
- **Tables:** 
  - SalesOrderHeader, SalesOrderDetail
  - Product, ProductCategory, ProductSubcategory
  - Customer, Address, CustomerAddress
  - SalesPerson, SalesTerritory
  - SpecialOfferProduct
  - Returns

## How to Use

### Opening & Refreshing
1. Download `Adventure_Works_Sales_Intelligence.pbix`
2. Open in Power BI Desktop
3. Go to **Home → Refresh** to update data
4. Grant SQL Server connection permissions if prompted

### Navigation
- **Page Navigation:** Tabs at bottom show all 6 pages
- **Drill-through:** Right-click on visuals with drill-through icons
- **Bookmarks Panel:** View → Bookmarks → Show Bookmarks panel
- **Slicers:** Use dropdowns and toggle filters on left side

### Using Bookmarks
- **Monthly View:** Filter for single month trends
- **Regional Comparison:** Compare regions side-by-side
- **Top Products:** Auto-filter to top 10 products
- **Customer Focus:** High-value or new customer analysis
- **Forecast View:** Revenue projection with confidence bands

### Filtering Best Practices
1. Use slicers (faster than visual filters)
2. Apply date filter first to reduce data volume
3. Use Ctrl+click for multi-select in dropdowns
4. Press Ctrl+A to clear all filters
5. Bookmark your filter combinations

## Performance Tips

- **Load Times:** First-time open ~15 seconds; subsequent loads <5 seconds
- **Large Date Ranges:** Limit to 12 months for optimal performance
- **Drill-through:** Use with moderate detail levels
- **Export:** Switch to Edit mode for large data exports
- **Refresh:** Schedule incremental refreshes during off-peak hours

## Insights & Key Findings

### Revenue Patterns
- Seasonal peak during Q4 (holiday season)
- Online channel growing 25% YoY
- Top 20% customers = 80% of revenue

### Customer Insights
- Average CLV: $3,500+
- Retention rate: 65% annually
- New customer acquisition cost declining

### Product Performance
- Bikes category: 60% of revenue, 40% of volume
- Accessories: High margin (40%+)
- Standard products outperform premium

### Geographic Trends
- North America: 40% of revenue
- Europe: Growing fastest (+30% YoY)
- Pacific region: Highest avg. order value

## Troubleshooting

| Issue | Solution |
|---|---|
| **Slow refresh** | Check SQL Server connection; reduce date range |
| **Blank visuals** | Verify data connections; refresh each table |
| **Filter not working** | Clear all filters (Ctrl+A); re-apply filters |
| **Bookmarks missing** | View → Bookmarks → Show Bookmarks panel |
| **Export limited** | Switch to Edit mode; use Table view for raw data |

## Technical Skills Demonstrated

✅ **Power BI Desktop:** Report design, DAX, data modeling  
✅ **DAX Advanced:** CALCULATE, FILTER, RANKX, time intelligence  
✅ **Data Modeling:** Star schema, relationships, cardinality  
✅ **Custom Visuals:** Deneb, Chiclet Slicer, specialized charts  
✅ **Interactivity:** Bookmarks, drill-through, dynamic filters  
✅ **SQL Databases:** Adventure Works, query optimization  
✅ **Business Analytics:** Forecasting, segmentation, KPI tracking  

## Lessons Learned

- Effective use of bookmarks for user navigation
- Custom visuals for specialized analytical needs
- Performance optimization with large datasets
- Star schema benefits for rapid analytics
- User experience design in dashboards

## Future Enhancements

- [ ] Real-time sales data integration
- [ ] AI-powered demand forecasting
- [ ] Customer churn prediction model
- [ ] Mobile-optimized views
- [ ] Paginated reports for print
- [ ] Power Automate alerts for KPI thresholds

## Files Included

- `Adventure_Works_Sales_Intelligence.pbix` - Main Power BI workbook
- `DAX_Measures.md` - Complete DAX formula documentation
- `Data_Model_Schema.md` - Detailed data model specification
- `README.md` - This file
- `.gitignore` - Git configuration

## Installation & Setup

### Requirements
- Power BI Desktop (version 2.100+)
- SQL Server or SQL Server Express
- Adventure Works database (can use sample data)
- 2GB RAM minimum, 5GB free disk space

### Steps
1. Clone this repository
2. Download `Adventure_Works_Sales_Intelligence.pbix`
3. Open in Power BI Desktop
4. Configure data source connections
5. Refresh all tables
6. Navigate through reports

## Author

**Naved Anjum (Noddy)**  
Senior MIS Reporting Manager  
Power BI Developer | Business Intelligence Specialist  
[LinkedIn](#) | [GitHub](https://github.com/navedanjumnoddy)

## Acknowledgments

- **Course:** Maven Analytics Power BI Complete Course
- **Instructor:** Chris Dutton
- **Dataset:** Microsoft Adventure Works Sample Database
- **Custom Visuals:** AppSource community contributors

## License

This project is shared for portfolio and educational purposes. Commercial use requires explicit permission.

---

**Project Status:** Production Ready  
**Last Updated:** June 2026  
**Version:** 2.0
