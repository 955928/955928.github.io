---
title: "Power BI Project:Superstore Sales Analysis"
date: 2026-07-07 00:00:00 +0000
categories: [Power BI, Data Analysis]
tags: [Power BI, Superstore, Dashboard, Data Visualization]
image:
  path: assets/images/Superstore/POST_IMAGE.png
  alt: "Superstore Power BI Dashboard"
---
---
##Introduction

For this first data project published on my blog, I chose to work with the classic **Superstore dataset** ,a fictional retail company selling furniture, office supplies, and technology products across multiple regions in the United States. This dataset is widely used in the data analytics community and is a great playground to practice business intelligence techniques.

The goal of this project was to build a complete and interactive Power BI report that answers three key business questions: **How are sales and profits evolving over time? Who are our best customers and top-performing products? And how efficient are our operations and shipping?**

To answer these, I structured the report into three pages, each with a specific analytical focus:

- **Executive Overview** : a high-level snapshot of overall performance
- **Product & Customer Analysis** : a deep dive into what sells and who buys
- **Operations & Profitability Analysis** : an examination of shipping efficiency and margin drivers

The data model follows a **star schema** with a central fact table (`FACTSALES`) linked to three dimension tables: `DimProduct`, `DimCustomer`, and `DimDate`.

---

## Page 1 Executive Overview
![Executive Overview](assets/images/Superstore/Executive_Overview.png)


This page serves as the landing dashboard. It gives decision-makers an immediate overview of the business through four key charts and a KPI summary table. Four slicers (region, category, segment, and date) allow dynamic filtering across the entire page.

---

### Chart 1  Month-Year Sales Trend *(Line Chart)*

![Month-Year Sales Trend](/assets/images/Superstore/Month-Year_Sales_Trend.png)

This line chart displays the evolution of total sales over time, broken down by year and month. It allows the reader to quickly identify **seasonal peaks**, **growth trends**, and any **drops in revenue**. The time axis uses a date hierarchy (Year → Month) built from the `DimDate` table.

---

### Chart 2 Profit by Region *(Column Chart)*

![Profit by Region](assets/images/Superstore/Profit_by_Region.png)

This column chart compares total profit across the four main regions (Central, East, South, West). It is a straightforward but essential view to understand **which territories are the most profitable** and where the business may need to refocus its efforts.

---

### Chart 3 KPI Summary Table *(Table)*

![KPI SUMMARY TABLE](assets/images/Superstore/KPI_SUMMARY_TABLE.png)

Positioned at the top center of the page, this table provides a quick read on key performance indicators,including total orders, total sales, and total profit. It works as a **reference anchor** for the rest of the page.

---

### Chart 4 Sales by Category *(Column Chart)*

![Sales by Category](assets/images/Superstore/Sales_by_Category.png)

This chart compares total sales across the three main product categories: Furniture, Office Supplies, and Technology. It gives an instant read on **which category drives the most revenue** and helps contextualize the profitability data seen in other visuals.

---

### Chart 5 Top 10 Products by Sales *(Bar Chart)*

![Top 10 Products by Sales](assets/images/Superstore/TOP_10P_BY_SALES.png)

This horizontal bar chart ranks the ten best-selling products by total sales. It is useful to identify **star products** and understand what is driving category-level performance. Product names come from the `DimProduct` dimension table.

---

## Page 2 Product & Customer Analysis
![Product & Customer Analysis](assets/images/Superstore/Product_and_Customer_Analysis.png)

This page goes deeper into the product catalog and customer base. It answers questions like: Which sub-categories are performing well? Who are the most valuable customers? Which segments generate the most profit?

---

### Chart 6 Top 10 Products by Sales *(Bar Chart)*

![Top 10 Products by Sales ](assets/images/Superstore/TOP_10P_BY_SALES.png)

A product-level bar chart used here as a starting point for the product-focused analysis page. It provides continuity and context before drilling into sub-category and customer data.

---

### Chart 7 Profit by Category *(Column Chart)*

![Profit by Category](assets/images/Superstore/Profit_by_Category.png)

While page 1 showed sales by category, this chart focuses on **profit**. The distinction is important: a category can generate high revenue but low (or even negative) profit. This visual highlights where the real margins are.

---

### Chart 8 Top 10 Customers by Sales *(Bar Chart)*

![Top 10 Customers by Sales](assets/images/Superstore/TOP_10C_BY_SALES.png)

This chart identifies the **ten most valuable customers** ranked by total sales. For a retail business, understanding the top customer base is key to loyalty and CRM strategies.

---

### Chart 9 Sales by Sub-Category *(Bar Chart)*

![Sales by Sub-Category](assets/images/Superstore/TOP_10S_BY_SUBCAT.png)

This chart breaks sales down to a more granular level, showing performance across all product sub-categories (e.g., Chairs, Phones, Binders…). It helps identify **hidden gems and underperformers** within each main category.

---

### Chart 10 Profit by Customer Segment *(Donut Chart)*

![Profit by Customer Segment](assets/images/Superstore/Profit_by_CustomerSeg.png)

The three customer segments (Consumer, Corporate, and Home Office) are compared here by their contribution to total profit. The donut chart format makes it easy to read **proportional share at a glance**, and reveals which segment is the most profitable relative to its size.

---

## Page 3 Operations & Profitability Analysis
![Operations & Profitability Analysis](assets/images/Superstore/Operations_and_Profitability_Analysis.png)

The third page shifts from "what" to "how". It examines shipping efficiency, the impact of discounts on profit, and the combined monthly evolution of sales and profit. This is where operational decisions and pricing strategies come under scrutiny.

---

### Chart 11 Month-Year Profit Trend *(Line Chart)*

![Month-Year Profit Trend](assets/images/Superstore/Mont-Year_Profit_Trend.png)

This line chart tracks **profit over time** by year and month. Comparing it to the sales trend from page 1 reveals whether revenue growth translates into proportional profit growth or whether costs and discounts are eroding margins.

---
---

## Calculated Column and Measure — Average Shipping Days

### The Calculated Column

![Shipping Days Column](/assets/images/Superstore/AVG_SHIPPINGDAYS_COLONNE.png)

Before creating any measure related to shipping performance, a calculated column called **Shipping Days** had to be added directly to the FACTSALES table. A calculated column computes a value row by row at the time the model is refreshed, and stores the result alongside the other columns in the table.

The formula used is the following:

```dax
Shipping Days = DATEDIFF(FactSales[Order Date], FactSales[Ship Date], DAY)
```

DATEDIFF takes two date columns and returns the number of units between them, in this case days. For each order row, it calculates exactly how many days elapsed between the order being placed and the item being shipped. The result is a new integer column that can then be aggregated in a measure.

This step is necessary because the raw dataset does not contain this information directly. Order Date and Ship Date exist as separate columns but the gap between them has to be derived explicitly.

### The Measure

