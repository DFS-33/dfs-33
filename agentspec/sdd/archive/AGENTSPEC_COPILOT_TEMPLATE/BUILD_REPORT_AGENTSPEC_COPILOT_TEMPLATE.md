# BUILD REPORT: AgentSpec GitHub Copilot Template

## Summary

| Metric | Value |
|--------|-------|
| **Feature** | AGENTSPEC_COPILOT_TEMPLATE |
| **Date** | 2026-04-16 |
| **Branch** | feature/copilot |
| **Commits** | 2 (git cleanup + full template genericization) |
| **Files Modified** | 11 edited, 9 created, 3 deleted, 5 moved |
| **Blockers** | 0 |
| **AT Results** | 6/6 passing |

---

## Acceptance Tests — Results

| ID | Scenario | Result | Notes |
|----|----------|--------|-------|
| AT-001 | Zero project-specific refs in core prompts | ✅ Pass | grep clean on workflow/, agents/, kb/, README.md, copilot-instructions.md |
| AT-002 | `copilot-instructions.md` has ≥5 `{YOUR_` placeholders | ✅ Pass | 12 placeholders found |
| AT-003 | Universal agents work without project context | ✅ Pass | All 5 agents genericized — no UberEats/Cloud Run/invoice refs |
| AT-004 | 4 universal KBs exist with ≥50 lines each | ✅ Pass | python(99), testing(135), rest-api(125), data-engineering(139) |
| AT-005 | 3 example KBs in `kb/examples/` | ✅ Pass | spark, mongodb, databricks created |
| AT-006 | Git state clean after build | ✅ Pass | `git status` shows clean working tree |

---

## File Status

### Created

| File | Lines | Notes |
|------|-------|-------|
| `.github/prompts/kb/python-reference.prompt.md` | 99 | Type hints, Pydantic v2, logging |
| `.github/prompts/kb/testing-reference.prompt.md` | 135 | pytest, fixtures, mocking, parametrize |
| `.github/prompts/kb/rest-api-reference.prompt.md` | 125 | HTTP design, FastAPI, auth |
| `.github/prompts/kb/data-engineering-reference.prompt.md` | 139 | ETL, batch/streaming, data quality |
| `.github/prompts/kb/examples/spark-reference.prompt.md` | ~80 | PySpark DataFrame API |
| `.github/prompts/kb/examples/mongodb-reference.prompt.md` | ~85 | Document design, indexes, aggregation |
| `.github/prompts/kb/examples/databricks-reference.prompt.md` | ~90 | Delta Lake, Unity Catalog, DLT |
| `agentspec/sdd/features/BRAINSTORM_AGENTSPEC_COPILOT_TEMPLATE.md` | Phase 0 artifact |
| `agentspec/sdd/features/DEFINE_AGENTSPEC_COPILOT_TEMPLATE.md` | Phase 1 artifact |
| `agentspec/sdd/features/DESIGN_AGENTSPEC_COPILOT_TEMPLATE.md` | Phase 2 artifact |

### Edited

| File | Change |
|------|--------|
| `.github/copilot-instructions.md` | Full rewrite as template with `{YOUR_*}` placeholders |
| `.github/prompts/workflow/brainstorm.prompt.md` | Removed project-specific "Project Context" section |
| `.github/prompts/workflow/define.prompt.md` | Removed project-specific section + "invoices" example |
| `.github/prompts/workflow/design.prompt.md` | Removed project-specific section + genericized agent assignments |
| `.github/prompts/workflow/build.prompt.md` | Removed project-specific section + genericized standards enforcement |
| `.github/prompts/workflow/ship.prompt.md` | Removed project-specific "Project Context" section |
| `.github/prompts/agents/code-reviewer.prompt.md` | Full rewrite — removed UberEats/Cloud Run/Pub/Sub specifics |
| `.github/prompts/agents/python-developer.prompt.md` | Full rewrite — removed UberEats context, kept Python standards |
| `.github/prompts/agents/test-generator.prompt.md` | Full rewrite — removed Cloud Run/GCS/LangFuse/BigQuery specifics |
| `.github/prompts/agents/llm-specialist.prompt.md` | Full rewrite — made provider-agnostic |
| `.github/prompts/agents/github-copilot-specialist.prompt.md` | Minor edits — fixed broken KB ref, genericized security checklist |
| `.github/prompts/README.md` | Full rewrite — reflects new structure with examples/ folders |

