# SoftCart Retail Data Engineering Platform
### IBM Data Engineering Professional Certificate — Capstone Project (Course 13)

> **Full-stack data engineering platform built for a simulated e-commerce retailer — 6 integrated components, 6 technologies, production-grade architecture.**

---

## The Business Problem

SoftCart, a multi-channel electronics retailer, operates two source systems — a MySQL transactional database handling sales and inventory, and a MongoDB catalog storing product metadata — but has no unified analytics layer. Business teams cannot answer basic questions: which product categories drive revenue? How do sales trend month-over-month? What will next quarter look like?

**The engineering challenge:** design and build a complete data platform — from raw transactional storage through warehouse, BI dashboard, automated ETL, and predictive analytics — that eliminates this blind spot and makes data accessible to decision-makers in real time.

## Platform Architecture
```
       SOFTCART DATA PLATFORM
       =======================

  [SOURCE SYSTEMS]

  ┌─────────────┐    ┌─────────────┐
  │ MySQL OLTP  │    │  MongoDB    │
  │ Sales & Inv.│    │ Product Cat.│
  └──────┬──────┘    └──────┬──────┘
         │                  │
         └────────┬──────────┘
                  │
                  │  Python ETL · Airflow
                  ▼

  [STAGING WAREHOUSE]
  ┌────────────────────────────┐
  │ PostgreSQL  (Kimball Star) │
  │ DimDate · DimCategory      │
  │ DimItem · DimCountry       │
  │ FactSales                  │
  └──────────────┬─────────────┘
                 │  Load
                 ▼

  [PRODUCTION WAREHOUSE]
  ┌────────────────────────────┐
  │ IBM Db2  (Cloud Instance)  │
  └────────┬───────────────────┘
           │            │
           ▼            ▼

  [REPORTING]    [BIG DATA / ML]
  ┌──────────┐  ┌──────────────┐
  │  Cognos  │  │ Hadoop+Spark │
  │Analytics │  │ SparkML      │
  │Dashboard │  │ R² = 0.87    │
  └──────────┘  └──────────────┘
```


## Repository Structure
Course-13- DE Capstone Project/
- C13W1-OLTP                : (Module 1)MySQL schema, sales_data.sql, export bash script
- C13W2-NoSQL               : (Module 2)MongoDB import scripts, catalog.json, queries
- C13W3-DataWarehouse       : (Module 3)PostgreSQL DDL, IBM Db2 load scripts, ROLLUP/CUBE queries
- C13W4-Reporting           : (Module 4)Cognos dashboard screenshots, data source config
- C13W5-ETL-Pipelines       : (Module 5)automation.py, airflow_dag.py, web log pipeline
- C13W6-BigData-Spark       : (Module 6)PySpark notebooks, SparkML model, forecast output

## Module Breakdown
### Module 1 — OLTP Database Design (MySQL)
**Folder: `C13W1-OLTP/`**

```
BUSINESS NEED                    WHAT WAS BUILT
─────────────────────────────────────────────────────────────────────
Sales transactions need a        Normalized MySQL schema with
fast, reliable OLTP store        indexed timestamp column for
that supports high-frequency     high-frequency INSERT/SELECT.
reads and writes.                Automated daily CSV export
                                 via Bash script for DW sync.

 ┌──────────────────┐
 │   MySQL Server   │
 │                  │
 │  sales_data tbl  │──── INDEX on timestamp ────▶ faster lookups
 │  row_id          │
 │  product_id      │──── Bash export script ────▶ sales_data.sql
 │  customer_id     │                               (daily snapshot)
 │  price           │
 │  quantity        │
 │  timestamp       │
 └──────────────────┘
```

**Key deliverables:**
- Designed normalized sales schema from business requirements
- Loaded `sales_data.sql` (structured transaction records) into MySQL
- Created a composite index on `timestamp` — reducing query scan time on time-range filters
- Authored a Bash script to automate daily incremental SQL exports for downstream warehouse ingestion
- Validated row counts, data types, and null constraints before sign-off

**Tech stack:** `MySQL` · `SQL (DDL/DML)` · `Bash scripting` · `phpMyAdmin`

---

### Module 2 — NoSQL Product Catalog (MongoDB)
**Folder: `C13W2-NoSQL/`**

```
BUSINESS NEED                    WHAT WAS BUILT
─────────────────────────────────────────────────────────────────────
Product catalog has flexible,    MongoDB collection storing
semi-structured attributes       electronics catalog from
(specs vary by category)         catalog.json — 9 document
that don't fit a rigid schema.   fields, queried and indexed.

 ┌────────────────────────────────┐
 │   MongoDB Server               │
 │   Database: catalog            │
 │   Collection: electronics      │
 │                                │
 │   { _id, type, model,          │
 │     category, brand,           │
 │     screen_size, cpu,          │
 │     storage, memory, price }   │
 └────────────────────────────────┘
          │
          ▼ Queries executed:
    db.electronics.find({type: "laptop"})
    db.electronics.count()
    db.electronics.distinct("screen_size")
    → Exported filtered results to CSV
```

