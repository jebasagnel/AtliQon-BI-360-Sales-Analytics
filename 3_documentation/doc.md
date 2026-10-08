# AtliQon BI 360 — Enterprise Sales Analytics

> End-to-end FMCG sales data platform built with **Databricks, PySpark, Delta Lake, AWS S3, Unity Catalog, and AI/BI**.

## 📌 Overview

AtliQon BI 360 is an end-to-end data engineering and analytics project that transforms raw FMCG sales data into an analytics-ready data platform.

The solution follows a **Bronze → Silver → Gold Medallion Architecture**, with data quality processing, incremental ingestion, Delta `MERGE` operations, SCD Type 2 handling, and Star Schema modeling.

The Gold layer provides a unified analytical serving view consumed by **Databricks AI/BI Dashboards** and **AI/BI Genie**.

---

## 🏗️ Architecture

![Medallion Architecture](4_architecture/medallion_architecture.png)

### Data Flow

```text
AWS S3
   │
   ▼
Bronze
   │
   ▼
Silver
   │
   ▼
Gold
   │
   ▼
Serving View
   │
   ├──► AI/BI Dashboard
   │
   └──► AI/BI Genie
```

### Architecture Components

| Layer | Purpose |
|---|---|
| **AWS S3** | Source data landing |
| **Bronze** | Raw Delta ingestion |
| **Silver** | Cleansing, validation and transformation |
| **Gold** | Star Schema and business-ready data |
| **Serving View** | Unified analytical dataset |
| **AI/BI Dashboard** | Interactive sales analytics |
| **AI/BI Genie** | Natural-language querying |

📖 **Detailed architecture:** [`4_architecture/`](4_architecture/)

---

# 📊 Dashboard

![Sales Dashboard](2_dashboard_images/01_sales_overview.png)

The dashboard provides interactive sales analysis across:

- Sales
- Units Sold
- Transactions
- Active Customers
- Average Unit Price
- Sales Trends
- Products
- Customers
- Divisions
- Categories
- Platforms
- Channels

### Filters

- Division
- Category
- Platform
- Channel
- Customer
- Date Range

📸 **Dashboard screenshots:** [`2_dashboard_images/`](2_dashboard_images/)

---

# 🔄 Data Engineering Pipeline

The pipeline processes data through three main stages.

```text
                    ┌──────────────┐
                    │    AWS S3    │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │    BRONZE    │
                    │ Raw Delta    │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │    SILVER    │
                    │ Clean +      │
                    │ Transform    │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │     GOLD     │
                    │ Star Schema  │
                    └──────┬───────┘
                           │
                           ▼
                  Analytical View
```

### Full Load

Historical data is processed from the source and loaded into the Bronze and Silver layers before being aggregated into the Gold fact table.

### Incremental Load

New source batches are processed independently and merged into existing Delta tables using Delta `MERGE` operations.

```text
New S3 Batch
     ↓
Bronze
     ↓
Silver Transformation
     ↓
Delta MERGE
     ↓
Gold
```

📖 **Pipeline documentation:** [`3_documentation/pipeline_specifications.md`](3_documentation/pipeline_specifications.md)

---

# 🧹 Data Transformation

The Silver layer performs the main data-quality and transformation operations.

### Customer

- Deduplication by `customer_id`
- Customer/address mapping
- Identifier validation
- Standardization

### Product

- Product and variant processing
- Product-code assignment
- Standardization

### Pricing

- Product-code mapping
- Year-based pricing

### Orders

- Quantity validation
- Invalid-value handling
- Product-code standardization
- Transaction-level processing

---

# ⭐ Data Model

The Gold layer uses a **Star Schema**.

![Star Schema](4_architecture/star_schema_diagram.png)

```text
                  dim_date
                     │
                     │
                     ▼
dim_customers ──► fact_orders ◄── dim_products
                     │
                     │
                     ▼
              dim_gross_price
```

### Core Tables

| Table | Type | Purpose |
|---|---|---|
| `dim_date` | Dimension | Date attributes |
| `dim_customers` | Dimension | Customer attributes |
| `dim_products` | Dimension | Product attributes |
| `dim_gross_price` | Dimension | Product pricing |
| `fact_orders` | Fact | Monthly sales |
| `sb_fact_orders` | Staging | Transaction-level staging |

📖 **Data dictionary:** [`3_documentation/data_dictionary.md`](3_documentation/data_dictionary.md)

---

# 📈 Business Metrics

The analytical layer provides the following core metrics:

| Metric | Definition |
|---|---|
| **Total Sales** | `SUM(total_amount_inr)` |
| **Units Sold** | `SUM(sold_quantity)` |
| **Transactions** | `COUNT(1)` |
| **Active Customers** | `COUNT(DISTINCT customer_code)` |
| **Avg Unit Price** | `SUM(total_amount_inr) / SUM(sold_quantity)` |

📖 **Metric definitions:** [`3_documentation/metric_definitions.md`](3_documentation/metric_definitions.md)

---

# 🤖 AI/BI Genie

The project also integrates **AI/BI Genie** with the analytical serving layer.

The enriched view provides the semantic foundation for natural-language questions such as:

```text
What are the total sales?

What are the monthly sales trends?

Which product categories generated the most sales?

How many units were sold?

How many active customers are there?
```

---

# 🗂️ Repository Structure

```text
AtliQon-BI-360-Sales-Analytics/
│
├── 1_resources/
│   ├── consolidated_pipeline/
│   │   ├── 1_setup/
│   │   ├── 2_dimension_data_processing/
│   │   └── 3_fact_data_processing/
│   │
│   ├── yaml_metrics/
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

# 🛠️ Technology Stack

**Data Platform**
- Databricks
- Unity Catalog
- Delta Lake

**Cloud**
- AWS S3

**Data Engineering**
- PySpark
- SQL
- Delta `MERGE`
- Change Data Feed
- SCD Type 2

**Analytics**
- Databricks AI/BI Dashboards
- AI/BI Genie
- YAML Metric Views

**Version Control**
- Git
- GitHub

---

# 📚 Documentation

Detailed technical documentation is available here:

| Document | Description |
|---|---|
| [Data Dictionary](3_documentation/data_dictionary.md) | Tables, columns, data types and relationships |
| [Metric Definitions](3_documentation/metric_definitions.md) | KPI formulas and business definitions |
| [Pipeline Specifications](3_documentation/pipeline_specifications.md) | ETL flow, incremental processing, MERGE and SCD Type 2 |
| [Architecture](4_architecture/) | Medallion and Star Schema diagrams |
| [Dashboard](2_dashboard_images/) | Dashboard screenshots |

---

# 🎯 What This Project Demonstrates

This project demonstrates practical implementation of:

- Cloud data ingestion
- Medallion Architecture
- PySpark ETL
- Delta Lake
- Incremental data processing
- Delta `MERGE`
- Change Data Feed
- Data-quality processing
- Deduplication
- SCD Type 2
- Dimensional modeling
- Star Schema
- Analytical serving layers
- Dashboard development
- AI-assisted analytics

---

## 👨‍💻 Project Repository

The repository contains the complete pipeline resources, documentation, architecture diagrams, metric definitions, and dashboard assets used to build the AtliQon BI 360 analytics platform.