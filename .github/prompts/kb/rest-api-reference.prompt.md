---
mode: 'ask'
description: 'REST API quick reference — HTTP design, status codes, FastAPI patterns, and auth'
---

# REST API Reference

> Quick reference for REST API design and FastAPI implementation patterns.

## HTTP Method Guide

| Method | Use For | Idempotent? | Body? |
|--------|---------|-------------|-------|
| `GET` | Read resource | Yes | No |
| `POST` | Create resource | No | Yes |
| `PUT` | Replace resource | Yes | Yes |
| `PATCH` | Partial update | No | Yes |
| `DELETE` | Remove resource | Yes | No |

## Status Code Cheat Sheet

| Code | When to Use |
|------|-------------|
| `200 OK` | Successful GET, PUT, PATCH |
| `201 Created` | Successful POST |
| `204 No Content` | Successful DELETE |
| `400 Bad Request` | Invalid input / validation error |
| `401 Unauthorized` | Missing or invalid auth token |
| `403 Forbidden` | Authenticated but not allowed |
| `404 Not Found` | Resource doesn't exist |
| `409 Conflict` | Duplicate / state conflict |
| `422 Unprocessable Entity` | Semantic validation failure (FastAPI default) |
| `500 Internal Server Error` | Unexpected server-side failure |

## FastAPI Endpoint Pattern

```python
from fastapi import FastAPI, HTTPException, Depends
from pydantic import BaseModel

app = FastAPI()


class CreateItemRequest(BaseModel):
    name: str
    amount: float


class ItemResponse(BaseModel):
    id: str
    name: str
    amount: float


@app.post("/items", response_model=ItemResponse, status_code=201)
async def create_item(
    request: CreateItemRequest,
    db: Database = Depends(get_db),
) -> ItemResponse:
    item = await db.create(request.model_dump())
    return ItemResponse(**item)


@app.get("/items/{item_id}", response_model=ItemResponse)
async def get_item(item_id: str, db: Database = Depends(get_db)) -> ItemResponse:
    item = await db.get(item_id)
    if not item:
        raise HTTPException(status_code=404, detail=f"Item {item_id} not found")
    return ItemResponse(**item)
```

## Error Response Format

```python
# Consistent error structure across all endpoints
class ErrorResponse(BaseModel):
    error: str
    detail: str | None = None
    request_id: str | None = None


@app.exception_handler(HTTPException)
async def http_exception_handler(request, exc):
    return JSONResponse(
        status_code=exc.status_code,
        content=ErrorResponse(error=exc.detail).model_dump(),
    )
```

## Auth Header Pattern

```python
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials

security = HTTPBearer()

def verify_token(credentials: HTTPAuthorizationCredentials = Depends(security)) -> str:
    token = credentials.credentials
    if not is_valid(token):
        raise HTTPException(status_code=401, detail="Invalid token")
    return decode_subject(token)
```

## URL Design Rules

```
# ✅ Nouns, plural, lowercase, hyphens
GET  /orders
GET  /orders/{id}
POST /orders
GET  /orders/{id}/line-items

# ❌ Verbs, mixed case, underscores
GET  /getOrder
POST /create_order
```

## Common Pitfalls

| Mistake | Fix |
|---------|-----|
| Using `200` for all responses | Use `201` for create, `204` for delete |
| Returning stacktrace in 500 | Log internally, return generic message |
| `GET` endpoints with side effects | GET must be read-only and idempotent |
| Inconsistent field naming | Pick `snake_case` or `camelCase` — stick to it |