![Average Shipping Days Measure](/assets/images/Superstore/AVG_SHIPPINGDAYS_measure.png)

With the Shipping Days column in place, the measure was straightforward to write:

```dax
Average Shipping Days = AVERAGE(FactSales[Shipping Days])
```

AVERAGE iterates over all rows currently in context and returns the mean value of the Shipping Days column. Because it is a measure and not a column, it recalculates dynamically depending on whatever filters are active in the report, whether that is a region slicer, a ship mode filter, or a date selection.

The result across the full dataset is **3.96 days**, meaning orders take on average just under 4 days to ship. This single number becomes the baseline against which shipping performance by region and by ship mode is compared in the Operations page of the report.

The combination of a calculated column for the row-level computation and a measure for the aggregation is the correct and standard approach in Power BI for this type of derived metric.

---

### Chart 12 Average Shipping Days *(Card Visual)*

![Average Shipping Days](assets/images/Superstore/AVG_SHIPPINGDAYS.png)

A single KPI card displaying the **average number of days between order date and ship date** across all orders. This is a key operational metric, a high average signals logistics issues; a low one reflects supply chain efficiency.

---

### Chart 13 Profit by Ship Mode *(Bar Chart)*

![Profit by Ship Mode](assets/images/Superstore/Profit_by_ShipMode.png)

This chart compares profitability across the different shipping modes (Standard Class, Second Class, First Class, Same Day). It answers a critical question: **does faster shipping come at the cost of profit margins?**

---

### Chart 14 Average Shipping Days by Ship Mode *(Bar Chart)*

![Average Shipping Days by Ship Mode](assets/images/Superstore/AVG_SHIPPINGDAYS_BY_SHIPMODE.png)

This chart shows the **average delivery time for each shipping mode**, making it easy to verify whether the modes are performing as expected (e.g., Same Day should have the lowest average, Standard Class the highest).

---

### Chart 15 Average Shipping Days by Region *(Bar Chart)*

![ Average Shipping Days by Region](assets/images/Superstore/AVG_SHIPPINGDAYS_BY_REGION.png)

Shipping speed is compared across the four regions here. **Regional disparities** in delivery time can point to logistics bottlenecks or infrastructure differences that deserve attention.

---

### Chart 16 Discount vs Profit *(Scatter Chart)*

![Discount vs Profit](assets/images/Superstore/Discount_vs_Profit.png)

This scatter plot is one of the most analytically rich visuals in the report. Each point represents an order, plotted by its **discount rate (X-axis) against its resulting profit (Y-axis)**. The expected and common finding is a **negative correlation**: higher discounts tend to destroy profit margins.

---

### Chart 17 Sales and Profit per Month *(Line Chart)*

![Sales and Profit per Month](assets/images/Superstore/Sales_and_Profit_monthly.png)
The final visual overlays **monthly sales and profit on the same line chart**, giving a direct comparison of both metrics over time. It closes the report by connecting revenue generation with bottom-line results.

---
## Conclusion & Key Insights

After building and exploring this report across three interactive pages, several patterns emerged that provide a comprehensive view of the Superstore's performance, highlighting both strengths and areas for improvement.

## Sales Are Growing, but Profit Does Not Always Follow

The **Month-Year Sales Trend** demonstrates consistent year-over-year revenue growth, with noticeable seasonal peaks during the fourth quarter. However, the **Profit Trend** reveals that increasing sales do not always translate into higher profitability. In several periods, profit growth lags behind sales growth, indicating margin compression. Comparing both metrics makes it clear that revenue alone is not a sufficient measure of business performance.

---

## Discounts Significantly Impact Profitability

One of the strongest insights from the report comes from the **Discount vs. Profit** scatter plot. A clear negative relationship exists between discount rates and profit: heavily discounted orders frequently generate little or even negative profit.

This suggests that while discounts may help increase sales volume, excessive discounting often comes at the expense of profitability. Revisiting the company's pricing and discount strategy could have a substantial positive impact on overall margins.

---

## Technology Is the Best-Performing Category

Among the three product categories, **Technology** consistently delivers both the highest sales and the strongest profits.

**Office Supplies** generates stable revenue while maintaining healthy profit margins, making it a reliable contributor to overall performance.

**Furniture**, however, presents a different picture. Although it produces significant sales revenue, profitability remains relatively weak. The sub-category analysis suggests that products such as **Tables** and **Bookcases** are particularly affected by aggressive discounting, reducing overall margins.

---

## Regional Performance Is Uneven

The regional analysis shows that the **West** and **East** regions contribute the majority of the company's profits.

In contrast, the **Central** region generates respectable sales but underperforms in profitability. This imbalance may be explained by regional pricing strategies, customer purchasing behavior, product mix, or discount practices, all of which warrant further investigation.

---

## Standard Class Shipping Performs Best

Shipping mode analysis reveals that **Standard Class** is both the most frequently used shipping option and the most profitable.

Premium shipping methods such as **First Class** and **Same Day** provide faster delivery but tend to reduce profit margins due to higher operational costs. Encouraging customers to select premium shipping only when they are willing to absorb the additional cost could help maintain profitability.

---

## A Small Number of Customers Drive a Large Share of Revenue

The Top 10 customer and product analyses demonstrate a classic retail pattern: a relatively small number of customers and products account for a disproportionate share of total revenue.

While these high-value customers represent an important growth opportunity, they also introduce concentration risk. Losing one of these key accounts could have a noticeable impact on overall business performance.

---

# Recommendations

Based on the analysis, three strategic priorities stand out:

1. **Review the discount policy**
   - Introduce category-specific discount limits.
   - Reduce excessive discounting, particularly within the Furniture category.
   - Prevent transactions that generate negative profit.

2. **Replicate successful regional strategies**
   - Investigate why the West region consistently outperforms others.
   - Apply successful pricing, sales, and operational practices to underperforming regions such as the Central region.

3. **Strengthen relationships with top customers**
   - Develop a structured customer retention or loyalty program.
   - Focus account management efforts on the highest-value customers to protect a significant portion of overall revenue.

---

# Final Thoughts

This dashboard demonstrates how Power BI can transform raw transactional data into actionable business insights through interactive visualizations and data storytelling.

By combining sales, profit, customer, regional, product, and shipping analyses, the report provides decision-makers with a clear understanding of where the business performs well and where opportunities for improvement exist.

---

*Thank you for taking the time to explore this project. It forms part of my data analytics portfolio and showcases my ability to transform business data into meaningful insights using Power BI. Feedback and suggestions are always welcome.
*This project was built with Power BI Desktop using the public Superstore sample dataset, as part of my ongoing data portfolio.*

---
title: "Power BI Project:Superstore Data Preparation & Modeling"
date: 2026-07-07 00:00:00 +0000
categories: [Power BI, Data Modeling]
tags: [Power BI, Power Query, Star Schema, Data Cleaning, DAX]
image:
  path: assets/images/cleaning_data.jpg
  alt: "Superstore Power BI Data Modeling"
