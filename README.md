# Adventure Works — Sales & Customer Intelligence Dashboard
### Global Revenue Analytics | Power BI | DAX | Interactive Design

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-0078D4?style=for-the-badge&logo=microsoft&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)

---

## 🔗 Live Dashboard

**[▶ View Live Report on Power BI Service](https://app.powerbi.com/view?r=eyJrIjoiYmQ4NGIzMDctY2FhZC00YzMwLTk4YzYtOWM2NDA2MTRlODJhIiwidCI6Ijg3NTIyNjVhLTMwYTctNDk0MS05YmFhLTQ3NmE3MzE4MGZlMSJ9)**

> ℹ️ **Project Note:** This is a **personal portfolio project** built on the Adventure Works sample dataset (a fictional bicycle manufacturer), used here to simulate a real sales organization. It is not a client engagement — the goal was to design a dashboard leadership could genuinely use to spot growth opportunities, catch problems early, and test decisions before making them.

---

## 📌 Project Overview

**Scenario:** Adventure Works sells bikes and accessories across **three global territories** — North America, Europe, and the Pacific — through multiple channels. Sales, product, and operations teams need one place to answer: *where is revenue coming from, who are our best customers, and where is the business losing money?*

**What This Dashboard Does:** Gives leadership real-time visibility into revenue performance, customer profitability, product performance, and geographic demand — with the tools to act on it: dynamic targets, scenario modeling, and root-cause views for returns.

**Key Capabilities:**
- 🎯 **Revenue tracking against targets**, with a full month-over-month and year-over-year comparison view
- 👥 **Customer profitability ranking** — instantly surface the top 10 accounts by revenue, orders, or profit
- 📦 **Product performance** by category and subcategory, so underperformers are easy to spot
- 🌍 **Geographic demand mapping**, plus a revenue-vs-return-rate view that flags markets at risk
- 🔄 **Returns root-cause analysis** by reason, product, and territory
- ⚙️ **Pricing scenario modeling** — see the profit impact of a price change before making it
- 🎨 **Built for fast decisions** — collapsible filters, one-click metric switching, and inline hover details keep the focus on insight, not navigation

---

## 🎯 Business Questions This Dashboard Answers

| Question | How the Dashboard Answers It | Business Action It Enables |
|----------|------------------------------|-----------------------------|
| **Which products and categories drive revenue?** | Ranked product performance by revenue, orders, and profit | Focus inventory and marketing spend on top performers |
| **Who are our most valuable customers?** | Dynamic top-10 ranking by revenue, orders, or profit | Prioritize account management where it pays off most |
| **Where is demand strongest — and where is it at risk?** | Revenue by continent, country, and region, plus a revenue-vs-return-rate view | Direct sales effort to strong markets; flag high-return markets before they erode margin |
| **How are we tracking against target — this month, last month, last year?** | Monthly trend vs. target with a full period-comparison view (last month, month-over-month, last year, year-over-year, quarter- and year-to-date) | Catch underperformance early, from any time angle |
| **Why are customers returning products?** | Return rate by product, reason, and territory, with the revenue impact quantified | Fix the root cause — quality, sizing, fit — instead of just tracking the symptom |
| **What happens to margin if we adjust pricing?** | A live pricing slider that recalculates revenue and profit instantly | Test pricing decisions before rolling them out |

---

## 📊 Dashboard Structure

### Pages

| # | Page | Built For | What It Shows |
|---|------|-----------|----------------|
| 1 | **Home** | Everyone | Dashboard overview and quick navigation |
| 2 | **Executive** | Leadership | Revenue vs. target, trend, orders by category, plus a one-click toggle into a full period-comparison view (last month, MoM, last year, YoY, QTD, YTD) |
| 3 | **Product** | Product & Marketing | Product-level performance with category/subcategory drill-down and a switchable trend metric |
| 4 | **Customer** | Sales Leadership | Top-10 customer ranking, occupation and income-level breakdowns |
| 5 | **Map** | Regional Leadership | Revenue by continent/country/region, plus a revenue-vs-return-rate view that flags at-risk markets |
| 6 | **Returns** | Operations & Quality | Return rate trend, revenue impact, and root causes by product and territory |
| 7–12 | **Hover Detail Pages** (6) | Everyone | Inline KPI and trend detail on hover — context without leaving the page |

### What Makes It Easy to Use

| Feature | What It Does | Why It's There |
|---------|---------------|-----------------|
| **Collapsible Filter Panel** | Hides or shows filters on every main page | Keeps the focus on data, not filter clutter |
| **Period-Comparison Toggle** | Switches the Executive page between the headline view and a full last-month/MoM/last-year/YoY/QTD/YTD matrix | One click to go from "what happened" to "how does that compare" |
| **Metric Switcher (Product page)** | Swaps the trend chart between Orders, Revenue, Profit, Returns, and Return % | Answers "what's driving this trend?" without switching pages |
| **Top Customer Ranking** | Re-ranks instantly by Revenue, Orders, or Profit | No manual sorting to find who matters most |
| **Territory Drill-Down** | Moves between Continent → Country → Region on the revenue chart | Navigate geography without a separate drill-through page |
| **Date Granularity Switcher** | Toggles the revenue trend between Year, Month, Week, and Day | Right level of detail for the question being asked |
| **Pricing Scenario Slider** | Models the profit impact of a price change from 0% to +50% | Test a pricing decision before committing to it |
| **6 Hover Detail Pages** | Inline KPI cards and trend charts appear on hover | Quick context without breaking flow |
| **Customer View Toggle** | Switches between active customer count and average revenue per customer | Separates "how many customers" from "how valuable are they" |

---

## ⏱️ Period-Over-Period Comparison

The Executive page includes a reusable period-comparison view — last month, month-over-month change, last year, year-over-year change, quarter-to-date, and year-to-date — that works against whichever metric is selected, rather than needing a separate calculation for every metric and every time period. In practice, that means Revenue, Profit, or Orders can all be dropped into the same comparison view without rebuilding the logic behind it.

```dax
-- Month-over-month % change, reusable across any base metric
'MoM %' =
VAR _currentValue = SELECTEDMEASURE()
VAR _previousValue =
    CALCULATE(
        SELECTEDMEASURE(),
        DATEADD('Calendar Lookup'[Date], -1, MONTH)
    )
RETURN
DIVIDE(_currentValue - _previousValue, _previousValue, "-")
```

**Why this matters for the business:** any new metric added to the model — a new revenue line, a new cost category — automatically gets last-month, year-over-year, and to-date comparisons for free, instead of someone having to write and test that logic again each time.

---

## 🧮 DAX Development — 150 Measures, Organized for Fast Discovery

The model includes **150 DAX measures**, grouped into folders so the right measure is easy to find rather than buried in a flat list:

| Group | Measures | What It Covers |
|---|---|---|
| **Visual Formatting** | 40 | Trend arrows, chart labels, axis scaling — makes the visuals communicate at a glance |
| **All Revenue Measures** | 23 | Total Revenue, prior-month/prior-year comparisons, MoM/YoY %, quarter- and year-to-date, pricing-scenario revenue |
| **Top Customer Measures** | 19 | Top customer identification, ranking, and name/revenue/order detail for the top-10 view |
| **All Return Measures** | 19 | Return rate, revenue impact, average return value, monthly return trend |
| **Other Measures** | 17 | Supporting logic — quantities sold, active customers, dynamic titles, filter state |
| **HomePage Measures** | 12 | Dataset summary stats shown on the landing page (total customers, products, territories) |
| **All Profit Measures** | 10 | Total Profit, prior-month comparison, profit target, pricing-scenario profit |
| **All Order Measures** | 7 | Total Orders, prior-month comparison, order target and target gap |

### A Few Measures Worth Highlighting

```dax
-- Revenue: the foundation every other revenue measure builds on
Total Revenue =
SUMX(
    'Sales Data',
    'Sales Data'[OrderQuantity] * RELATED('Product Lookup'[ProductPrice])
)

-- Return Rate: returned units as a share of units sold
Return Rate =
DIVIDE([Quantity Returned], [Quantity Sold], 0)

-- Pricing scenario: what would revenue look like at a different price point?
Adjusted Revenue =
SUMX(
    'Sales Data',
    'Sales Data'[OrderQuantity] *
    (1 + 'Price Adjustment (%)'[Price Adjustment (%) Value] / 100) *
    RELATED('Product Lookup'[ProductPrice])
)

-- Dynamic target: this month's target is last month's actual, +10%
Revenue Target = [Revenue Previous Month] * 1.1
```

*Full reference for all 150 measures, organized by the folders above, is in [`DAX_Measures.md`](DAX_Measures.md).*

---

## 🗄️ Data Model

**Star schema** with 2 fact tables, 6 dimension tables, 6 parameter tables, and 2 supporting tables — 16 tables in total, connected by 9 relationships:

```
FACT TABLES
  • Sales Data     — one row per order line (order date, product, customer, territory, quantity)
  • Returns Data   — one row per returned item (return date, product, territory, quantity, reason)

DIMENSION TABLES
  • Calendar Lookup                — date hierarchy (day, week, month, quarter, year)
  • Customer Lookup                — customer profile (demographics, income, occupation)
  • Product Lookup                 — product detail (name, cost, price, color, size)
  • Product Categories Lookup      — top-level product category
  • Product Subcategories Lookup   — product subcategory, linked to category
  • Territory Lookup               — region, country, continent

PARAMETER TABLES (drive interactivity, no relationship to the data)
  • Price Adjustment (%)           — pricing scenario slider (0% to +50%)
  • Product Metric Selection       — Orders / Revenue / Profit / Returns / Return % toggle
  • Customer Metric Selection      — Active Customers / Avg Revenue per Customer toggle
  • TopCustomerBy                  — Revenue / Orders / Profit ranking toggle
  • Territory Parameter            — Continent / Country / Region drill toggle
  • Dates Parameter                — Year / Month / Week / Day granularity toggle

SUPPORTING TABLES
  • Measure Table        — houses all 150 DAX measures, organized by folder
  • Time Intelligence    — reusable period-comparison logic (see above)
```

*Full column-level detail for every table is in [`Data_Model_Schema.md`](Data_Model_Schema.md).*

**Why this design works:** parameter tables keep pricing sliders, metric toggles, and drill-downs completely separate from the actual sales data, so the interactive features don't slow down the core model or risk double-counting anything.

---

## 📸 Screenshots & Visual Guides

| Page | Filename | Key Elements to Capture |
|------|---------|------------------------|
| Home | `01_home_page.png` | <img width="1384" height="777" alt="01_home_page" src="https://github.com/user-attachments/assets/59aaa7ed-76ee-4d5d-a8fc-fdf5937508e5" />|
| Executive | `02_executive.png` | <img width="1384" height="777" alt="02_executive" src="https://github.com/user-attachments/assets/dcc86ce4-b93a-4f14-af71-966b343b9021" />|
| Product | `03_product.png` | <img width="1384" height="777" alt="03_product" src="https://github.com/user-attachments/assets/a5c183d1-cf51-49f2-986a-291e1a306a0a" />|
| Customer | `04_customer.png` | <img width="1384" height="777" alt="04_customer" src="https://github.com/user-attachments/assets/add1b11c-73c2-494d-bc5b-b7b95017642f" />|
| Map | `05_map.png` | <img width="1384" height="777" alt="05_map" src="https://github.com/user-attachments/assets/a84e009f-5e8b-400e-8341-b941eebaa68a" />|
| Returns | `06_returns.png` | <img width="1384" height="777" alt="06_returns" src="https://github.com/user-attachments/assets/298e7991-9e60-485d-bb4a-60037b3e44f7" />|


https://github.com/user-attachments/assets/fb07e298-3c60-4838-abbf-c1bc25dea4b9


---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|------|---------|
| **Power BI Desktop** | Report authoring, data modeling, DAX |
| **Power Query (M)** | Data cleansing and transformation |
| **DAX** | 150 measures — rankings, scenario modeling, period comparisons, conditional formatting |
| **Bookmarks** | Filter toggles, metric switches, the Executive period-comparison view |
| **Hover Detail Pages** | Inline context without navigating away |

---

## ⚡ Scope & Scale

- **Measures:** 150 DAX measures, organized into 8 folders for fast discovery
- **Pages:** 12 total — 6 main pages, 6 hover-detail pages
- **Bookmarks:** 14 total (7 toggle pairs) — filter panels, metric toggles, and the Executive period-comparison switch
- **Reusable Comparison Logic:** 1 calculation group covering last-month, MoM, last-year, YoY, QTD, and YTD in one place
- **Tables:** 16, connected by 9 relationships in a star schema

---

## 💡 What This Project Demonstrates

1. **Turning business questions into a working tool** — every page maps to a question a sales or ops leader would actually ask
2. **Scenario planning built in** — the pricing slider lets a decision get tested before it's made, not after
3. **Reusable logic over repetition** — one period-comparison structure serves every metric instead of duplicating the same calculation for each one
4. **Fast, low-friction navigation** — collapsible filters, one-click metric switching, and hover details keep users focused on insight
5. **Root-cause thinking on returns** — not just "returns went up," but which product, which reason, which region
6. **Clean, discoverable structure** — 150 measures organized into folders so the model stays usable as it grows

---

## 📁 Repository Structure

```
adventure-works-sales-intelligence/
│
├── README.md (this file)
├── Live Dashboard Link (https://app.powerbi.com/...)
├── DAX_Measures.md              ← Reference for all 150 measures, grouped by folder
├── Data_Model_Schema.md         ← Full table and column-level model documentation
├── Screenshots/
│   ├── 01_home_page.png
│   ├── 02_executive.png
│   ├── 03_product.png
│   ├── 04_customer.png
│   ├── 05_map.png
│   └── 06_returns.png
└── Theme/
    └── Portfolio_Theme.json     ← Import-ready Power BI theme
```

---

## 👤 Author & Contact

**Naved Anjum** — Senior MIS Reporting Manager | Power BI Developer

📧 **Email:** navedanjum1989@gmail.com
🔗 **LinkedIn:** [linkedin.com/in/navedanjum1989](https://linkedin.com/in/navedanjum1989)
🔗 **GitHub:** [github.com/navedanjumnoddy](https://github.com/navedanjumnoddy)
💼 **Portfolio:** [GitHub Projects](https://github.com/navedanjumnoddy)

---

## 📚 References & Acknowledgments

- **Dataset:** Adventure Works — a publicly available Microsoft sample dataset, commonly used for BI practice
- **Architecture:** Star schema design following Kimball methodology

---

**Last Updated:** July 2026
**Status:** Complete | Personal Portfolio Project | Live Dashboard Accessible
