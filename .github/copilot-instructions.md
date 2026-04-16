# {YOUR_PROJECT_NAME}

<!-- SETUP INSTRUCTIONS:
     Replace every {YOUR_*} placeholder with your project's actual content.
     This file is loaded automatically in every GitHub Copilot session.
     Keep it under ~200 lines — each line competes for the context window.
     Run a search for "{YOUR_" to find all placeholders at once.
-->

## What This Project Does

<!-- 2-3 sentences: what the project does and what problem it solves -->
{YOUR_PROJECT_DESCRIPTION}

---

## Technology Stack

<!-- List the main technologies. Be specific — versions matter for Copilot accuracy. -->

| Layer | Technology | Version |
|-------|------------|---------|
| Language | {e.g., Python} | {e.g., 3.11+} |
| Framework | {e.g., FastAPI} | {e.g., 0.104+} |
| Database | {e.g., PostgreSQL} | {e.g., 15} |
| Messaging | {e.g., Kafka / Pub/Sub / SQS} | - |
| Infrastructure | {e.g., AWS Lambda / Cloud Run} | - |
| Observability | {e.g., Datadog / LangFuse / OpenTelemetry} | - |
| IaC | {e.g., Terraform / CDK / Pulumi} | - |

---

## Coding Standards

<!-- List non-negotiable standards for this project. Be specific — "use Pydantic" is better than "validate data". -->

- **{YOUR_STANDARD_1}**: {e.g., "Type hints required on ALL function signatures — no bare `def f(x)`"}
- **{YOUR_STANDARD_2}**: {e.g., "Pydantic v2 for ALL structured data — never pass raw dicts between functions"}
- **{YOUR_STANDARD_3}**: {e.g., "Structured JSON logging — never use `print()` in production code"}
- **{YOUR_STANDARD_4}**: {e.g., "Adapter pattern for all external services — each service has its own adapter class"}
- **{YOUR_STANDARD_5}**: {e.g., "Ruff linter: line-length 100, select E/F/I/UP/B/SIM"}

---

## Key File Locations

<!-- Help Copilot navigate your project structure -->

| What | Where |
|------|-------|
| Main source | `{e.g., src/}` |
| Tests | `{e.g., tests/}` |
| Config | `{e.g., config/}` |
| Infrastructure | `{e.g., infra/}` |
| Shared utilities | `{e.g., src/shared/}` |

---

## Domain Vocabulary

<!-- Terms specific to your domain that Copilot should understand -->

| Term | Meaning |
|------|---------|
| {YOUR_TERM_1} | {Definition — what this concept means in your domain} |
| {YOUR_TERM_2} | {Definition} |
| {YOUR_TERM_3} | {Definition} |

---

## AgentSpec Workflow

| Phase | Command | Output |
|-------|---------|--------|
| 0 | `/brainstorm` | `BRAINSTORM_{FEATURE}.md` |
| 1 | `/define` | `DEFINE_{FEATURE}.md` |
| 2 | `/design` | `DESIGN_{FEATURE}.md` |
| 3 | `/build` | Code + `BUILD_REPORT.md` |
| 4 | `/ship` | `SHIPPED_{DATE}.md` in archive |

Artifacts: `agentspec/sdd/features/` (active) → `agentspec/sdd/archive/` (shipped)

---

## Copilot Integration

Specialist agents: `.github/prompts/agents/` — reference via `#file:`
KB references: `.github/prompts/kb/` — use `#file:` for domain patterns
Full usage guide: `.github/prompts/README.md`