---

## Introduction

Before building any dashboard in Power BI, the foundation has to be solid. A well-structured data model is what separates a report that works from one that actually scales, performs, and gives reliable results. In this post, I walk through everything that happened before the first visual was placed: cleaning the raw Superstore dataset, creating dimension tables, and building the star schema that powers the entire report.

The raw Superstore file is a flat table. Every row contains information about an order, a customer, a product, a region, and a date, all mixed together in a single sheet. That structure works fine for a spreadsheet, but it is not suited for analytical modeling. The goal of this preparation phase was to reshape that flat file into a proper relational model made of one fact table and several dimension tables, connected through clean and intentional relationships.

---

## Part 1 Cleaning the Source Dataset

### The Source Table (SUPERSTORE)

The first step was to load the raw CSV file into Power Query. This created the base SUPERSTORE query, which serves as the untouched reference from which all other tables are derived. The source is never modified directly. Every transformation happens on a reference or duplicate of this query, which ensures the original data remains intact throughout the process.

![ Cleaned Dataset](assets/images/Superstore/Cleanning and Modeling my data/Cleaned_dataset_source.png)

The steps applied to the source query were the following:

**Source** - connects to the raw CSV file.

**En-têtes promus** - promotes the first row as column headers, since the raw file does not automatically detect them.

**ABC to DATE type** - converts the Order Date and Ship Date columns from text format to proper date types. This is critical because Power BI cannot build time intelligence or date hierarchies on text columns.

**ABC to DECIMAL type** - converts numeric columns like Sales, Profit, and Discount from text to decimal numbers so they can be aggregated in measures.

**ABC to NUMBER type** - converts integer columns like Quantity and Row ID to whole number format.

**Removal of empty rows** - this is the step that appeared as a "Filtered Rows" step. Power Query uses Table.SelectRows with the condition "each true" after removing empty rows, which filters out any rows where all values are null. This typically happens when the source file has trailing blank lines at the bottom, which is common with CSV exports.

Each of these steps is essential. Wrong data types are one of the most common causes of broken measures and incorrect aggregations in Power BI. Getting them right at the source level means every derived table inherits clean, reliable data.

---

### The FACTSALES Table

![Columns Deletd](assets/images/Superstore/Cleanning and Modeling my data/Deleted Columns.png)

The FACTSALES table is a reference of the cleaned SUPERSTORE query. From that reference, unnecessary columns were removed using Table.SelectColumns, keeping only the fields relevant to transactions: Order ID, Order Date, Ship Date, Ship Mode, Customer ID, Segment, Region, Product ID, Product Name, Sales, Quantity, Discount, Profit, and Category.

This table is the core of the model. It contains one row per order line and holds all the measurable facts (Sales, Profit, Discount, Quantity) alongside the foreign keys that link to each dimension table (Customer ID, Product ID, Order Date).

Keeping only the necessary columns reduces the file size and keeps the model clean. Any column that belongs to a dimension has no place in the fact table once the relationships are in place.

---

## Part 2 Creating the Dimension Tables

### DimCustomer

![Customer Dimension](assets/images/Superstore/Cleanning and Modeling my data/Customer_dimension.png)

The DimCustomer table was created by referencing FACTSALES and keeping only three columns: Customer Name, Customer ID, and Segment. A Table.Distinct step was then applied on Customer ID to remove duplicate rows and keep only one record per unique customer.

The result is a clean lookup table with 793 unique customers, each with their associated segment (Consumer, Corporate, or Home Office). This table will be used to filter and group data by customer in the report.

### DimProduct

![Product Dimension](assets/images/Superstore/Cleanning and Modeling my data/Product_Dimension.png)

DimProduct follows the same logic. It references FACTSALES, keeps Product Name, Sub-Category, Category, and Product ID, then applies Table.Distinct on Product ID to deduplicate. The result is a product catalog where each product appears exactly once with its full classification hierarchy.

This table allows the report to slice sales and profit by Category and Sub-Category without duplicating that information inside the fact table.

### DimDate

![Date Dimension](assets/images/Superstore/Cleanning and Modeling my data/Date_Dimmension.png)

DimDate was created by referencing FACTSALES, keeping only the Order Date column, and applying Table.Distinct to get one row per unique date. This gives a list of all dates present in the dataset.

A proper date table ideally covers every calendar day in the range (not just transaction dates), and includes columns like Year, Month Number, Month Name, and Quarter to support time intelligence. The Month Name and Month Number columns visible in the model view confirm that these were added to support the monthly trend visuals in the report.

---

## Part 3 Building the Star Schema

### The Model

![Modeling](assets/images/Superstore/Cleanning and Modeling my data/Modeling.png)

With all four tables ready (FACTSALES, DimCustomer, DimProduct, DimDate), the next step was to build the relationships in the Model view. The target structure is a star schema: one central fact table connected to multiple dimension tables, each linked by a shared key.

The relationships defined in this model are the following:

- FACTSALES[Customer ID] connected to DimCustomer[Customer ID]
- FACTSALES[Product ID] connected to DimProduct[Product ID]
- FACTSALES[Order Date] connected to DimDate[Order Date]

Each of these is a one-to-many relationship (1 on the dimension side, many on the fact side), which is the standard and correct configuration for a star schema in Power BI.

The SUPERSTORE source table remains visible in the model but is hidden from report view since it is only used as a source for the other queries and is not meant to be used in visuals directly.

### The Relations and Their Importance

![Relationships](assets/images/Superstore/Cleanning and Modeling my data/Relations.png)

Relationships are what allow Power BI to filter across tables automatically. When a slicer on the report filters by Segment (from DimCustomer), Power BI uses the relationship between DimCustomer and FACTSALES to filter the corresponding sales rows. Without relationships, every measure would need to be written with explicit filter logic in DAX, which is far more complex and error-prone.

Getting the cardinality right matters. A many-to-many relationship between two tables that should be one-to-many will produce incorrect filter propagation and unreliable results. Using a deduplicated dimension table on the "one" side of each relationship is what guarantees the cardinality is correct.

### The Filter Directions

![Directions](assets/images/Superstore/Cleanning and Modeling my data/Directions.png)

Each relationship has a filter direction, indicated by the arrow in the model view. In this model, all relationships use single direction filtering, meaning filters flow from the dimension table toward the fact table.

This is the recommended default for star schemas. It means that selecting a value in a dimension (for example, a product category in DimProduct) will filter the rows in FACTSALES accordingly. The reverse does not happen automatically, which prevents ambiguous and circular filter paths that can break measures.

The two relationships involving DimProduct and DimDate show their arrows pointing toward FACTSALES, confirming that the filter direction is correctly set from the dimension to the fact table.

---

## Conclusion

The data preparation phase is the least visible part of a Power BI project but arguably the most important. Every visual, every measure, and every insight in the report depends on the quality of what was built here.

