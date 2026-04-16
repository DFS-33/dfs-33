# /dev Command

> **Dev Loop** — Agentic Development (Level 2) with structured iteration and intelligent routing.

## Usage

```bash
# Craft a new PROMPT (prompt-crafter guides you)
/dev "I want to build a date parser"
/dev "Add caching to the API"

# Execute an existing PROMPT
/dev tasks/PROMPT_DATE_PARSER.md
/dev tasks/PROMPT_CACHE.md --mode afk

# Resume an interrupted session
/dev tasks/PROMPT_CACHE.md --resume

# Validate without executing
/dev tasks/PROMPT_AUTH.md --dry-run

# List available PROMPTs
/dev --list
```

## How It Works

The `/dev` command intelligently routes between two modes:

```text
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              /dev COMMAND ROUTING                                │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│   User Input                              Action                                 │
│   ──────────                              ──────                                 │
│                                                                                  │
│   /dev "description"         →  prompt-crafter (ask questions, build PROMPT)    │
│   /dev tasks/PROMPT_*.md     →  dev-loop-executor (execute the PROMPT)          │
│   /dev --list                →  Show available PROMPTs                          │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## Mode 1: Craft (New Request)

When you provide a description (not a file path), the **prompt-crafter** agent:

1. **Explores** the codebase for context
2. **Asks** targeted questions to clarify requirements
3. **Generates** a complete PROMPT.md file
4. **Confirms** with you before handoff

```bash
/dev "I want to create a Redis caching layer"
```

**Output:**
```text
PROMPT CRAFTER
==============
I'll help you create a ready-to-execute PROMPT for: Redis caching layer

Let me explore the codebase and ask a few questions...

[Questions about scope, quality, integration, verification]

✅ PROMPT CREATED
=================
File: agentspec/dev/tasks/PROMPT_REDIS_CACHE.md
Tasks: 6 (🔴2 🟡3 🟢1)

To execute:
  /dev tasks/PROMPT_REDIS_CACHE.md
```

---

## Mode 2: Execute (Existing PROMPT)

When you provide a PROMPT file path, the **dev-loop-executor** agent:

1. **Loads** PROMPT.md + existing PROGRESS.md
2. **Picks** next task by priority (🔴→🟡→🟢)
3. **Executes** task (invokes @agent if specified)
4. **Verifies** with objective commands
5. **Updates** progress (memory bridge)
6. **Loops** until done or safeguard triggers

```bash
/dev tasks/PROMPT_REDIS_CACHE.md
```

**Options:**
```bash
--mode afk     # Autonomous execution (no pauses)
--mode hitl    # Human-in-the-loop (default)
--max N        # Override max iterations
--dry-run      # Validate without executing
```

---

## Arguments

| Argument | Description |
|----------|-------------|
| `"description"` | Natural language request → triggers prompt-crafter |
| `tasks/PROMPT_*.md` | Path to PROMPT file → triggers executor |
| `--list` | List available PROMPTs in `agentspec/dev/tasks/` |
| `--mode` | Execution mode: `hitl` (default) or `afk` |
| `--resume` | Resume from existing PROGRESS file (memory bridge) |
| `--dry-run` | Validate and show plan without executing |
| `--max N` | Override max iterations (default: 30) |

---

## Workflow

### Complete Flow

```text
1. /dev "I want to build X"        # Craft phase
   ↓
2. [Questions and clarifications]   # Interactive
   ↓
3. PROMPT.md generated              # Ready to execute
   ↓
4. /dev tasks/PROMPT_X.md           # Execute phase
   ↓
5. [Loop with verification]         # Automated
   ↓
6. EXIT_COMPLETE                    # Done
```

### Skip Crafting (If You Know What You Want)

```bash
# Copy template and edit manually
cp agentspec/dev/templates/PROMPT_TEMPLATE.md \
   agentspec/dev/tasks/PROMPT_MY_TASK.md

# Then execute
/dev tasks/PROMPT_MY_TASK.md
```

---

## The 3-Level Development Spectrum

```text
┌─────────────────────────────────────────────────────────────────────────────────┐
│                          DEVELOPMENT SPECTRUM                                    │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│   LEVEL 1                  LEVEL 2                     LEVEL 3                  │
│   Vibe Coding              Agentic Development         Spec-Driven Dev          │
│   ───────────              ────────────────────        ───────────────          │
│                                                                                  │
│   • Just prompts           • PROMPT.md driven          • 8-phase pipeline       │
│   • No structure           • Verification loops        • Full traceability      │
│   • Hope it works          • Agent leverage            • Quality gates          │
│   • Quick fixes            • Memory bridge             • Enterprise audit       │
│                            • Question-first            • ADRs and specs         │
│                                                                                  │
│   Command: (none)          Command: /dev               Command: /build-feature  │
│   Time: < 30 min           Time: 1-4 hours             Time: Multi-day          │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## Examples

