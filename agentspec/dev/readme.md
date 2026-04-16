# Dev Loop

> **Agentic Development (Level 2)** — Ask first, execute perfectly, recover gracefully.

Dev Loop is a structured iteration system that sits between "vibe coding" and full Spec-Driven Development (SDD). It provides PROMPT-driven task execution with verification loops, session recovery, and intelligent agent orchestration.

---

## Quick Start

### Option 1: Guided PROMPT Creation (Recommended)

```bash
/dev "I want to build a date parser utility"
```

The **Prompt Crafter** will:
1. Explore your codebase for context
2. Ask targeted questions about scope and quality
3. Generate a complete `PROMPT.md` file
4. Hand off for execution

### Option 2: Execute Existing PROMPT

```bash
/dev tasks/PROMPT_DATE_PARSER.md
```

### Option 3: Manual PROMPT Creation

```bash
# Copy template
cp templates/PROMPT_TEMPLATE.md tasks/PROMPT_MY_TASK.md

# Edit the file, then execute
/dev tasks/PROMPT_MY_TASK.md
```

---

## Features

| Feature | Description |
|---------|-------------|
| **Question-First** | Prompt Crafter asks before building |
| **Priority Execution** | 🔴 RISKY → 🟡 CORE → 🟢 POLISH |
| **Verification Loops** | Objective, exit-code based checks |
| **Memory Bridge** | PROGRESS files prevent token burn |
| **Session Recovery** | `--resume` continues interrupted work |
| **Dry Run** | `--dry-run` validates before execution |
| **Agent Integration** | `@agent-name` delegates to specialists |
| **Audit Trail** | LOG files capture execution history |

---

## Folder Structure

```text
agentspec/dev/
├── README.md                        # This file
├── _index.md                        # Full documentation
│
├── tasks/                           # Your PROMPT files (active work)
│   └── PROMPT_*.md
│
├── progress/                        # Memory bridge (auto-managed)
│   └── PROGRESS_*.md
│
├── logs/                            # Execution logs (auto-generated)
│   └── LOG_*.md
│
└── templates/                       # Templates and examples
    ├── PROMPT_TEMPLATE.md           # Blank template
    ├── PROGRESS_TEMPLATE.md         # Progress file template
    ├── PROMPT_EXAMPLE_FEATURE.md    # Example: Python utility
    └── PROMPT_EXAMPLE_KB.md         # Example: KB domain
```

---

## Command Reference

### Basic Usage

```bash
# Craft a new PROMPT (interactive)
/dev "description of what you want"

# Execute a PROMPT
/dev tasks/PROMPT_NAME.md

# List available PROMPTs
/dev --list
```

### Command Options

| Option | Description |
|--------|-------------|
| `--mode hitl` | Human-in-the-loop (default) — pause for review |
| `--mode afk` | Autonomous — run without pauses |
| `--resume` | Resume from existing PROGRESS file |
| `--dry-run` | Validate and show plan without executing |
| `--max N` | Override max iterations (default: 30) |

---

## PROMPT Structure

Every PROMPT file follows this structure:

```markdown
# PROMPT: NAME

## Goal
Single sentence describing "done" state

## Quality Tier
prototype | production | library

## Tasks (Prioritized)
### 🔴 RISKY (Do First)
- [ ] Architectural decisions, unknowns

### 🟡 CORE
- [ ] Main implementation
- [ ] @agent-name: Task for specialist

### 🟢 POLISH (Do Last)
- [ ] Cleanup, optimization

## Exit Criteria
- [ ] Objective verification: `command`

## Config
mode: hitl
max_iterations: 30
```

---

## Task Priority System

| Priority | Symbol | Execute Order | Use For |
|----------|--------|---------------|---------|
| RISKY | 🔴 | First | Fail fast on hard problems |
| CORE | 🟡 | Second | Main implementation |
| POLISH | 🟢 | Last | Cleanup and optimization |

---

## Quality Tiers

| Tier | Expectations |
|------|--------------|
| `prototype` | Speed over perfection. Minimal verification. |
| `production` | Tests required. Best practices. Full verification. |
| `library` | Backward compatibility. Full documentation. |

---

## Session Recovery

Dev Loop automatically saves progress to enable session recovery:

```bash
# Session interrupted? Resume from where you left off:
/dev tasks/PROMPT_MY_TASK.md --resume
```

### How It Works

1. **During execution**: Progress saved to `progress/PROGRESS_{NAME}.md`
2. **On resume**: Executor loads progress, skips completed tasks
3. **On completion**: Final log written to `logs/LOG_{NAME}_{TS}.md`

---

## Agent Integration

Reference specialized agents with `@agent-name` in tasks:

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
| `@test-generator` | Creating tests |
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

---

## Exit Conditions

| Exit | Code | Description |
|------|------|-------------|
| ✅ EXIT_COMPLETE | 0 | All tasks done, criteria met |
| ⚠️ MAX_ITERATIONS | 1 | Reached iteration limit |
| 🛑 CIRCUIT_BREAKER | 2 | No progress detected |
| 🚫 USER_INTERRUPT | 3 | User stopped execution |
| ❌ VALIDATION_ERROR | 4 | PROMPT file invalid |

---

## Examples

### Example 1: Build a Feature

```bash
/dev "Create a date parser that handles multiple formats"
```

### Example 2: Build a Knowledge Base

```bash
/dev "Create a Redis KB with concepts and patterns"
```

### Example 3: Validate Before Running

```bash
/dev tasks/PROMPT_AUTH.md --dry-run
```

### Example 4: Resume After Interruption

```bash
/dev tasks/PROMPT_CACHE.md --resume
```

---

## The 3-Level Development Spectrum

```text
┌─────────────────────────────────────────────────────────────────┐
│                    DEVELOPMENT SPECTRUM                          │
├─────────────────────────────────────────────────────────────────┤
│  LEVEL 1           LEVEL 2              LEVEL 3                 │
│  Vibe Coding       Dev Loop             Spec-Driven Dev         │
│  ───────────       ────────             ───────────────         │
│  • Just prompts    • PROMPT.md driven   • 8-phase pipeline      │
│  • No structure    • Verification loops • Full traceability     │
│  • Hope it works   • Agent leverage     • Quality gates         │
│                    • Memory bridge      • Enterprise audit      │
│                                                                  │
│  Time: < 30 min    Time: 1-4 hours      Time: Multi-day         │
│  Command: (none)   Command: /dev        Command: /build-feature │
└─────────────────────────────────────────────────────────────────┘
```

---

## Related Resources

| Resource | Path |
|----------|------|
| Full Documentation | `_index.md` |
| PROMPT Template | `templates/PROMPT_TEMPLATE.md` |
| Feature Example | `templates/PROMPT_EXAMPLE_FEATURE.md` |
| KB Example | `templates/PROMPT_EXAMPLE_KB.md` |
| Prompt Crafter Agent | `agentspec/agents/dev/prompt-crafter.md` |
| Dev Loop Executor | `agentspec/agents/dev/dev-loop-executor.md` |

---

## References

- [11 Tips For AI Coding With Ralph Wiggum](https://www.aihero.dev/tips-for-ai-coding-with-ralph-wiggum) — Matt Pocock
- [Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) — Anthropic

---

*Dev Loop v1.1 — Ask first, execute perfectly, recover gracefully*
