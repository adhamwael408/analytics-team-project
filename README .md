Commerce Compass

Comprehensive Documentation of the Power BI Sales Analytics Project

Developed for the Digital Egypt Pioneers Initiative (DEPI)

Commerce Compass is an interactive sales analytics project developed using Power BI to analyze 100,000 e-commerce sales orders covering the period from 2020 to 2024. The project provides a detailed, data-driven understanding of revenue growth opportunities, financial leakage, and the impact of discount policies on profitability and customer purchasing behavior.

---

1. Project Overview

Commerce Compass was designed to support business leaders and decision-makers by answering critical business questions through interactive dashboards and actionable insights.

The report consists of six visible pages and two hidden pages dedicated to interactive product-level analysis and tooltips. It concludes with a dedicated recommendations page that translates analytical findings into practical business strategies.

Project Summary

Metric| Details
Data Period| 2020–2024 (5 consecutive years)
Total Sales Orders| 100,000
Unique Customers| 43,233
Number of Products| 50
Markets Covered| 5 countries
Total Net Sales| $84.24 million
Average Order Value (AOV)| $842.38
Weighted Discount Rate| 7.45%
Total Discount Amount| $6.78 million
Report Structure| 6 visible pages + 2 hidden pages
Shared Slicers| Year, Country, and Category

---

2. Data Cleaning and Preparation

The raw dataset underwent a structured data-cleaning and preparation process in Power Query to ensure data quality, consistency, and reliability before data modeling and analysis.

2.1 Data Quality Assessment and Type Conversion

A total of 100,000 rows and 20 columns were examined using Power Query's Column Profiling tools.

The assessment indicated that the dataset contained no missing values or duplicate rows. The "TotalAmount" calculation was also checked for consistency.

The following data preparation steps were applied:

- "OrderDate": Converted to the Date data type to support time-based analysis.
- "Quantity": Converted to Whole Number because product quantities are integer values.
- Financial Columns: "UnitPrice", "Tax", "ShippingCost", and "TotalAmount" were converted to Fixed Decimal Number to maintain financial precision.
- "Discount": Converted to Decimal Number, with unnecessary spaces and unwanted characters removed using the Trim and Clean operations.

2.2 Excluding Inconsistent Columns

Four columns were excluded from the analytical model to avoid misleading results and maintain data consistency.

1. "CustomerName": Excluded because the same "CustomerID" appeared with multiple different customer names.
2. "Brand": Excluded because the ten brands were distributed inconsistently across products.
3. "City": Excluded because geographical data was inconsistent, with city names repeated across different countries.
4. "State": Excluded for the same geographical consistency issues.

2.3 Source Calculated Columns

Five calculated columns were added to enrich the original orders data in Power Query:

- "GrossAmount": Total amount before discounts.
  
  "GrossAmount = Quantity × UnitPrice"

- "DiscountAmount": Monetary value of the discount.
  
  "DiscountAmount = GrossAmount × Discount"

- "NetSales": Sales revenue after discounts but before taxes and shipping charges.
  
  "NetSales = GrossAmount − DiscountAmount"

- "PriceBand": Categorizes products into High, Mid, and Low price bands.

- Product Category Mapping: An external Product Mapping file was integrated to assign products consistently to six main categories and prevent the same product from being associated with multiple categories.

2.4 Query References and Data Preparation Workflow

The "Orders_Clean" query was established as the central source for all subsequent tables, with Enable Load disabled to prevent it from being loaded directly into the data model.

All fact and dimension tables were derived from this query using the Reference feature in Power Query.

This approach ensures that updates to the original cleaning process are reflected in the dependent tables during refreshes, while maintaining a structured and reusable data preparation workflow.

---

3. Data Modeling: Star Schema Architecture

The project uses a Star Schema to organize the data into a central fact table surrounded by six dimension tables.

This design separates numerical transactional data from descriptive business attributes, making the model easier to understand, maintain, and analyze.

3.1 Model Structure

Central Fact Table

- "Fact_Orders": Stores order-level transactions, foreign keys, and financial measures.

Dimension Tables

- "Dim_Customer"
- "Dim_Date"
- "Dim_Geography"
- "Dim_OrderStatus"
- "Dim_Payment"
- "Dim_Product"

