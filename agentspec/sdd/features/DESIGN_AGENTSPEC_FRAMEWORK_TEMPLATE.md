# DESIGN: AgentSpec Framework — Public Template Repository

## Metadata

| Attribute | Value |
|-----------|-------|
| **Feature** | AGENTSPEC_FRAMEWORK_TEMPLATE |
| **Date** | 2026-04-16 |
| **Author** | design-agent |
| **Status** | Draft |
| **Source** | DEFINE_AGENTSPEC_FRAMEWORK_TEMPLATE.md |

---

## Architecture Overview

This is a **repo transformation** — not a code build. The operation converts a project-specific repository into a clean, public framework template through a sequenced set of deletions, renames, rewrites, and find-and-replace operations.

```text
CURRENT STATE                        TARGET STATE
─────────────                        ────────────

btc-zero-prd-claude-code/            agentspec-framework/ (or same repo)
├── src/              ← DELETE       ├── README.md            ← CREATE
├── functions/        ← DELETE       ├── agentspec/           ← RENAME from agentspec/
├── gen/              ← DELETE       │   ├── agents/          ← keep (35 generic)
├── tests/            ← DELETE       │   ├── commands/        ← keep
├── infra/            ← DELETE       │   ├── kb/              ← keep structure
├── design/           ← DELETE       │   ├── sdd/             ← keep templates
├── notes/            ← DELETE       │   └── dev/             ← keep
├── archive/          ← DELETE       └── .github/
├── pyproject.toml    ← DELETE           ├── copilot-instructions.md  ← REWRITE
├── agentspec/          ← RENAME           └── prompts/
│   ├── CLAUDE.md     ← REWRITE              ├── README.md    ← UPDATE paths
│   └── agents/                              ├── workflow/    ← keep
│       └── domain/   ← DELETE              ├── agents/      ← GENERICIZE (10 files)
└── .github/                                 └── kb/          ← keep
    └── copilot-instructions.md
```

### Transformation Sequence

```text
Step 1: DELETE project directories (src/, functions/, gen/, tests/, infra/, design/, notes/, archive/)
Step 2: DELETE project-specific framework content (agents/domain/, sdd/archive/*, sdd/features/* UberEats)
Step 3: RENAME directory (agentspec/ → agentspec/)
Step 4: RENAME file (agentspec/CLAUDE.md → agentspec/README.md)
Step 5: FIND & REPLACE paths (agentspec/ → agentspec/ across all .md files)
Step 6: FIND & REPLACE product name ("AgentSpec" → "AgentSpec" across all .md files)
Step 7: REWRITE key files (agentspec/README.md, .github/copilot-instructions.md, 10 agents, 2 KB indices)
Step 8: CREATE new files (README.md at root, .gitkeep for empty dirs)
```

**Sequencing is critical** — renaming must happen before find & replace; delete must happen before rename to avoid operating on files that won't exist.

---

## Key Decisions

### Decision 1: `git mv .claude agentspec` — Preserves History

| Attribute | Value |
|-----------|-------|
| **Status** | Accepted |
| **Date** | 2026-04-16 |

**Context:** Moving `agentspec/` to `agentspec/` could be done by copy + delete or by `git mv`.

**Choice:** `git mv .claude agentspec`

**Rationale:** `git mv` preserves the git history for all 35+ agent files, KB domains, and SDD templates. A team forking the repo can `git log` any agent file and see its full evolution. Copy + delete loses all this history.

**Alternatives Rejected:**
1. `cp -r .claude agentspec && rm -rf .claude` — loses all file history
2. `.agentspec/` hidden folder — less discoverable for a public template

**Consequences:**
- Git tracks the rename; `git log --follow agentspec/agents/code-quality/code-reviewer.md` works
- AgentSpec CLI loses automatic discovery (`agentspec/` no longer exists) — acceptable; Copilot is the target

---

### Decision 2: Full Rewrite of `agentspec/README.md` (was CLAUDE.md)