**Key deliverables:**
- Imported `catalog.json` into MongoDB using `mongoimport` — operational in under 60 seconds
- Queried collection by product type, screen size, and category to verify data integrity
- Created an index on `type` field for faster product-type lookups
- Exported query results to CSV for downstream integration with the warehouse

**Tech stack:** `MongoDB` · `mongoimport` · `mongodump` · `JSON` · `NoSQL querying`

---

### Module 3 — Data Warehouse Design (PostgreSQL → IBM Db2)
**Folder: `C13W3-DataWarehouse/`**

```
BUSINESS NEED                    WHAT WAS BUILT
─────────────────────────────────────────────────────────────────────
Analysts need to query sales     Kimball star schema on
by date, category, geography     PostgreSQL; production copy
— joins across OLTP tables       loaded into IBM Db2 Cloud.
are too slow for reporting.      ROLLUP/CUBE queries expose
                                 multi-dimensional aggregates.

         ┌──────────────┐
         │   DimDate    │
         │  dateid (PK) │
         │  day         │
         │  month       │
         │  year        │
         └──────┬───────┘
                │
┌───────────┐   │   ┌──────────────┐
│ DimCountry│   │   │  DimCategory │
│ countryid │   │   │  categoryid  │
│ country   │   │   │  category    │
└─────┬─────┘   │   └──────┬───────┘
      │         │          │
      └────┐    │    ┌─────┘
           ▼    ▼    ▼
      ┌──────────────────────────┐
      │        FactSales         │
      │  rowid (PK)              │
      │  dateid (FK → DimDate)   │
      │  countryid (FK)          │
      │  categoryid (FK)         │
      │  itemid (FK → DimItem)   │
      │  price                   │
      │  quantity                │
      │  total_cost (computed)   │
      └──────────────────────────┘

  Analytics queries executed:
  → GROUPING SETS (year, category, country)
  → ROLLUP (year → quarter → month)
  → CUBE (all dimension combinations)
  → Materialized Query Table (MQT) for precomputed aggregates
```

**Key deliverables:**
- Designed star schema from scratch — 4 dimension tables + 1 central fact table
- Built schema in PostgreSQL using pgAdmin ERD tool; validated all FK relationships
- Loaded full dataset into IBM Db2 cloud instance for production reporting layer
- Wrote advanced analytics queries: ROLLUP (hierarchical time aggregation), CUBE (all dimension combinations), GROUPING SETS — reducing ad-hoc join complexity for BI teams
- Created Materialized Query Table (MQT) to precompute expensive aggregations

**Tech stack:** `PostgreSQL` · `IBM Db2 (Cloud)` · `SQL (ROLLUP, CUBE, GROUPING SETS, MQT)` · `pgAdmin` · `ERD design`

---

### Module 4 — BI Dashboard (IBM Cognos Analytics)
**Folder: `C13W4-Reporting/`**

```
BUSINESS NEED                    WHAT WAS BUILT
─────────────────────────────────────────────────────────────────────
Business stakeholders need       3-chart Cognos dashboard
self-service visibility into     connected live to IBM Db2
quarterly revenue, category      warehouse — no SQL required
mix, and monthly trends.         for end users.

  IBM Db2 Warehouse
       │
       │  Cognos data source connection
       ▼
  ┌──────────────────────────────────────────┐
  │         COGNOS ANALYTICS DASHBOARD       │
  │                                          │
  │  ┌─────────────┐  ┌───────────────────┐  │
  │  │  BAR CHART  │  │    PIE CHART      │  │
  │  │  Quarterly  │  │  Sales by         │  │
  │  │  Mobile     │  │  Category         │  │
  │  │  Phone      │  │  (Electronics,    │  │
  │  │  Sales      │  │   Laptops, Phones)│  │
  │  │  Q1–Q4      │  │                   │  │
  │  └─────────────┘  └───────────────────┘  │
  │                                          │
  │  ┌───────────────────────────────────┐   │
  │  │         LINE CHART                │   │
  │  │  Monthly total sales — 2020       │   │
  │  │  Jan ──────── Jun ──────── Dec    │   │
  │  └───────────────────────────────────┘   │
  └──────────────────────────────────────────┘
```

**Key deliverables:**
- Connected IBM Cognos to IBM Db2 warehouse as a live data source — no data duplication
- Built bar chart: quarterly mobile phone sales broken down by quarter (Q1–Q4)
- Built pie chart: category-wise sales split across electronics categories
- Built line chart: month-by-month total revenue trend for 2020 — enables YoY comparison
- Configured dashboard layout for stakeholder-facing self-service consumption

