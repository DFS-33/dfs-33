---
mode: 'ask'
description: 'Apache Spark quick reference — PySpark DataFrame API, joins, partitioning, and performance patterns'
---

# Apache Spark Reference

> Quick reference for PySpark patterns.
> Copy this file to `.github/prompts/kb/` when your project uses Spark.

## Core Imports

```python
from pyspark.sql import SparkSession, DataFrame
from pyspark.sql import functions as F
from pyspark.sql.types import StructType, StructField, StringType, DoubleType
```

## Session Creation

```python
spark = SparkSession.builder \
    .appName("my-pipeline") \
    .config("spark.sql.shuffle.partitions", "200") \
    .getOrCreate()
```

## DataFrame Cheat Sheet

| Operation | Code |
|-----------|------|
| Read Parquet | `df = spark.read.parquet("s3://bucket/path/")` |
| Filter | `df.filter(F.col("status") == "active")` |
| Select + rename | `df.select(F.col("id"), F.col("name").alias("full_name"))` |
| Add column | `df.withColumn("total", F.col("qty") * F.col("price"))` |
| Group + aggregate | `df.groupBy("category").agg(F.sum("amount").alias("total"))` |
| Join | `df1.join(df2, on="id", how="left")` |
| Write Parquet | `df.write.mode("overwrite").parquet("s3://bucket/output/")` |

## Partitioning Pattern

```python
# Partition by date for time-series data
df.write \
    .partitionBy("year", "month") \
    .mode("overwrite") \
    .parquet("s3://bucket/data/")

# Read a specific partition
df = spark.read.parquet("s3://bucket/data/year=2026/month=04/")
```

## Avoid Data Skew

```python
# Salting technique for skewed joins
df_skewed = df.withColumn(
    "salted_key",
    F.concat(F.col("id"), F.lit("_"), (F.rand() * 10).cast("int"))
)
```

## Schema Definition

```python
schema = StructType([
    StructField("id", StringType(), nullable=False),
    StructField("amount", DoubleType(), nullable=True),
    StructField("status", StringType(), nullable=True),
])

df = spark.read.schema(schema).json("s3://bucket/raw/")
```

## Performance Tips

| Issue | Fix |
|-------|-----|
| Too many small files | `df.coalesce(10).write.parquet(...)` |
| Slow shuffle | Reduce `spark.sql.shuffle.partitions` for small datasets |
| OOM on executor | Increase `spark.executor.memory` or use `persist(StorageLevel.DISK_ONLY)` |
| Cartesian join | Always specify join key — never `df1.crossJoin(df2)` on large data |
