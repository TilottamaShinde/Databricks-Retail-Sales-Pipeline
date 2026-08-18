# Databricks Retail Sales Analytics Pipeline

An end-to-end retail sales analytics pipeline built using **Databricks, PySpark, Delta Lake, SQL, and Medallion Architecture**.

The project transforms raw retail data through **Bronze, Silver, and Gold layers**, applies data-quality and business transformations, produces analytics-ready datasets, visualizes key business metrics through a Databricks dashboard, and orchestrates the complete pipeline using Databricks Workflows.

## Architecture

Raw CSV Files
↓
Bronze Layer — Raw ingestion
↓
Silver Layer — Cleaning & data quality
↓
Gold Layer — Business aggregations
↓
Databricks Dashboard

**Orchestration:** Databricks Workflows  
**Schedule:** Daily automated execution  
**Monitoring:** Failure notifications

## Files
- `customers.csv`: customer master data
- `products.csv`: product master data
- `orders.csv`: order-line transactions; an order can contain multiple product lines

## Deliberate data-quality issues
- Duplicate customer and product records
- Inconsistent country, category, and order-status capitalization/spacing
- One missing country
- Duplicate order lines
- Zero and negative quantities
- One missing customer reference and one unknown product reference

These issues are intentional and will be handled in the Silver layer.

## Pipeline Status

- Bronze layer: ✅ Completed
- Silver layer: ✅ Completed
- Gold layer: ✅ Completed
- Databricks SQL Dashboard: ✅ Completed
- Workflow orchestration: ✅ Completed
- Daily scheduled execution: ✅ Configured
- Failure notifications: ✅ Configured