3.2 Fact and Dimension Tables

Table| Table Type| Main Columns| Business Purpose
"Fact_Orders"| Fact Table| "Country_Id", "CustomerID", "Date_Id", "Payment_Id", "ProductID", "Status_Id", "SellerID", "OrderID", "GrossAmount", "NetSales", "TotalAmount"| Records detailed transactions and their financial values.
"Dim_Customer"| Dimension| "CustomerID", "CustomerType", "OrderCount"| Identifies customer segments and purchasing behavior.
"Dim_Date"| Dimension| "Date_Id", "OrderDate"| Supports time-based analysis and trend identification.
"Dim_Geography"| Dimension| "Country_Id", "Country"| Enables country-level market and revenue analysis.
"Dim_OrderStatus"| Dimension| "Status_Id", "OrderStatus", "StatusGroup"| Tracks order statuses and identifies operational bottlenecks.
"Dim_Payment"| Dimension| "Payment_Id", "PaymentMethod", "PaymentGroup"| Analyzes payment methods and customer payment preferences.
"Dim_Product"| Dimension| "ProductID", "ProductName", "Category"| Evaluates product and category performance.

3.3 Relationship Configuration

The model follows these relationship principles:

- Relationship Cardinality: All relationships are one-to-many (1:*) from the dimension tables to "Fact_Orders".
- Filter Direction: Single-direction filtering flows from dimension tables to the fact table.
- Model Efficiency: The design reduces unnecessary duplication of descriptive data and supports efficient DAX calculations.
- Scalability: The model can be extended by adding dimensions such as "Dim_Seller" for more detailed seller performance analysis.

The Star Schema provides a reliable foundation for interactive reporting, consistent calculations, and future analytical expansion.

---

4. DAX Measures Development

A comprehensive collection of DAX measures was developed to support financial analysis, operational monitoring, and customer behavior evaluation.

4.1 Core and Advanced KPIs

1. Total Net Sales

Calculates the total revenue after discounts.

Total Net Sales =
SUM(Fact_Orders[NetSales])

2. Sales Growth %

Measures sales growth compared with the previous period.

Sales Growth % =
DIVIDE(
    [Total Net Sales] - [Previous Period Sales],
    [Previous Period Sales],
    0
)

3. Weighted Discount Rate

Calculates the discount rate based on total discounts relative to gross sales.

Discount Rate =
DIVIDE(
    SUM(Fact_Orders[DiscountAmount]),
    SUM(Fact_Orders[GrossAmount]),
    0
)

4. Cancellation Rate

Measures the proportion of cancelled orders relative to total orders.

Cancel Rate =
DIVIDE(
    [Cancelled Orders],
    [Total Orders],
    0
)

5. Lost Sales

Calculates the net sales value associated with cancelled and returned orders.

6. In-Progress Sales

Calculates the net sales value associated with orders classified as "Shipped" or "Pending".

4.2 Dynamic Measures and Titles

Dynamic DAX measures were created to make report titles and labels respond automatically to the selected filters.

These include:

- "Dashboard Title"
- "Country Label"
- "Category Label"
- "Year Label"

For example, when a user selects a specific year, country, and category, the report title can dynamically display:

Commerce Compass | 2023 | Canada | Electronics

This functionality improves the user experience by making the selected analytical context immediately visible.

---

5. Power BI Dashboard Architecture

The report contains six visible analytical pages and two hidden pages that support deeper exploration and contextual information.

5.1 Welcome Page

Purpose: Introduces the project and guides users through the report.

Key elements include:

- Project introduction and objectives.
- High-level performance indicators.
- Navigation instructions.
- Access to the main analytical pages.

5.2 Executive Overview

Business Question: How is the business performing overall?

This page provides an executive-level summary of business performance through key indicators, including:

- Total Net Sales: $84.24M
- Total Orders: 100K
- Lost Sales: $5.16M
- Dynamic insights identifying the top-performing product, country, and category.

The page allows decision-makers to quickly assess overall performance and identify areas requiring further investigation.

5.3 Sales & Products

Business Question: What drives revenue?

This page analyzes product-level and pricing performance through:

