# Retail Sales ETL Pipeline using PySpark, Databricks & Delta Lake

## Overview

This project demonstrates an end-to-end Data Engineering pipeline built using PySpark, Databricks, SQL, and Delta Lake. The pipeline follows the Medallion Architecture (Bronze, Silver, Gold) to ingest, clean, transform, and analyze retail sales data.

The dataset intentionally contains data quality issues such as duplicate records, null values, invalid dates, and negative quantities to simulate real-world ETL challenges.

---

## Architecture

```text
Raw CSV Data
      │
      ▼
Bronze Layer (Raw Data)
      │
      ▼
Data Quality Validation
      │
      ├── Duplicate Detection
      ├── Null Value Validation
      ├── Invalid Date Validation
      └── Negative Quantity Validation
      │
      ▼
Silver Layer (Cleaned Data)
      │
      ▼
Revenue Calculation
      │
      ▼
Gold Layer (Business Aggregations)
      │
      ▼
Analytics & Reporting
```

---

## Technologies Used

- PySpark
- Databricks
- SQL
- Delta Lake
- Python

---

## Dataset

Retail sales dataset containing:

- Order Details
- Customer Information
- Product Information
- Category Details
- City Information
- Order Date
- Quantity and Price

### Data Quality Issues Introduced

- Duplicate Records
- Missing Values
- Invalid Dates
- Negative Quantities

---

## Bronze Layer

The raw CSV data is ingested into Databricks and stored without any transformations.

### Operations

- Read CSV file
- Create Bronze Table
- Preserve original data

Table:

```text
workspace.default.orders
```

---

## Data Quality Validation

Before transformation, several quality checks were performed:

### Duplicate Record Detection

```python
duplicates = (
    df.groupBy("order_id")
      .count()
      .filter("count > 1")
)
```

### Null Value Detection

```python
df.filter(
    col("quantity").isNull() |
    col("city").isNull()
)
```

### Invalid Date Detection

```python
try_to_timestamp(order_date)
```

### Negative Quantity Detection

```python
df.filter(col("quantity") < 0)
```

---

## Silver Layer

The Silver Layer contains cleaned and validated data.

### Transformations Performed

- Removed duplicate records
- Removed invalid dates
- Removed negative quantities
- Removed null values
- Standardized data format

### Revenue Calculation

```python
revenue = quantity * price
```

Table:

```text
workspace.default.orders_silver
```

---

## Gold Layer

Business-ready aggregated tables were created for analytics.

### Product Sales

```python
gold_df = (
    silver_df.groupBy("product_name")
             .agg(sum("revenue").alias("total_sales"))
)
```

Table:

```text
workspace.default.orders_gold
```

### City Revenue

Table:

```text
workspace.default.city_sales_gold
```

### Customer Spending

Table:

```text
workspace.default.customer_sales_gold
```

### Category Revenue

Table:

```text
workspace.default.category_sales_gold
```

---

## Sample Analytics Queries

### Top Selling Products

```sql
SELECT
    product_name,
    SUM(revenue) AS total_sales
FROM workspace.default.orders_silver
GROUP BY product_name
ORDER BY total_sales DESC;
```

### Top Customers

```sql
SELECT
    customer_id,
    SUM(revenue) AS total_spent
FROM workspace.default.orders_silver
GROUP BY customer_id
ORDER BY total_spent DESC
LIMIT 10;
```

### Revenue by City

```sql
SELECT
    city,
    SUM(revenue) AS total_revenue
FROM workspace.default.orders_silver
GROUP BY city
ORDER BY total_revenue DESC;
```

---

## Delta Lake Features Demonstrated

### Table History

```sql
DESCRIBE HISTORY workspace.default.orders_silver;
```

### File Optimization

```sql
OPTIMIZE workspace.default.orders_silver;
```

### Storage Cleanup

```sql
VACUUM workspace.default.orders_silver RETAIN 168 HOURS;
```

### Benefits

- ACID Transactions
- Data Reliability
- Efficient Storage
- Version Tracking
- Performance Optimization

---

## Project Screenshots

### Raw Data Ingestion

- Load Raw Data

### Data Validation

- Duplicate Record Check
- Null Value Check

### Gold Layer Analytics

- Product Sales Analysis
- City Revenue Analysis
- Top Customers Analysis

### Delta Lake Operations

- DESCRIBE HISTORY
- OPTIMIZE
- VACUUM

---

## Key Learnings

- Building ETL pipelines using PySpark
- Implementing Medallion Architecture
- Data quality validation techniques
- Delta Lake optimization features
- Business analytics using SQL and PySpark
- Creating analytics-ready datasets

---

## Project Outcome

Successfully developed an end-to-end ETL pipeline using PySpark, Databricks, SQL, and Delta Lake. The project transforms raw retail sales data into clean, analytics-ready datasets using industry-standard Data Engineering practices and Medallion Architecture.

---

## Author

**Sakthivel Murugan**

MCA Graduate | Data Engineering Enthusiast | Python | SQL | PySpark | Databricks
