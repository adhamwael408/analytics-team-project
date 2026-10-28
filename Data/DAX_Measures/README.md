# Commerce Compass
## DAX Measures Development

This document describes the DAX measures developed for the **Commerce Compass** e-commerce analytics dashboard. The work focuses on the calculation layer used by report visuals, KPI cards, performance indicators, customer behavior analysis, order-status metrics, and dynamic dashboard text.

---

## 1. Role and Responsibilities

**Role:** DAX Measures Developer  
**Project:** Commerce Compass  
**Power BI file:** `E-Commerce_Modeling&Schema.pbix`

The responsibility was to continue the project after the data model had been prepared and develop the DAX calculation layer.

The work included:

- Reviewing the available fact and dimension tables and identifying fields required for calculations.
- Creating reusable DAX measures rather than repeating calculations in individual visuals.
- Building core aggregation measures for sales, orders, quantities, discounts, shipping costs, and customers.
- Developing KPI measures for growth, returns, cancellations, customer loyalty, and contribution percentages.
- Creating dynamic measures for dashboard titles, subtitles, and labels that respond to report filters.
- Organizing measures for reuse in cards, charts, tables, and other visuals.

## 2. Basic Aggregation Measures

These measures provide the core calculations used by more advanced KPIs.

| Measure | Calculation / Purpose |
|---|---|
| **Total Net Sales** | Sums `Fact_Orders[NetSales]` to calculate net sales. |
| **Total GrossAmount** | Sums `Fact_Orders[GrossAmount]` to calculate gross amount. |
| **Total DiscountAmount** | Sums `Fact_Orders[DiscountAmount]` to calculate total discounts. |
| **Total Revenue** | Sums `Fact_Orders[TotalAmount]` to calculate total revenue based on that field. |
| **Total Quantity** | Sums ordered quantities in `Fact_Orders[Quantity]`. |
| **Total ShippingCost** | Sums shipping costs in `Fact_Orders[ShippingCost]`. |
| **Total Orders** | Counts distinct order IDs using `DISTINCTCOUNT(Fact_Orders[OrderID])`. |
| **Unique Customers** | Counts distinct customer IDs in `Dim_Customer[CustomerID]`. |
| **Unique Products** | Counts distinct product IDs in `Fact_Orders[ProductID]`. |
| **Unique Sellers** | Counts distinct seller IDs in `Fact_Orders[SellerID]`. |

### Average Measures

The project also includes:

- `Average GrossAmount`
- `Average DiscountAmount`
- `Average NetSales`
- `Average Quantity`
- `Average ShippingCost`
- `Average UnitPrice`
- `Avg Discount`

**Interpretation Note:** These `AVERAGE` measures calculate the average across rows in the relevant column. They should not automatically be described as averages per order unless the data model and calculation specifically support that interpretation.

## 3. Sales and Discount Analysis

| Measure | Purpose |
|---|---|
| **Discount Rate** | Divides total discount amount by total gross amount; returns 0 if the denominator is zero. |
| **Discounted Orders %** | Calculates the share of total orders associated with rows where `Discount` is greater than zero. |
| **Sales Contribution %** | Calculates net sales as a share of net sales across all products, removing the product filter. |
| **Country Sales Contribution %** | Calculates net sales as a share of net sales across geography selections, removing filters from `Dim_Geography`. |
| **Payment Sales Contribution %** | Calculates net sales as a share of net sales across payment selections, removing filters from `Dim_Payment`. |
| **In Progress Sales** | Calculates net sales for records grouped as `In Progress` in `Dim_OrderStatus[StatusGroup]`. |
| **Lost Sales** | Calculates net sales for records grouped as `Lost` in `Dim_OrderStatus[StatusGroup]`. |

These measures help compare sales performance, discounts, and the contribution of product, country, or payment selections.

## 4. Time-Based Performance Measures

| Measure | Purpose |
|---|---|
| **Previous Period Sales** | Calculates net sales for the corresponding period in the previous year using `SAMEPERIODLASTYEAR`. |
| **Sales Growth %** | Calculates the percentage change in net sales compared with Previous Period Sales. |
| **Orders YoY %** | Calculates the percentage change in total orders compared with the corresponding period in the previous year. |
| **AOV YoY %** | Calculates the year-over-year percentage change in `Average NetSales`. |

These calculations compare the currently selected period with the corresponding period one year earlier. They depend on the date context provided by `Dim_Date[OrderDate]`.

**Important Note:** `AOV YoY %` is currently based on `Average NetSales`. It represents the year-over-year change in that average measure and should only be called true Average Order Value if the measure is defined at order level.

## 5. Order Status Measures

| Measure | Purpose |
|---|---|
| **Cancelled Orders** | Counts orders with the `Cancelled` status by applying a filter to `Dim_OrderStatus[OrderStatus]`. |
| **Cancel Rate** | Divides cancelled orders by total orders. |
| **Returned Orders** | Counts orders with the `Returned` status. |
| **Return Rate** | Divides returned orders by total orders. |

These measures provide visibility into cancelled and returned orders and their rates relative to total orders.

## 6. Customer Behavior Measures