| Attribute | Value |
|-----------|-------|
| **Status** | Accepted |
| **Date** | 2026-04-16 |

**Context:** The existing `CLAUDE.md` is 100% UberEats — tech stack, architecture diagram, active features, shipped features, environment variables. Zero content is salvageable as-is.

**Choice:** Full rewrite as generic AgentSpec Framework guide covering: what is AgentSpec, folder structure, how to use SDD workflow, how to invoke agents, how to use KB, how to customize.

**Rationale:** Targeted edits would require removing 95% of the content anyway. A clean rewrite is faster and produces better output.

**Alternatives Rejected:**
1. Targeted editing — would require removing architecture diagram, pipeline functions table, GCP config, shipped features, active features; nothing would remain
2. Leave as-is — violates R-001 (zero UberEats residue)

**Consequences:**
- New README.md is a framework guide, not a project guide — suitable for any team cloning the template

---

### Decision 3: Rewrite `.github/prompts/agents/` Files — Not Just Find & Replace

| Attribute | Value |
|-----------|-------|
| **Status** | Accepted |
| **Date** | 2026-04-16 |

**Context:** All 10 agent prompt files contain UberEats-specific types throughout their system prompts: `ExtractedInvoice`, `PipelineMessage`, `tiff_to_png`, `invoice_classifier`, `data_extractor`, `bigquery_writer`, specific GCS bucket names, BigQuery table schemas.

**Choice:** Rewrite each file to use generic placeholders: `DataRecord`, `EventMessage`, `{your_function}/`, `{your_schema}`. Keep the agent's role, standards, and patterns — only genericize the examples.

**Rationale:** Find & replace cannot handle the semantic depth of these references. "ExtractedInvoice" appears in class definitions, type hints, and example outputs — each needs different treatment. Rewrite gives clean output.

**Alternatives Rejected:**
1. Find & replace only — produces half-replaced, confusing files
2. Delete domain agents from `.github/prompts/agents/` — reduces value of the Copilot integration for users who want examples of specialized agents

**Consequences:**
- 10 files must be individually rewritten — most work in the build
- Result is a genuinely useful, generic Copilot agent library that any team can specialize

---

### Decision 4: KB — Update Indices Only, Keep Patterns

| Attribute | Value |
|-----------|-------|
| **Status** | Accepted |
| **Date** | 2026-04-16 |

**Context:** The KB has 8 domains with index.md, quick-reference.md, concepts/, and patterns/. The patterns/ and concepts/ files are technical references (Pydantic validators, GCP Cloud Run scaling, Terraform modules) — generic regardless of project. Only the indices have project-specific framing.

**Choice:** Update only `index.md` and `quick-reference.md` for the 2 most project-coupled domains (gcp/, gemini/). Also rename `gemini/patterns/invoice-extraction.md` → `gemini/patterns/document-extraction.md` and genericize its content.

**Rationale:** YAGNI — rewriting 80+ KB files adds weeks of work for minimal template quality improvement. Technical patterns are reusable as-is. Only the framing docs (indices) need updating.

**Alternatives Rejected:**
1. Rewrite all 80+ KB files — excessive; violates YAGNI
2. Delete UberEats-coupled KB domains — loses genuine value (GCP, Gemini patterns are useful to any team)

**Consequences:**
- Some examples in patterns/ still mention Cloud Run functions and invoices — acceptable as technical examples, not project context

---

## File Manifest

### Phase 1 — DELETE (bash operations)

