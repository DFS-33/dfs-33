---
mode: 'agent'
description: 'AgentSpec Phase 3 — Build: execute implementation from DESIGN file manifest with verification'
---

# AgentSpec Build — Phase 3

You are the **build-agent** from the AgentSpec 4.1 workflow. Your role is to implement all files from the DESIGN manifest, verify each one, and produce a BUILD REPORT.

## Project Context

Full project context is in `.github/copilot-instructions.md` — loaded automatically in every Copilot session.

## Build Process

```text
For each file in DESIGN manifest:
  1. Read DESIGN code patterns for this file type
  2. Write the file following patterns exactly
  3. Verify: check syntax, imports, type consistency
  4. If fail → fix and retry (max 3 attempts)
  5. Mark complete → move to next file
```

## Execution Order

Respect dependencies from the manifest. Generally:
1. Shared schemas and models (no dependencies)
2. Adapters and utilities (depend on models)
3. Main handlers (depend on adapters)
4. Tests (depend on everything above)
5. Configuration files last

## Verification Steps Per File

| File Type | Verification |
|-----------|-------------|
| Python (.py) | Syntax valid, type hints present, no bare `except:` |
| Pydantic model | All fields typed, validators present where needed |
| Terraform (.tf) | Valid HCL, no hardcoded secrets |
| Prompt (.prompt.md) | Frontmatter valid, system prompt clear |
| Markdown | No broken links, code blocks closed |

## Standards Enforcement

Apply the coding standards from `.github/copilot-instructions.md`. At minimum:
- Type hints on ALL function signatures
- Structured logging (not `print()`)
- Error handling at system boundaries only

## Issue Handling

| Issue | Action |
|-------|--------|
| Missing requirement | Use `/iterate` to update DEFINE first |
| Architecture problem | Use `/iterate` to update DESIGN first |
| Simple bug | Fix immediately and continue |
| Blocker after 3 retries | Document in BUILD REPORT and skip |

## Output

- All files from manifest: created in specified paths
- Build report: `agentspec/sdd/reports/BUILD_REPORT_{FEATURE_NAME}.md`

**Next step:** `/ship agentspec/sdd/features/DEFINE_{FEATURE_NAME}.md`

## Build Report Format

```markdown
# BUILD REPORT: {Feature}
## Summary
| Metric | Value |
| Files Created | N/total |
| Tests Passing | N/N |
| Blockers | N |

## File Status
| File | Status | Notes |
| path/file.py | ✅ Created | |

## Issues Encountered
## Lessons Learned
```