### Moved

| From | To |
|------|----|
| `agents/extraction-specialist.prompt.md` | `agents/examples/extraction-specialist.prompt.md` |
| `agents/pipeline-architect.prompt.md` | `agents/examples/pipeline-architect.prompt.md` |
| `agents/function-developer.prompt.md` | `agents/examples/function-developer.prompt.md` |
| `agents/dataops-builder.prompt.md` | `agents/examples/dataops-builder.prompt.md` |
| `agents/infra-deployer.prompt.md` | `agents/examples/infra-deployer.prompt.md` |

Also fixed broken KB references in moved examples (gcp-reference/gemini-reference/pydantic-reference → active KBs).

### Deleted

| File | Reason |
|------|--------|
| `.github/prompts/kb/gcp-reference.prompt.md` | GCP-specific — replaced by universal KBs |
| `.github/prompts/kb/gemini-reference.prompt.md` | Gemini-specific — replaced by llm-specialist agent |
| `.github/prompts/kb/pydantic-reference.prompt.md` | Invoice-specific examples — replaced by python-reference KB |

---

## Issues Encountered

| Issue | Resolution |
|-------|-----------|
| `extraction-specialist.prompt.md` had broken KB ref after move | Fixed: updated to `python-reference` + `llm-specialist` |
| `function-developer.prompt.md` had broken GCP + Pydantic KB refs | Fixed: updated to `python-reference` |
| `pipeline-architect.prompt.md` had broken GCP KB ref | Fixed: updated to `data-engineering-reference` |
| `github-copilot-specialist.prompt.md` had `pydantic-reference` in invocation example | Fixed: updated to `python-reference` |
| `define.prompt.md` had "invoices" in a rule example | Fixed: changed to "records/day" |
| `rest-api-reference.prompt.md` had `/invoices` URL example | Fixed: changed to `/orders` |

---

## Final Structure

```text
.github/
├── copilot-instructions.md        ← template with {placeholders}
└── prompts/
    ├── README.md                  ← updated usage guide
    ├── workflow/                  ← 5 phases (clean)
    │   ├── brainstorm.prompt.md
    │   ├── define.prompt.md
    │   ├── design.prompt.md
    │   ├── build.prompt.md
    │   └── ship.prompt.md
    ├── agents/
    │   ├── code-reviewer.prompt.md          ← universal
    │   ├── python-developer.prompt.md       ← universal
    │   ├── test-generator.prompt.md         ← universal
    │   ├── llm-specialist.prompt.md         ← universal
    │   ├── github-copilot-specialist.prompt.md ← universal
    │   └── examples/
    │       ├── extraction-specialist.prompt.md
    │       ├── pipeline-architect.prompt.md
    │       ├── function-developer.prompt.md
    │       ├── dataops-builder.prompt.md
    │       └── infra-deployer.prompt.md
    └── kb/
        ├── python-reference.prompt.md       ← universal
        ├── testing-reference.prompt.md      ← universal
        ├── rest-api-reference.prompt.md     ← universal
        ├── data-engineering-reference.prompt.md ← universal
        └── examples/
            ├── spark-reference.prompt.md
            ├── mongodb-reference.prompt.md
            └── databricks-reference.prompt.md
```

---

## Lessons Learned

- Example agents (now in `examples/`) still had broken KB references pointing to deleted files — always grep for KB refs when deleting KB files
- The `{e.g., ...}` placeholder pattern in `copilot-instructions.md` is safe to have technology names — context makes clear they're examples, not project references
- Content in `examples/` folders intentionally retains project-specific flavor — that's their value

---

## Next Step

**Ready for:** `/ship agentspec/sdd/features/DEFINE_AGENTSPEC_COPILOT_TEMPLATE.md`