- Top 10 best-performing products.
- Bottom 10 lowest-performing products.
- Revenue contribution by price band.
- Product and category comparisons.

The analysis shows that the High Price Band generates 55.7% of net sales, despite representing only a portion of total orders.

This finding highlights the importance of high-value products in driving overall revenue.

5.4 Discount Impact

Business Question: Do discounts increase sales enough to justify their cost?

This page examines the relationship between discounts, order value, and purchasing volume.

Key findings include:

- Average order value decreases from $907 to $665 under the compared discount conditions.
- Average quantity remains unchanged at 3.0 units.
- Total discounts amount to $6.78M.

The analysis raises concerns about whether discount policies generate sufficient additional demand to justify their financial cost.

5.5 Customers & Markets

Business Question: Who are the customers, and where are they located?

This page focuses on customer retention, purchasing behavior, and geographical revenue distribution.

Key findings include:

- Repeat customers represent 68.7% of the customer base.
- Repeat customers contribute 86.4% of total net sales.
- The United States accounts for approximately 70% of total net sales.

These findings emphasize the importance of customer retention while highlighting the potential risks associated with dependence on a single market.

5.6 Insights & Recommendations

Purpose: Converts analytical findings into actionable business strategies.

This interactive page presents seven key business insights, each supported by numerical evidence and a corresponding recommendation.

Each insight row functions as a navigation element that directs users to the relevant analytical page for further investigation.

This design connects business findings with their supporting evidence and makes the report more useful for decision-making.

5.7 Hidden Pages: Product Details & Tooltip

Product Details — Drill-through Page

Provides a detailed product-level analysis when users right-click a product and select "Drill-through → Product Details".

The page includes:

- Product sales performance.
- Return rate.
- Discount analysis.

Tooltip Page

Displays contextual information when users hover over supported charts and data points, allowing them to explore additional details without leaving the current page.

---

6. Key Business Insights and Strategic Recommendations

The analysis identified seven major business findings, each supported by numerical evidence and a corresponding strategic recommendation.

Insight 1: Sales Are Stable but Not Growing

Evidence

Annual net sales remain relatively stable across the five-year period:

Year| Net Sales
2020| $17.0M
2021| $16.7M
2022| $16.8M
2023| $17.0M
2024| $16.7M

Interpretation

Despite consistent revenue levels, the business shows limited sustained growth across the analyzed period.

Recommendation

Identify new growth opportunities by exploring additional acquisition channels, expanding successful product categories, and testing new market segments rather than relying exclusively on existing sales channels.

Insight 2: Repeat Customers Are the Backbone of Revenue

Evidence

- Repeat customers represent 68.7% of the customer base.
- They generate 86.4% of total net sales.

Interpretation

The business relies heavily on customers who purchase more than once, making customer retention a major contributor to revenue stability.

Recommendation

Invest in customer retention strategies, including loyalty programs, personalized offers, targeted communication, and initiatives that encourage repeat purchases.

Insight 3: Discounts Reduce Order Value Without Increasing Quantity

Evidence

- Average order value decreases from $907 to $665 under the compared discount conditions.
- Average quantity remains constant at 3.0 units.
- Total discount expenditure reaches $6.78M.

Interpretation

The observed pattern suggests that discounts may reduce the value of transactions without generating a measurable increase in the average quantity purchased.

Recommendation

Establish discount limits, test promotional campaigns on selected customer segments, and evaluate incremental sales and profit margins before expanding discount programs.

Insight 4: High-Priced Products Drive Revenue

Evidence

The High Price Band contributes 55.7% of total net sales, despite accounting for a relatively small share of orders.

Interpretation

Higher-priced products make a substantial contribution to overall revenue.

Recommendation

Prioritize high-value products in marketing campaigns and promotional strategies. Evaluate product-level profitability before increasing promotional investment.

Insight 5: A Significant Share of Orders Remains Operationally Unresolved

Evidence

- Approximately 19.4% of sales, equivalent to $16.3M, is associated with orders classified as "Shipped" or "Pending".
- Approximately 3,800 orders from 2020 remain within these statuses.

Interpretation

The presence of older orders in these categories may indicate delayed status updates, incomplete order closure, or operational tracking issues.

