# Description
[IBM-Data-Engineering Professional Certificate](https://www.coursera.org/professional-certificates/ibm-data-engineer)

# IBM Data Engineering Professional Certificate
### End-to-End Data Platform Engineering — 13 Courses · 71 Commits · Capstone: Full-Stack Retail Analytics Platform

---

## What this repository demonstrates

This repository documents a complete journey through data engineering fundamentals to production-grade platform design — built hands-on across relational databases, NoSQL stores, cloud data warehouses, big data infrastructure, ETL pipelines, BI dashboards, and distributed ML. Every module involved writing real code, designing real schemas, and operating real infrastructure — not slide decks.

**The headline deliverable is the [Course-13 Capstone Project](#capstone-project-course-13) — a fully integrated, multi-layer data platform built from scratch for a simulated e-commerce retailer, spanning 6 distinct technologies across 5 production-grade engineering modules.**

---

## Capstone Project (Course-13)

> **Full-stack retail data analytics platform — MySQL · MongoDB · PostgreSQL · IBM Db2 · Apache Airflow · Apache Spark · IBM Cognos**

Built an end-to-end data engineering platform for a fictional e-commerce retailer (SoftCart) that mirrors real-world enterprise architecture: transactional OLTP system, NoSQL product catalog, cloud data warehouse, automated ETL pipelines, BI dashboard, and big data ML analytics — all integrated and operational.

| Module | What was built | Key tools |
|--------|---------------|-----------|
| M1 — OLTP Database | Normalized MySQL schema for sales transactions; indexed; automated CSV export via Bash | MySQL, SQL, Bash |
| M2 — NoSQL Catalog | MongoDB product catalog with 9 collections; CRUD + aggregation queries | MongoDB, JSON |
| M3 — Data Warehouse | Star schema (PostgreSQL → IBM Db2); ROLLUP, CUBE, MQT analytics queries | PostgreSQL, IBM Db2, SQL |
| M4 — BI Dashboard | 3-chart Cognos dashboard: quarterly sales bar, category pie, monthly trend line | IBM Cognos Analytics |
| M5 — ETL Pipelines | Python ETL automating MySQL→Db2 sync; Airflow DAG for web log pipeline | Python, Pandas, Apache Airflow |
| M6 — Big Data ML | PySpark + SparkML Linear Regression on search-term data; sales forecast output | Apache Spark, PySpark, SparkML |

→ **[Full capstone documentation with architecture diagrams](./Course-13-%20DE%20Capstone%20Project/README.md)**

---

## Standalone Projects

### ETL Pipeline — Housing Dataset *(separate repository)*
> Python · SQLAlchemy · PostgreSQL · xlsxwriter

A 6-stage modular ETL pipeline processing 63,474 property transaction records: HTTP extraction → SQLAlchemy transformation → PostgreSQL load → insight generation → Excel export. Built as independent, reusable Python modules with an orchestration entry point.

→ **[github.com/smshde/ETL_Housing-Dataset](https://github.com/smshde/ETL_Housing-Dataset)**

---

### Exploratory Data Analysis — PA 911 Emergency Calls
> Python · Pandas · NumPy · Matplotlib · Seaborn

Analysed 663,000+ emergency call records from Montgomery County, PA. Performed feature engineering, temporal pattern analysis, and heatmap visualisation to surface peak-demand windows and call-type distribution — the kind of analysis that informs staffing and resource allocation decisions.

→ **[github.com/smshde/PA-911-Calls-EDA](https://github.com/smshde/PA-911-Calls-EDA)**

---

## Course Modules — Skills Built

| # | Module | Skills demonstrated |
|---|--------|---------------------|
| 01 | Introduction to Data Engineering | Data lifecycle, pipeline patterns, architecture principles |
| 02 | Python for Data Science, AI & Development | Python fundamentals, APIs, data structures |
| 03 | Python Project for Data Engineers | ETL scripting, data extraction, file I/O |
| 04 | RDBMS Fundamentals | Relational modelling, normalization, DDL/DML |
| 05 | Databases & SQL for Data Science | Joins, subqueries, views, stored procedures, transactions |
| 06 | Linux Commands & Shell Scripting | Bash scripting, cron scheduling, file automation |
| 07 | Relational Database Administration | Backup, recovery, user management, performance tuning |
| 08 | ETL & Data Pipelines — Shell, Airflow, Kafka | DAG design, pipeline orchestration, streaming concepts |
| 09 | Data Warehousing & BI Analytics | Star schema, OLAP, Cognos dashboards |
| 10 | Introduction to NoSQL Databases | MongoDB, Cassandra, DynamoDB, document modelling |
| 11 | Big Data with Spark & Hadoop | HDFS, MapReduce, Spark DataFrames, Hive, Sqoop |
| 12 | Data Engineering & ML using Spark | SparkML, feature engineering, model pipelines |
| 13 | **DE Capstone Project** | **Full-stack platform integration — see above** |

---

## Technology Footprint

```
Languages     Python · SQL · Bash/Shell · Scala
Databases     MySQL · PostgreSQL · IBM Db2 · MongoDB · Cassandra · DynamoDB
Big Data      Apache Spark · PySpark · SparkML · Hadoop · Hive · Sqoop · HBase
Pipelines     Apache Airflow · Kafka · Shell ETL · Python ETL
Cloud         IBM Cloud (Db2, Object Storage, Watson Studio) · AWS (supplementary)
BI            IBM Cognos Analytics · Matplotlib · Seaborn · Plotly
DevOps        Git · GitHub · Jupyter Notebooks · Linux
```

## Certification

**IBM Data Engineering Professional Certificate** — Coursera / IBM Skills Network  
13-course program · Capstone verified · ![image](https://github.com/smitshah1920/IBM-Data-Engineering/assets/116938231/de69d4f0-4b72-49eb-8eef-e8e09b9fa762)


*→ For the capstone architecture, module-by-module breakdown, and diagrams: [Course-13 README](./Course-13-%20DE%20Capstone%20Project/README.md)*

