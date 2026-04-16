---
mode: 'ask'
description: 'Databricks quick reference — Delta Lake, Unity Catalog, notebooks, jobs, and cluster configuration'
---

# Databricks Reference

> Quick reference for Databricks patterns.
> Copy this file to `.github/prompts/kb/` when your project uses Databricks.

## Delta Lake Core Operations

```python
from delta.tables import DeltaTable
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .config("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension") \
    .config("spark.sql.catalog.spark_catalog", "org.apache.spark.sql.delta.catalog.DeltaCatalog") \
    .getOrCreate()

# Write Delta table
df.write.format("delta").mode("overwrite").saveAsTable("catalog.schema.table")

# Read Delta table
df = spark.table("catalog.schema.table")

# Time travel
df_yesterday = spark.read.format("delta").option("versionAsOf", 5).load("path/to/table")
```

## Upsert (MERGE) Pattern

```python
delta_table = DeltaTable.forName(spark, "catalog.schema.orders")

delta_table.alias("target").merge(
    source=new_records.alias("source"),
    condition="target.id = source.id"
).whenMatchedUpdateAll() \
 .whenNotMatchedInsertAll() \
 .execute()
```

## Unity Catalog Naming

```
catalog.schema.table
├── catalog   → e.g., "prod", "dev", "raw"
├── schema    → e.g., "bronze", "silver", "gold", "sales"
└── table     → e.g., "orders", "customers"
```

## Job / Workflow Pattern

```python
# Use dbutils.widgets for parameterized notebooks
dbutils.widgets.text("env", "dev")
dbutils.widgets.text("run_date", "2026-01-01")

env = dbutils.widgets.get("env")
run_date = dbutils.widgets.get("run_date")

# Return value to calling job
dbutils.notebook.exit(f'{{"status": "ok", "rows_written": {rows}}}')
```

## Cluster Configuration Guide

| Workload | Cluster Type | Notes |
|----------|-------------|-------|
| Interactive dev | Single-node or small cluster | Cost-effective for exploration |
| Scheduled batch | Standard cluster, autoscaling | Scale to data volume |
| Streaming | Fixed cluster, no autoscaling | Stable throughput |
| ML training | GPU cluster | Use `ml.g4dn.xlarge` or similar |

## Lakeflow (DLT) Pipeline Pattern

```python
import dlt
from pyspark.sql import DataFrame

@dlt.table(
    name="bronze_orders",
    comment="Raw orders from source system",
    table_properties={"quality": "bronze"},
)
def bronze_orders() -> DataFrame:
    return spark.readStream.format("cloudFiles") \
        .option("cloudFiles.format", "json") \
        .load("/mnt/raw/orders/")


@dlt.table(name="silver_orders", comment="Cleaned and validated orders")
@dlt.expect_or_drop("valid_id", "id IS NOT NULL")
@dlt.expect_or_drop("positive_amount", "amount > 0")
def silver_orders() -> DataFrame:
    return dlt.read_stream("bronze_orders") \
        .withColumnRenamed("order_id", "id") \
        .dropDuplicates(["id"])
```

## Common Pitfalls

| Mistake | Fix |
|---------|-----|
| Using `overwrite` on Delta with concurrent writers | Use `MERGE` or `foreachBatch` |
| No `OPTIMIZE` on frequently updated tables | Run `OPTIMIZE` + `ZORDER` weekly |
| Notebooks with hardcoded paths | Use widgets + Unity Catalog paths |
| No data quality expectations in DLT | Add `@dlt.expect_or_drop` for critical columns |
