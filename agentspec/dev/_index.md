# Dev Loop

> **Agentic Development (Level 2)** — Ask first, execute perfectly, recover gracefully.
> Structured iteration with intelligent PROMPT crafting and session recovery.

---

## The 3-Level Development Spectrum

```text
┌─────────────────────────────────────────────────────────────────────────────────┐
│                          DEVELOPMENT SPECTRUM                                    │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│   LEVEL 1                  LEVEL 2                     LEVEL 3                  │
│   Vibe Coding              Agentic Development         Spec-Driven Dev (SDD)   │
│   ───────────              ────────────────────        ─────────────────────    │
│                                                                                  │
│   • Just prompts           • PROMPT.md driven          • 8-phase pipeline       │
│   • No structure           • Question-first crafting   • Full traceability      │
│   • Hope it works          • Verification loops        • Quality gates          │
│   • Quick fixes            • Agent leverage            • Enterprise audit       │
│                            • Memory bridge             • ADRs and specs         │
│                            • Priority execution                                 │
│                                                                                  │
│   Command: (none)          Command: /dev               Command: /build-feature  │
│   Time: < 30 min           Time: 1-4 hours             Time: Multi-day          │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## How It Works

```text
┌─────────────────────────────────────────────────────────────────────────────────┐
│                           AGENTIC DEVELOPMENT FLOW                               │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                  │
│   /dev "description"                      /dev tasks/PROMPT_*.md                │
│         │                                        │                               │
│         ▼                                        ▼                               │
│   ┌─────────────────┐                    ┌─────────────────┐                    │
│   │  PROMPT CRAFTER │                    │  DEV LOOP       │                    │
│   │                 │                    │  EXECUTOR       │                    │
│   │  1. Explore     │                    │                 │                    │
│   │  2. Ask         │ ──── generates ──→ │  1. Load        │                    │
│   │  3. Design      │     PROMPT.md      │  2. Pick (🔴→🟡→🟢) │                    │
│   │  4. Confirm     │                    │  3. Execute     │                    │
│   └─────────────────┘                    │  4. Verify      │                    │
│                                          │  5. Update      │                    │
│                                          │  6. Loop        │                    │
│                                          └────────┬────────┘                    │
│                                                   │                              │
│                                                   ▼                              │
│                                          ┌─────────────────┐                    │
│                                          │  EXIT_COMPLETE  │                    │
│                                          └─────────────────┘                    │
│                                                                                  │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## Quick Start

### Option 1: Let the Crafter Guide You (Recommended)

```bash
/dev "I want to build a date parser utility"
```

The **prompt-crafter** will:
1. Explore your codebase for context
2. Ask targeted questions about scope, quality, verification
3. Generate a complete PROMPT.md
4. Hand off for execution

### Option 2: Execute an Existing PROMPT

```bash
/dev tasks/PROMPT_DATE_PARSER.md
```

### Option 3: Manual PROMPT Creation

```bash
# Copy template
cp agentspec/dev/templates/PROMPT_TEMPLATE.md \
   agentspec/dev/tasks/PROMPT_MY_TASK.md

# Edit, then execute
/dev tasks/PROMPT_MY_TASK.md
```

---

## Folder Structure

```text
agentspec/dev/
├── _index.md                        # This documentation
├── readme.md                        # Feature overview
│
├── tasks/                           # Your PROMPT files (active work)
│   └── PROMPT_*.md
│
├── progress/                        # Memory bridge (auto-managed)
│   └── PROGRESS_*.md
│
├── logs/                            # Execution logs
│   └── LOG_*.md
│
├── templates/                       # Templates
│   ├── PROMPT_TEMPLATE.md           # Blank template
│   ├── PROGRESS_TEMPLATE.md         # Progress file template
│   ├── PROMPT_EXAMPLE_FEATURE.md    # Example: Python utility
│   └── PROMPT_EXAMPLE_KB.md         # Example: KB domain
│
└── examples/                        # Real-world examples
    ├── README.md                    # Examples documentation
    ├── PROMPT_*.md                  # Example PROMPT files
    ├── PROGRESS_*.md                # Example PROGRESS files
    └── LOG_*.md                     # Example LOG files
```

---

## The Two Agents

### 1. Prompt Crafter (`prompt-crafter`)

**When:** You describe what you want in natural language
**What:** Asks questions, explores codebase, generates PROMPT.md

```bash
/dev "Add Redis caching to the API"
```

### 2. Dev Loop Executor (`dev-loop-executor`)

**When:** You have a PROMPT.md ready to execute
**What:** Runs tasks with verification, updates progress, logs results

```bash
/dev tasks/PROMPT_REDIS_CACHE.md
```

---

## Key Concepts

### Quality Tiers

| Tier | Behavior |
|------|----------|
| `prototype` | Speed over perfection. Minimal verification. |
| `production` | Tests required. Full verification. |
| `library` | Backward compatibility. Full docs. |

### Task Priority

