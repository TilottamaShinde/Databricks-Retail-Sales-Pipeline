# Retail Sales Analytics starter data

This is synthetic data created for a beginner Databricks medallion-architecture portfolio project.

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