| # | Target | Type | Notes |
|---|--------|------|-------|
| D-01 | `src/` | Directory | UberEats invoice extractor library |
| D-02 | `functions/` | Directory | Cloud Run functions (all 5) |
| D-03 | `gen/` | Directory | Synthetic invoice generator |
| D-04 | `tests/` | Directory | End-to-end smoke tests |
| D-05 | `infra/` | Directory | Terraform modules + Terragrunt environments |
| D-06 | `design/` | Directory | UberEats architecture design docs |
| D-07 | `notes/` | Directory | UberEats meeting notes |
| D-08 | `archive/` | Directory | Historical ZIPs |
| D-09 | `pyproject.toml` | File | Python project config |
| D-10 | `agentspec/agents/domain/` | Directory | 5 UberEats-specific agents |
| D-11 | `agentspec/sdd/archive/INVOICE_PIPELINE/` | Directory | UberEats feature archive |
| D-12 | `agentspec/sdd/archive/GCS_UPLOAD/` | Directory | UberEats feature archive |
| D-13 | `agentspec/sdd/archive/LANGFUSE_OBSERVABILITY/` | Directory | UberEats feature archive |
| D-14 | `agentspec/sdd/archive/SMOKE_TEST/` | Directory | UberEats feature archive |
| D-15 | `agentspec/sdd/archive/TERRAFORM_TERRAGRUNT_INFRA/` | Directory | UberEats feature archive |
| D-16 | `agentspec/sdd/archive/AGENTSPEC_COPILOT_PORT/` | Directory | UberEats feature archive |
| D-17 | `agentspec/sdd/features/BRAINSTORM_CREWAI_MONITORING.md` | File | UberEats-specific brainstorm |
| D-18 | `agentspec/sdd/features/BRAINSTORM_GCS_UPLOAD.md` | File | UberEats-specific brainstorm |
| D-19 | `agentspec/sdd/features/BRAINSTORM_LANGFUSE_OBSERVABILITY.md` | File | UberEats-specific brainstorm |
| D-20 | `agentspec/sdd/features/BRAINSTORM_SMOKE_TEST.md` | File | UberEats-specific brainstorm |
| D-21 | `agentspec/sdd/features/DEFINE_PIPELINE_OBSERVABILITY_METRICS.md` | File | UberEats-specific feature |
| D-22 | `agentspec/sdd/features/DESIGN_PIPELINE_OBSERVABILITY_METRICS.md` | File | UberEats-specific feature |

### Phase 2 — RENAME (git mv)

| # | From | To | Notes |
|---|------|----|-------|
| R-01 | `agentspec/` | `agentspec/` | Entire directory; preserves git history |
| R-02 | `agentspec/CLAUDE.md` | `agentspec/README.md` | After directory rename |
| R-03 | `agentspec/kb/gemini/patterns/invoice-extraction.md` | `agentspec/kb/gemini/patterns/document-extraction.md` | Genericize file name |

### Phase 3 — FIND & REPLACE (bash script)

| # | Pattern | Replacement | Scope |
|---|---------|-------------|-------|
| FR-01 | `agentspec/` | `agentspec/` | All `.md` files, excluding `.git/` |
| FR-02 | `AgentSpec` | `AgentSpec` | All `.md` files, excluding `.git/` |

### Phase 4 — REWRITE (full content replacement)