The key decisions made in this phase were the following. The source dataset was never modified directly, all transformations were applied to references and duplicates. Data types were corrected at the source level so that all derived tables inherit clean formats. Dimension tables were deduplicated to guarantee the integrity of one-to-many relationships. The star schema was built with single-direction filters to ensure predictable and correct filtering across the model.

A clean model is what makes DAX measures simple to write, reports fast to load, and results reliable to trust. Taking the time to structure the data properly before touching a single visual is what separates a well-built Power BI report from one that causes problems down the line.

The next post will cover the DAX measures written on top of this model and how the three report pages were built.

---
*Thank you for taking the time to explore this project. It forms part of my data analytics portfolio and showcases my ability to transform business data into meaningful insights using Power BI. Feedback and suggestions are always welcome.
*This project was built with Power BI Desktop using the public Superstore sample dataset, as part of my ongoing data portfolio.*


---
title: "Power BI Project:Superstore Data Preparation & Modeling"
date: 2026-07-07 00:00:00 +0000
categories: [Power BI, Data Modeling]
tags: [Power BI, Power Query, Star Schema, Data Cleaning, DAX]
image:
  path: assets/images/cleaning_data.jpg
  alt: "Superstore Power BI Data Modeling"
---

## Introduction

Before building any dashboard in Power BI, the foundation has to be solid. A well-structured data model is what separates a report that works from one that actually scales, performs, and gives reliable results. In this post, I walk through everything that happened before the first visual was placed: cleaning the raw Superstore dataset, creating dimension tables, and building the star schema that powers the entire report.

The raw Superstore file is a flat table. Every row contains information about an order, a customer, a product, a region, and a date, all mixed together in a single sheet. That structure works fine for a spreadsheet, but it is not suited for analytical modeling. The goal of this preparation phase was to reshape that flat file into a proper relational model made of one fact table and several dimension tables, connected through clean and intentional relationships.

---

## Part 1 Cleaning the Source Dataset

### The Source Table (SUPERSTORE)

The first step was to load the raw CSV file into Power Query. This created the base SUPERSTORE query, which serves as the untouched reference from which all other tables are derived. The source is never modified directly. Every transformation happens on a reference or duplicate of this query, which ensures the original data remains intact throughout the process.

![ Cleaned Dataset](assets/images/Superstore/Cleanning and Modeling my data/Cleaned_dataset_source.png)

The steps applied to the source query were the following:

**Source** - connects to the raw CSV file.

**En-têtes promus** - promotes the first row as column headers, since the raw file does not automatically detect them.

**ABC to DATE type** - converts the Order Date and Ship Date columns from text format to proper date types. This is critical because Power BI cannot build time intelligence or date hierarchies on text columns.

**ABC to DECIMAL type** - converts numeric columns like Sales, Profit, and Discount from text to decimal numbers so they can be aggregated in measures.

**ABC to NUMBER type** - converts integer columns like Quantity and Row ID to whole number format.

**Removal of empty rows** - this is the step that appeared as a "Filtered Rows" step. Power Query uses Table.SelectRows with the condition "each true" after removing empty rows, which filters out any rows where all values are null. This typically happens when the source file has trailing blank lines at the bottom, which is common with CSV exports.

Each of these steps is essential. Wrong data types are one of the most common causes of broken measures and incorrect aggregations in Power BI. Getting them right at the source level means every derived table inherits clean, reliable data.

---

### The FACTSALES Table

![Columns Deletd](assets/images/Superstore/Cleanning and Modeling my data/Deleted Columns.png)

The FACTSALES table is a reference of the cleaned SUPERSTORE query. From that reference, unnecessary columns were removed using Table.SelectColumns, keeping only the fields relevant to transactions: Order ID, Order Date, Ship Date, Ship Mode, Customer ID, Segment, Region, Product ID, Product Name, Sales, Quantity, Discount, Profit, and Category.

This table is the core of the model. It contains one row per order line and holds all the measurable facts (Sales, Profit, Discount, Quantity) alongside the foreign keys that link to each dimension table (Customer ID, Product ID, Order Date).

Keeping only the necessary columns reduces the file size and keeps the model clean. Any column that belongs to a dimension has no place in the fact table once the relationships are in place.

---

## Part 2 Creating the Dimension Tables

### DimCustomer

![Customer Dimension](assets/images/Superstore/Cleanning and Modeling my data/Customer_dimension.png)

The DimCustomer table was created by referencing FACTSALES and keeping only three columns: Customer Name, Customer ID, and Segment. A Table.Distinct step was then applied on Customer ID to remove duplicate rows and keep only one record per unique customer.

The result is a clean lookup table with 793 unique customers, each with their associated segment (Consumer, Corporate, or Home Office). This table will be used to filter and group data by customer in the report.

### DimProduct

![Product Dimension](assets/images/Superstore/Cleanning and Modeling my data/Product_Dimension.png)

DimProduct follows the same logic. It references FACTSALES, keeps Product Name, Sub-Category, Category, and Product ID, then applies Table.Distinct on Product ID to deduplicate. The result is a product catalog where each product appears exactly once with its full classification hierarchy.

This table allows the report to slice sales and profit by Category and Sub-Category without duplicating that information inside the fact table.

### DimDate

![Date Dimension](assets/images/Superstore/Cleanning and Modeling my data/Date_Dimmension.png)

DimDate was created by referencing FACTSALES, keeping only the Order Date column, and applying Table.Distinct to get one row per unique date. This gives a list of all dates present in the dataset.

A proper date table ideally covers every calendar day in the range (not just transaction dates), and includes columns like Year, Month Number, Month Name, and Quarter to support time intelligence. The Month Name and Month Number columns visible in the model view confirm that these were added to support the monthly trend visuals in the report.

---

## Part 3 Building the Star Schema

### The Model

![Modeling](assets/images/Superstore/Cleanning and Modeling my data/Modeling.png)

With all four tables ready (FACTSALES, DimCustomer, DimProduct, DimDate), the next step was to build the relationships in the Model view. The target structure is a star schema: one central fact table connected to multiple dimension tables, each linked by a shared key.

The relationships defined in this model are the following:

- FACTSALES[Customer ID] connected to DimCustomer[Customer ID]
- FACTSALES[Product ID] connected to DimProduct[Product ID]
- FACTSALES[Order Date] connected to DimDate[Order Date]

Each of these is a one-to-many relationship (1 on the dimension side, many on the fact side), which is the standard and correct configuration for a star schema in Power BI.

The SUPERSTORE source table remains visible in the model but is hidden from report view since it is only used as a source for the other queries and is not meant to be used in visuals directly.

### The Relations and Their Importance

![Relationships](assets/images/Superstore/Cleanning and Modeling my data/Relations.png)

