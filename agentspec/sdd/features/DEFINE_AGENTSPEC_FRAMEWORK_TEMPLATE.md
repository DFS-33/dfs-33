# DEFINE: AgentSpec Framework — Public Template Repository

> Transform the existing project repo into a clean, generic, public AgentSpec Framework template that any team can clone and use with GitHub Copilot — no project-specific content, no AgentSpec CLI dependency.

## Metadata

| Attribute | Value |
|-----------|-------|
| **Feature** | AGENTSPEC_FRAMEWORK_TEMPLATE |
| **Date** | 2026-04-16 |
| **Author** | define-agent |
| **Status** | Draft |
| **Clarity Score** | 14/15 |
| **Source** | BRAINSTORM_AGENTSPEC_FRAMEWORK_TEMPLATE.md |

---

## Problem Statement

The AgentSpec framework (SDD workflow, 40 specialist agents, 8 KB domains, Dev Loop, Copilot integration) is currently embedded inside the UberEats invoice processing project. Anyone who clones the repo receives 9 project-specific folders, a `agentspec/` directory tied to AgentSpec CLI, and a CLAUDE.md full of UberEats context. This makes the framework unusable as a standalone template — teams have to manually identify and strip project code before they can use the framework. There is no clean entry point for someone who just wants the AgentSpec workflow.

---

## Target Users

| User | Role | Pain Point |
|------|------|------------|
| Development teams | Engineers wanting a structured AI-assisted workflow | Must clone and manually strip UberEats project code before using the framework |
| New team members | Engineers joining a project that adopted AgentSpec | No clean template to bootstrap from — must reverse-engineer what is framework vs. project |
| Open-source contributors | Engineers who want to extend or fork AgentSpec | Cannot contribute without understanding UberEats-specific context mixed into framework files |

---

## Goals

| Priority | Goal |
|----------|------|
| **MUST** | Delete all UberEats project code: `src/`, `functions/`, `gen/`, `tests/`, `infra/`, `design/`, `notes/`, `archive/`, `pyproject.toml` |
| **MUST** | Rename `agentspec/` directory → `agentspec/` (visible, generic, no AgentSpec CLI dependency) |
| **MUST** | Rename `agentspec/CLAUDE.md` → `agentspec/README.md` and rewrite as generic AgentSpec Framework guide |
| **MUST** | Replace all occurrences of "AgentSpec" → "AgentSpec" across all files in the repo |
| **MUST** | Remove the 5 UberEats-specific domain agents from `agentspec/agents/domain/` |
| **MUST** | Genericize `.github/copilot-instructions.md` — remove UberEats stack, replace with generic AgentSpec template |
| **MUST** | Genericize `.github/prompts/agents/` — remove UberEats-specific types (ExtractedInvoice, PipelineMessage, etc.) |
| **MUST** | Clean `agentspec/sdd/archive/` — remove 6 UberEats feature archives |
| **MUST** | Clean `agentspec/sdd/features/` and `agentspec/sdd/reports/` — must be empty in template |
| **MUST** | Create `README.md` at repo root — public-facing presentation of the AgentSpec Framework |
| **SHOULD** | Update `agentspec/kb/` indices and quick-reference files — replace UberEats examples with generic ones |
| **COULD** | Add a `CONTRIBUTING.md` explaining how to add new agents and KB domains |

---

## Success Criteria

- [ ] Cloning the repo produces **zero** project-specific files (`src/`, `functions/`, `infra/`, etc.)
- [ ] `grep -r "AgentSpec" .` across the repo returns **0 results**
- [ ] `agentspec/` directory **does not exist**; `agentspec/` **exists** with 5 subdirectories (`agents/`, `commands/`, `kb/`, `sdd/`, `dev/`)
- [ ] `agentspec/agents/domain/` **does not exist**
- [ ] `agentspec/sdd/archive/`, `agentspec/sdd/features/`, `agentspec/sdd/reports/` are **empty** (or contain only `.gitkeep`)
- [ ] `README.md` exists at repo root and contains "AgentSpec" as the framework name
- [ ] `.github/copilot-instructions.md` contains **zero** UberEats/invoice references
- [ ] `.github/prompts/agents/` files contain **zero** UberEats-specific type names

---

## Acceptance Tests

| ID | Scenario | Given | When | Then |
|----|----------|-------|------|------|
| AT-001 | Repo clone is clean | Developer clones the repo | Lists root directory | Only sees `README.md`, `agentspec/`, `.github/`, `.git/` |
| AT-002 | No "AgentSpec" references | Repo is cloned | Runs `grep -r "AgentSpec" .` (excluding .git/) | Returns 0 matches |
| AT-003 | Framework structure intact | Developer opens `agentspec/` | Lists subdirectories | Finds `agents/`, `commands/`, `kb/`, `sdd/`, `dev/` — with all 35 generic agents |
| AT-004 | Copilot integration works | Developer opens VS Code with Copilot | Types `/brainstorm` in Copilot Chat | Copilot responds with AgentSpec Phase 0 structure |
| AT-005 | No domain agents | Developer looks for domain-specific agents | Lists `agentspec/agents/domain/` | Directory does not exist |
| AT-006 | SDD ready for new project | Developer wants to start first feature | Opens `agentspec/sdd/features/` | Directory is empty — ready for first BRAINSTORM |
| AT-007 | Public README is welcoming | New user finds repo on GitHub | Reads `README.md` | Understands what AgentSpec is, how to use it, and how to start |
| AT-008 | No UberEats in Copilot instructions | Developer uses Copilot Chat | Copilot answers "what is this project?" | Describes AgentSpec Framework, not UberEats invoice pipeline |