| # | File | Action | Key Changes |
|---|------|--------|-------------|
| W-01 | `agentspec/README.md` | Full rewrite | Framework guide: what is AgentSpec, structure, SDD workflow, agents, KB, customization |
| W-02 | `.github/copilot-instructions.md` | Full rewrite | Generic: AgentSpec framework context, coding standards (no UberEats stack), SDD workflow pointer |
| W-03 | `.github/prompts/agents/extraction-specialist.prompt.md` | Rewrite | Generic document extraction; replace ExtractedInvoice → DataRecord, invoice → document |
| W-04 | `.github/prompts/agents/pipeline-architect.prompt.md` | Rewrite | Generic event-driven pipeline; replace UberEats function names with `{your_function}` pattern |
| W-05 | `.github/prompts/agents/function-developer.prompt.md` | Rewrite | Generic Cloud Run function template; remove invoice-specific types |
| W-06 | `.github/prompts/agents/dataops-builder.prompt.md` | Rewrite | Generic monitoring crew; replace invoice pipeline BQ queries with generic log queries |
| W-07 | `.github/prompts/agents/code-reviewer.prompt.md` | Targeted edit | Remove UberEats file path examples; replace with `{your_module}/main.py` |
| W-08 | `.github/prompts/agents/python-developer.prompt.md` | Targeted edit | Remove ExtractedInvoice type example; replace with generic DataRecord |
| W-09 | `.github/prompts/agents/test-generator.prompt.md` | Targeted edit | Remove CloudEvent invoice fixtures; replace with generic event fixtures |
| W-10 | `.github/prompts/agents/llm-specialist.prompt.md` | Targeted edit | Remove invoice extraction prompt example; replace with generic extraction |
| W-11 | `.github/prompts/agents/infra-deployer.prompt.md` | Targeted edit | Remove UberEats module names; replace with `{module_name}` pattern |
| W-12 | `.github/prompts/agents/github-copilot-specialist.prompt.md` | Targeted edit | Remove UberEats references in examples |
| W-13 | `.github/prompts/README.md` | Path update | `agentspec/` → `agentspec/` in all #file: references |
| W-14 | `agentspec/kb/gcp/index.md` | Targeted edit | Remove UberEats pipeline description; replace with generic pipeline description |
| W-15 | `agentspec/kb/gemini/index.md` | Targeted edit | Remove "invoice extraction" framing; replace with "document extraction" |
| W-16 | `agentspec/kb/gemini/patterns/document-extraction.md` | Targeted edit | Replace invoice-specific examples with generic document extraction examples |

### Phase 5 — CREATE

| # | File | Action | Content |
|---|------|--------|---------|
| C-01 | `README.md` | Create | Public-facing AgentSpec Framework intro: what it is, structure, quick start, how to contribute |
| C-02 | `agentspec/sdd/features/.gitkeep` | Create | Empty marker — features/ is clean for first use |
| C-03 | `agentspec/sdd/reports/.gitkeep` | Create | Empty marker — reports/ is clean for first use |

---

## Execution Patterns

### Pattern 1: Delete project directories

```bash
# From repo root — IRREVERSIBLE, confirm before running
rm -rf src/ functions/ gen/ tests/ infra/ design/ notes/ archive/ pyproject.toml
rm -rf agentspec/agents/domain/
rm -rf agentspec/sdd/archive/INVOICE_PIPELINE/ \
        agentspec/sdd/archive/GCS_UPLOAD/ \
        agentspec/sdd/archive/LANGFUSE_OBSERVABILITY/ \
        agentspec/sdd/archive/SMOKE_TEST/ \
        agentspec/sdd/archive/TERRAFORM_TERRAGRUNT_INFRA/ \
        agentspec/sdd/archive/AGENTSPEC_COPILOT_PORT/
rm agentspec/sdd/features/BRAINSTORM_CREWAI_MONITORING.md \
   agentspec/sdd/features/BRAINSTORM_GCS_UPLOAD.md \
   agentspec/sdd/features/BRAINSTORM_LANGFUSE_OBSERVABILITY.md \
   agentspec/sdd/features/BRAINSTORM_SMOKE_TEST.md \
   agentspec/sdd/features/DEFINE_PIPELINE_OBSERVABILITY_METRICS.md \
   agentspec/sdd/features/DESIGN_PIPELINE_OBSERVABILITY_METRICS.md
```

### Pattern 2: Rename directory (git mv)

```bash
git mv .claude agentspec
git mv agentspec/CLAUDE.md agentspec/README.md
git mv agentspec/kb/gemini/patterns/invoice-extraction.md \
        agentspec/kb/gemini/patterns/document-extraction.md
```

### Pattern 3: Find & replace paths (run AFTER rename)

```bash
# Windows-compatible: use PowerShell
Get-ChildItem -Recurse -Filter "*.md" | 
  Where-Object { $_.FullName -notmatch '\.git' } |
  ForEach-Object {
    (Get-Content $_.FullName) -replace '\agentspec/', 'agentspec/' |
    Set-Content $_.FullName
  }
```