Relationships are what allow Power BI to filter across tables automatically. When a slicer on the report filters by Segment (from DimCustomer), Power BI uses the relationship between DimCustomer and FACTSALES to filter the corresponding sales rows. Without relationships, every measure would need to be written with explicit filter logic in DAX, which is far more complex and error-prone.

Getting the cardinality right matters. A many-to-many relationship between two tables that should be one-to-many will produce incorrect filter propagation and unreliable results. Using a deduplicated dimension table on the "one" side of each relationship is what guarantees the cardinality is correct.

### The Filter Directions

![Directions](assets/images/Superstore/Cleanning and Modeling my data/Directions.png)

Each relationship has a filter direction, indicated by the arrow in the model view. In this model, all relationships use single direction filtering, meaning filters flow from the dimension table toward the fact table.

This is the recommended default for star schemas. It means that selecting a value in a dimension (for example, a product category in DimProduct) will filter the rows in FACTSALES accordingly. The reverse does not happen automatically, which prevents ambiguous and circular filter paths that can break measures.

The two relationships involving DimProduct and DimDate show their arrows pointing toward FACTSALES, confirming that the filter direction is correctly set from the dimension to the fact table.

---

## Conclusion

The data preparation phase is the least visible part of a Power BI project but arguably the most important. Every visual, every measure, and every insight in the report depends on the quality of what was built here.

The key decisions made in this phase were the following. The source dataset was never modified directly, all transformations were applied to references and duplicates. Data types were corrected at the source level so that all derived tables inherit clean formats. Dimension tables were deduplicated to guarantee the integrity of one-to-many relationships. The star schema was built with single-direction filters to ensure predictable and correct filtering across the model.

A clean model is what makes DAX measures simple to write, reports fast to load, and results reliable to trust. Taking the time to structure the data properly before touching a single visual is what separates a well-built Power BI report from one that causes problems down the line.

The next post will cover the DAX measures written on top of this model and how the three report pages were built.

---
*Thank you for taking the time to explore this project. It forms part of my data analytics portfolio and showcases my ability to transform business data into meaningful insights using Power BI. Feedback and suggestions are always welcome.
*This project was built with Power BI Desktop using the public Superstore sample dataset, as part of my ongoing data portfolio.*



---
title: "Power BI Project:Superstore Data Preparation & Modeling"
date: 2026-07-13 00:00:00 +0000
categories: [Power BI, Data Modeling]
tags: [Power BI, Power Query, Star Schema, Data Cleaning, DAX]
image:
  path: assets/images/cleaning_data.jpg
  alt: "Superstore Power BI Data Modeling"
---

## Introduction

Before building any dashboard in Power BI, the foundation has to be solid. A well-structured data model is what separates a report that works from one that actually scales, performs, and gives reliable results. In this post, I walk through everything that happened before the first visual was placed: cleaning the raw Superstore dataset, creating dimension tables, and building the star schema that powers the entire report.

The raw Superstore file is a flat table. Every row contains information about an order, a customer, a product, a region, and a date, all mixed together in a single sheet. That structure works fine for a spreadsheet, but it is not suited for analytical modeling. The goal of this preparation phase was to reshape that flat file into a proper relational model made of one fact table and several dimension tables, connected through clean and intentional relationships.

---

## Part 1 Cleaning the Source Dataset

### The Source Table (SUPERSTORE)

The first step was to load the raw CSV file into Power Query. This created the base SUPERSTORE query, which serves as the untouched reference from which all other tables are derived. The source is never modified directly. Every transformation happens on a reference or duplicate of this query, which ensures the original data remains intact throughout the process.

![ Cleaned Dataset](assets/images/Superstore/Cleanning and Modeling my data/Cleaned_dataset_source.png)

The steps applied to the source query were the following:

**Source** - connects to the raw CSV file.

**En-têtes promus** - promotes the first row as column headers, since the raw file does not automatically detect them.

**ABC to DATE type** - converts the Order Date and Ship Date columns from text format to proper date types. This is critical because Power BI cannot build time intelligence or date hierarchies on text columns.

**ABC to DECIMAL type** - converts numeric columns like Sales, Profit, and Discount from text to decimal numbers so they can be aggregated in measures.

**ABC to NUMBER type** - converts integer columns like Quantity and Row ID to whole number format.

**Removal of empty rows** - this is the step that appeared as a "Filtered Rows" step. Power Query uses Table.SelectRows with the condition "each true" after removing empty rows, which filters out any rows where all values are null. This typically happens when the source file has trailing blank lines at the bottom, which is common with CSV exports.

Each of these steps is essential. Wrong data types are one of the most common causes of broken measures and incorrect aggregations in Power BI. Getting them right at the source level means every derived table inherits clean, reliable data.

---

### The FACTSALES Table

![Columns Deletd](assets/images/Superstore/Cleanning and Modeling my data/Deleted Columns.png)

The FACTSALES table is a reference of the cleaned SUPERSTORE query. From that reference, unnecessary columns were removed using Table.SelectColumns, keeping only the fields relevant to transactions: Order ID, Order Date, Ship Date, Ship Mode, Customer ID, Segment, Region, Product ID, Product Name, Sales, Quantity, Discount, Profit, and Category.

This table is the core of the model. It contains one row per order line and holds all the measurable facts (Sales, Profit, Discount, Quantity) alongside the foreign keys that link to each dimension table (Customer ID, Product ID, Order Date).

Keeping only the necessary columns reduces the file size and keeps the model clean. Any column that belongs to a dimension has no place in the fact table once the relationships are in place.

---

## Part 2 Creating the Dimension Tables

### DimCustomer

![Customer Dimension](assets/images/Superstore/Cleanning and Modeling my data/Customer_dimension.png)

The DimCustomer table was created by referencing FACTSALES and keeping only three columns: Customer Name, Customer ID, and Segment. A Table.Distinct step was then applied on Customer ID to remove duplicate rows and keep only one record per unique customer.

The result is a clean lookup table with 793 unique customers, each with their associated segment (Consumer, Corporate, or Home Office). This table will be used to filter and group data by customer in the report.

### DimProduct

![Product Dimension](assets/images/Superstore/Cleanning and Modeling my data/Product_Dimension.png)

DimProduct follows the same logic. It references FACTSALES, keeps Product Name, Sub-Category, Category, and Product ID, then applies Table.Distinct on Product ID to deduplicate. The result is a product catalog where each product appears exactly once with its full classification hierarchy.

This table allows the report to slice sales and profit by Category and Sub-Category without duplicating that information inside the fact table.

### DimDate

![Date Dimension](assets/images/Superstore/Cleanning and Modeling my data/Date_Dimmension.png)

DimDate was created by referencing FACTSALES, keeping only the Order Date column, and applying Table.Distinct to get one row per unique date. This gives a list of all dates present in the dataset.

A proper date table ideally covers every calendar day in the range (not just transaction dates), and includes columns like Year, Month Number, Month Name, and Quarter to support time intelligence. The Month Name and Month Number columns visible in the model view confirm that these were added to support the monthly trend visuals in the report.

