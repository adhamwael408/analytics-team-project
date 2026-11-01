# 🧭 Commerce Compass

### *Turning Data into Smarter Business Decisions*

An interactive **Power BI** sales analytics project that analyzes **100,000 e-commerce orders (2020–2024)** to uncover revenue drivers, financial leakage, discount effectiveness, and customer and market concentration.

Developed for the **Digital Egypt Pioneers Initiative (DEPI)**.

<!-- Add a dashboard screenshot here, e.g.:
![Commerce Compass Dashboard](images/executive-overview.png)
-->

---

## 📑 Table of Contents

1. [Project Overview](#-project-overview)
2. [Key Numbers](#-key-numbers)
3. [Business Questions](#-business-questions)
4. [Data Cleaning & Preparation](#-data-cleaning--preparation)
5. [Data Model (Star Schema)](#-data-model-star-schema)
6. [DAX Measures](#-dax-measures)
7. [Dashboard Pages](#-dashboard-pages)
8. [Key Insights & Recommendations](#-key-insights--recommendations)
9. [Visual Design](#-visual-design)
10. [How to Open the Report](#-how-to-open-the-report)
11. [Tech Stack](#-tech-stack)
12. [Team](#-team)

---

## 📌 Project Overview

Commerce Compass supports business leaders by answering critical questions through interactive dashboards and actionable insights. The report contains **6 visible pages** and **2 hidden pages** (a product drill-through and a tooltip), and ends with a recommendations page that turns analytical findings into practical business strategies.

## 📊 Key Numbers

| Metric | Value |
|---|---|
| Data period | 2020–2024 (5 years) |
| Total sales orders | 100,000 |
| Unique customers | 43,233 |
| Products | 50 |
| Markets | 5 countries |
| Total net sales | **$84.24M** |
| Average order value (AOV) | $842.38 |
| Weighted discount rate | 7.45% |
| Total discounts | $6.78M |
| Lost sales | $5.16M |
| Shared slicers | Year, Country, Category |

## ❓ Business Questions

| # | Page | Question |
|---|---|---|
| 1 | Executive Overview | How is the business performing overall? |
| 2 | Sales & Products | What drives revenue? |
| 3 | Discount Impact | Do discounts increase sales enough to justify their cost? |
| 4 | Customers & Markets | Who are the customers, and where are they located? |
| 5 | Insights & Recommendations | What should the business do next? |

---

## 🧹 Data Cleaning & Preparation

All preparation was done in **Power Query** on 100,000 rows × 20 columns. Column profiling found no missing values and no duplicate rows.

**Type conversions**

- `OrderDate` → Date
- `Quantity` → Whole Number
- `UnitPrice`, `Tax`, `ShippingCost`, `TotalAmount` → Fixed Decimal Number
- `Discount` → Decimal Number (Trim and Clean applied)

**Columns excluded for inconsistency**

| Column | Reason |
|---|---|
| `CustomerName` | Same `CustomerID` appeared with multiple names |
| `Brand` | Ten brands distributed inconsistently across products |
| `City` | City names repeated across different countries |
| `State` | Same geographical inconsistency |

**Calculated columns added**

```
GrossAmount    = Quantity × UnitPrice
DiscountAmount = GrossAmount × Discount
NetSales       = GrossAmount − DiscountAmount
```

- `PriceBand`: classifies products as High, Mid, or Low.
- **Category mapping**: an external Product Mapping file assigns each product to exactly one of six categories.

**Workflow:** `Orders_Clean` is the central query (Enable Load disabled). All fact and dimension tables are derived from it using **Reference**, so changes to the cleaning logic flow through on refresh.

---

## ⭐ Data Model (Star Schema)

One central fact table surrounded by six dimension tables, with all relationships one-to-many (dimension → fact) and single-direction filtering.

| Table | Type | Main Columns | Purpose |
|---|---|---|---|
| `Fact_Orders` | Fact | `Country_Id`, `CustomerID`, `Date_Id`, `Payment_Id`, `ProductID`, `Status_Id`, `SellerID`, `OrderID`, `GrossAmount`, `NetSales`, `TotalAmount` | Order-level transactions and financial values |
| `Dim_Customer` | Dimension | `CustomerID`, `CustomerType`, `OrderCount` | Customer segments and behavior |
| `Dim_Date` | Dimension | `Date_Id`, `OrderDate` | Time-based analysis |
| `Dim_Geography` | Dimension | `Country_Id`, `Country` | Country-level analysis |
| `Dim_OrderStatus` | Dimension | `Status_Id`, `OrderStatus`, `StatusGroup` | Order status and bottlenecks |
| `Dim_Payment` | Dimension | `Payment_Id`, `PaymentMethod`, `PaymentGroup` | Payment preferences |
| `Dim_Product` | Dimension | `ProductID`, `ProductName`, `Category` | Product and category performance |

The model can be extended later, for example with a `Dim_Seller` table.

---

## 🧮 DAX Measures

**Core KPIs:** Total Net Sales, Sales Growth %, Weighted Discount Rate, Cancellation Rate, Lost Sales (cancelled and returned orders), In-Progress Sales (orders with status "Shipped" or "Pending").

```dax
Total Net Sales =
SUM ( Fact_Orders[NetSales] )

Sales Growth % =
DIVIDE (
    [Total Net Sales] - [Previous Period Sales],
    [Previous Period Sales],
    0
)

Discount Rate =
DIVIDE (
    SUM ( Fact_Orders[DiscountAmount] ),
    SUM ( Fact_Orders[GrossAmount] ),
    0
)

Cancel Rate =
DIVIDE ( [Cancelled Orders], [Total Orders], 0 )
```

**Dynamic titles and labels:** `Dashboard Title`, `Country Label`, `Category Label`, `Year Label`. For example, the title updates to `Commerce Compass | 2023 | Canada | Electronics` based on the active filters.

---

## 🖥️ Dashboard Pages

| Page | Purpose / Highlights |
|---|---|
| **Welcome** | Project introduction, high-level KPIs, navigation guide |
| **Executive Overview** | Net Sales $84.24M, 100K orders, Lost Sales $5.16M, dynamic top product, country, and category |
| **Sales & Products** | Top 10 and bottom 10 products, revenue by price band; High Price Band drives **55.7%** of net sales |
| **Discount Impact** | AOV falls from $907 to $665 while average quantity stays at 3.0 units; total discounts $6.78M |
| **Customers & Markets** | Repeat customers are 68.7% of customers and 86.4% of net sales; the US is ~70% of net sales |
| **Insights & Recommendations** | Seven insights with evidence and recommendations; each row navigates to the supporting page |
| *Product Details* (hidden) | Drill-through page: product sales, return rate, discount analysis |
| *Tooltip* (hidden) | Contextual details on hover |

---

## 💡 Key Insights & Recommendations

| # | Insight | Evidence | Recommendation |
|---|---|---|---|
| 1 | Sales are stable but not growing | Net sales stay at ~$16.7M–$17.0M every year (2020–2024) | Explore new acquisition channels, successful categories, and new segments |
| 2 | Repeat customers are the backbone of revenue | 68.7% of customers generate 86.4% of net sales | Invest in loyalty programs, personalized offers, and retention |
| 3 | Discounts reduce order value without increasing quantity | AOV $907 → $665; quantity flat at 3.0; $6.78M in discounts | Set discount limits, pilot on small segments, measure incremental profit |
| 4 | High-priced products drive revenue | High Price Band contributes 55.7% of net sales | Prioritize high-value products in marketing; check profitability first |
| 5 | Many orders remain operationally unresolved | ~19.4% of sales ($16.3M) in "Shipped"/"Pending"; ~3,800 orders from 2020 still open | Audit the order lifecycle and enforce prompt status updates |
| 6 | Revenue is concentrated in one market | US ≈ 70% of net sales | Diversify markets and marketing spend; validate regional demand |
| 7 | Sales leakage is a cross-functional problem | Lost sales $5.16M (~6.1% of net sales); cancellations and returns ~3% each | Investigate root causes across product info, fulfillment, and delivery |

---

## 🎨 Visual Design

A dark, high-contrast **Dark HUD** theme on a 16:9 canvas.

| Element | Color |
|---|---|
| Background (Navy) | `#071C2E` |
| Primary accent (Cyan) | `#2FD6F0` |
| Secondary accent (Teal) | `#0E7C90` |
| Risk / loss (Red) | `#FF5A5F` |
| Text | `#E6F7FB` |

---

## 🚀 How to Open the Report

1. Download the report file: **`Final M Project.pbix`**
2. Open it with **Power BI Desktop** (latest version recommended).
3. Start on the **Welcome** page and use the vertical page navigator.
4. Filter with the shared slicers: **Year**, **Country**, **Category**.
5. For product analysis, right-click a product and choose **Drill-through → Product Details**.
6. Hover over supported charts to see tooltips.

> **Note:** In Power BI Desktop, buttons and navigation actions generally need **Ctrl + Click**. In Power BI Service, a single click is enough.

---

## 🛠️ Tech Stack

- **Microsoft Power BI**: visualization and reporting
- **Power Query**: data cleaning and transformation
- **DAX**: measures and dynamic titles
- **Star Schema**: dimensional data modeling

## 👥 Team

| Member | Role |
|---|---|
| **Adham Wael** | Team Leader: Power BI dashboard design, layout, and project follow-up |
| **Alaa** | DAX Measures |
| **Rouqaya** | Data Cleaning |
| **Youssef** | Data Modeling and Star Schema |
| **Omar** | Business Analysis and Documentation |

---

**Developed for:** Digital Egypt Pioneers Initiative (DEPI)
**Scope:** E-commerce Sales Analytics · Customer Behavior · Discount Impact · Business Intelligence
