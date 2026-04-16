---
mode: 'ask'
description: 'Python 3.11+ quick reference — type hints, dataclasses, Pydantic v2, and common patterns'
---

# Python Reference

> Quick reference for modern Python 3.11+ patterns.

## Type Hints Cheat Sheet

| Type | Example | Notes |
|------|---------|-------|
| `str` | `name: str` | Required |
| `Optional[str]` | `tag: Optional[str] = None` | Nullable, defaults None |
| `int` | `count: int = Field(..., ge=0)` | Non-negative |
| `float` | `price: float = Field(..., ge=0)` | Non-negative float |
| `list[T]` | `items: list[str]` | Typed list |
| `dict[K, V]` | `meta: dict[str, str] = {}` | Typed dict |
| `Literal` | `status: Literal["active","inactive"]` | Constrained values |
| `datetime` | `created_at: datetime` | ISO string auto-coerced by Pydantic |

## Pydantic v2 Model Pattern

```python
from pydantic import BaseModel, Field, computed_field, model_validator, field_validator
from typing import Optional
from datetime import datetime

class Record(BaseModel):
    id: str = Field(..., min_length=1)
    name: str
    amount: float = Field(..., ge=0)
    tags: list[str] = Field(default_factory=list)
    created_at: Optional[datetime] = None

    @computed_field
    @property
    def has_tags(self) -> bool:
        return len(self.tags) > 0

    @field_validator('name')
    @classmethod
    def normalize_name(cls, v: str) -> str:
        return v.strip().title()

    @model_validator(mode='after')
    def validate_business_rules(self) -> 'Record':
        if self.amount > 10_000 and not self.tags:
            raise ValueError("High-value records must have tags")
        return self
```

## Validation Methods

| Method | Use When |
|--------|----------|
| `Model(**data)` | Constructing from known-good dict |
| `Model.model_validate(obj)` | Validating dict from external input |
| `Model.model_validate_json(json_str)` | Parsing JSON string |
| `model.model_dump()` | Export to dict |
| `Model.model_json_schema()` | Generate JSON schema |

## Dataclass Pattern (for internal data, no validation)

```python
from dataclasses import dataclass, field

@dataclass
class Config:
    host: str
    port: int = 8080
    tags: list[str] = field(default_factory=list)
```

## Structured Logging

```python
import structlog

logger = structlog.get_logger()

# ✅ Correct
logger.info("record_processed", record_id=record.id, duration_ms=elapsed)
logger.error("validation_failed", record_id=record.id, errors=str(e))

# ❌ Never
print(f"processed {record.id}")
```

## Common Pitfalls

| Mistake | Fix |
|---------|-----|
| `raise ValidationError(...)` in validator | `raise ValueError(...)` — Pydantic wraps it |
| `Optional[float]` without default | `Optional[float] = None` |
| `model.dict()` (Pydantic v1) | `model.model_dump()` (Pydantic v2) |
| `@validator` decorator (Pydantic v1) | `@field_validator` (Pydantic v2) |
| Mutable default `= []` | Use `= Field(default_factory=list)` |
