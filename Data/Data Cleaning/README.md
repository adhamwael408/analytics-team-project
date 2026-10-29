# E-Commerce Data Cleaning & Preparation (Power Query / Power BI)

## Project Overview
This repository contains the documentation and workflow for the **Data Cleaning & Transformation Phase** of the E-Commerce Data Analytics project (DEPI - Pioneers Initiative). 

The goal of this phase was to process, clean, standardize, and structure raw transactional data to build a reliable base for **Data Modeling (Star Schema)** and analysis.

---

## Dataset Summary
* **Total Records:** 100,000 rows
* **Total Features:** 20 columns
* **Time Range:** 2020 – 2024

---

## Data Cleaning & Transformation Workflow

### 1. Column Profiling & Quality Audit
Using Power Query's **Column Profiling** tools, the raw dataset was audited for consistency and integrity:
* **Missing Values:** Confirmed **0% nulls** across all columns.
* **Duplicates:** Verified zero duplicate records; every `OrderID` is unique.
* **Business Logic Check:** Validated the existing `TotalAmount` equation across rows to ensure numerical consistency.

---

### 2. Data Types & Text Formatting
To ensure efficient memory usage and correct calculations during modeling:
* **`OrderDate`**: Standardized as `Date`.
* **`Quantity`**: Converted to `Whole Number` (1, 2, 3...).
* **Monetary Columns**: Set `UnitPrice`, `Tax`, `ShippingCost`, and `TotalAmount` to `Fixed Decimal Number` (Currency).
* **`Discount`**: Converted to `Decimal Number` (Percentage format).
* **Categorical Fields**: Set to `Text`.
* **Text Normalization**: Applied `Trim` (removes leading/trailing spaces) and `Clean` (removes non-printable characters) across all text attributes.

---

### 3. Data Anomaly Handling & Column Dropping
Four redundant or corrupted columns were removed to improve data quality:
1. **`CustomerName`**: Dropped due to inconsistent mapping (the same `CustomerID` was associated with multiple varying names).
2. **`Brand`**: Dropped due to random distribution (each product was randomly mapped across all 10 brands).
3. **`City` & `State`**: Dropped due to geographical inconsistencies (e.g., the same city name appearing under completely different countries).

---

### 4. Feature Engineering (Custom Calculated Columns)
Created four essential business metrics in Power Query:
* **`GrossAmount`**: `Quantity` × `UnitPrice`
* **`DiscountAmount`**: `GrossAmount` × `Discount`
* **`NetSales`**: `GrossAmount` - `DiscountAmount`
* **`PriceBand`**: Categorical classification segmenting products into price tiers based on `UnitPrice`.

---

### 5. Staging Architecture & External Mapping

* **Staging Query (`Orders_Clean`)**:
  * Configured as the core **single source of truth**.
  * **Disabled Load (`Enable Load = False`)**: Prevents loading raw staging data into the Power BI data model to save memory.
  * All Fact and Dimension tables are generated using **`Reference`** from `Orders_Clean`, ensuring fully automated dynamic updates when changes occur in the staging layer.

* **Product Mapping Integration**:
  * Imported an external **Product Mapping** lookup file.
  * Standardized the `Product` to `Category` relationship to resolve many-to-many anomalies where a single product was mapped to multiple distinct categories.

---

## Next Steps: Data Modeling (Star Schema)
The cleaned staging model is prepared to be split into a star schema architecture consisting of:
* **Fact Table:** `Fact_Orders` (excluding redundant descriptors like `ProductName` and raw `OrderDate`, linking via surrogate keys).
* **7 Dimension Tables:**
  1. `Dim_Product`
  2. `Dim_Category`
  3. `Dim_Customer`
  4. `Dim_Seller`
  5. `Dim_Geography`
  6. `Dim_Payment`
  7. `Dim_OrderStatus`
