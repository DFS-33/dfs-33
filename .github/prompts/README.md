# AgentSpec — GitHub Copilot Usage Guide

> The AgentSpec 4.1 workflow ported to GitHub Copilot. This folder contains everything you need to run structured feature development, invoke specialist agents, and reference knowledge base patterns — natively in VS Code.

## Requirements

- VS Code 1.95+
- GitHub Copilot extension (enterprise license)
- Open this repository in VS Code

---

## Quick Start (3 steps)

**1. Open Copilot Chat** — `Ctrl+Shift+I` (Windows/Linux) or `Cmd+Shift+I` (Mac)

**2. Start a feature** — type `/brainstorm` in the chat input and describe your idea

**3. Follow the workflow** — Copilot guides you through Phase 0 → 4

---

## Folder Structure

```text
.github/
├── copilot-instructions.md   ← loaded automatically in every session
└── prompts/
    ├── README.md             ← you are here
    ├── workflow/             ← 5-phase SDD pipeline
    ├── agents/               ← 10 specialist agents
    └── kb/                   ← 3 knowledge base domains
```

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
# In Copilot Chat — slash command (Phase 0)
/brainstorm "I want to add PDF support to the pipeline"

# Or via #file: reference
#file:.github/prompts/workflow/define.prompt.md
Process this BRAINSTORM: #file:agentspec/sdd/features/BRAINSTORM_PDF_SUPPORT.md

# Pass an existing document
#file:.github/prompts/workflow/design.prompt.md
Design from: #file:agentspec/sdd/features/DEFINE_PDF_SUPPORT.md
```

### Output location

SDD artifacts go to `agentspec/sdd/features/` (same as AgentSpec — shared).

---

## Specialist Agents

10 domain-specific agents available for focused tasks.

| Agent | File | Use When |
|-------|------|----------|
| `code-reviewer` | `agents/code-reviewer.prompt.md` | Review any Python or Terraform file |
| `python-developer` | `agents/python-developer.prompt.md` | Write type-safe Python with Pydantic |
| `extraction-specialist` | `agents/extraction-specialist.prompt.md` | Design Gemini extraction prompts |
| `pipeline-architect` | `agents/pipeline-architect.prompt.md` | Design Cloud Run + Pub/Sub pipelines |
| `function-developer` | `agents/function-developer.prompt.md` | Implement Cloud Run functions |
| `test-generator` | `agents/test-generator.prompt.md` | Generate pytest tests |
| `llm-specialist` | `agents/llm-specialist.prompt.md` | Optimize LLM prompts and output |
| `infra-deployer` | `agents/infra-deployer.prompt.md` | Terraform/Terragrunt IaC |
| `dataops-builder` | `agents/dataops-builder.prompt.md` | CrewAI monitoring agents |
| `github-copilot-specialist` | `agents/github-copilot-specialist.prompt.md` | Configure Copilot itself |

### How to invoke an agent

```
# Review a specific file
#file:.github/prompts/agents/code-reviewer.prompt.md
Review #file:functions/gcp/v1/src/functions/data_extractor/main.py

# Write new code
#file:.github/prompts/agents/python-developer.prompt.md
Write a new Pydantic model for classifying invoice documents

# Generate tests
#file:.github/prompts/agents/test-generator.prompt.md
Generate pytest tests for #file:functions/gcp/v1/src/shared/schemas/invoice.py
```

---

## Knowledge Base

3 flattened domain references — use with `#file:` to ground Copilot in project-specific patterns.

| Domain | File | Use For |
|--------|------|---------|
| Pydantic v2 | `kb/pydantic-reference.prompt.md` | Models, validators, LLM parsing |
| GCP | `kb/gcp-reference.prompt.md` | Cloud Run, Pub/Sub, GCS, BigQuery |
| Gemini | `kb/gemini-reference.prompt.md` | Extraction prompts, structured output |

### How to use KB references

```
# Ask a domain question
#file:.github/prompts/kb/pydantic-reference.prompt.md
How do I validate that due_date is after invoice_date?

# Combine KB + file context
#file:.github/prompts/kb/gemini-reference.prompt.md
#file:functions/gcp/v1/src/shared/adapters/llm.py
How should I add schema-guided output to this adapter?
```

---

## Common Patterns

### Full SDD cycle for a new feature

```
1. /brainstorm "Add retry logic to the DLQ processor"
   → answer discovery questions → Copilot produces BRAINSTORM doc

2. /define agentspec/sdd/features/BRAINSTORM_{FEATURE}.md
   → Copilot extracts requirements → produces DEFINE doc

3. /design agentspec/sdd/features/DEFINE_{FEATURE}.md
   → Copilot designs architecture → produces DESIGN doc

4. /build agentspec/sdd/features/DESIGN_{FEATURE}.md
   → Copilot implements all files → produces BUILD REPORT

5. /ship
   → Copilot archives artifacts + captures lessons learned
```

### Quick code review before PR

```
#file:.github/prompts/agents/code-reviewer.prompt.md
Review my changes: #file:functions/gcp/v1/src/functions/data_extractor/main.py
```

### Write tests for existing code

```
#file:.github/prompts/agents/test-generator.prompt.md
Generate unit tests for #file:functions/gcp/v1/src/shared/schemas/invoice.py
Focus on: validation errors, computed fields, edge cases
```

### Design a new pipeline component

```
#file:.github/prompts/agents/pipeline-architect.prompt.md
Design a new function that validates invoice totals against BigQuery records
```

---

## Adding a New Agent

1. Copy any file from `prompts/agents/` as a starting point
2. Update `description` in the YAML frontmatter
3. Replace the system prompt with your agent's role, standards, and patterns
4. Reference relevant KB files with `#file:.github/prompts/kb/{domain}-reference.prompt.md`
5. Test: `#file:.github/prompts/agents/your-new-agent.prompt.md test task here`
6. Commit and open a PR — agents are versioned in git

---

## Adding MCP Support (Future)

When you need MCP server functionality (library docs lookup, code search), see the guide in:
`agentspec/sdd/features/BRAINSTORM_AGENTSPEC_COPILOT_PORT.md` → "Future Guide: Adding MCP Support"

Three options available: VS Code Tasks proxy (quick), Copilot Extension (full parity), AgentSpec sidecar (zero effort).

---

## Troubleshooting

| Problem | Solution |
|---------|---------|
| `/brainstorm` not recognized | Check VS Code Copilot extension is updated; try `#file:` invocation instead |
| Agent gives generic (non-project) answer | Add `#file:.github/copilot-instructions.md` to your message |
| Copilot doesn't know the tech stack | Verify `.github/copilot-instructions.md` exists in repo root |
| Prompt file not found | Use VS Code file autocomplete — type `#file:.github/prompts/` and browse |
| KB answer seems outdated | Cross-check with `agentspec/kb/{domain}/` via AgentSpec for full depth |