| Measure | Purpose |
|---|---|
| **Repeat Customers** | Counts customer IDs with more than one row in `Fact_Orders` within the current filter context. |
| **Repeat Customer %** | Divides repeat customers by unique customers. |
| **One-time Customers** | Calculates unique customers minus repeat customers. |
| **One-time Customers Net Sales** | Calculates net sales for customer IDs whose fact-table row count is one in the current filter context. |
| **Orders per Customer** | Divides total orders by unique customers. |

These measures support analysis of repeat purchasing and customer activity.

**Implementation Note:** The current repeat-customer and one-time-customer calculations use fact-table row counts. If an order can produce multiple fact rows, for example one row per product, customer classification may need to be adjusted to count distinct order IDs rather than fact rows.

## 7. Dynamic Dashboard Measures

These measures create dashboard text that responds to the report's filter context.

| Measure | Purpose |
|---|---|
| **Year Label** | Shows one year when the selected date context contains a single year, or a year range when it contains multiple years. |
| **Country Label** | Shows the selected country, `All Markets`, or the number of selected markets. |
| **Category Label** | Shows the selected category, `All Categories`, or the number of selected categories. |
| **Dashboard Title** | Combines the dashboard name with dynamic year, country, and category labels. |
| **Dashboard Subtitle** | Combines net sales in millions, total orders, and average net sales into one subtitle string. |
| **Title Sales** | Creates a sales title using the selected year when filtered, or the default range `2020–2024` otherwise. |

### How the Dynamic Labels Work

- `Year Label` reads the minimum and maximum years in the current date context.
- `Country Label` and `Category Label` compare the number of selected values with the total available values and return a suitable label.
- `Dashboard Title` combines the dynamic labels into one title.
- `Dashboard Subtitle` uses `FORMAT` to display sales and order figures as readable text.

These measures make report headings and labels respond to the viewer's selections.

## 8. Examples of DAX Logic

### Example 1 — Total Net Sales

```dax
Total Net Sales = SUM(Fact_Orders[NetSales])
```

This measure adds the values in the `NetSales` column. It can be reused in KPI cards, charts, and other visuals, and responds to report filters.

### Example 2 — Sales Growth %

```dax
Sales Growth % =
DIVIDE(
    [Total Net Sales] - [Previous Period Sales],
    [Previous Period Sales],
    0
)
```

The calculation subtracts previous-period sales from current net sales, then divides the difference by previous-period sales. `DIVIDE` returns 0 when the denominator is zero.

### Example 3 — Cancel Rate

```dax
Cancel Rate =
DIVIDE(
    [Cancelled Orders],
    [Total Orders]
)
```

This measure divides cancelled orders by total orders.

### Example 4 — Repeat Customer %

```dax
Repeat Customer % =
DIVIDE(
    [Repeat Customers],
    [Unique Customers]
)
```

This measure expresses repeat customers as a proportion of unique customers, based on the definitions used in the underlying measures.

## 9. DAX Functions Used

| Function | Use |
|---|---|
| `SUM` | Adds numeric values. |
| `AVERAGE` | Calculates a column average. |
| `DISTINCTCOUNT` | Counts unique IDs. |
| `CALCULATE` | Evaluates a measure under a modified filter context. |
| `DIVIDE` | Divides values with optional handling for a zero denominator. |
| `SAMEPERIODLASTYEAR` | Shifts the selected date context to the corresponding period in the previous year. |
| `REMOVEFILTERS` | Removes filters from a specified table or column context. |
| `FILTER` and `VALUES` | Build filtered sets of values for customer-level calculations. |
| `COUNTROWS` | Counts rows in a table expression. |
| `SUMX` | Iterates over a table and sums an expression. |
| `VAR` and `RETURN` | Store intermediate values and return a result. |
| `SWITCH`, `IF`, `SELECTEDVALUE`, `ISFILTERED`, `MIN`, `MAX`, `YEAR`, `FORMAT` | Support conditional logic and dynamic labels. |

## 10. Deliverables and Outcome

The DAX work provides a reusable set of measures for the Commerce Compass dashboard, covering:

- Core sales, revenue, order, quantity, discount, shipping, and customer metrics.
- Sales and order year-over-year comparisons.
- Cancellation and return counts and rates.
- Repeat-purchase indicators for customer analysis.
- Product, geography, and payment contribution calculations.
- Dynamic titles, subtitles, and selection labels.

The measures are intended to support KPI cards and analytical visuals while responding to relevant report filters.

## 11. Suggested Repository Contents

A contribution folder may contain:

```text
Commerce-Compass/
├── README.md
├── DAX_Measures.txt
├── DAX_Measures.dax
└── screenshots/
```

- `README.md` — documentation of the DAX work.
- `DAX_Measures.txt` or `DAX_Measures.dax` — complete DAX definitions, if the project team stores the measure code separately.
- `screenshots/` — optional screenshots of KPI cards or visuals, if required by the project submission.

### Scope Clarification

This README documents the DAX measures and dynamic dashboard text. It does not claim ownership of data modeling, table creation, relationships, or the entire dashboard unless those tasks were separately assigned and completed.

---

*Project documentation title: Commerce Compass.*

