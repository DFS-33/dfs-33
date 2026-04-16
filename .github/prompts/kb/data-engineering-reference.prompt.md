---
mode: 'ask'
description: 'Data engineering quick reference — ETL patterns, batch vs streaming, data quality, and schema evolution'
---

# Data Engineering Reference

> Quick reference for data engineering patterns applicable to any stack.

## Pipeline Architecture Patterns

### Batch vs Streaming Decision

| Factor | Batch | Streaming |
|--------|-------|-----------|
| Latency requirement | Minutes to hours | Seconds |
| Data volume | High (GBs–TBs) | Low–medium per event |
| Processing logic | Complex joins, aggregations | Simple transforms, filters |
| Infrastructure cost | Lower (scheduled) | Higher (always-on) |
| Fault tolerance | Replayable | Requires checkpointing |

### Medallion Architecture

```text
Source → [Bronze] → [Silver] → [Gold] → Consumer
          Raw copy    Cleaned    Aggregated
          No changes  Deduped    Business-ready
                      Typed      Modeled
```

| Layer | Principle | Schema |
|-------|-----------|--------|
| Bronze | Never modify source data | As-is from source |
| Silver | Clean, validate, deduplicate | Enforce types, remove nulls |
| Gold | Business logic, aggregations | Star schema / wide tables |

## ETL Pattern

```python
from dataclasses import dataclass
from typing import Iterator


@dataclass
class ETLPipeline:
    source: SourceAdapter
    transformer: Transformer
    sink: SinkAdapter

    def run(self, batch_size: int = 1000) -> dict:
        stats = {"processed": 0, "failed": 0}
        for batch in self._extract_batches(batch_size):
            try:
                transformed = self.transformer.transform(batch)
                self.sink.write(transformed)
                stats["processed"] += len(batch)
            except TransformError as e:
                logger.error("batch_failed", size=len(batch), error=str(e))
                stats["failed"] += len(batch)
        return stats

    def _extract_batches(self, size: int) -> Iterator[list]:
        buffer = []
        for record in self.source.read():
            buffer.append(record)
            if len(buffer) >= size:
                yield buffer
                buffer = []
        if buffer:
            yield buffer
```

## Data Quality Checks

```python
from pydantic import BaseModel, Field
from typing import Optional


class QualityReport(BaseModel):
    table: str
    row_count: int
    null_pct: dict[str, float]  # column → null percentage
    duplicates: int
    passed: bool

    @property
    def issues(self) -> list[str]:
        problems = []
        for col, pct in self.null_pct.items():
            if pct > 0.01:  # >1% nulls
                problems.append(f"{col}: {pct:.1%} nulls")
        if self.duplicates > 0:
            problems.append(f"{self.duplicates} duplicate rows")
        return problems


def check_quality(df, table_name: str) -> QualityReport:
    return QualityReport(
        table=table_name,
        row_count=len(df),
        null_pct={col: df[col].isna().mean() for col in df.columns},
        duplicates=df.duplicated().sum(),
        passed=len(df) > 0 and df.duplicated().sum() == 0,
    )
```

## Incremental Load Pattern

```python
def incremental_load(source, sink, watermark_col: str, last_value) -> int:
    """Load only records newer than last_value watermark."""
    new_records = source.query(f"{watermark_col} > '{last_value}'")
    if not new_records:
        return 0
    sink.upsert(new_records, key_col="id")
    new_watermark = max(r[watermark_col] for r in new_records)
    sink.save_watermark(watermark_col, new_watermark)
    return len(new_records)
```

## Schema Evolution Rules

| Change Type | Safe? | Strategy |
|-------------|-------|----------|
| Add nullable column | ✅ Yes | Add with `DEFAULT NULL` |
| Add NOT NULL column | ⚠️ Risk | Add nullable first, backfill, then constraint |
| Remove column | ❌ No | Deprecate → soft delete → hard delete after N days |
| Rename column | ❌ No | Add new, dual-write, migrate consumers, drop old |
| Change type | ❌ No | Dual-write period, then migrate |

## Common Pitfalls

| Mistake | Fix |
|---------|-----|
| Processing whole table on every run | Implement watermark-based incremental load |
| No null checks on join keys | Add quality gate before joins |
| Hardcoded schema in transform | Use schema registry or Pydantic models |
| No idempotency | Add upsert (not insert) to handle reruns |
