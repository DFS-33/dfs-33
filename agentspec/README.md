# AgentSpec Framework

> Structured AI-assisted development workflow for any project.

---

## What Is AgentSpec

AgentSpec is a 5-phase development workflow that combines structured specification (SDD), specialist AI agents, and a knowledge base to help teams build software with traceability and consistency.

```text
/brainstorm → /define → /design → /build → /ship
   Phase 0      Phase 1    Phase 2    Phase 3    Phase 4
```

**What you get:**
- 40 specialist agents organized by category (code quality, data engineering, AI/ML, cloud, workflow)
- 8 knowledge base domains (Pydantic, GCP, Gemini, LangFuse, Terraform, Terragrunt, CrewAI, OpenRouter)
- 13 slash commands for structured workflows
- Dev Loop (Level 2 agentic development with PROMPT.md files)
- GitHub Copilot integration (`.github/prompts/`)

---

## Framework Structure

```text
your-repo/
├── README.md                      # Your project README
├── agentspec/                     # AgentSpec Framework
│   ├── agents/                    # 40 specialist agents
│   │   ├── ai-ml/                 # AI/ML specialists (5)
│   │   ├── aws/                   # AWS/cloud specialists (4)
│   │   ├── code-quality/          # Code review, testing (6)
│   │   ├── communication/         # Documentation, planning (3)
│   │   ├── data-engineering/      # Spark, Lakeflow, Medallion (8)
│   │   ├── dev/                   # Dev Loop agents (2)
│   │   ├── exploration/           # Codebase exploration (2)
│   │   ├── workflow/              # SDD pipeline agents (6)
│   │   └── _template.md.example   # Template for new agents
│   │
│   ├── commands/                  # 13 slash commands
│   │   ├── core/                  # /memory, /sync-context, /readme-maker
│   │   ├── dev/                   # /dev (Dev Loop)
│   │   ├── knowledge/             # /create-kb
│   │   ├── review/                # /review, /create-pr
│   │   └── workflow/              # SDD commands
│   │
│   ├── kb/                        # Knowledge Base (8 domains)
│   │   ├── _templates/            # KB file templates
│   │   ├── pydantic/
│   │   ├── gcp/
│   │   ├── gemini/
│   │   ├── langfuse/
│   │   ├── terraform/
│   │   ├── terragrunt/
│   │   ├── crewai/
│   │   └── openrouter/
│   │
│   ├── sdd/                       # Spec-Driven Development
│   │   ├── features/              # Active BRAINSTORM/DEFINE/DESIGN docs
│   │   ├── reports/               # BUILD reports
│   │   ├── archive/               # Shipped feature artifacts
│   │   ├── examples/              # Reference examples
│   │   └── templates/             # Document templates
│   │
│   └── dev/                       # Dev Loop (Level 2)
│       ├── tasks/                 # PROMPT.md task files
│       ├── progress/              # Session recovery
│       ├── logs/                  # Execution logs
│       ├── examples/
│       └── templates/
│
└── .github/
    ├── copilot-instructions.md    # Copilot global context
    └── prompts/                   # Copilot integration
        ├── README.md              # Usage guide
        ├── workflow/              # 5 SDD phases
        ├── agents/                # 10 specialist agents
        └── kb/                    # 3 KB domains
```

---

## Quick Start

### Option A: GitHub Copilot (VS Code)

1. Open repo in VS Code with GitHub Copilot extension
2. Open Copilot Chat (`Ctrl+Shift+I`)
3. Type `/brainstorm` and describe your idea
4. Follow the 5-phase workflow

Full Copilot guide: `.github/prompts/README.md`

### Option B: AgentSpec Workflow (CLI)

```bash
# Start a new feature
/brainstorm "I want to build a notification system"

# From meeting notes
/define notes/meeting.md

# Continue from brainstorm
/define agentspec/sdd/features/BRAINSTORM_MY_FEATURE.md
```

---

## Development Workflows

### AgentSpec 4.1 (Spec-Driven Development)

5-phase structured workflow for features requiring traceability:

```text
/brainstorm → /define → /design → /build → /ship
  (Opus)      (Opus)    (Opus)   (Sonnet)  (Haiku)
```

| Command | Phase | Purpose |
|---------|-------|---------|
| `/brainstorm` | 0 | Explore ideas through dialogue (optional) |
| `/define` | 1 | Capture and validate requirements |
| `/design` | 2 | Create architecture and specification |
| `/build` | 3 | Execute implementation with verification |
| `/ship` | 4 | Archive with lessons learned |
| `/iterate` | Any | Update documents when changes needed |

**Artifacts:** `agentspec/sdd/features/` and `agentspec/sdd/archive/`

### Dev Loop (Level 2 Agentic Development)

Structured iteration with PROMPT.md files and session recovery:

```bash
# Let the crafter guide you
/dev "I want to build a date parser utility"

# Execute existing PROMPT
/dev tasks/PROMPT_DATE_PARSER.md

# Resume interrupted session
/dev tasks/PROMPT_DATE_PARSER.md --resume
```

**When to use:**
- KB building
- Prototypes
- Single features
- Utilities and parsers

---

## Agent Usage Guidelines

### Available Agents by Category