---

## Out of Scope

- **Port remaining 30 agents to Copilot** — Phase 2, documented in SHIPPED_AGENTSPEC_COPILOT_PORT
- **Rewrite KB patterns/ and concepts/ deeply** — technical patterns (GCP, Spark, CrewAI) are generic; only indices and quick-reference need updating
- **Create new domain agents to replace the 5 deleted** — `_template.md.example` guides users to create their own
- **CI/CD for the template repo** — no code to test; markdown and config files only
- **Documentation website** — README.md is sufficient for MVP public release
- **Remove git history** — preserve full history; users can `git clone --depth 1` if desired
- **JetBrains or other IDE support** — VS Code + Copilot only (inherited from AGENTSPEC_COPILOT_PORT)

---

## Constraints

| Type | Constraint | Impact |
|------|------------|--------|
| Operational | In-place transformation — no new repo | All changes happen on `main` branch; irreversible without git history |
| Technical | `.github/` must remain at repo root | Copilot only auto-loads `copilot-instructions.md` from `.github/` root |
| Technical | `agentspec/` replaces `agentspec/` entirely | AgentSpec CLI will no longer find agents/commands automatically (acceptable — goal is Copilot-first) |
| Content | Find & replace "AgentSpec" must not break internal links | Must replace directory references (`agentspec/`) separately from product name references ("AgentSpec") |
| Content | `.github/prompts/agents/` files reference real types and paths | Generic replacements must still be plausible and useful, not just empty placeholders |

---

## Technical Context

| Aspect | Value | Notes |
|--------|-------|-------|
| **Transformation type** | File deletion + rename + content rewrite | No new code generated — purely structural and content changes |
| **Primary tools** | Bash (rm, mv), Edit/Write, Grep | Find & replace across many files requires grep-driven approach |
| **Risk: broken links** | Medium | `agentspec/` references in `.github/` files must be updated to `agentspec/` |
| **Risk: residual UberEats** | Low-Medium | KB patterns/ files have GCP/Gemini context — acceptable as generic patterns; indices need updating |
| **Files to delete** | ~9 directories + pyproject.toml | Irreversible — confirm before executing |
| **Files to rename** | 1 directory (`agentspec/` → `agentspec/`), 1 file (CLAUDE.md → README.md) | |
| **Find & replace scope** | All `.md`, `.yaml`, `.hcl`, `.py`, `.json` files | Exclude `.git/` |

### Agents in `agentspec/agents/domain/` to be REMOVED

| Agent | File |
|-------|------|
| dataops-builder | `agentspec/agents/domain/dataops-builder.md` |
| extraction-specialist | `agentspec/agents/domain/extraction-specialist.md` |
| function-developer | `agentspec/agents/domain/function-developer.md` |
| infra-deployer | `agentspec/agents/domain/infra-deployer.md` |
| pipeline-architect | `agentspec/agents/domain/pipeline-architect.md` |

### SDD Archive to be REMOVED

| Feature | Archive Path |
|---------|-------------|
| INVOICE_PIPELINE | `agentspec/sdd/archive/INVOICE_PIPELINE/` |
| GCS_UPLOAD | `agentspec/sdd/archive/GCS_UPLOAD/` |
| LANGFUSE_OBSERVABILITY | `agentspec/sdd/archive/LANGFUSE_OBSERVABILITY/` |
| SMOKE_TEST | `agentspec/sdd/archive/SMOKE_TEST/` |
| TERRAFORM_TERRAGRUNT_INFRA | `agentspec/sdd/archive/TERRAFORM_TERRAGRUNT_INFRA/` |
| AGENTSPEC_COPILOT_PORT | `agentspec/sdd/archive/AGENTSPEC_COPILOT_PORT/` |

---

## Assumptions

| ID | Assumption | If Wrong, Impact | Validated? |
|----|------------|------------------|------------|
| A-001 | Git `mv` preserves history for renamed `agentspec/` → `agentspec/` | History lost for agent files — acceptable since content is what matters | [ ] |
| A-002 | `.github/prompts/` files are independent of `agentspec/agents/domain/` — no cross-references | Broken references in Copilot prompt files after domain/ deletion | [x] — Confirmed during AGENTSPEC_COPILOT_PORT build |
| A-003 | KB patterns/ content is generic enough to remain without deep rewrite | Residual UberEats examples confuse users; Phase 2 cleanup needed | [ ] |

---

## Clarity Score Breakdown

| Element | Score (0-3) | Notes |
|---------|-------------|-------|
| Problem | 3 | Specific: framework embedded in project, agentspec/ dependency, UberEats context |
| Users | 2 | Teams identified; pain points clear but "any team" is slightly broad |
| Goals | 3 | MUST/SHOULD/COULD with specific file paths and named operations |
| Success | 3 | 8 acceptance tests with grep commands and directory checks |
| Scope | 3 | 7 explicit out-of-scope items with reasons |
| **Total** | **14/15** | |

---

## Open Questions

None — all assumptions flagged above; ready for Design.

---

## Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-04-16 | define-agent | Initial version from BRAINSTORM_AGENTSPEC_FRAMEWORK_TEMPLATE.md |

---

## Next Step

**Ready for:** `/design agentspec/sdd/features/DEFINE_AGENTSPEC_FRAMEWORK_TEMPLATE.md`
