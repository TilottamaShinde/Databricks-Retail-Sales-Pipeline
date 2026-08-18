# Databricks Retail Sales Analytics Pipeline

An end-to-end retail sales analytics pipeline built using **Databricks, PySpark, Delta Lake, SQL, Unity Catalog, and Medallion Architecture**.

The project ingests raw retail CSV data, transforms it through **Bronze, Silver, and Gold layers**, applies data-quality and business rules, produces analytics-ready Delta tables, visualizes key business metrics through a Databricks dashboard, and orchestrates the complete pipeline using **Databricks Workflows**.

---

## Project Architecture

```text
Raw CSV Files
      |
      v
Unity Catalog Volume
      |
      v
Bronze Layer
Raw Delta Tables
      |
      v
Silver Layer
Cleaned & Validated Delta Tables
      |
      v
Gold Layer
Business Aggregations / KPIs
      |
      +----------------------+
      |                      |
      v                      v
Databricks SQL       Databricks Dashboard
```

**Orchestration:** Databricks Workflows  
**Compute:** Databricks Serverless  
**Schedule:** Daily automated execution  
**Monitoring:** Failure notifications

---

## Project Objectives

* Build an end-to-end Databricks data engineering pipeline.
* Implement **Medallion Architecture** using Bronze, Silver, and Gold layers.
* Perform data cleaning and validation using **PySpark**.
* Store datasets as **Delta tables**.
* Validate relationships between orders, customers, and products.
* Create business-ready aggregations and KPIs.
* Query Gold datasets using **Databricks SQL**.
* Build an interactive business dashboard.
* Automate the pipeline using **Databricks Workflows**.
* Version-control project code using **Git and GitHub**.

---

## Source Data

The project uses three retail datasets:

* `customers.csv`
* `products.csv`
* `orders.csv`

The sample data intentionally contains data-quality issues such as:

* Duplicate records
* Missing values
* Inconsistent capitalization
* Extra whitespace
* Invalid customer references
* Invalid product references
* Zero or negative quantities

These issues are identified and handled during **Silver-layer processing**.

---

## Unity Catalog and Storage

Raw CSV files are uploaded to a **Unity Catalog Volume**.

Unity Catalog provides governed access to Databricks data assets including:

* Catalogs
* Schemas
* Tables
* Volumes

The raw CSV files are read from the Volume into **Spark DataFrames** and subsequently persisted as Delta tables.

---

# Medallion Architecture

## Bronze Layer

The Bronze layer stores the raw source data with minimal transformation.

### Implemented

* Read raw `customers`, `products`, and `orders` CSV files.
* Loaded the data into Spark DataFrames.
* Inspected source schemas and records.
* Persisted the raw datasets as **Delta tables**.

### Bronze Tables

```text
workspace.bronze.customers
workspace.bronze.products
workspace.bronze.orders
```

---

## Silver Layer

The Silver layer converts raw Bronze data into **clean, standardized, and validated datasets**.

### Customers

* Removed duplicate records.
* Handled missing country values.
* Trimmed unnecessary whitespace.
* Standardized country capitalization.
* Verified `customer_id` uniqueness.

### Products

* Removed duplicate records.
* Standardized product categories.
* Trimmed unnecessary whitespace.
* Verified `product_id` uniqueness.

### Orders

* Removed duplicate records.
* Removed records with missing customer references.
* Validated customer IDs against the Silver customers table.
* Validated product IDs against the Silver products table.
* Removed invalid product references.
* Removed order lines with zero or negative quantities.
* Standardized order status values.
* Verified `order_date` uses the correct Date datatype.

### Silver Tables

```text
workspace.silver.customers
workspace.silver.products
workspace.silver.orders
```

---

## Referential Integrity Validation

Orders were validated against the customer and product master datasets.

A **Left Anti Join** was used to identify orders containing customer or product IDs that did not exist in the corresponding Silver tables.

A **Left Semi Join** was then used to retain only valid records.

Example:

```text
Orders
   |
   +---- customer_id ----> Silver Customers
   |
   +---- product_id -----> Silver Products
```

This prevents orphan or invalid transactions from reaching the Gold analytics layer.

---

## Gold Layer

The Gold layer contains **business-ready analytical datasets**.

Customers, products, and orders were joined into a unified sales dataset.

### Revenue Calculation

```text
Line Revenue = Quantity × Unit Price
```

Revenue-based analytics use **Completed orders**.

### Gold KPIs

* Monthly Revenue
* Revenue by Product
* Revenue by Country
* Average Order Value
* Top Customers by Revenue

### Gold Tables

```text
workspace.gold.monthly_revenue
workspace.gold.product_revenue
workspace.gold.country_revenue
workspace.gold.average_order_value
workspace.gold.top_customers
```

---

# SQL Analytics

Business queries are stored in:

```text
sql/gold_business_queries.sql
```

The SQL analysis includes:

* Monthly revenue
* Product revenue
* Revenue by country
* Average order value
* Top 10 customers by revenue

For example, Top Customers are ranked using:

```sql
SELECT *
FROM workspace.gold.top_customers
ORDER BY total_revenue DESC
LIMIT 10;
```

---