---

## Part 3 Building the Star Schema

### The Model

![Modeling](assets/images/Superstore/Cleanning and Modeling my data/Modeling.png)

With all four tables ready (FACTSALES, DimCustomer, DimProduct, DimDate), the next step was to build the relationships in the Model view. The target structure is a star schema: one central fact table connected to multiple dimension tables, each linked by a shared key.

The relationships defined in this model are the following:

- FACTSALES[Customer ID] connected to DimCustomer[Customer ID]
- FACTSALES[Product ID] connected to DimProduct[Product ID]
- FACTSALES[Order Date] connected to DimDate[Order Date]

Each of these is a one-to-many relationship (1 on the dimension side, many on the fact side), which is the standard and correct configuration for a star schema in Power BI.

The SUPERSTORE source table remains visible in the model but is hidden from report view since it is only used as a source for the other queries and is not meant to be used in visuals directly.

### The Relations and Their Importance

![Relationships](assets/images/Superstore/Cleanning and Modeling my data/Relations.png)

Relationships are what allow Power BI to filter across tables automatically. When a slicer on the report filters by Segment (from DimCustomer), Power BI uses the relationship between DimCustomer and FACTSALES to filter the corresponding sales rows. Without relationships, every measure would need to be written with explicit filter logic in DAX, which is far more complex and error-prone.

Getting the cardinality right matters. A many-to-many relationship between two tables that should be one-to-many will produce incorrect filter propagation and unreliable results. Using a deduplicated dimension table on the "one" side of each relationship is what guarantees the cardinality is correct.

### The Filter Directions

![Directions](assets/images/Superstore/Cleanning and Modeling my data/Directions.png)

Each relationship has a filter direction, indicated by the arrow in the model view. In this model, all relationships use single direction filtering, meaning filters flow from the dimension table toward the fact table.

This is the recommended default for star schemas. It means that selecting a value in a dimension (for example, a product category in DimProduct) will filter the rows in FACTSALES accordingly. The reverse does not happen automatically, which prevents ambiguous and circular filter paths that can break measures.

The two relationships involving DimProduct and DimDate show their arrows pointing toward FACTSALES, confirming that the filter direction is correctly set from the dimension to the fact table.

---

## Conclusion

The data preparation phase is the least visible part of a Power BI project but arguably the most important. Every visual, every measure, and every insight in the report depends on the quality of what was built here.

The key decisions made in this phase were the following. The source dataset was never modified directly, all transformations were applied to references and duplicates. Data types were corrected at the source level so that all derived tables inherit clean formats. Dimension tables were deduplicated to guarantee the integrity of one-to-many relationships. The star schema was built with single-direction filters to ensure predictable and correct filtering across the model.

A clean model is what makes DAX measures simple to write, reports fast to load, and results reliable to trust. Taking the time to structure the data properly before touching a single visual is what separates a well-built Power BI report from one that causes problems down the line.

The next post will cover the DAX measures written on top of this model and how the three report pages were built.

---
*Thank you for taking the time to explore this project. It forms part of my data analytics portfolio and showcases my ability to transform business data into meaningful insights using Power BI. Feedback and suggestions are always welcome.
*This project was built with Power BI Desktop using the public Superstore sample dataset, as part of my ongoing data portfolio.*


---
layout: post
title: "Northwind SQL Analysis : From Raw CSV Files to Advanced SQL"
date: 2026-08-08
categories: [SQL, Data Analysis, Northwind]
tags: [SQL Server, SSMS, Northwind, SQL, Data Analysis, BULK INSERT, JOIN, CTE, Subquery]
image: assets/images/Northwind/SQL_img/Northwind_SQL_Analysis.png
---

# Introduction

The first part of this project focuses entirely on the **SQL analysis of the Northwind dataset**.

Unlike my previous Superstore project, the objective here was not to immediately build a dashboard or focus primarily on visualization.

Instead, I wanted to understand what happens **before the data reaches a visualization tool**.

The project therefore starts with raw CSV files and follows the complete process of:

**Raw data → SQL Server → Database structure → Relationships → SQL analysis**

I also wanted to progressively develop my SQL skills by starting with relatively simple queries and gradually moving towards more advanced concepts.

The progression covered:

- filtering with `WHERE`;
- pattern matching with `LIKE`;
- aggregate functions;
- `GROUP BY`;
- `JOIN`;
- multiple-table `JOIN`s;
- `LEFT JOIN`;
- subqueries;
- correlated subqueries;
- nested subqueries;
- CTEs;
- window functions;
- `LAG()`.

The objective was not simply to learn individual SQL commands, but to understand how these concepts can be combined to answer increasingly complex questions about the data.

---

# 1. Getting the Northwind Dataset

The project started with the **Northwind dataset**, provided as a collection of CSV files.

The dataset contains several related tables representing customers, orders, products, employees, categories and shippers.

The files I used included:

- `categories.csv`
- `customers.csv`
- `employees.csv`
- `order_details.csv`
- `orders.csv`
- `products.csv`
- `shippers.csv`

![Northwind dataset files](assets/images/Northwind/SQL_img/Northwind_Datasets_CSV.png)

The first step was therefore to get these CSV files ready to be imported into SQL Server.

---

# 2. Creating the SQL Server Database

Before importing the CSV files, I created a dedicated database in **SQL Server Management Studio (SSMS)**.

The purpose was to have a structured environment where I could store the Northwind tables and perform the analysis using SQL.

![Creating the Northwind database](assets/images/Northwind/SQL_img/Creating_Northwind_Database.png)

Once the database had been created, I could start working on the individual CSV files.

---

# 3. Importing the Northwind Dataset

Once the database had been created, the next step was to bring the Northwind CSV files into SQL Server.

My first approach was to try importing the CSV files directly through the SQL Server interface.

However, I ran into problems with the import process and was not able to use the standard import method successfully.

Rather than continuing to rely on the graphical import interface, I decided to take a more direct SQL approach.

This meant creating the tables myself and then importing the contents of the CSV files using SQL.

# 3.1 First Attempt Importing the CSV Files

My initial approach was to use the SQL Server import functionality to bring the CSV files directly into the database.

This approach did not work as expected, so I changed the method I was using.

Instead of allowing SQL Server to automatically create the tables from the CSV files, I decided to define the table structure myself.

This also gave me more control over:

* column names;
* data types;
* primary keys;
* foreign keys;
* the structure of each table.

# 3.2 Creating the Tables Manually

I therefore created the Northwind tables directly in SQL Server before importing the data.

The tables corresponded to the different CSV files:

| CSV file            | SQL table       |
| :------------------ | :-------------- |
| `categories.csv`    | `Categories`    |
| `customers.csv`     | `Customers`     |
| `employees.csv`     | `Employees`     |
| `order_details.csv` | `Order_Details` |
| `orders.csv`        | `Orders`        |
| `products.csv`      | `Products`      |
| `shippers.csv`      | `Shippers`      |

