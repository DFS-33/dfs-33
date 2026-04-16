# AgentSpec Framework

AI-assisted development framework with structured 5-phase SDD workflow, 40 specialist agents, and 8 knowledge base domains. Use this for any project — clone, customize, and start building.

## Framework Structure

| Location | Purpose |
|----------|---------|
| `agentspec/agents/` | 40 specialist agents by category |
| `agentspec/commands/` | 13 slash commands |
| `agentspec/kb/` | 8 knowledge base domains |
| `agentspec/sdd/` | SDD workflow templates and artifacts |
| `agentspec/dev/` | Dev Loop (Level 2 agentic development) |
| `.github/prompts/` | Copilot-native integration (this directory) |

## Coding Standards

- **Type hints required** on ALL function signatures — no bare `def f(x)`
- **Pydantic v2** for ALL structured data — never pass raw dicts between functions
- **Structured JSON logging** — never use `print()`
- **Ruff** linter: line-length 100, select `E/F/I/UP/B/SIM`
- **`@computed_field`** for derived values
- **`@model_validator(mode='after')`** for cross-field validation
- **Adapter pattern** for all external services — each service has its own adapter

## AgentSpec SDD Workflow

| Phase | Command | Output |
|-------|---------|--------|
| 0 | `/brainstorm` | `BRAINSTORM_{FEATURE}.md` |
| 1 | `/define` | `DEFINE_{FEATURE}.md` |
| 2 | `/design` | `DESIGN_{FEATURE}.md` |
| 3 | `/build` | Code + `BUILD_REPORT.md` |
| 4 | `/ship` | `SHIPPED_{DATE}.md` in archive |

Artifacts: `agentspec/sdd/features/` (active) → `agentspec/sdd/archive/` (shipped)

## Copilot Integration

Specialist agents: `.github/prompts/agents/` — reference via `#file:`
KB references: `.github/prompts/kb/` — use `#file:` for domain patterns
Full usage guide: `.github/prompts/README.md`

---

> **Customize this file** — replace this content with your project's tech stack, file locations, domain vocabulary, and team conventions. This file is loaded automatically in every Copilot session.
