---
mode: 'ask'
description: 'Gemini 2.0 Flash quick reference — multimodal extraction, structured output, and LangFuse tracing'
---

# Gemini 2.0 Flash Reference — Invoice Pipeline

> Quick reference for Gemini 2.0 Flash patterns used in this project.
> Full KB: `agentspec/kb/gemini/`

## Basic Setup

```python
import google.generativeai as genai
import os

genai.configure(api_key=os.environ["GOOGLE_API_KEY"])
# Or use Application Default Credentials (recommended in Cloud Run):
# genai.configure()  # uses ADC automatically
```

## Multimodal Extraction (Image + Text)

```python
def extract_invoice(image_bytes: bytes, prompt: str) -> str:
    """Extract structured data from invoice image."""
    model = genai.GenerativeModel("gemini-2.0-flash")
    image_part = {"mime_type": "image/png", "data": image_bytes}
    response = model.generate_content(
        [prompt, image_part],
        generation_config=genai.GenerationConfig(temperature=0),
    )
    return response.text
```

## Structured JSON Output (Schema-Guided)

```python
from pydantic import BaseModel

def extract_structured(image_bytes: bytes, schema: type[BaseModel]) -> dict:
    """Extract with Pydantic schema enforcement."""
    model = genai.GenerativeModel(
        "gemini-2.0-flash",
        generation_config=genai.GenerationConfig(
            temperature=0,
            response_mime_type="application/json",
            response_schema=schema.model_json_schema(),
        ),
    )
    image_part = {"mime_type": "image/png", "data": image_bytes}
    response = model.generate_content([EXTRACTION_PROMPT, image_part])
    return json.loads(response.text)
```

## Extraction Prompt Template

```python
EXTRACTION_PROMPT = """
You are an invoice data extraction specialist.

Extract ALL fields from the invoice image.

## Rules
- Return ONLY valid JSON — no markdown, no explanation text
- Use null for any field you cannot find with certainty — never guess
- Amounts must be positive floats with exactly 2 decimal places
- Dates must be YYYY-MM-DD format or null

## Required Fields
{
  "invoice_number": "string — the unique invoice identifier",
  "vendor_name": "string — the billing company name",
  "invoice_date": "YYYY-MM-DD or null",
  "due_date": "YYYY-MM-DD or null",
  "subtotal": "float — pre-tax amount",
  "tax_amount": "float — tax charged (0.0 if none)",
  "total_amount": "float — final amount due",
  "line_items": [
    {
      "description": "string",
      "quantity": "float",
      "unit_price": "float",
      "amount": "float — quantity × unit_price"
    }
  ]
}
"""
```

## Error Handling

```python
import google.api_core.exceptions as gcp_exceptions

def safe_extract(image_bytes: bytes) -> str:
    try:
        return extract_invoice(image_bytes, EXTRACTION_PROMPT)
    except gcp_exceptions.ResourceExhausted:
        # Rate limit — wait and retry
        raise GeminiRateLimitError("Quota exceeded")
    except gcp_exceptions.InvalidArgument as e:
        # Bad request — log and fail fast
        raise GeminiInvalidRequestError(str(e))
    except Exception as e:
        # Unexpected — trigger OpenRouter fallback
        logger.error("gemini_unexpected_error", error=str(e))
        raise
```

## LangFuse Tracing

```python
from langfuse import Langfuse

langfuse = Langfuse()

def extract_with_trace(image_bytes: bytes, invoice_id: str) -> str:
    trace = langfuse.trace(name="invoice_extraction", id=invoice_id)
    generation = trace.generation(
        name="gemini_extraction",
        model="gemini-2.0-flash",
        input={"image_size_bytes": len(image_bytes), "prompt": EXTRACTION_PROMPT},
    )
    try:
        result = extract_invoice(image_bytes, EXTRACTION_PROMPT)
        generation.end(output={"raw_response": result[:500]})
        return result
    except Exception as e:
        generation.end(level="ERROR", status_message=str(e))
        raise
```

## OpenRouter Fallback

```python
import openai

def call_openrouter_fallback(image_bytes: bytes) -> str:
    """Fallback to Claude or GPT-4o via OpenRouter."""
    client = openai.OpenAI(
        base_url="https://openrouter.ai/api/v1",
        api_key=os.environ["OPENROUTER_API_KEY"],
    )
    import base64
    b64_image = base64.b64encode(image_bytes).decode()
    response = client.chat.completions.create(
        model="anthropic/claude-3-5-sonnet",
        messages=[{
            "role": "user",
            "content": [
                {"type": "text", "text": EXTRACTION_PROMPT},
                {"type": "image_url", "image_url": {"url": f"data:image/png;base64,{b64_image}"}},
            ]
        }],
        temperature=0,
    )
    return response.choices[0].message.content
```

## Model Reference

| Model | Context | Best For | Cost |
|-------|---------|----------|------|
| `gemini-2.0-flash` | 1M tokens | Invoice extraction (primary) | Low |
| `gemini-1.5-pro` | 2M tokens | Complex multi-page docs | Medium |

**Always use `gemini-2.0-flash`** for production extraction — best speed/cost ratio.
