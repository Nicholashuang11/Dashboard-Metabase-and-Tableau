Olist E-Commerce Data Engineering Pipeline
An end-to-end data engineering pipeline built on the Olist Brazilian E-Commerce dataset. This project simulates a real-world data warehouse pipeline — from raw CSV ingestion to analytics-ready views — using industry-standard tools.

Architecture
Raw CSVs (Olist Dataset)
        │
        ▼
  PySpark Transform          transform_dim.py  /  transform_fact.py
  (clean, aggregate,         ─────────────────────────────────────
   build date keys)          Writes Parquet staging files
        │
        ▼
  PostgreSQL Loader          loadscript.py
  (upsert dimensions         ─────────────
   + fact table)             Resolves surrogate keys via SQL JOIN
        │
        ▼
  Data Quality Checks        quality_checks.py
  (row counts, nulls,        ─────────────────
   referential integrity)    Exits 1 on failure → Airflow marks task failed
        │
        ▼
  Analytics Views            analytics_views.sql
  (pre-aggregated KPIs       ───────────────────
   for BI consumption)       Refreshed after every successful load
        │
        ▼
  Airflow DAG                pipeline.py
  (orchestrates all          ───────────
   steps on schedule)        Runs daily at 01:00

Star Schema
                    dim_time
                       │
dim_location ──── dim_customer
      │                │
dim_location ──── dim_seller ──── fact_order ──── dim_product
                                       │
                                   dim_time (approved, delivered)
TableDescriptionfact_orderOne row per order item — all measures (price, freight, payment, review)dim_customerCustomer info linked to location via zip codedim_sellerSeller info linked to location via zip codedim_productProduct catalog with English category translationdim_locationZip code, city, state, avg lat/lng from geolocation datadim_timeFull calendar breakdown from all order timestamps

Tech Stack
ToolPurposePySparkLarge-scale data transformationPostgreSQLData warehouseApache AirflowPipeline orchestration and schedulingDocker ComposeLocal infrastructurepandas + psycopg2Loading parquet staging files into PostgreSQL