| Category | Agents | Use When |
| -------- | ------ | -------- |
| **Workflow** | brainstorm-agent, define-agent, design-agent, build-agent, ship-agent, iterate-agent | Building features with SDD |
| **Code Quality** | code-reviewer, code-cleaner, code-documenter, dual-reviewer, python-developer, test-generator | Improving code quality |
| **Data Engineering** | spark-specialist, spark-troubleshooter, spark-performance-analyzer, spark-streaming-architect, lakeflow-architect, lakeflow-expert, lakeflow-pipeline-builder, medallion-architect | Spark/Lakeflow work |
| **AI/ML** | llm-specialist, genai-architect, ai-prompt-specialist, ai-data-engineer, github-copilot-specialist | LLM prompts, AI systems, Copilot |
| **AWS** | aws-deployer, aws-lambda-architect, lambda-builder, ci-cd-specialist | AWS deployments |
| **Communication** | adaptive-explainer, meeting-analyst, the-planner | Explanations, planning |
| **Exploration** | codebase-explorer, kb-architect | Codebase exploration, KB creation |
| **Dev** | prompt-crafter, dev-loop-executor | Dev Loop workflow |

### Agent Reference Syntax

In PROMPT.md files, reference agents with `@agent-name`:

```markdown
### CORE
- [ ] @kb-architect: Create Redis KB domain
- [ ] @python-developer: Implement cache wrapper
- [ ] @test-generator: Add unit tests
```

---

## Coding Standards

### Language: Python 3.11+

- **Style:** Ruff (line-length 100, select E/F/I/UP/B/SIM)
- **Testing:** pytest with -v --tb=short
- **Validation:** Pydantic v2 for all data models
- **Package Management:** pyproject.toml with hatchling
- **Type Hints:** Required on all function signatures

### Code Quality Rules

1. **Pydantic for schemas** — All structured data must use Pydantic v2 models
2. **Type hints required** — All function signatures must be typed
3. **Structured logging** — Use structured JSON logging (no bare `print()`)
4. **Adapter interfaces** — Use adapters for external services (future portability)
5. **Computed fields** — Use `@computed_field` for derived values
6. **Model validators** — Use `@model_validator` for cross-field validation

---

## Commands

| Command | Purpose |
| ------- | ------- |
| `/brainstorm` | Explore ideas through collaborative dialogue |
| `/define` | Capture and validate requirements |
| `/design` | Create technical architecture |
| `/build` | Execute implementation |
| `/ship` | Archive completed features |
| `/iterate` | Update documents mid-stream |
| `/dev` | Dev Loop for structured iteration |
| `/create-kb` | Create knowledge base domains |
| `/review` | Code review workflow |
| `/create-pr` | Create pull requests |
| `/memory` | Save session insights |
| `/sync-context` | Update CLAUDE.md with project context |
| `/readme-maker` | Generate comprehensive README |

---

## Knowledge Base

8 MCP-validated domains with concepts, patterns, and quick references:

| Domain | Purpose | Entry Point |
| ------ | ------- | ----------- |
| **pydantic** | Data validation for LLM output parsing | `agentspec/kb/pydantic/index.md` |
| **gcp** | GCP serverless data engineering | `agentspec/kb/gcp/index.md` |
| **gemini** | Gemini multimodal LLM for document extraction | `agentspec/kb/gemini/index.md` |
| **langfuse** | LLMOps observability platform | `agentspec/kb/langfuse/index.md` |
| **terraform** | Infrastructure as Code for GCP | `agentspec/kb/terraform/index.md` |
| **terragrunt** | Multi-environment orchestration | `agentspec/kb/terragrunt/index.md` |
| **crewai** | Multi-agent AI orchestration | `agentspec/kb/crewai/index.md` |
| **openrouter** | Unified LLM API gateway | `agentspec/kb/openrouter/index.md` |

### KB Structure

```text
agentspec/kb/{domain}/
├── index.md           # Domain overview
├── quick-reference.md # Cheat sheet
├── concepts/          # Core concepts
├── patterns/          # Implementation patterns
└── specs/             # YAML specifications (optional)
```

---

## Adding Your Project Context

After cloning this template, customize AgentSpec for your project:

1. **Update `agentspec/README.md`** — add your project's tech stack and patterns
2. **Add domain agents** — copy `agentspec/agents/_template.md.example`, specialize for your stack
3. **Extend the KB** — run `/create-kb "{domain}"` to add knowledge domains
4. **Update Copilot instructions** — edit `.github/copilot-instructions.md` with your stack
5. **Run `/sync-context`** — auto-updates `agentspec/README.md` from codebase patterns

---

## MCP Tools Available

| MCP Server | Purpose |
| ---------- | ------- |
| **context7-mcp** | Library documentation lookup |
| **exa** | Code context search |
| **firecrawl** | Web scraping and crawling |
| **ref-tools** | Documentation search |

---

## Getting Help

- **SDD Workflow:** See [agentspec/sdd/_index.md](agentspec/sdd/_index.md)
- **SDD Examples:** See [agentspec/sdd/examples/](agentspec/sdd/examples/)
- **Dev Loop:** See [agentspec/dev/_index.md](agentspec/dev/_index.md)
- **Dev Examples:** See [agentspec/dev/examples/](agentspec/dev/examples/)
- **Agents:** Browse [agentspec/agents/](agentspec/agents/)
- **KB Index:** See [agentspec/kb/_index.yaml](agentspec/kb/_index.yaml)
- **Copilot Guide:** See [.github/prompts/README.md](.github/prompts/README.md)

---

## Version History

| Date | Changes |
| ---- | ------- |
| 2026-04-16 | Transformed from project repo to generic public template; renamed .claude/ → agentspec/ |