### Example 1: Build a Utility

```bash
# Start with a description
/dev "I need a date parser that handles multiple formats"

# Answer questions about scope, quality, verification
# Get a generated PROMPT

# Execute
/dev tasks/PROMPT_DATE_PARSER.md
```

### Example 2: Build a KB Domain

```bash
/dev "Create a Redis knowledge base"

# Questions about topics, depth, examples
# Generated PROMPT with @kb-architect tasks

/dev tasks/PROMPT_REDIS_KB.md
```

### Example 3: Refactor Code

```bash
/dev "Refactor the authentication module to use JWT"

# Questions about backward compatibility, tests
# Generated PROMPT with risky tasks first

/dev tasks/PROMPT_AUTH_REFACTOR.md --mode hitl
```

### Example 4: Validate Before Execution

```bash
# Dry run to see task breakdown and verify structure
/dev tasks/PROMPT_COMPLEX_FEATURE.md --dry-run

# Output shows:
# - Task summary (🔴 RISKY, 🟡 CORE, 🟢 POLISH counts)
# - Agent references (@python-developer, @test-generator)
# - Verification commands
# - Any validation issues
```

### Example 5: Resume Interrupted Session

```bash
# Session was interrupted (context rot, timeout, etc.)
# Progress was saved to: agentspec/dev/progress/PROGRESS_REDIS_CACHE.md

# Resume from where you left off
/dev tasks/PROMPT_REDIS_CACHE.md --resume

# Executor will:
# - Load completed tasks from PROGRESS file
# - Skip already-done work
# - Continue from next incomplete task
# - Preserve key decisions and context
```

---

## Session Recovery

The memory bridge system ensures no work is lost:

```text
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              SESSION RECOVERY FLOW                               │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│   Session 1 (Interrupted)              Session 2 (Resumed)                      │
│   ────────────────────────             ──────────────────────                   │
│                                                                                  │
│   /dev tasks/PROMPT_X.md               /dev tasks/PROMPT_X.md --resume          │
│         │                                    │                                   │
│         ▼                                    ▼                                   │
│   ┌─────────────┐                      ┌─────────────┐                          │
│   │ Task 1 ✅   │                      │ Load        │                          │
│   │ Task 2 ✅   │ ──── saves ────→     │ PROGRESS.md │                          │
│   │ Task 3 🔄   │     progress         └──────┬──────┘                          │
│   │ [TIMEOUT]   │                             │                                  │
│   └─────────────┘                             ▼                                  │
│                                        ┌─────────────┐                          │
│                                        │ Skip 1, 2   │                          │
│                                        │ Continue 3  │                          │
│                                        │ Task 4...   │                          │
│                                        └─────────────┘                          │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### Recovery Files

| File | Purpose |
|------|---------|
| `progress/PROGRESS_{NAME}.md` | Tracks completed tasks, key decisions, iteration log |
| `logs/LOG_{NAME}_{TS}.md` | Final execution report with statistics |

---

## Folder Structure

```text
agentspec/dev/
├── _index.md                        # Documentation
├── tasks/                           # Your PROMPT files (active work)
│   └── PROMPT_*.md
├── progress/                        # Memory bridge (auto-managed)
│   └── PROGRESS_*.md
├── logs/                            # Execution logs
│   └── LOG_*.md
└── templates/                       # Templates and examples
    ├── PROMPT_TEMPLATE.md           # Blank template
    ├── PROGRESS_TEMPLATE.md         # Progress file template
    ├── PROMPT_EXAMPLE_FEATURE.md    # Example: Python utility
    └── PROMPT_EXAMPLE_KB.md         # Example: KB domain
```

---

## See Also

| Resource | Path |
|----------|------|
| Level 2 Overview | `agentspec/dev/_index.md` |
| Prompt Crafter Agent | `agentspec/agents/dev/prompt-crafter.md` |
| Dev Loop Executor | `agentspec/agents/dev/dev-loop-executor.md` |
| PROMPT Template | `agentspec/dev/templates/PROMPT_TEMPLATE.md` |
| Level 3 (SDD) | `agentspec/sdd/_index.md` |

---

*Dev Loop v1.1 — Ask first, execute perfectly, recover gracefully*
