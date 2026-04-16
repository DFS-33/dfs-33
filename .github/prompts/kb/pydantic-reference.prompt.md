---
mode: 'ask'
description: 'Pydantic v2 quick reference — validation patterns for LLM output and invoice data models'
---

# Pydantic v2 Reference — Invoice Pipeline

> Quick reference for Pydantic v2 patterns used in this project.
> Full KB: `agentspec/kb/pydantic/` | Source: `agentspec/kb/pydantic/quick-reference.md`

## Core Imports

```python
from pydantic import BaseModel, Field, ValidationError, computed_field
from pydantic import field_validator, model_validator
from typing import Optional, Literal
from datetime import date
```

## Field Types Cheat Sheet

| Type | Example | Notes |
|------|---------|-------|
| `str` | `name: str` | Required |
| `Optional[str]` | `tax_id: Optional[str] = None` | Nullable, defaults None |
| `float` | `amount: float = Field(..., ge=0)` | Non-negative |
| `date` | `invoice_date: date` | ISO string auto-coerced |
| `Literal` | `status: Literal["active","void"]` | Constrained values |
| `list[T]` | `items: list[LineItem]` | Typed list |

## Validation Methods

| Method | Use When |
|--------|----------|
| `Model(**data)` | Constructing from known-good dict |
| `Model.model_validate(obj)` | Validating dict from API/Pub/Sub |
| `Model.model_validate_json(json_str)` | Parsing LLM JSON response |
| `model.model_dump()` | Export to dict (logging, BigQuery) |
| `Model.model_json_schema()` | Generate schema for LLM prompt |

## Computed Fields

```python
from pydantic import computed_field

class LineItem(BaseModel):
    quantity: float
    unit_price: float

    @computed_field
    @property
    def amount(self) -> float:
        return round(self.quantity * self.unit_price, 2)

class ExtractedInvoice(BaseModel):
    line_items: list[LineItem] = Field(default_factory=list)

    @computed_field
    @property
    def line_item_count(self) -> int:
        return len(self.line_items)
```

## Cross-Field Validation

```python
from pydantic import model_validator

class ExtractedInvoice(BaseModel):
    subtotal: float
    tax_amount: float
    total_amount: float
    invoice_date: Optional[date] = None
    due_date: Optional[date] = None

    @model_validator(mode='after')
    def validate_totals(self) -> 'ExtractedInvoice':
        expected = round(self.subtotal + self.tax_amount, 2)
        if abs(expected - self.total_amount) > 0.05:
            raise ValueError(f"total_amount {self.total_amount} != subtotal+tax {expected}")
        return self

    @model_validator(mode='after')
    def validate_dates(self) -> 'ExtractedInvoice':
        if self.invoice_date and self.due_date:
            if self.due_date < self.invoice_date:
                raise ValueError("due_date cannot be before invoice_date")
        return self
```

## LLM Output Parsing Pattern

```python
import json
from pydantic import ValidationError

def parse_llm_output(raw_json: str) -> ExtractedInvoice:
    """Parse and validate LLM JSON output. Raises ExtractionError on failure."""
    try:
        data = json.loads(raw_json)
        return ExtractedInvoice(**data)
    except json.JSONDecodeError as e:
        raise ExtractionError(f"Invalid JSON from LLM: {e}") from e
    except ValidationError as e:
        # Log all field errors for debugging
        errors = [f"{'.'.join(str(l) for l in err['loc'])}: {err['msg']}" for err in e.errors()]
        raise ExtractionError(f"Validation failed: {'; '.join(errors)}") from e
```

## Field Validator (Single Field)

```python
from pydantic import field_validator

class InvoiceModel(BaseModel):
    invoice_number: str

    @field_validator('invoice_number')
    @classmethod
    def normalize_invoice_number(cls, v: str) -> str:
        return v.strip().upper()
```

## Common Pitfalls

| Mistake | Fix |
|---------|-----|
| `raise ValidationError(...)` in validator | `raise ValueError(...)` — Pydantic wraps it |
| `Optional[float]` without default | `Optional[float] = None` |
| Using `dict` for nested data | Define nested `BaseModel` |
| `model.dict()` (Pydantic v1) | `model.model_dump()` (Pydantic v2) |
| `@validator` decorator (Pydantic v1) | `@field_validator` (Pydantic v2) |