| Priority | Symbol | Execute Order |
|----------|--------|---------------|
| RISKY | 🔴 | First — fail fast on hard problems |
| CORE | 🟡 | Second — main implementation |
| POLISH | 🟢 | Last — cleanup and optimization |

### Execution Modes

| Mode | Behavior | Best For |
|------|----------|----------|
| `hitl` | Human-in-the-loop. Pause for review. | Learning, risky tasks |
| `afk` | Autonomous. Commit per task. | Bulk work, low-risk tasks |

### Memory Bridge

Progress files persist state between iterations:
- Prevents token burn from re-exploration
- Records key decisions
- Tracks files changed
- Enables session recovery after interruption

---

## Session Recovery

### Why Recovery Matters

Long-running agentic sessions face several challenges:
- **Context rot** — Token limits cause information loss
- **Timeouts** — Network or system interruptions
- **Human interruption** — User needs to pause and resume later

The memory bridge solves these with automatic state persistence.

### How It Works

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

| File | Location | Purpose |
|------|----------|---------|
| **PROGRESS** | `progress/PROGRESS_{NAME}.md` | Iteration log, key decisions, files changed |
| **LOG** | `logs/LOG_{NAME}_{TS}.md` | Final execution report with statistics |

### Resume Command

```bash
# Resume an interrupted session
/dev tasks/PROMPT_REDIS_CACHE.md --resume

# The executor will:
# 1. Load PROGRESS file
# 2. Skip completed tasks
# 3. Restore key decisions context
# 4. Continue from next incomplete task
```

### Validate Before Execution

```bash
# Dry run to validate PROMPT structure
/dev tasks/PROMPT_AUTH.md --dry-run

# Shows:
# - Task counts by priority (🔴 🟡 🟢)
# - Agent references (@python-developer, etc.)
# - Verification commands
# - Any validation issues
```

---

## Command Options

| Option | Description |
|--------|-------------|
| `--mode hitl` | Human-in-the-loop (default) — pause for review |
| `--mode afk` | Autonomous — run without pauses |
| `--resume` | Resume from existing PROGRESS file |
| `--dry-run` | Validate and show plan without executing |
| `--max N` | Override max iterations (default: 30) |

---

## Agent Integration

Reference agents with `@agent-name` in tasks:

```markdown
### 🟡 CORE
- [ ] @kb-architect: Create Redis KB domain
- [ ] @python-developer: Implement cache wrapper
- [ ] @test-generator: Add unit tests
```

### Available Agents

| Agent | Use For |
|-------|---------|
| `@kb-architect` | Building knowledge bases |
| `@python-developer` | Writing Python code |
| `@test-generator` | Adding tests |
| `@code-reviewer` | Quality checks |
| `@llm-specialist` | Prompt engineering |

---

## Safeguards

| Safeguard | Default | Purpose |
|-----------|---------|---------|
| `max_iterations` | 30 | Prevent infinite loops |
| `max_retries` | 3 | Retry failed tasks |
| `circuit_breaker` | 3 | Stop if no progress |
| `small_steps` | true | One logical change per task |
| `feedback_loops` | [] | Commands to run between tasks |

---

## When to Use Level 2 vs Level 3

| Scenario | Level 2 (/dev) | Level 3 (/build-feature) |
|----------|----------------|--------------------------|
| KB building | ✅ | |
| Prototypes | ✅ | |
| Single features | ✅ | |
| Utilities/parsers | ✅ | |
| Multi-component features | | ✅ |
| Production systems | | ✅ |
| Team projects | | ✅ |
| Full audit trail needed | | ✅ |

---

## Best Practices

1. **Start with questions** — Use `/dev "description"` to let crafter guide you
2. **Prioritize risky work** — Fail fast on hard problems
3. **Use verification commands** — Objective, exit-code based
4. **Track progress** — Memory bridge reduces token burn
5. **Take small steps** — One logical change per task

---

## Related Files

| File | Purpose |
|------|---------|
| `agentspec/commands/dev/dev.md` | Command definition |
| `agentspec/agents/dev/prompt-crafter.md` | PROMPT crafting agent |
| `agentspec/agents/dev/dev-loop-executor.md` | Execution agent |
| `agentspec/dev/templates/PROMPT_TEMPLATE.md` | Blank PROMPT template |
| `agentspec/dev/templates/PROMPT_EXAMPLE_FEATURE.md` | Example: Python utility |
| `agentspec/dev/templates/PROMPT_EXAMPLE_KB.md` | Example: KB domain |
| `agentspec/dev/templates/PROGRESS_TEMPLATE.md` | Progress file template |
| `agentspec/dev/examples/` | Real-world examples from actual feature builds |

---

## References

- [11 Tips For AI Coding With Ralph Wiggum](https://www.aihero.dev/tips-for-ai-coding-with-ralph-wiggum) — Matt Pocock
- [Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) — Anthropic

---

*Dev Loop v1.1 — Ask first, execute perfectly, recover gracefully*
