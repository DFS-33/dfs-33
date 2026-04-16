---
mode: 'ask'
description: 'GCP quick reference — Cloud Run Functions, Pub/Sub, GCS, BigQuery patterns for the invoice pipeline'
---

# GCP Reference — Invoice Pipeline

> Quick reference for GCP patterns used in this project.
> Full KB: `agentspec/kb/gcp/`

## Cloud Run Functions (Python 3.11)

### Pub/Sub Trigger Handler

```python
import base64
import json
import functions_framework
from cloudevents.http import CloudEvent

@functions_framework.cloud_event
def main(cloud_event: CloudEvent) -> None:
    """Triggered by Pub/Sub message."""
    raw = base64.b64decode(
        cloud_event.data["message"]["data"]
    ).decode("utf-8")
    message = json.loads(raw)
    # process message...
```

### GCS Eventarc Trigger Handler

```python
@functions_framework.cloud_event
def main(cloud_event: CloudEvent) -> None:
    """Triggered by GCS object creation."""
    bucket = cloud_event.data["bucket"]
    object_path = cloud_event.data["name"]
    # process file...
```

### Environment Variables Pattern

```python
import os
from dataclasses import dataclass, field

@dataclass
class Config:
    project_id: str = field(
        default_factory=lambda: os.environ["GOOGLE_CLOUD_PROJECT"]
    )
    input_bucket: str = field(
        default_factory=lambda: os.environ["INPUT_BUCKET"]
    )
    output_topic: str = field(
        default_factory=lambda: os.environ["OUTPUT_TOPIC"]
    )
```

## Pub/Sub

### Publish Message

```python
from google.cloud import pubsub_v1
import json

publisher = pubsub_v1.PublisherClient()

def publish(project_id: str, topic_id: str, data: dict) -> str:
    topic_path = publisher.topic_path(project_id, topic_id)
    message_bytes = json.dumps(data).encode("utf-8")
    future = publisher.publish(topic_path, message_bytes)
    return future.result()  # returns message_id
```

### Subscription Config (Terraform)

```hcl
resource "google_pubsub_subscription" "extractor_sub" {
  name  = "invoice.classified-extractor-sub"
  topic = google_pubsub_topic.classified.name

  ack_deadline_seconds = 300

  dead_letter_policy {
    dead_letter_topic     = google_pubsub_topic.dlq.id
    max_delivery_attempts = 5
  }

  retry_policy {
    minimum_backoff = "10s"
    maximum_backoff = "600s"
  }
}
```

## GCS (Cloud Storage)

### Download / Upload

```python
from google.cloud import storage

client = storage.Client()

def download_bytes(bucket_name: str, object_path: str) -> bytes:
    bucket = client.bucket(bucket_name)
    return bucket.blob(object_path).download_as_bytes()

def upload_bytes(
    bucket_name: str,
    object_path: str,
    data: bytes,
    content_type: str = "image/png"
) -> None:
    bucket = client.bucket(bucket_name)
    blob = bucket.blob(object_path)
    blob.upload_from_string(data, content_type=content_type)
```

## BigQuery

### Write Rows

```python
from google.cloud import bigquery

client = bigquery.Client()

def write_invoice(project: str, dataset: str, table: str, row: dict) -> None:
    table_id = f"{project}.{dataset}.{table}"
    errors = client.insert_rows_json(table_id, [row])
    if errors:
        raise BigQueryWriteError(f"BQ insert failed: {errors}")
```

### Schema Pattern (Terraform)

```hcl
resource "google_bigquery_table" "invoices" {
  dataset_id = google_bigquery_dataset.invoices.dataset_id
  table_id   = "extracted_invoices"

  schema = jsonencode([
    { name = "invoice_id",   type = "STRING",  mode = "REQUIRED" },
    { name = "vendor_name",  type = "STRING",  mode = "NULLABLE" },
    { name = "total_amount", type = "FLOAT64", mode = "NULLABLE" },
    { name = "extracted_at", type = "TIMESTAMP", mode = "REQUIRED" },
  ])
}
```

## Structured Logging (Cloud Run)

```python
import structlog

# Configure once at module level
structlog.configure(
    processors=[structlog.processors.JSONRenderer()],
)
logger = structlog.get_logger()

# Use bound loggers for request context
log = logger.bind(invoice_id="INV-001", stage="extraction")
log.info("processing_started", image_size_bytes=1024)
log.error("extraction_failed", error="ValidationError: ...")
```

## Project IDs

| Environment | Project |
|-------------|---------|
| dev | `invoice-pipeline-dev` |
| prod | `invoice-pipeline-prod` |

**Always use `os.environ["GOOGLE_CLOUD_PROJECT"]`** — never hardcode project IDs.