Recommendation

Audit the order lifecycle, investigate outdated transactions, establish clear order-closure procedures, and require operational teams to update order statuses promptly.

Insight 6: Revenue Is Highly Concentrated in One Market

Evidence

The United States generates approximately 70% of total net sales.

Interpretation

The business has substantial exposure to a single geographical market, which may increase its vulnerability to market-specific risks.

Recommendation

Explore opportunities to expand in the other markets, diversify marketing investment, and evaluate regional demand before allocating additional resources.

Insight 7: Sales Leakage Is a Cross-Functional Problem

Evidence

- Total lost sales amount to $5.16M, representing approximately 6.1% of net sales.
- The reported cancellation and return rates are approximately 3% each.

Interpretation

Cancellations and returns represent a significant source of lost sales that may affect multiple stages of the order lifecycle.

Recommendation

Investigate the root causes of cancellations and returns across the entire operation. Review product information, order fulfillment, delivery performance, and customer expectations to identify recurring problems and prioritize corrective actions.

---

7. Visual Design and Operating Guide

Commerce Compass uses a dark, high-contrast Dark HUD Theme designed to present complex analytical information clearly and consistently.

7.1 Visual Identity

Design Element| Specification
Dashboard Canvas| 16:9 aspect ratio
Primary Background — Navy| "#071C2E"
Primary Accent — Cyan| "#2FD6F0"
Secondary Accent — Teal| "#0E7C90"
Risk and Loss Indicator — Red| "#FF5A5F"
Text Color| "#E6F7FB"

Color Usage

- Navy: Provides a consistent dark background.
- Cyan: Highlights key metrics, analytical findings, and growth opportunities.
- Teal: Supports secondary highlights and visual hierarchy.
- Red: Draws attention to financial losses, cancellations, and operational risks.
- Light Text: Maintains readability against the dark background.

7.2 How to Open and Navigate the Report

Step 1 — Download the Report

Obtain the main Power BI report file:

"Final M Project.pbix"

Step 2 — Open Power BI Desktop

Open the file using Microsoft Power BI Desktop. Using the latest compatible version is recommended.

Step 3 — Start from the Welcome Page

Begin on the "Welcome" page and use the vertical Page Navigator to move between the analytical sections.

Step 4 — Interact with the Dashboard

Use the shared slicers to filter the report by:

- Year
- Country
- Category

The visuals, KPIs, and dynamic titles respond to the selected filters.

Step 5 — Explore Product Details

To analyze an individual product, right-click its data point and select:

"Drill-through → Product Details"

The report then displays the detailed product analysis.

Step 6 — Navigate Using Interactive Elements

The Insights & Recommendations page provides direct navigation to the relevant analytical pages. Hover over supported charts to view additional information through tooltips.

Important Note: In Power BI Desktop, buttons and navigation actions generally require Ctrl + Click. In Power BI Service, published reports generally support single-click interaction.

---

8. Project Value and Conclusion

Commerce Compass demonstrates how raw e-commerce transaction data can be transformed into a structured analytical solution that supports business decision-making.

The project combines data preparation, dimensional modeling, DAX development, interactive visualization, and business interpretation within a unified Power BI report.

Its primary business contributions include:

- Revenue Analysis: Identifies sales trends, high-performing products, and major revenue drivers.
- Customer Insights: Quantifies the importance of repeat customers and retention strategies.
- Discount Evaluation: Highlights potential inefficiencies in discount policies and their relationship with order value.
- Operational Monitoring: Identifies unresolved order statuses and potential process bottlenecks.
- Market Diversification: Reveals geographical revenue concentration and opportunities for expansion.
- Actionable Recommendations: Connects measurable findings with practical steps that decision-makers can investigate and implement.

Ultimately, Commerce Compass is designed to help decision-makers move beyond descriptive reporting toward evidence-based decisions, operational improvements, and more sustainable business growth.

Project Name: Commerce Compass
Technology: Microsoft Power BI, Power Query, DAX, Star Schema Data Modeling
Project Scope: E-commerce Sales Analytics, Customer Behavior, Discount Impact, and Business Intelligence
Developed for: Digital Egypt Pioneers Initiative (DEPI)