![Northwind tables created in SSMS](assets/images/Northwind/SQL_img/Northwind_SSMS_Explorer.png)

Creating the tables first meant that I could define the database structure myself rather than relying on SQL Server to infer everything from the CSV files.

This became particularly important when I later defined the primary and foreign keys.

# 3.3 Importing the CSV Files Using BULK INSERT

Once the tables had been created, I needed to populate them with the data contained in the CSV files.

For this, I used SQL Server's `BULK INSERT` statement.

Instead of importing the files through a graphical wizard, I instructed SQL Server directly to read the CSV files and insert their contents into the corresponding tables.

The general process was:

CSV file → BULK INSERT → SQL table

![BULK INSERT queries](assets/images/Northwind/SQL_img/Import_Table_query.png)

I repeated the same process for the other Northwind tables.

The process therefore became:

Create table

Specify the table structure

Use `BULK INSERT`

Load the CSV data

Verify the imported records

This approach gave me a better understanding of what was actually happening during data import instead of relying entirely on a graphical interface.

# 3.4 Verifying the Imported Data

After loading each CSV file, I checked that the data had actually been inserted into the corresponding table.

![Verifying imported row counts](assets/images/Northwind/SQL_img/Import_Table_Verification.png)

I also checked the tables inside SQL Server Management Studio.

At this point, the CSV files had successfully been transformed into a structured relational database.

The database was now ready for the next stage:

defining the relationships between the tables.

# 4. Understanding the Database Structure

Before starting the SQL analysis, I needed to understand how the different tables were connected.

Northwind is a **relational database**, meaning that information is distributed across multiple tables and connected through keys.

For example:

**Customers → Orders**

The `CustomerID` stored in the `Orders` table allows an order to be associated with a customer.

Another relationship is:

**Orders → Order_Details**

The `OrderID` connects an order to its individual order details.

And:

**Products → Order_Details**

The `ProductID` connects an individual order detail to the product that was purchased.

A simplified representation is therefore:

**Customers → Orders → Order_Details → Products**

Other tables such as `Employees`, `Shippers` and `Categories` are connected through their respective keys.

# 5. Defining Primary Keys

Once the tables had been created and populated, I identified the columns that uniquely identify records within each table.

Examples include:

* `Customers` → `CustomerID`
* `Orders` → `OrderID`
* `Products` → `ProductID`
* `Employees` → `EmployeeID`
* `Categories` → `CategoryID`
* `Shippers` → `ShipperID`

These columns were defined as **Primary Keys**.

A primary key uniquely identifies a record inside a table.

![CustomerID primary key](assets/images/Northwind/SQL_img/Customers_PK.png)

A good example would be the `Customers` table with `CustomerID` identified as the primary key.

# 6. Defining Foreign Keys and Relationships

After defining the primary keys, I created the relationships between the tables using foreign keys.

For example:

**Customers.CustomerID → Orders.CustomerID**

Here:

* `Customers.CustomerID` is the primary key.
* `Orders.CustomerID` is the foreign key.

Another relationship is:

**Orders.OrderID → Order_Details.OrderID**

And another:

**Products.ProductID → Order_Details.ProductID**

![Foreign keys in the Orders table](assets/images/Northwind/SQL_img/Multiple_FK_Orders.png)

This step was particularly important because these relationships would later be used when writing `JOIN` queries.

# 7. Starting the SQL Analysis

Once the database had been imported, structured and connected, I could finally begin analysing the data.

I deliberately started with very simple queries.

The objective was to build my understanding progressively rather than immediately jumping into complicated analytical queries.

The progression was:

**SELECT → WHERE → ORDER BY → Aggregations → GROUP BY → JOINs → Subqueries → CTEs → Window Functions**

The following sections show selected examples from that progression.

# 8. Basic Data Retrieval — SELECT

The first concept I worked with was `SELECT`.

The purpose was simply to retrieve information from a table.

For example, I retrieved customer contact names from the `Customers` table.

At this stage, I was learning the basic structure of a SQL query:

**SELECT → FROM → Table**

The query was simple, but it established the foundation for everything that followed.

# 9. Filtering Data with WHERE

Once I understood how to retrieve data, I moved on to filtering.

The next question was more specific:

Which products have a unit price between 20 and 50?

This introduced:

* `WHERE`;
* comparison operators;
* `AND`;
* `ORDER BY`.

![WHERE filtering query](assets/images/Northwind/SQL_img/Query_filtrage_de_base.png)

![WHERE filtering result](assets/images/Northwind/SQL_img/Result_query_filtrage_de_base.png)

This represented an important progression because SQL was no longer simply retrieving all records.

I was now asking SQL to return only records matching specific conditions.

# 10. Aggregate Functions

After learning how to retrieve and filter individual records, I started looking at the data from a more analytical perspective.

I worked with aggregate functions such as:

* `COUNT()`;
* `SUM()`;
* `AVG()`;
* `MIN()`;
* `MAX()`.

For example, I calculated the most expensive and cheapest products.

![Aggregate functions query and result](assets/images/Northwind/SQL_img/Aggregration_simple_query.png)

This represented an important change in the type of analysis.

Instead of returning individual records, SQL was now calculating a metric from the dataset.

# 11. GROUP BY

The next step was to group information.

Instead of asking how many customers existed in total, I wanted to know how many customers existed in each country.

This introduced the `GROUP BY` clause.

This allowed me to compare different groups within the dataset rather than looking at the dataset as a whole.

# 12. JOIN : Connecting Tables

At this point, I had mainly been working with individual tables.

However, one of the main advantages of a relational database is the ability to combine information from multiple tables.

This is where `JOIN` became essential.

For example, an order contains a `CustomerID`, while the customer's name is stored in the `Customers` table.

I therefore needed to connect the two tables.

The JOIN uses the matching key between the two tables.

This was the point where the relationships created during the database setup became directly useful for analysis.

# 13. Multiple JOINs

After understanding a basic JOIN, I increased the complexity by joining several tables together.

The objective was to retrieve information about:

* the order;
* the customer;
* the employee;
* the shipper.

![Multiple JOINs query and result](assets/images/Northwind/SQL_img/Join_multitables_query.png)

This demonstrated how several relationships within the database could be combined to create a richer result.

# 14. JOIN + WHERE : Answering a Business Question

I then combined the concepts I had learned so far.

The question became:

Which products have been purchased by customers from Germany?

To answer this, I needed to connect:

**Customers → Orders → Order_Details → Products**

This represented an important change in the way I was using SQL.

I was no longer writing queries only to practise SQL syntax.

I was beginning to use SQL to answer actual business questions.

# 15. Calculating Revenue

The next stage was to create an actual business metric.

Using the order details, I calculated revenue using:

**Unit Price × Quantity × (1 − Discount)**