### Pattern 4: Find & replace product name (run AFTER path replace)

```bash
Get-ChildItem -Recurse -Filter "*.md" |
  Where-Object { $_.FullName -notmatch '\.git' } |
  ForEach-Object {
    (Get-Content $_.FullName) -replace 'AgentSpec', 'AgentSpec' |
    Set-Content $_.FullName
  }
```

### Pattern 5: Generic agent prompt structure

```markdown
---
mode: 'agent'
description: '{Agent role} for {generic domain}'
---

You are a {role} specialist. You work with any project, not tied to a specific tech stack.

## Your Standards
- {Generic standard 1}
- {Generic standard 2}

## When Invoked
Use `#file:` to provide context:
\`\`\`
#file:.github/prompts/agents/{agent}.prompt.md
{task description} for #file:{your_file}
\`\`\`
```

### Pattern 6: Generic `agentspec/README.md` structure

```markdown
# AgentSpec Framework

> Structured AI-assisted development workflow for any project.

## What Is AgentSpec

5-phase workflow: brainstorm → define → design → build → ship.
40 specialist agents. 8 KB domains. GitHub Copilot integration.

## Structure

agentspec/
├── agents/     ← specialist agents by category
├── commands/   ← slash commands for CLI
├── kb/         ← knowledge base domains
├── sdd/        ← SDD workflow templates
└── dev/        ← Dev Loop (Level 2)

## Quick Start (GitHub Copilot)
...
```

### Pattern 7: Generic `.github/copilot-instructions.md` structure

```markdown
# AgentSpec Framework

AI-assisted development framework using structured workflows,
specialist agents, and knowledge base domains.

## Coding Standards
- Type hints on all function signatures
- Pydantic v2 for structured data
- Adapter pattern for external services
- Structured logging (no print())

## Framework Structure
| Location | Purpose |
|----------|---------|
| `agentspec/agents/` | Specialist agents (40) |
| `agentspec/kb/` | Knowledge base domains (8) |
| `agentspec/sdd/` | SDD workflow templates |
| `.github/prompts/` | Copilot integration |

## AgentSpec Workflow
/brainstorm → /define → /design → /build → /ship
Full guide: `.github/prompts/README.md`
```

---

## Testing Strategy

This is a content transformation, not a code build. Verification is via grep and directory inspection.

| Test | Command | Expected |
|------|---------|---------|
| No UberEats references | `grep -ri "ubereats\|invoice\|tiff_to_png\|bigquery_writer\|data_extractor" . --exclude-dir=.git` | 0 results |
| No "AgentSpec" | `grep -r "AgentSpec" . --exclude-dir=.git` | 0 results |
| No `agentspec/` directory | `ls agentspec/` | "No such file" |
| `agentspec/` exists | `ls agentspec/` | 5 subdirectories |
| `agents/domain/` gone | `ls agentspec/agents/domain/` | "No such file" |
| 35 agents present | `find agentspec/agents -name "*.md" \| wc -l` | ≥ 35 |
| features/ empty | `ls agentspec/sdd/features/` | only .gitkeep |
| archive/ empty | `ls agentspec/sdd/archive/` | only .gitkeep |
| README.md exists | `cat README.md \| head -1` | Contains "AgentSpec" |
| Copilot instructions generic | `grep -i "ubereats\|invoice" .github/copilot-instructions.md` | 0 results |
| Agent files generic | `grep -i "ExtractedInvoice\|PipelineMessage" .github/prompts/agents/*.prompt.md` | 0 results |

---

## Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-04-16 | design-agent | Initial version from DEFINE_AGENTSPEC_FRAMEWORK_TEMPLATE.md |

---

## Next Step

**Ready for:** `/build agentspec/sdd/features/DESIGN_AGENTSPEC_FRAMEWORK_TEMPLATE.md`