# Databricks Dashboard

An interactive **Retail Sales Analytics Dashboard** was created using Gold-layer datasets.

### Dashboard Visualizations

* **Monthly Revenue Trend** — line chart
* **Revenue by Product** — bar chart
* **Revenue by Country** — bar chart
* **Average Order Value** — currency KPI
* **Top 10 Customers by Revenue** — ranked bar chart

The dashboard demonstrates how Gold-layer datasets can be directly consumed for **business intelligence and reporting**.

---

# Workflow Orchestration

The complete Medallion pipeline is orchestrated using **Databricks Workflows**.

### Task Dependencies

```text
bronze_ingestion
       |
       v
silver_cleaning
       |
       v
gold_analytics
```

Each task executes only after its upstream task completes successfully.

### Automation

* **Compute:** Databricks Serverless
* **Execution:** Bronze → Silver → Gold
* **Schedule:** Daily automated execution
* **Monitoring:** Failure notifications
* **End-to-end workflow:** Successfully tested

This allows the entire pipeline to run without manually executing individual notebooks.

---

# Delta Lake

Delta tables are used throughout the Medallion Architecture.

### Why Delta Lake?

Delta Lake provides:

* **ACID transactions**
* **Schema enforcement**
* **Reliable table updates**
* **Data versioning**
* **Improved reliability over raw CSV/Parquet pipelines**

---

# Technologies Used

* **Databricks**
* **Apache Spark**
* **PySpark**
* **Delta Lake**
* **Unity Catalog**
* **Databricks SQL**
* **Databricks Dashboards**
* **Databricks Workflows**
* **Serverless Compute**
* **Python**
* **SQL**
* **Git**
* **GitHub**

---

# Repository Structure

```text
Databricks-Retail-Sales-Pipeline/
│
├── config/
│
├── data/
│   └── sample/
│
├── docs/
│
├── notebooks/
│   ├── 01_bronze_ingestion.ipynb
│   ├── 02_silver_cleaning.ipynb
│   └── 03_gold_analytics.ipynb
│
├── sql/
│   └── gold_business_queries.sql
│
├── src/
│
├── tests/
│
├── README.md
│
└── .gitignore
```

---

# Pipeline Status

* **Bronze Layer:** ✅ Completed
* **Silver Layer:** ✅ Completed
* **Gold Layer:** ✅ Completed
* **SQL Analytics:** ✅ Completed
* **Databricks Dashboard:** ✅ Completed
* **Workflow Orchestration:** ✅ Completed
* **Daily Scheduled Execution:** ✅ Configured
* **Failure Notifications:** ✅ Configured

---

# How to Run the Project

### 1. Load Source Data

Upload the source CSV files to the configured **Unity Catalog Volume**.

### 2. Bronze Ingestion

Run:

```text
01_bronze_ingestion
```

This creates the raw Bronze Delta tables.

### 3. Silver Processing

Run:

```text
02_silver_cleaning
```

This cleans, standardizes, and validates the source data.

### 4. Gold Analytics

Run:

```text
03_gold_analytics
```

This creates the business-ready Gold datasets.

### 5. SQL Analytics

Execute:

```text
sql/gold_business_queries.sql
```

### 6. Dashboard

Open the **Retail Sales Analytics Dashboard** to view business KPIs.

### Automated Execution

Alternatively, run the configured **Databricks Workflow**, which automatically executes:

```text
Bronze → Silver → Gold
```

in dependency order.

---

# Key Data Engineering Concepts Demonstrated

* Medallion Architecture
* Spark DataFrames
* PySpark transformations
* Data-quality validation
* Duplicate handling
* Null handling
* Data standardization
* Referential integrity
* Left Anti Join
* Left Semi Join
* Delta tables
* Unity Catalog
* Data aggregation
* Business KPI development
* SQL analytics
* Dashboard development
* Workflow orchestration
* Pipeline scheduling
* Failure monitoring
* Git-based version control

---

#  Project Summary

> I built an end-to-end retail sales analytics pipeline in Databricks using Medallion Architecture. Raw CSV files are governed through Unity Catalog and ingested into Bronze Delta tables.
>
> In the Silver layer, I used PySpark to remove duplicates, handle missing values, standardize categorical data, enforce business rules, and validate customer and product referential integrity.
>
> In the Gold layer, I joined the curated datasets and created business metrics including monthly revenue, product revenue, country revenue, average order value, and top customers.
>
> I exposed the Gold datasets through Databricks SQL and an interactive dashboard. Finally, I orchestrated Bronze, Silver, and Gold notebooks using a scheduled Databricks Workflow with failure notifications and version-controlled the complete project using GitHub.

---

# Future Improvements

Potential production enhancements include:

* Incremental ingestion instead of full overwrite processing
* Delta `MERGE` for upserts
* Automated data-quality tests
* Quarantine tables for rejected records
* Parameterized notebooks
* Environment-specific configuration
* CI/CD deployment
* Enhanced monitoring and alerting
* Additional dashboard KPIs
* Performance optimization for larger datasets

---

# Purpose

This project was created as a **hands-on Databricks portfolio project** to demonstrate practical data engineering knowledge beyond course or certification-level understanding.
