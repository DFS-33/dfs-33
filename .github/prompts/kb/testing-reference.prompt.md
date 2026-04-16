---
mode: 'ask'
description: 'Testing quick reference — pytest patterns, fixtures, mocking, and parametrize'
---

# Testing Reference

> Quick reference for pytest patterns used in Python projects.

## Test File Structure

```text
tests/
├── conftest.py          ← shared fixtures (session/module/function scope)
├── unit/
│   ├── test_models.py   ← pure model/logic tests, no I/O
│   └── test_handlers.py ← handler tests with mocked dependencies
└── integration/
    └── test_api.py      ← real HTTP calls against a running server
```

## Basic Test Pattern

```python
import pytest
from myapp.models import Record


class TestRecord:
    def test_valid_record_creates_successfully(self):
        record = Record(id="r-001", name="Test", amount=10.00)
        assert record.id == "r-001"
        assert record.has_tags is False

    def test_negative_amount_rejected(self):
        with pytest.raises(ValidationError):
            Record(id="r-001", name="Test", amount=-1.0)
```

## Parametrize Pattern

```python
@pytest.mark.parametrize("amount,tags,should_raise", [
    (100.0, ["finance"], False),
    (20_000.0, [], True),   # high value without tags
    (0.0, [], False),
])
def test_business_rule(amount, tags, should_raise):
    if should_raise:
        with pytest.raises(ValueError):
            Record(id="x", name="y", amount=amount, tags=tags)
    else:
        record = Record(id="x", name="y", amount=amount, tags=tags)
        assert record.amount == amount
```

## Fixtures

```python
# conftest.py
import pytest


@pytest.fixture(scope="session")
def sample_bytes():
    """Loaded once per test session."""
    return b"fake-binary-content"


@pytest.fixture
def mock_storage():
    """Fresh mock for each test."""
    from unittest.mock import MagicMock
    adapter = MagicMock()
    adapter.download.return_value = b"content"
    adapter.upload.return_value = True
    return adapter


@pytest.fixture
def sample_record():
    return Record(id="test-001", name="Test Record", amount=42.00)
```

## Mocking Pattern

```python
from unittest.mock import MagicMock, patch


def test_handler_calls_storage(mock_storage, sample_record):
    with patch("myapp.handler.get_storage", return_value=mock_storage):
        from myapp.handler import process
        result = process({"id": "test-001"})
        mock_storage.download.assert_called_once_with("test-001")
        assert result["status"] == "ok"
```

## Async Test Pattern

```python
import pytest
import asyncio


@pytest.mark.asyncio
async def test_async_handler():
    result = await process_async({"id": "x"})
    assert result is not None
```

## Coverage & Run Commands

```bash
# Run all tests
pytest tests/ -v --tb=short

# With coverage
pytest tests/ -v --cov=src --cov-report=term-missing

# Run single test file
pytest tests/unit/test_models.py -v

# Run tests matching a keyword
pytest tests/ -k "test_valid" -v
```

## Common Pitfalls

| Mistake | Fix |
|---------|-----|
| Importing inside test class | Import at top of file or in fixture |
| `scope="session"` on stateful mocks | Use `scope="function"` for stateful dependencies |
| No `monkeypatch` for env vars | `monkeypatch.setenv("KEY", "value")` |
| Testing framework internals | Test your code, not the library |