I then used this calculation to identify the categories generating the highest revenue.

![Revenue by category query and result](assets/images/Northwind/SQL_img/GROUPBY_QUERY.png)

This query combined several concepts that I had previously learned individually:

* `JOIN`;
* `SUM()`;
* calculations;
* `GROUP BY`;
* `ORDER BY`.

The question had now evolved from:

What data is in the database?

to:

Which categories generate the most revenue?

# 16. LEFT JOIN : Finding Missing Relationships

I then used JOINs for another type of problem.

Instead of looking for records that matched between tables, I wanted to identify customers who had never placed an order.

This introduced the combination of:

**LEFT JOIN + IS NULL**

![LEFT JOIN finding customers with no orders](assets/images/Northwind/SQL_img/Left_Join_Query_find_abscence.png)

This demonstrated that JOINs can also be used to identify missing relationships between tables.

# 17. Subqueries : Comparing Against an Average

After becoming comfortable with joins and aggregations, I moved towards more advanced SQL logic.

The question was:

Which products are more expensive than the average product price?

This required a query inside another query.

![Subquery comparing against average price](assets/images/Northwind/SQL_img/Sous_requete_simple.png)

The inner query calculates the average product price.

The outer query then compares each product against that value.

The logic is:

Calculate average → Compare products → Keep products above average

This was an important step because SQL was now being used to produce one result and then use that result as part of another calculation.

# 18. Correlated Subqueries

I then increased the complexity further.

Instead of comparing every product against the average price of all products, I wanted to compare each product against the average price of products within its own category.

This required a **correlated subquery**.

![Correlated subquery by category average](assets/images/Northwind/SQL_img/Sous_requete_corrélé.png)

The important difference is that the inner query refers back to the current row of the outer query.

This meant that the average was calculated according to the product's category.

The progression was now:

**Basic query → Aggregate → Subquery → Correlated subquery**

# 19. Common Table Expressions (CTEs)

As the analytical questions became more complicated, I needed a way to structure my queries into clearer steps.

This led me to **Common Table Expressions**, commonly called CTEs.

I first used a CTE to calculate monthly sales.

A CTE allowed me to create an intermediate result that could then be used by the main query.

The logical structure became:

**Orders + Order_Details → Monthly Sales → Further Analysis**

This made more complex queries easier to organise.

# 20. CTE + Window: Function Cumulative Sales

Once I understood CTEs, I combined them with a window function.

The objective was to calculate cumulative sales over time.

![CTE with window function for cumulative sales](assets/images/Northwind/SQL_img/CTE_SIMPLE_avec_fonctiondefenetrage.png)

The result allowed me to compare:

* the revenue generated during each individual month;
* the revenue accumulated up to that month.

This was another major progression because I was now analysing values across rows while preserving the individual monthly records.

# 21. Final SQL Analysis: Month-over-Month Growth

For the final stage of this SQL analysis, I wanted to calculate how sales changed compared with the previous month.

The question was:

How much did sales increase or decrease compared with the previous month?

To answer this, I used:

* CTEs;
* aggregation;
* `LAG()`;
* calculated columns;
* percentage calculations.

![CTE with LAG for month-over-month growth](assets/images/Northwind/SQL_img/CTE_double_LAG.png)

The logic can be simplified as:

Calculate monthly sales

Retrieve previous month's sales

Compare current month against previous month

Calculate percentage growth

The `LAG()` function allowed me to access the previous month's value while keeping the current month's row.

This represented the most advanced SQL analysis of this first part of the project.

# 22. The Progression of My SQL Learning

Looking back at the project, the queries were not isolated exercises.

They formed a progression in my understanding of SQL.

The development can be summarised as:

**Database creation**

**CSV import using BULK INSERT**

**Primary and foreign keys**

**Relationships**

**SELECT**

**WHERE**

**ORDER BY**

**Aggregate functions**

**GROUP BY**

**JOIN**

**Multiple JOINs**

**Business calculations**

**LEFT JOIN**

**Subqueries**

**Correlated subqueries**

**CTEs**

**Window functions**

**LAG()**

**Month-over-month analysis**

The important progression was therefore not simply learning more SQL commands.

It was learning how to combine these concepts to answer increasingly complex questions.

I started with:

How do I retrieve information?

Then:

How do I filter and summarise information?

Then:

How do I combine information from different tables?

And finally:

How can I use SQL to perform actual business analysis?

# Conclusion

This first part of the Northwind project allowed me to work through the SQL analysis process from the database setup to advanced analytical queries.

I started with the raw CSV files.

My initial attempt was to import them directly through SQL Server, but after encountering problems with that approach, I changed my method.

Instead, I created the tables myself and used `BULK INSERT` to load the CSV data into SQL Server.

This gave me a better understanding of the database structure and allowed me to control the tables before establishing their relationships.

I then identified the primary keys, defined the foreign keys and created the relationships between the different tables.

From there, I progressively developed my SQL analysis.

The progression took me from:

**Basic data retrieval**

to:

**Filtering and aggregation**

then:

**Grouping and JOINs**

then:

**Business calculations**

and finally:

**Subqueries, CTEs and window functions**

The final month-over-month analysis represents the most advanced SQL stage of this project.

More importantly, I was no longer simply practising SQL syntax.

I was using SQL to answer questions about customers, products, orders, revenue and sales performance.

This established the analytical foundation for the second part of the project.

# Part 2: Connecting SQL Server to Power BI

The next stage of the project will move away from SQL syntax and towards visualization.

Now that the database has been structured and analysed using SQL Server, I can use it as the foundation for a Power BI project.

The next part will focus on:

* Connecting Power BI to SQL Server;
* importing the required tables;
* checking the existing relationships;
* preparing the data model;
* creating DAX measures;
* building visualizations;
* analysing the results;
* creating the final interactive dashboard.

The question will therefore change from:

How can I analyse the data using SQL?

to:

How can I transform this analysis into an interactive Power BI report?

# Project Progression

This project will therefore be divided into two major stages.

## Part 1: SQL Analysis

**Raw CSV files**

**SQL Server database**

**Manual table creation**

**BULK INSERT**

**Primary and foreign keys**

**Relationships**

**Basic SQL**

**JOINs**

**Subqueries**

**CTEs**

**Window Functions**

**Advanced SQL analysis**

## Part 2: Power BI Analysis

**SQL Server**

**Power BI connection**

**Data model**

**DAX**

**Visualisations**

**Dashboard**

The objective is to show the complete journey from raw relational data to a finished analytical report.
---
layout: post
title: "From SQL Server to Power BI: Turning Northwind Data into an Interactive Dashboard"
date: 2026-08-12
categories: [SQL, Power BI, Data Analysis]
tags: [SQL Server, Power BI, DAX, Northwind, Data Analytics]
image: assets/images/Northwind/POWERBI/POWERBI_COVER_PHOTO1.jpg
---
