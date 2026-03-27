# Automobile Repair Data Engineering Mini Project

## Overview
This project implements an end-to-end data engineering pipeline for an automobile repair business, transforming raw operational data from S3 into analytics-ready fact tables, dimension tables, and KPI cubes using Databricks and PySpark on AWS.

## Architecture
The project follows a medallion architecture (Bronze → Silver → Gold) using Unity Catalog:

Catalog: `automobile_catalog`

# Data Layers

# Bronze Layer (`001_bronze`)
Raw data ingestion from S3 bucket with minimal transformations.

Source: `s3://my-company-dms-postgres/`

Tables:
* `customer_survey` - Customer satisfaction survey responses
* `estimate` - Repair cost estimates and versioning
* `invoice` - Invoice transactions
* `ns_budget` - Store budget allocations
* `order` - Service orders and repair details
* `store` - Store information and management

Notebook: [nb_ingestion_from_s3](#notebook-527253304726694)

# Silver Layer (`002_silver`)
Cleaned and transformed data with data quality improvements.

Transformations Applied:
* Deduplication of records
* Date format standardization (dd-MM-yyyy HH:mm → date)
* Data type casting (invoice_amount to double)
* Null handling

Tables:
* `invoice` - Cleaned invoice data
* `order` - Cleaned order data

Notebook: [nb_invoices_transform](#notebook-1034954202829522)

# Gold Layer (`003_gold`)
Analytics-ready star schema with fact tables, dimension tables, and denormalized KPI cubes.

Fact Tables:
* `fact_orders` - Order operations with calculated metrics (days_in_shop, work_duration_days, is_on_time)
* `fact_invoices` - Invoice transactions linked to orders
* `fact_estimates` - Estimate versions with actual vs estimated amounts
* `fact_survey_responses` - Customer satisfaction ratings
* `fact_budget` - Monthly store budgets

Dimension Tables:
* `dim_store` - Store attributes (location, type, manager)
* `dim_technician` - Technician details
* `dim_estimator` - Estimator information

KPI Data Cubes (Denormalized for BI Tools):
* `cube_operations_detail` - Denormalized operations with store/technician dimensions
* `cube_financial_detail` - Financial metrics with estimates and budget variance
* `cube_survey_detail` - Customer satisfaction metrics by store

Notebooks:
* [nb_fact_tables_generation](#notebook-3204850360737615) - Creates star schema fact and dimension tables
* [nb_KPIS_generation](#notebook-883333509481658) - Generates KPI aggregations
* [nb_KPI_Data_Cubes](#notebook-1309973729006447) - Creates denormalized cubes for reporting

# Key Features

# Data Quality
* Deduplication using `ROW_NUMBER()` window functions
* Date parsing with error handling
* Data type validation and casting
* NULL filtering on critical date fields

# Performance Optimizations
* Delta Lake format for ACID transactions
* Partitioning by time periods (year, month)
* Pre-joined denormalized cubes for fast BI queries
* Efficient window functions for deduplication

# Analytics Capabilities
The gold layer enables analysis of:
* Operational Efficiency: Days in shop, on-time delivery rates, work duration
* Financial Performance: Revenue, budget variance, estimate accuracy
* Customer Satisfaction: Survey response rates, ratings by dimension
* Store Performance: Multi-dimensional analysis by store, technician, service type

# Technical Stack
* Platform: Databricks on AWS
* Storage: AWS S3, Unity Catalog
* Processing: PySpark, Delta Lake
* Languages: Python, SQL
* Format: Delta tables with overwrite mode

## Data Pipeline Flow
```
S3 (CSV files)
    ↓
Bronze Layer - Raw ingestion
    ↓
Silver Layer - Cleaning & transformation
    ↓
Gold Layer - Star schema (Fact + Dim tables)
    ↓
KPI Cubes - Denormalized for BI
```

## Business Use Cases
1. Store Performance Tracking - Compare stores by revenue, efficiency, and customer satisfaction
2. Technician Productivity - Analyze work duration and on-time completion rates
3. Financial Planning - Budget vs actual analysis, estimate accuracy
4. Customer Experience - Survey ratings correlation with operational metrics
5. Service Type Analysis - Performance patterns by repair type

## Project Structure
```
data-eng-mini-project/
── notebooks/
   -- bronze/
      --nb_ingestion_from_s3
   -- silver/
      -- nb_invoices_transform
   -- gold/
       -- nb_fact_tables_generation
       -- nb_KPIS_generation
       --nb_KPI_Data_Cubes
--README.md
```

## Getting Started
1. Ensure access to S3 bucket: `s3://my-company-dms-postgres/`
2. Run notebooks in order: Bronze → Silver → Gold
3. Query the KPI cubes for analytics and reporting

## Author
Mahitha Kasireddi (kasireddimahitha1729@gmail.com)