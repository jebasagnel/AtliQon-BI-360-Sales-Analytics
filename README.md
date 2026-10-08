# AtliQon BI 360 — Enterprise Sales Analytics Data Platform

![Databricks](https://img.shields.io/badge/Databricks-FF3621?style=for-the-badge&logo=databricks&logoColor=white)
![PySpark](https://img.shields.io/badge/PySpark-E25A1C?style=for-the-badge&logo=apachespark&logoColor=white)
![AWS S3](https://img.shields.io/badge/AWS_S3-569A31?style=for-the-badge&logo=amazonaws&logoColor=white)
![Delta Lake](https://img.shields.io/badge/Delta_Lake-000000?style=for-the-badge&logo=delta&logoColor=white)
![Unity Catalog](https://img.shields.io/badge/Unity_Catalog-00A4EF?style=for-the-badge&logo=azure-devops&logoColor=white)
![Databricks AI/BI](https://img.shields.io/badge/Databricks_AI/BI-FF3621?style=for-the-badge&logo=databricks&logoColor=white)

An end-to-end data engineering and analytics solution built on Databricks for AtliQon's FMCG domain. The platform ingests multi-table order and reference data from AWS S3, processes it through a **Bronze → Silver → Gold Medallion Architecture** using PySpark and Delta MERGE operations, implements **SCD Type 2 tracking** and a **Star Schema data model**, and exposes enriched analytical views to **Databricks AI/BI Dashboards** and **AI/BI Genie**.

# AtliQon BI 360 — Enterprise Sales Analytics Data Platform

An end-to-end **data engineering and analytics platform** built on Databricks for FMCG sales analytics.

The project implements a **Bronze → Silver → Gold Medallion Architecture** using PySpark and Delta Lake, with incremental processing, Delta `MERGE` operations, SCD Type 2 tracking, dimensional modeling, and a Star Schema. The Gold serving layer is exposed through a unified analytical view for **Databricks AI/BI Dashboards** and **AI/BI Genie**.

---

## 📊 Project Overview

The platform transforms raw FMCG sales data from AWS S3 into an analytics-ready Gold layer.

### Key Capabilities

- AWS S3-based data ingestion
- Bronze → Silver → Gold Medallion Architecture
- Delta Lake storage
- Change Data Feed (CDF)
- PySpark-based ETL pipelines
- Delta `MERGE` for idempotent upserts
- Full and incremental fact processing
- Data cleansing and validation
- Customer deduplication
- SCD Type 2 tracking
- Star Schema dimensional modeling
- Monthly sales aggregation
- Enriched serving view for analytics
- Databricks AI/BI Dashboard
- Databricks AI/BI Genie natural-language analytics

---

## 🏗️ End-to-End Architecture

```text
                         ┌─────────────────────┐
                         │      AWS S3         │
                         │   Landing / Raw     │
                         │                     │
                         │ Orders              │
                         │ Customers           │
                         │ Products            │
                         │ Gross Prices        │
                         └──────────┬──────────┘
                                    │
                                    ▼
                    ┌─────────────────────────────┐
                    │       BRONZE LAYER          │
                    │       fmcg.bronze           │
                    │                             │
                    │ Raw Delta Tables            │
                    │ Ingestion Metadata          │
                    │ Change Data Feed            │
                    └──────────────┬──────────────┘
                                   │
                                   ▼
                    ┌─────────────────────────────┐
                    │        SILVER LAYER         │
                    │        fmcg.silver           │
                    │                             │
                    │ Data Cleansing              │
                    │ Type Standardization        │
                    │ Deduplication               │
                    │ Validation                  │
                    │ Delta MERGE / Upserts       │
                    └──────────────┬──────────────┘
                                   │
                                   ▼
                    ┌─────────────────────────────┐
                    │         GOLD LAYER          │
                    │         fmcg.gold            │
                    │                             │
                    │ Star Schema                 │
                    │ Fact + Dimensions           │
                    │ Monthly Aggregation         │
                    │ SCD Type 2                 │
                    └──────────────┬──────────────┘
                                   │
                                   ▼
                ┌─────────────────────────────────────┐
                │   vw_fact_orders_enriched            │
                │                                     │
                │ Unified Analytical Serving View      │
                └───────────────┬─────────────────────┘
                                │
                     ┌──────────┴──────────┐
                     ▼                     ▼
          ┌─────────────────────┐  ┌──────────────────┐
          │ Databricks AI/BI    │  │ AI/BI Genie      │
          │ Dashboard           │  │                  │
          │                     │  │ Natural Language │
          │ Interactive KPIs    │  │ Analytics        │
          │ Filters & Trends    │  │                  │
          └─────────────────────┘  └──────────────────┘
```

---

## 📸 Dashboard Preview

### Sales Analytics Dashboard

![Sales Dashboard](2_dashboard_images/01_sales_overview.png)

### Filtered Dashboard View

![Filtered Dashboard](2_dashboard_images/02_filtered_view.png)

### Architecture

![Architecture](2_dashboard_images/03_architecture.jpg)

---

# 📁 Repository Structure

```text
AtliQon-BI-360-Sales-Analytics/
│
├── 1_resources/
│   ├── consolidated_pipeline/
│   │   ├── 1_setup/
│   │   │   ├── setup_catalogs.py
│   │   │   ├── utilities.py
│   │   │   └── dim_date_table_creation.py
│   │   │
│   │   ├── 2_dimension_data_processing/
│   │   │   ├── customer_data_processing.py
│   │   │   ├── 2_products_data_processing.py
│   │   │   └── 3_pricing_data_processing.py
│   │   │
│   │   └── 3_fact_data_processing/
│   │       ├── 1_full_load_fact.py
│   │       └── 2_incremental_load_fact.py
│   │
│   ├── yaml_metrics/
│   │   ├── sales_metrics.yml
│   │   └── sales_trend.yml
│   │
│   └── sample_data/
│
├── 2_dashboard_images/
│   ├── 01_sales_overview.png
│   ├── 02_filtered_view.png
│   └── 03_architecture.jpg
│
├── 3_documentation/
│   ├── data_dictionary.md
│   ├── metric_definitions.md
│   └── pipeline_specifications.md
│
├── 4_architecture/
│   ├── medallion_architecture.png
│   └── star_schema_diagram.png
│
├── .gitignore
└── README.md
```

---

# 🛠️ Technical Architecture

## 1. Unity Catalog & Schema Design

The project is organized under the `fmcg` Unity Catalog using three processing layers.

### Bronze — `fmcg.bronze`

The Bronze layer stores raw source data in Delta format.

Responsibilities:

- Raw CSV ingestion from AWS S3
- Preserve source-level information
- Capture ingestion metadata
- Enable Change Data Feed

Example ingestion metadata:

```text
_file_name
_file_size
_read_timestamp
```

---

### Silver — `fmcg.silver`

The Silver layer contains cleaned and standardized datasets.

Key transformations include:

- Data type standardization
- Null handling
- Invalid value handling
- Duplicate removal
- Product code standardization
- Surrogate-key handling
- Delta `MERGE` operations
- Idempotent processing

Example:

```text
Raw Data
   ↓
Validation
   ↓
Cleansing
   ↓
Deduplication
   ↓
Standardization
   ↓
Delta MERGE
   ↓
Silver Tables
```

---

### Gold — `fmcg.gold`

The Gold layer is designed for business analytics.

It contains:

- Fact table
- Customer dimension
- Product dimension
- Gross price dimension
- Date dimension
- Granular staging table
- Enriched analytical serving view

The main analytical fact table is aggregated to **monthly grain** by:

```text
Month
Product
Customer
```

---

# 🔄 Pipeline Processing

## Pipeline Flow

```text
Source CSV
   │
   ▼
AWS S3
   │
   ▼
Bronze Delta
   │
   ▼
Silver Transformation
   │
   ├── Cleansing
   ├── Validation
   ├── Deduplication
   ├── Standardization
   └── MERGE
   │
   ▼
Gold Star Schema
   │
   ├── dim_date
   ├── dim_customers
   ├── dim_products
   ├── dim_gross_price
   └── fact_orders
   │
   ▼
Enriched Serving View
   │
   ├── Dashboard
   └── AI/BI Genie
```

---

# ⚙️ Dimension & Fact Processing

| Pipeline Stage | Processing |
|---|---|
| Setup | Unity Catalog/schema setup, shared utilities and date dimension generation |
| Customers | Deduplication by `customer_id`, address mapping and standardization |
| Products | Product/variant processing and product-code assignment |
| Pricing | Year-over-year price mapping by `product_code` |
| Full Fact Load | Historical order ingestion followed by aggregation and Gold MERGE |
| Incremental Fact Load | New S3 batch processing followed by Silver transformation and Gold upsert |

---

# 📐 Data Model

The Gold layer follows a **Star Schema** centered around the `fact_orders` table.

```text
                       ┌──────────────┐
                       │   dim_date   │
                       └──────┬───────┘
                              │
                              │ date
                              ▼
┌─────────────────┐    ┌───────────────┐    ┌─────────────────┐
│ dim_customers   │───►│  fact_orders  │◄───│  dim_products   │
└─────────────────┘    └───────┬───────┘    └─────────────────┘
                               │
                               │ product_code + year
                               ▼
                       ┌──────────────────┐
                       │ dim_gross_price  │
                       └──────────────────┘
```

---

# 🗃️ Gold Tables

## `fmcg.gold.fact_orders`

Monthly aggregated sales fact table.

| Column | Type | Description |
|---|---|---|
| `date` | DATE | Month start date |
| `product_code` | STRING | Product identifier |
| `customer_code` | STRING | Customer identifier |
| `sold_quantity` | LONG | Monthly aggregated quantity sold |

---

## `fmcg.gold.dim_customers`

Customer/business dimension.

| Column | Type | Description |
|---|---|---|
| `customer_code` | STRING | Unique customer identifier |
| `customer` | STRING | Customer/retailer name |
| `market` | STRING | Geographic market |
| `platform` | STRING | Sales platform |
| `channel` | STRING | Distribution channel |

---

## `fmcg.gold.dim_products`

Product dimension.

| Column | Type | Description |
|---|---|---|
| `product_code` | STRING | Standardized product identifier |
| `division` | STRING | Highest product grouping |
| `category` | STRING | Product category |
| `product` | STRING | Product/brand name |
| `variant` | STRING | Product variant |

---

## `fmcg.gold.dim_gross_price`

Product pricing dimension.

| Column | Type | Description |
|---|---|---|
| `product_code` | STRING | Product identifier |
| `price_inr` | DOUBLE | Unit price in INR |
| `year` | INT | Pricing year |

---

# 🔗 Analytical Serving View

## `fmcg.gold.vw_fact_orders_enriched`

A denormalized analytical view combining the fact table with the required dimensions.

The view provides a single source for dashboarding and ad-hoc analysis.

### View Columns

| Column | Type | Source | Description |
|---|---|---|---|
| `date` | DATE | fact_orders | Order month |
| `product_code` | STRING | fact_orders | Product identifier |
| `customer_code` | STRING | fact_orders | Customer identifier |
| `date_key` | INT | dim_date | Date key |
| `year` | INT | dim_date | Calendar year |
| `month_name` | STRING | dim_date | Full month name |
| `month_short_name` | STRING | dim_date | Month abbreviation |
| `quarter` | STRING | dim_date | Quarter |
| `year_quarter` | STRING | dim_date | Year-quarter |
| `customer` | STRING | dim_customers | Customer name |
| `market` | STRING | dim_customers | Market |
| `platform` | STRING | dim_customers | Sales platform |
| `channel` | STRING | dim_customers | Distribution channel |
| `division` | STRING | dim_products | Product division |
| `category` | STRING | dim_products | Product category |
| `product` | STRING | dim_products | Product name |
| `variant` | STRING | dim_products | Product variant |
| `sold_quantity` | LONG | fact_orders | Units sold |
| `price_inr` | DOUBLE | dim_gross_price | Unit price |
| `total_amount_inr` | DOUBLE | Calculated | `sold_quantity × price_inr` |

---

# 📊 Business Metrics

| Metric | Formula | Purpose |
|---|---|---|
| **Total Sales** | `SUM(total_amount_inr)` | Total gross sales value |
| **Units Sold** | `SUM(sold_quantity)` | Total product volume sold |
| **Transactions** | `COUNT(1)` | Number of order line records |
| **Active Customers** | `COUNT(DISTINCT customer_code)` | Unique customers in selected period |
| **Avg Unit Price** | `SUM(total_amount_inr) / SUM(sold_quantity)` | Weighted average selling price |

---

# 🔁 Incremental Data Processing

The project supports both **full-load** and **incremental-load** processing.

### Full Load

```text
Historical S3 Data
       ↓
Bronze
       ↓
Silver
       ↓
sb_fact_orders
       ↓
Monthly Aggregation
       ↓
fact_orders
```

### Incremental Load

```text
New S3 Batch
     ↓
Bronze
     ↓
Silver Transformation
     ↓
Delta MERGE
     ↓
Gold Tables
     ↓
Updated Analytics
```

Delta `MERGE` operations are used to support idempotent upserts and incremental processing.

---

# 🧩 Granular Staging & SCD Type 2

## `sb_fact_orders`

The `sb_fact_orders` staging layer preserves transactional order-level information before monthly aggregation.

It supports:

- Daily transaction-level processing
- `order_id` preservation
- Incremental processing
- Lineage validation
- SCD Type 2 tracking requirements

The Gold `fact_orders` table then aggregates these records into monthly sales grain.

```text
Daily Transaction Grain
        ↓
sb_fact_orders
        ↓
Group by:
  • month
  • product_code
  • customer_code
        ↓
Monthly Grain
        ↓
fact_orders
```

---

# 🧹 Data Quality & Transformation

The Silver layer applies multiple data-quality rules before records reach the Gold layer.

### Customer Data

- Deduplicate records using `customer_id`
- Standardize customer attributes
- Map customer/address information
- Handle missing identifiers

### Order Data

- Validate order quantities
- Remove invalid/missing order quantities
- Standardize product identifiers
- Handle invalid values

### Product Data

- Process product variants
- Standardize product codes
- Map transactional records to product definitions

### Pricing Data

- Map prices using `product_code`
- Maintain year-based pricing information

---

# 📈 Databricks AI/BI Dashboard

The project includes an interactive sales dashboard built using **Databricks AI/BI Dashboards**.

### Dashboard Features

- Total Sales
- Units Sold
- Transactions
- Active Customers
- Average Unit Price
- Sales trend analysis
- Product analysis
- Customer analysis
- Division analysis
- Category analysis
- Platform analysis
- Channel analysis

### Interactive Filters

```text
Division
Category
Platform
Channel
Customer
Date Range
```

The dashboard contains **16 interactive visual widgets**.

---

# 📅 Sales Trend Analysis

A dedicated metric definition file, `sales_trend.yml`, is used for the temporal trend analysis.

This separates the trend dataset from the primary dashboard metrics and allows the sales trend visualization to maintain its intended multi-period view while other dashboard filters are applied.

```text
sales_metrics.yml
        │
        └── Executive / KPI Metrics

sales_trend.yml
        │
        └── Temporal Sales Trend
```

---

# 🤖 AI/BI Genie

The Gold serving view and semantic metadata are designed to support **Databricks AI/BI Genie**.

Users can query business information using natural language, such as:

```text
What are the total sales?

Which product categories generated the highest sales?

How many units were sold?

What are the monthly sales trends?

Which customers contributed the most sales?

What is the average unit price?
```

Genie queries the analytical serving layer rather than directly interacting with the raw ingestion layer.

---

# ☁️ Data Ingestion

### Source

```text
AWS S3
s3://sportsbar-ng/
```

The landing area contains source datasets for:

```text
Orders
Customers
Products
Gross Prices
```

### Ingestion Metadata

Bronze ingestion captures source-file metadata:

```text
_file_name
_file_size
_read_timestamp
```

---

# 🔄 Delta Lake & Change Data Feed

Delta Lake is used throughout the data platform to provide reliable transactional data processing.

Change Data Feed is enabled on relevant Delta tables.

Example:

```sql
ALTER TABLE fmcg.bronze.orders
SET TBLPROPERTIES (
    delta.enableChangeDataFeed = true
);
```

CDF supports change tracking and provides a foundation for incremental data-processing workflows.

---

# 🧰 Technology Stack

| Category | Technology |
|---|---|
| Cloud Storage | AWS S3 |
| Data Platform | Databricks |
| Processing | PySpark |
| Query Language | SQL |
| Storage Format | Delta Lake |
| Governance | Unity Catalog |
| Architecture | Medallion Architecture |
| Data Modeling | Star Schema |
| Slowly Changing Dimensions | SCD Type 2 |
| Incremental Processing | Delta MERGE / CDF |
| Dashboarding | Databricks AI/BI Dashboards |
| Natural Language Analytics | AI/BI Genie |
| Configuration | YAML |
| Version Control | Git / GitHub |

---

# 📚 Documentation

Detailed project documentation is available in the repository:

### Data Dictionary

`3_documentation/data_dictionary.md`

Contains definitions for Bronze, Silver and Gold datasets.

### Metric Definitions

`3_documentation/metric_definitions.md`

Contains dashboard metric formulas and business rules.

### Pipeline Specifications

`3_documentation/pipeline_specifications.md`

Documents:

- Full-load processing
- Incremental processing
- Delta MERGE logic
- SCD Type 2 implementation
- Data-quality rules
- Pipeline dependencies

---

# 🎯 Key Engineering Concepts Demonstrated

This project demonstrates practical implementation of:

- Medallion Architecture
- ETL / ELT workflows
- PySpark transformations
- Delta Lake
- Delta MERGE
- Change Data Feed
- Incremental data processing
- Full-load processing
- Data quality checks
- Deduplication
- Data validation
- SCD Type 2
- Star Schema
- Dimensional modeling
- Fact and dimension tables
- Monthly aggregation
- Serving-layer design
- Analytical views
- Dashboard semantic modeling
- Natural-language analytics

---

# 🚀 Project Workflow

```text
1. Source Data
      ↓
2. AWS S3 Landing
      ↓
3. Bronze Delta Ingestion
      ↓
4. Silver Data Transformation
      ↓
5. Data Quality & Deduplication
      ↓
6. Delta MERGE / Incremental Processing
      ↓
7. Gold Star Schema
      ↓
8. Monthly Fact Aggregation
      ↓
9. Enriched Serving View
      ↓
10. AI/BI Dashboard
      ↓
11. AI/BI Genie
```

---

# 👨‍💻 Project Focus

The project was designed to demonstrate how raw operational FMCG data can be transformed into a governed, analytics-ready data platform using modern cloud data-engineering practices.

The implementation focuses on the complete path from **raw data ingestion to business-facing analytics**, rather than treating dashboarding as an isolated reporting task.

---

## ⭐ Highlights

```text
AWS S3
   ↓
Databricks
   ↓
Unity Catalog
   ↓
Bronze → Silver → Gold
   ↓
Delta Lake + CDF + MERGE
   ↓
SCD Type 2
   ↓
Star Schema
   ↓
Serving View
   ↓
AI/BI Dashboard + Genie
```

---

## 📌 Repository

This repository contains the pipeline notebooks, SQL/YAML metric definitions, architecture diagrams, dashboard screenshots, sample schemas, and technical documentation required to understand and reproduce the analytical workflow.

