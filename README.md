# Electronics Sales Data Analytics Pipeline

## Project Overview

This project implements an end-to-end data analytics pipeline for an electronics retail dataset using a Medallion Architecture (Bronze, Silver, Gold). The objective is to transform raw transactional data into structured, business-ready datasets and generate meaningful insights through key performance indicators (KPIs).

---

## Objectives

* Ingest and store raw data
* Clean and standardize datasets
* Build a star schema data model
* Create a curated analytics layer
* Develop KPI-driven insights for business analysis

---

## Architecture

### Bronze Layer

The bronze layer contains raw ingested data without any transformations. It serves as the source of truth for all downstream processing.

Tables include:

* sales
* customers
* products
* stores
* exchange_rates

---

### Silver Layer

The silver layer focuses on data cleaning and standardization. Transformations performed include:

* Handling missing values
* Standardizing column names and formats
* Correcting data types
* Removing inconsistencies

---

### Gold Layer

The gold layer contains business-ready data modeled using a star schema.

#### Fact Table

fact_sales
Includes transactional metrics such as:

* order_number
* productkey
* customerkey
* storekey
* revenue_usd
* delivery_days
* channel

#### Dimension Tables

* dim_customer (customer attributes such as gender and continent)
* dim_product (product category and subcategory)
* dim_store (store location details)

#### Curated Table

curated_sales
A denormalized dataset combining fact and dimension tables for analytics and reporting.

---

## Data Model

The project uses a star schema where the fact table is connected to multiple dimension tables. This design improves query performance and simplifies analytical queries.

---

## Key Transformations

* Conversion of exchange rates to numeric format
* Calculation of revenue in USD
* Derivation of delivery time in days
* Identification of sales channel (online or store)
* Replacement of null values with 'unknown' where applicable

---

## Key KPIs

1. Monthly Revenue Trend
2. Monthly Contribution to Total Revenue
3. Peak Sales by Category
4. Delivery Performance
5. Country-wise Delivery Analysis
6. Channel Performance (Average Order Value for online vs store)
7. Top Categories by Units Sold
8. Top Categories by Revenue
9. Customer Demographics Analysis
10. Repeat Customer Rate

---

## Sample KPI Query (Channel Performance)

```sql
SELECT
    COALESCE(continent, 'unknown') AS continent,
    ROUND(
        SUM(CASE WHEN channel = 'online' THEN revenue_usd ELSE 0 END) /
        NULLIF(COUNT(DISTINCT CASE WHEN channel = 'online' THEN order_number END), 0)
    ,2) AS aov_online,
    ROUND(
        SUM(CASE WHEN channel = 'store' THEN revenue_usd ELSE 0 END) /
        NULLIF(COUNT(DISTINCT CASE WHEN channel = 'store' THEN order_number END), 0)
    ,2) AS aov_store
FROM electronics_cat.gold.curated_sales
GROUP BY COALESCE(continent, 'unknown');
```

---

## Pipeline Flow

Bronze → Silver → Gold → Curated → KPI Analysis → Dashboard

---

## Technologies Used

* SQL (Databricks / Spark SQL)
* Data Warehousing Concepts
* Medallion Architecture
* Git and GitHub

---

## Challenges and Solutions

### Data Type Issues

Some numeric fields contained inconsistent formats. These were cleaned and converted using appropriate casting functions.

### Date Format Handling

Date fields required standardization to ensure accurate calculations and filtering.

### Channel Identification

Online and in-store sales were distinguished using the store key logic and handled consistently across datasets.

---

## Business Impact

* Identified top-performing product categories
* Analyzed delivery delays across countries
* Compared online and in-store sales performance
* Enabled customer segmentation and behavioral insights

---

## Project Structure

electronics-sales-analytics/

* bronze/
* silver/
* gold/

  * dim_tables/
  * fact_tables/
  * curated/
* notebooks/

  * nb_silver
  * nb_gold
  * nb_gold_2
* dashboards/
* screenshots/
* README.md

---

## Key Learnings

* Practical implementation of Medallion Architecture
* Data cleaning and transformation techniques
* Star schema data modeling
* Writing efficient SQL queries for analytics

---

## Conclusion

This project demonstrates how structured data engineering practices can transform raw data into actionable insights. The layered architecture ensures scalability, maintainability, and efficient analysis.

---

## Author

Rafigathunisa D
