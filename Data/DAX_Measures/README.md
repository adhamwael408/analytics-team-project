Commerce Compass
DAX Measures Development
Commerce Compass is an e-commerce analytics dashboard supported by a
reusable set of DAX measures. This document describes the calculation layer
developed for the Power BI report, including sales performance, order status,
customer behavior, contribution analysis, and dynamic dashboard text.
Contribution scope: DAX measures and dynamic dashboard text. This
documentation does not claim ownership of the data model, table creation,
relationships, or the entire dashboard unless those tasks were separately
assigned and completed.
1. Role and Responsibility
Role: DAX Measures Developer
Project: Commerce Compass
Power BI model file referenced in the original documentation:
E-Commerce_Modeling&Schema.pbix
The work continued after the data model had been prepared. It focused on
the DAX calculation layer used to calculate business metrics, performance
indicators, customer behavior, order-status metrics, and dynamic dashboard
text.
Key responsibilities: - Reviewing the available fact and dimension tables and
identifying fields needed for calculations. - Creating reusable DAX measures
rather than repeating calculations in individual visuals. - Building aggregation
measures for sales, orders, quantities, discounts, shipping costs, and
customers. - Developing KPIs for growth, returns, cancellations, customer
loyalty, and contribution percentages. - Creating dynamic titles, subtitles, and
labels that respond to report filters. - Organizing measures for reuse in cards,
charts, tables, and other visuals.
2. Basic Aggregation Measures
These measures provide the core calculations used by more advanced KPIs.
Measure Calculation / purpose
Total Net Sales Adds Fact_Orders[NetSales] to calculate net sales.
Total GrossAmount Adds Fact_Orders[GrossAmount] to calculate gross
amount.
Total DiscountAmount Adds Fact_Orders[DiscountAmount] to calculate
total discounts.
Total Revenue Adds Fact_Orders[TotalAmount] to calculate revenue based
on that field.
Total Quantity Adds ordered quantities in Fact_Orders[Quantity].
Total ShippingCost Adds shipping costs in Fact_Orders[ShippingCost].
Total Orders Counts distinct order IDs using
DISTINCTCOUNT(Fact_Orders[OrderID]).
Unique Customers Counts distinct customer IDs in
Dim_Customer[CustomerID].
Unique Products Counts distinct product IDs in Fact_Orders[ProductID].
Unique Sellers Counts distinct seller IDs in
Fact_Orders[SellerID].
Average measures
The documented average measures are: - Average GrossAmount - Average
DiscountAmount - Average NetSales - Average Quantity - Average
ShippingCost - Average UnitPrice - Avg Discount
Interpretation note: These AVERAGE measures calculate the average
across rows in the relevant column. They should not automatically be
described as averages per order unless the model and calculation specifically
support that interpretation.
3. Sales and Discount Analysis
Measure Purpose
Discount Rate Divides total discount amount by total gross amount; returns 0
if the denominator is zero.
Discounted Orders % Calculates the share of total orders associated with
rows where Discount > 0.
Sales Contribution % Calculates net sales as a share of net sales across all
products, removing the product filter.
Country Sales Contribution % Calculates net sales as a share of net sales
across geography selections, removing filters from Dim_Geography.
Payment Sales Contribution % Calculates net sales as a share of net sales
across payment selections, removing filters from Dim_Payment.
In Progress Sales Calculates net sales for records grouped as In Progress in
Dim_OrderStatus[StatusGroup].
Lost Sales Calculates net sales for records grouped as
Lost in Dim_OrderStatus[StatusGroup].
These measures help compare sales performance, discounts, and the
contribution of different product, country, or payment selections.
4. Time-Based Performance Measures
Measure Purpose
Previous Period Sales Calculates net sales for the corresponding period in
the previous year using SAMEPERIODLASTYEAR.
Sales Growth % Calculates the percentage change in net sales compared
with Previous Period Sales.
Orders YoY % Calculates the percentage change in total orders compared
with the corresponding period in the previous year.
AOV YoY % Calculates the year-over-year percentage
change in Average NetSales.
Growth calculations compare the currently selected period with the
corresponding period one year earlier. They depend on the date context
provided by Dim_Date[OrderDate].
Important note: AOV YoY % is currently based on Average NetSales. This
represents year-over-year change in that average measure; it should only be
called true Average Order Value if the measure is defined at order level.
5. Order Status Measures
Measure Purpose
Cancelled Orders Counts orders in the Cancelled status by applying a filter
to Dim_OrderStatus[OrderStatus].
Cancel Rate Divides cancelled orders by total orders.
Returned Orders Counts orders in the Returned status.
Return Rate Divides returned orders by total orders.
These measures provide visibility into cancelled and returned orders and their
rates relative to total orders.
6. Customer Behavior Measures
Measure Purpose
Repeat Customers Counts customer IDs that have more than one row in
Fact_Orders within the current filter context.
Repeat Customer % Divides repeat customers by unique customers.
One-time Customers Calculates unique customers minus repeat customers.
One-time Customers Net Sales Calculates net sales for customer IDs
whose fact-table row count is one in the current filter context.
Orders per Customer Divides total orders by unique
customers.
These measures support analysis of repeat purchasing and customer activity.
Implementation note: The current repeat-customer and one-time-customer
calculations use fact-table row counts. If an order can produce multiple fact
rows (for example, one row per product), customer classification may need to
be adjusted to count distinct order IDs rather than fact rows.
7. Dynamic Dashboard Measures
These measures create dashboard text that responds to the report's filter
context.
Measure Purpose
Year Label Shows one year when the selected date context contains a single
year, or a year range when it contains multiple years.
Country Label Shows the selected country, All Markets, or the number of
selected markets.
Category Label Shows the selected category, All Categories, or the number
of selected categories.
Dashboard Title Combines the dashboard name with the dynamic year,
country, and category labels.
Dashboard Subtitle Combines net sales in millions, total orders, and
average net sales into one subtitle string.
Title Sales Creates a sales title using the selected year
when filtered, or the default range 2020–2024 otherwise.
How the dynamic labels work
1. Year Label reads the minimum and maximum years in the current date
context.
2. Country Label and Category Label compare selected values with the total
available values and return a suitable label.
3. Dashboard Title combines the dynamic labels into one title.
4. Dashboard Subtitle uses FORMAT to display sales and order figures as
readable text.
These measures make report headings and labels respond to selections
made by the viewer.
8. Examples of DAX Logic
Example 1 --- Total Net Sales
Total Net Sales = SUM(Fact_Orders[NetSales])
This measure adds values in the NetSales column. It can be reused in KPI
cards, charts, and other visuals, and responds to report filters.
Example 2 --- Sales Growth %
Sales Growth % =
DIVIDE(
[Total Net Sales] - [Previous Period Sales],
[Previous Period Sales],
0
)
The calculation subtracts previous-period sales from current net sales, then
divides the difference by previous-period sales. DIVIDE returns 0 when the
denominator is zero.
Example 3 --- Cancel Rate
Cancel Rate =
DIVIDE(
[Cancelled Orders],
[Total Orders]
)
This measure divides cancelled orders by total orders.
Example 4 --- Repeat Customer %
Repeat Customer % =
DIVIDE(
[Repeat Customers],
[Unique Customers]
)
This measure expresses repeat customers as a proportion of unique
customers, based on the definitions used in the underlying measures.
9. DAX Functions Used
Function Use
SUM Adds numeric values.
AVERAGE Calculates a column average.
DISTINCTCOUNT Counts unique IDs.
CALCULATE Evaluates a measure under a modified filter context.
DIVIDE Divides values with optional handling for a zero denominator.
SAMEPERIODLASTYEAR Shifts the selected date context to the
corresponding period in the previous year.
REMOVEFILTERS Removes filters from a specified table or column context.
FILTER and VALUES Build filtered sets of values for customer-level
calculations.
COUNTROWS Counts rows in a table expression.
SUMX Iterates over a table and sums an expression.
VAR and RETURN Store intermediate values and return a result.
SWITCH, IF, SELECTEDVALUE, Support conditional
logic and ISFILTERED, MIN, MAX, YEAR, dynamic
labels. FORMAT
10. Deliverables and Outcome
The DAX work provides a reusable set of measures for the Commerce
Compass dashboard, covering:
Core sales, revenue, order, quantity, discount, shipping, and customer
metrics.
Sales and order year-over-year comparisons.
Cancellation and return counts and rates.
Repeat-purchase customer indicators.
Product, geography, and payment contribution calculations.
Dynamic titles, subtitles, and selection labels.
The measures are intended to support KPI cards and analytical visuals while
responding to relevant report filters.
11. Suggested Repository Structure
Commerce-Compass/
├── README.md
├── DAX_Measures.dax
├── E-Commerce_Modeling&Schema.pbix
└── screenshots/
└── dashboard-preview.png
Recommended files: - README.md --- project and DAX documentation. -
DAX_Measures.dax or DAX_Measures.txt --- complete DAX definitions, if
stored separately. - E-Commerce_Modeling&Schema.pbix --- Power BI
model/report file, if permitted to share. - screenshots/ --- screenshots of
relevant KPI cards or visuals, if required.
Note: The original document describes the measure purposes and examples,
but it does not include the complete definitions for every measure. Add the full
DAX definitions to a separate file if the repository requires them.
