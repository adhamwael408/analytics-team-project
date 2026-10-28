DAX Measures Development ---
E-Commerce Analytics Dashboard
1. Role and Responsibility
Role: DAX Measures Developer
Project: E-Commerce Analytics Dashboard (
E-Commerce_Modeling&Schema.pbix)
My responsibility was to continue the project after the data model had been
prepared. I worked on the DAX calculation layer used by the dashboard to
calculate business metrics, performance indicators, customer behavior, order
status metrics, and dynamic dashboard text.
The work included: - Reviewing the available fact and dimension tables and
identifying the fields needed for calculations. - Creating reusable DAX
measures instead of repeating calculations in individual visuals. - Building
basic aggregation measures for sales, orders, quantities, discounts, shipping
costs, and customers. - Developing KPI measures for growth, returns,
cancellations, customer loyalty, and contribution percentages. - Creating
dynamic measures that update dashboard titles, subtitles, and labels based
on the selected filters. - Organizing measures so they can be reused by
report cards, charts, tables, and other visuals.
2. Basic Aggregation Measures
These measures provide the core calculations used by more advanced KPIs.
Measure Calculation / purpose
Total Net Sales Adds the values in Fact_Orders[NetSales] to calculate net
sales.
Total GrossAmount Adds Fact_Orders[GrossAmount] to calculate gross
amount.
Total DiscountAmount Adds Fact_Orders[DiscountAmount] to calculate the
total discount amount.
Total Revenue Adds Fact_Orders[TotalAmount] to calculate total revenue
based on that field.
Total Quantity Adds the ordered quantities in Fact_Orders[Quantity].
Total ShippingCost Adds shipping costs in Fact_Orders[ShippingCost].
Total Orders Counts distinct order IDs using
DISTINCTCOUNT(Fact_Orders[OrderID]).
Unique Customers Counts distinct customer IDs in
Dim_Customer[CustomerID].
Unique Products Counts distinct product IDs in Fact_Orders[ProductID].
Unique Sellers Counts distinct seller IDs in
Fact_Orders[SellerID].
Average Measures
These measures calculate the average value of the corresponding column:
Average GrossAmount
Average DiscountAmount
Average NetSales
Average Quantity
Average ShippingCost
Average UnitPrice
Avg Discount
Important interpretation: These AVERAGE measures calculate the average
across rows in the relevant column. They should not automatically be
described as averages per order unless the data model and calculation
specifically support that interpretation.
3. Sales and Discount Analysis
Measure Purpose
Discount Rate Divides total discount amount by total gross amount; returns 0
if the denominator is zero.
Discounted Orders % Calculates the share of total orders associated with
rows where Discount is greater than zero.
Sales Contribution % Calculates net sales as a share of net sales across all
products, removing the product filter.
Country Sales Contribution % Calculates net sales as a share of net sales
across all geography selections, removing filters from Dim_Geography.
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
The growth calculations compare the current selected period with the
corresponding period one year earlier. They depend on the date context
provided by Dim_Date[OrderDate].
Note: AOV YoY % is currently based on Average NetSales. This represents
year-over-year change in that average measure; it should only be called true
Average Order Value if the measure is defined at order level.
5. Order Status Measures
Measure Purpose
Cancelled Orders Counts orders in the Cancelled status by applying a filter to
Dim_OrderStatus[OrderStatus].
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
One-time Customers Net Sales Calculates net sales for customer IDs whose
fact-table row count is one in the current filter context.
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
Dashboard Subtitle Combines net sales in millions, total orders, and average
net sales into one subtitle string.
Title Sales Creates a sales title using the selected year
when filtered, or the default range 2020 – 2024
otherwise.
How the dynamic labels work
Year Label reads the minimum and maximum years in the current date
context.
Country Label and Category Label compare the number of selected
values with the total available values and return a suitable label.
Dashboard Title combines the dynamic labels into a single title.
Dashboard Subtitle uses FORMAT to display sales and order figures as
readable text.
These measures make the report headings and labels respond to the
selections made by the viewer.
8. Examples of DAX Logic
Example 1 --- Total Net Sales
Total Net Sales = SUM(Fact_Orders[NetSales])
This measure adds the values in the NetSales column. It can be reused in
KPI cards, charts, and other visuals, and it responds to the filters applied in
the report.
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
This measure divides the number of cancelled orders by the total number of
orders.
Example 4 --- Repeat Customer %
Repeat Customer % =
DIVIDE(
[Repeat Customers],
[Unique Customers]
)
This measure expresses the number of repeat customers as a proportion of
unique customers, based on the definitions used in the underlying measures.
9. DAX Functions Used
SUM --- adds numeric values.
AVERAGE --- calculates a column average.
DISTINCTCOUNT --- counts unique IDs.
CALCULATE --- evaluates a measure under a modified filter context.
DIVIDE --- divides values with optional handling for a zero denominator.
SAMEPERIODLASTYEAR --- shifts the selected date context to the
corresponding period in the previous year.
REMOVEFILTERS --- removes filters from a specified table or column
context.
FILTER and VALUES --- build filtered sets of values for customer-level
calculations.
COUNTROWS --- counts rows in a table expression.
SUMX --- iterates over a table and sums an expression.
VAR and RETURN --- store intermediate values and return a result.
SWITCH, IF, SELECTEDVALUE, ISFILTERED, MIN, MAX, YEAR, and
FORMAT --- support conditional logic and dynamic labels.
10. Deliverables and Outcome
The DAX work provides a reusable set of measures for the e-commerce
dashboard, covering:
Core sales, revenue, order, quantity, discount, shipping, and customer
metrics.
Sales and order year-over-year comparisons.
Cancellation and return counts and rates.
Customer repeat-purchase indicators.
Product, geography, and payment contribution calculations.
Dynamic titles, subtitles, and selection labels.
The measures are intended to support the report's KPI cards and analytical
visuals while responding to the relevant report filters.
11. Suggested GitHub Folder Contents
Recommended files for this contribution folder:
README.md --- this documentation of the DAX work.
DAX_Measures.txt or DAX_Measures.dax --- the complete DAX
definitions, if the project team is storing the measure code separately.
Screenshots of relevant KPI cards or visuals, if screenshots are part of the
team's submission requirements.
Scope clarification: This contribution documents the DAX measures and
dynamic dashboard text. It does not claim ownership of the data model, table
creation, relationships, or the entire dashboard unless those tasks were
separately assigned and completed.
