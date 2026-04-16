---
mode: 'ask'
description: 'MongoDB quick reference — document design, indexes, aggregation pipeline, and PyMongo patterns'
---

# MongoDB Reference

> Quick reference for MongoDB patterns.
> Copy this file to `.github/prompts/kb/` when your project uses MongoDB.

## Core Concepts

| Concept | SQL Equivalent | Notes |
|---------|---------------|-------|
| Database | Database | Logical container |
| Collection | Table | Schema-less |
| Document | Row | BSON (JSON with types) |
| Field | Column | Flexible per document |
| `_id` | Primary key | Auto-generated ObjectId |

## PyMongo Pattern

```python
from pymongo import MongoClient, ASCENDING, DESCENDING
from pymongo.collection import Collection
from datetime import datetime

client = MongoClient("mongodb://localhost:27017/")
db = client["myapp"]
collection: Collection = db["orders"]

# Insert
result = collection.insert_one({"status": "pending", "created_at": datetime.utcnow()})
print(result.inserted_id)

# Find one
doc = collection.find_one({"_id": result.inserted_id})

# Find many with filter
cursor = collection.find({"status": "pending"}).sort("created_at", ASCENDING).limit(100)
documents = list(cursor)

# Update
collection.update_one({"_id": result.inserted_id}, {"$set": {"status": "processed"}})

# Delete
collection.delete_one({"_id": result.inserted_id})
```

## Index Design

```python
# Single field index
collection.create_index("status")

# Compound index (order matters — matches left-prefix queries)
collection.create_index([("status", ASCENDING), ("created_at", DESCENDING)])

# Unique index
collection.create_index("email", unique=True)

# TTL index (auto-expire documents)
collection.create_index("created_at", expireAfterSeconds=86400)  # 24h
```

## Aggregation Pipeline

```python
pipeline = [
    {"$match": {"status": "processed"}},
    {"$group": {
        "_id": "$category",
        "total": {"$sum": "$amount"},
        "count": {"$sum": 1},
    }},
    {"$sort": {"total": -1}},
    {"$limit": 10},
]

results = list(collection.aggregate(pipeline))
```

## Document Design Rules

| Rule | Reason |
|------|--------|
| Embed data accessed together | Avoid extra queries |
| Reference data queried independently | Avoid document bloat |
| Keep documents under 16MB | Hard BSON limit |
| Use arrays for 1-to-few relationships | Efficient for <100 items |
| Use references for 1-to-many | When child set is large or queried standalone |

## Common Pitfalls

| Mistake | Fix |
|---------|-----|
| No indexes on query fields | Add index for every `find()` filter field |
| Fetching all fields when only a few needed | Use projection: `find({}, {"name": 1, "status": 1})` |
| Using `$where` with JavaScript | Use native operators — `$where` is slow and insecure |
| No TTL on temporary data | Add TTL index to avoid collection bloat |