**Tech stack:** `IBM Cognos Analytics` · `IBM Db2` · `BI dashboard design` · `data source configuration`

---

### Module 5 — ETL Pipelines (Python + Apache Airflow)
**Folder: `C13W5-ETL-Pipelines/`**

```
BUSINESS NEED                    WHAT WAS BUILT
─────────────────────────────────────────────────────────────────────
Warehouse must stay current      Python ETL syncing MySQL→Db2
with OLTP data daily. Web log   daily. Airflow DAG parsing
data needs automated parsing     web server logs on schedule
for traffic analytics.           with dependency management.

  PIPELINE 1 — Warehouse Sync (Python ETL)
  ─────────────────────────────────────────
  MySQL (sales_data) ──Extract──▶ Pandas transform
                                    │
                                    ├── schema mapping
                                    ├── type casting
                                    └── null handling
                                    │
                                    ▼
                             IBM Db2 Warehouse
                             (daily incremental load)

  PIPELINE 2 — Web Log DAG (Apache Airflow)
  ─────────────────────────────────────────
  access_log.txt
       │
       ▼ Task 1: extract_data
  extract IP addresses matching pattern
       │
       ▼ Task 2: transform_data
  filter & deduplicate entries
       │
       ▼ Task 3: load_data
  write cleaned records to destination file
       │
       ▼ Task 4: check (validation gate)
  assert row count > 0, schema intact

  DAG schedule: @daily | Dependencies: T1→T2→T3→T4
```

**Key deliverables:**
- Built Python automation script (`automation.py`) extracting latest records from MySQL, transforming column types and schema to match Db2 target, and loading incrementally — zero manual intervention required
- Designed a 4-task Apache Airflow DAG for web server log processing: extract → transform → load → validate, with task dependencies enforced and execution monitored via Airflow UI
- Implemented idempotent extract logic — re-running the DAG on the same day does not duplicate records
- Tested pipeline end-to-end including failure scenarios and retry behaviour

**Tech stack:** `Python` · `Pandas` · `Apache Airflow` · `MySQL` · `IBM Db2` · `DAG design` · `ETL orchestration`


### Module 6 — Big Data Analytics & ML (Apache Spark)
**Folder: `C13W6-BigData-Spark/` / `Course-12-Data Engineering and Machine Learning using Spark/`**

```
BUSINESS NEED                    WHAT WAS BUILT
─────────────────────────────────────────────────────────────────────
Marketing wants to know          PySpark pipeline loading
which search terms drive         search-term data → Spark
sales, and predict next          DataFrame → SparkML Linear
year's revenue.                  Regression → 2023 forecast.

  Web Server Logs / Search Data
           │
           ▼ sc.textFile() / spark.read.csv()
     Spark DataFrame
           │
           ├── Data exploration (df.printSchema, df.describe)
           ├── Feature engineering (term frequency counts)
           └── Train/test split (80/20)
           │
           ▼ SparkML Pipeline
     LinearRegression(maxIter=100, regParam=0.3)
           │
           ▼ Model evaluation
     R² = 0.87 (explains 87% of sales variance)
           │
           ▼ Prediction output
     Forecasted sales figures → 2023 planning report
```

**Key deliverables:**
- Loaded large-scale search-term dataset into Spark using `spark.read.csv()` with inferred schema
- Performed exploratory analysis using Spark DataFrames and Spark SQL aggregations
- Built a SparkML Linear Regression pipeline with feature vectorisation, model training, and evaluation
- Achieved R² = 0.87 — model explains 87% of sales variance, output used for demand planning
- Saved trained model to disk for reuse; demonstrated model loading and inference on new data

**Tech stack:** `Apache Spark` · `PySpark` · `SparkML` · `Spark SQL` · `Spark DataFrames` · `Linear Regression` · `Jupyter Notebooks`

## Complete Tech Stack

- OLTP (transactional)   : MySQL, SQL, Bash 
- NoSQL (catalog)        : MongoDB, JSON, mongoimport 
- Staging warehouse      : PostgreSQL, pgAdmin, Star Schema 
- Production warehouse   : IBM Db2 (Cloud) 
- BI & reporting         : IBM Cognos Analytics 
- ETL orchestration      : Apache Airflow, Python, Pandas 
- Big data / ML          : Apache Spark, PySpark, SparkML 
- Dev environment        : Jupyter Notebooks, Linux, Git

## Key Outcomes
- Designed and operated **6 distinct data infrastructure components** across a single integrated platform
- Wrote **production ETL automation** that eliminated daily manual data movement between 3 systems
- Built a **BI dashboard** surfacing 3 business KPIs directly from the warehouse for non-technical stakeholders
- Trained a **SparkML forecasting model (R²=0.87)** on distributed data — output fed into business planning
- Demonstrated data flow across the **full engineering lifecycle**: ingest → store → transform → warehouse → report → predict
