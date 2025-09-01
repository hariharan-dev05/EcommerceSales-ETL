
# 🛒 Ecommerce Sales ETL Pipeline

A comprehensive data engineering solution that processes ecommerce sales data using Databricks and the Medallion Architecture. This pipeline transforms raw transactional data into business-ready insights through automated cleansing, standardization, and aggregation processes.

**Key Capabilities:**
- ✅ Daily automated data ingestion
- ✅ Robust data quality management  
- ✅ Real-time business KPI generation
- ✅ Scalable incremental processing


## Features

- **Bronze Layer**: Raw data ingestion with automatic partitioning
- **Silver Layer**: Advanced data cleansing and standardization
- **Gold Layer**: Business-ready aggregations and KPIs
- **Incremental Processing**: Delta Lake merge operations for efficiency
- **Data Quality**: Handles malformed dates, nulls, and invalid values
- **Business Insights**: Revenue trends, top products, category analysis
- **Orchestration**: Parameterized Databricks Jobs with dynamic date handling


## Tech Stack

**Cloud & Compute:**
- Databricks (PySpark, Delta Lake)
- Unity Catalog

**Data Architecture:**
- Medallion Architecture (Bronze → Silver → Gold)
- Delta Lake for ACID transactions
- Partitioned storage optimization

**Development:**
- Python/PySpark for ETL logic
- GitHub for version control
- Databricks Jobs for orchestration


## Orchestration

- Orchestrated using Databricks Jobs.

- Supports dynamic parameterization — StartDate is passed when triggering the job.

- Ensures only relevant partition data (year/month/day) is processed for incremental loads.


## SQL Query Usage


**Sales Fact Table Creation**

The business logic aggregates transactional data for sales_fact(without Order_date):
```sql
SELECT
product_id,
product_name,
category,
COUNT(DISTINCT order_id) AS total_orders, -- of unique orders
SUM(quantity) AS quantity_sold, -- units sold
SUM(price * quantity) AS gross_sales, -- monetary before discount
SUM((price * quantity) - discount) AS net_revenue  after discount
FROM ecommerce_catalog.silver.SalesCleansedData
GROUP BY product_id, product_name, category
ORDER BY net_revenue DESC;
```

**Total revenue per category**

```sql
SELECT 
    category, 
    SUM(net_revenue) AS total_revenue
FROM ecommerce_catalog.gold.sales_fact
GROUP BY category
ORDER BY total_revenue DESC;
```

**Top 5 products by revenue**

```sql
SELECT 
    product_id, product_name, 
    SUM(net_revenue) AS product_revenue
FROM ecommerce_catalog.gold.sales_fact
GROUP BY product_id, product_name
ORDER BY product_revenue DESC
LIMIT 5;
```

**Monthly sales trends**

```sql
SELECT 
    category,
    sales_year,
    sales_month,
    SUM(net_revenue) AS monthly_revenue,
    SUM(quantity_sold) AS monthly_quantity,
    SUM(total_orders) AS monthly_orders
FROM ecommerce_catalog.gold.sales_fact
GROUP BY category, sales_year, sales_month
ORDER BY sales_year, sales_month, category DESC;
```
## Installation

## 🏃‍♂️ How to Run

### Option 1: Databricks Jobs (Recommended)
1. **Create Job in Databricks**
2. **Chain notebooks**: Bronze → Silver → Gold
3. **Set parameters**: `{"StartDate": "20250901"}`
4. **Execute**: Click "Run Now" or schedule

### Option 2: Manual Notebook Execution
1. **Upload sample data** to `/Volumes/ecommerce_catalog/bronze/raw/`
2. **Run Bronze notebook** for data ingestion
3. **Run Silver notebook** for data cleansing  
4. **Run Gold notebook** for KPI generation
5. **Query results** from Unity Catalog tables

### Expected Results
- **Bronze**: Raw partitioned data by year/month/day
- **Silver**: Cleansed transactional data
- **Gold**: Business KPIs and aggregated insights

    
## Screenshots



