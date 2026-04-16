# AgentSpec — GitHub Copilot Usage Guide

> The AgentSpec 4.1 workflow integrated with GitHub Copilot. This folder contains everything you need to run structured feature development, invoke specialist agents, and reference knowledge base patterns — natively in VS Code.

## Requirements

- VS Code 1.95+
- GitHub Copilot extension
- Open this repository in VS Code

---

## Quick Start (3 steps)

**1. Customize** — fill in the `{placeholders}` in `.github/copilot-instructions.md`

**2. Open Copilot Chat** — `Ctrl+Shift+I` (Windows/Linux) or `Cmd+Shift+I` (Mac)

**3. Start a feature** — type `/brainstorm` and describe your idea

---

## Folder Structure

```text
.github/
├── copilot-instructions.md   ← loaded automatically in every session (CUSTOMIZE THIS FIRST)
└── prompts/
    ├── README.md             ← you are here
    ├── workflow/             ← 5-phase SDD pipeline
    ├── agents/               ← universal specialist agents
    │   └── examples/         ← specialized agents (reference for creating your own)
    └── kb/                   ← universal knowledge base domains
        └── examples/         ← specialized KB domains (reference for creating your own)
```

---

## Step 0: Customize copilot-instructions.md

Before using any agents or workflows, fill in the placeholders in `.github/copilot-instructions.md`.

Search for `{YOUR_` to find every placeholder:

| Placeholder | Replace With |
|-------------|--------------|
| `{YOUR_PROJECT_NAME}` | Your project name |
| `{YOUR_PROJECT_DESCRIPTION}` | 2-3 sentences on what the project does |
| `{YOUR_TECH_STACK}` | Language, framework, database, infra |
| `{YOUR_CODING_STANDARD_*}` | Your team's non-negotiable standards |
| `{YOUR_TERM_*}` | Domain vocabulary specific to your project |

---

## SDD Workflow (5 Phases)

Run features from idea to archive using the structured development workflow.

| Phase | Command | What it does | Output |
|-------|---------|--------------|--------|
| 0 | `/brainstorm` | Explore idea via dialogue | `BRAINSTORM_{FEATURE}.md` |
| 1 | `/define` | Capture requirements | `DEFINE_{FEATURE}.md` |
| 2 | `/design` | Technical architecture | `DESIGN_{FEATURE}.md` |
| 3 | `/build` | Implement from manifest | Code + `BUILD_REPORT.md` |
| 4 | `/ship` | Archive with lessons | `SHIPPED_{DATE}.md` |

### How to invoke

```
# In Copilot Chat — slash command
/brainstorm "I want to add retry logic to the API"

# Via #file: reference
#file:.github/prompts/workflow/define.prompt.md
Process this BRAINSTORM: #file:agentspec/sdd/features/BRAINSTORM_MY_FEATURE.md

# Pass an existing document
#file:.github/prompts/workflow/design.prompt.md
Design from: #file:agentspec/sdd/features/DEFINE_MY_FEATURE.md
```

SDD artifacts go to `agentspec/sdd/features/` (active) and `agentspec/sdd/archive/` (shipped).

---

## Universal Agents

5 agents ready to use on any project.

| Agent | File | Use When |
|-------|------|----------|
| `code-reviewer` | `agents/code-reviewer.prompt.md` | Review any Python or IaC file |
| `python-developer` | `agents/python-developer.prompt.md` | Write type-safe Python |
| `test-generator` | `agents/test-generator.prompt.md` | Generate pytest tests |
| `llm-specialist` | `agents/llm-specialist.prompt.md` | Design LLM prompts and extraction |
| `github-copilot-specialist` | `agents/github-copilot-specialist.prompt.md` | Configure Copilot itself |

### How to invoke an agent

```
# Review a specific file
#file:.github/prompts/agents/code-reviewer.prompt.md
Review #file:src/mymodule/handler.py

# Write new code
#file:.github/prompts/agents/python-developer.prompt.md
Write a Pydantic model for representing a customer order

# Generate tests
#file:.github/prompts/agents/test-generator.prompt.md
Generate pytest tests for #file:src/mymodule/models.py
```

### Example Agents (reference for creating your own)

The `agents/examples/` folder contains specialized agents from a real project. Use them as templates when creating agents for your specific stack:

| Agent | Reference Stack |
|-------|----------------|
| `extraction-specialist` | LLM document extraction |
| `pipeline-architect` | Cloud Run + Pub/Sub pipelines |
| `function-developer` | Serverless functions |
| `dataops-builder` | CrewAI monitoring agents |
| `infra-deployer` | Terraform/Terragrunt IaC |

**To create a new agent:** copy any file from `agents/examples/`, update the `description` frontmatter, and replace the system prompt body.

---

## Knowledge Base

4 universal KBs — use with `#file:` to ground Copilot in proven patterns.

| Domain | File | Use For |
|--------|------|---------|
| Python | `kb/python-reference.prompt.md` | Type hints, Pydantic v2, logging |
| Testing | `kb/testing-reference.prompt.md` | pytest, fixtures, mocking |
| REST API | `kb/rest-api-reference.prompt.md` | HTTP design, FastAPI, auth |
| Data Engineering | `kb/data-engineering-reference.prompt.md` | ETL, batch/streaming, data quality |

### How to use KBs

```
# Ask a domain question
#file:.github/prompts/kb/python-reference.prompt.md
How do I validate cross-field relationships in Pydantic?

# Combine KB + file context
#file:.github/prompts/kb/testing-reference.prompt.md
#file:src/mymodule/models.py
Generate pytest tests for this module
```

### Example KBs (reference for creating your own)

The `kb/examples/` folder shows how to create specialized KBs for specific technologies:

| KB | Technology |
|----|-----------|
| `spark-reference` | Apache Spark / PySpark |
| `mongodb-reference` | MongoDB / PyMongo |
| `databricks-reference` | Databricks / Delta Lake |

**To create a new KB:** copy any file from `kb/examples/`, update `description` frontmatter, replace content with your technology's patterns.

---

## Full SDD Feature Cycle

```
1. /brainstorm "Add retry logic to the processor"
   → answer discovery questions → produces BRAINSTORM doc

2. /define agentspec/sdd/features/BRAINSTORM_{FEATURE}.md
   → extracts requirements → produces DEFINE doc

3. /design agentspec/sdd/features/DEFINE_{FEATURE}.md
   → designs architecture → produces DESIGN doc

4. /build agentspec/sdd/features/DESIGN_{FEATURE}.md
   → implements all files → produces BUILD REPORT

5. /ship
   → archives artifacts + captures lessons learned
```

---

## Troubleshooting

| Problem | Solution |
|---------|---------|
| `/brainstorm` not recognized | Check VS Code Copilot extension is updated; try `#file:` invocation instead |
| Agent gives generic answer | Add `#file:.github/copilot-instructions.md` to your message |
| Copilot doesn't know the tech stack | Verify `.github/copilot-instructions.md` has `{placeholders}` filled in |
| Prompt file not found | Use VS Code autocomplete: type `#file:.github/prompts/` and browse |
