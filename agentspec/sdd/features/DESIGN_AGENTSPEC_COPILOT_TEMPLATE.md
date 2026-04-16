# DESIGN: AgentSpec GitHub Copilot Template

> Especificação técnica para genericizar o repo em template AgentSpec + Copilot limpo, sem referências a projeto específico.

## Metadata

| Attribute | Value |
|-----------|-------|
| **Feature** | AGENTSPEC_COPILOT_TEMPLATE |
| **Date** | 2026-04-16 |
| **Author** | design-agent |
| **DEFINE** | [DEFINE_AGENTSPEC_COPILOT_TEMPLATE.md](./DEFINE_AGENTSPEC_COPILOT_TEMPLATE.md) |
| **Status** | Ready for Build |

---

## Architecture Overview

```text
ESTADO ATUAL                          ESTADO ALVO
─────────────                         ──────────

.github/                              .github/
├── copilot-instructions.md           ├── copilot-instructions.md
│   └── [projeto-específico]          │   └── [template com {placeholders}]
└── prompts/                          └── prompts/
    ├── workflow/ (5 arquivos)             ├── workflow/ (5 arquivos)
    │   └── refs a UberEats/invoice   │   └── [genérico — sem projeto específico]
    ├── agents/ (10 arquivos)              ├── agents/
    │   └── refs a UberEats/Cloud Run │   │   ├── code-reviewer.prompt.md     [universal]
    └── kb/ (3 arquivos)              │   │   ├── python-developer.prompt.md  [universal]
        ├── gcp-reference             │   │   ├── test-generator.prompt.md    [universal]
        ├── gemini-reference          │   │   ├── llm-specialist.prompt.md    [universal]
        └── pydantic-reference        │   │   ├── github-copilot-specialist.prompt.md [universal]
                                      │   │   └── examples/                   [especializados]
                                      │   │       ├── extraction-specialist.prompt.md
                                      │   │       ├── pipeline-architect.prompt.md
                                      │   │       ├── function-developer.prompt.md
                                      │   │       ├── dataops-builder.prompt.md
                                      │   │       └── infra-deployer.prompt.md
                                      │   ├── kb/
                                      │   │   ├── python-reference.prompt.md  [novo]
                                      │   │   ├── testing-reference.prompt.md [novo]
                                      │   │   ├── rest-api-reference.prompt.md [novo]
                                      │   │   ├── data-engineering-reference.prompt.md [novo]
                                      │   │   └── examples/
                                      │   │       ├── spark-reference.prompt.md    [novo]
                                      │   │       ├── mongodb-reference.prompt.md  [novo]
                                      │   │       └── databricks-reference.prompt.md [novo]
                                      │   └── README.md [atualizado]
```

---

## Components

| Component | Ação | Descrição |
|-----------|------|-----------|
| `copilot-instructions.md` | Reescrever | Template com `{placeholders}` para stack, padrões, vocabulário do projeto |
| `workflow/*.prompt.md` (5) | Editar | Remover seção "Project Context" com UberEats/invoice; manter processo SDD genérico |
| Agentes universais (5) | Editar | Remover referências a UberEats, Cloud Run, Pub/Sub, BigQuery dos prompts |
| Agentes especializados (5) | Mover | `.github/prompts/agents/` → `.github/prompts/agents/examples/` |
| KBs antigas (3) | Deletar | `gcp-reference`, `gemini-reference`, `pydantic-reference` |
| KBs universais (4) | Criar | `python`, `testing`, `rest-api`, `data-engineering` |
| KBs de exemplo (3) | Criar | `spark`, `mongodb`, `databricks` em `examples/` |
| `README.md` | Editar | Refletir nova estrutura de agentes e KBs |
| Git state | Commitar | Deletados de `.claude/` + workflows modificados |

---

## Key Decisions

### Decision 1: Agentes universais na raiz, especializados em `examples/`

| Attribute | Value |
|-----------|-------|
| **Status** | Accepted |
| **Date** | 2026-04-16 |

**Context:** O repo tem 10 agentes em `.github/prompts/agents/`. Cinco são genéricos (code-reviewer, python-developer, test-generator, llm-specialist, github-copilot-specialist) e cinco são específicos do pipeline de invoices (extraction-specialist, pipeline-architect, function-developer, dataops-builder, infra-deployer).

**Choice:** Manter os 5 universais na raiz. Mover os 5 especializados para `.github/prompts/agents/examples/`.

**Rationale:** Quem clona o template encontra imediatamente os agentes prontos para uso. A pasta `examples/` serve de referência de como criar agentes especializados para o próprio stack — sem poluir o caminho principal.

**Alternatives Rejected:**
1. Deletar os especializados — Rejeita valor de referência; o time perde exemplos concretos de como especializar
2. Manter todos na raiz — Confunde o que é base vs. extensão; onboarding mais difícil

**Consequences:**
- O time precisa saber procurar em `examples/` para ver agentes especializados
- Ganho: onboarding imediato com 5 agentes prontos para uso

---

### Decision 2: KBs universais com `mode: 'ask'`, KBs de exemplo com estrutura idêntica

| Attribute | Value |
|-----------|-------|
| **Status** | Accepted |
| **Date** | 2026-04-16 |

**Context:** As KBs existentes usam `mode: 'ask'` (read-only Q&A). As novas KBs precisam ser úteis de forma standalone, sem depender de contexto de projeto.

**Choice:** Todas as KBs (universais e de exemplo) usam `mode: 'ask'`. Conteúdo: quick reference tables + code snippets prontos para usar + common pitfalls. Estrutura idêntica em universais e `examples/` — o time entende o padrão vendo qualquer uma.

**Rationale:** `mode: 'ask'` é correto para KBs — leitura, não edição. Estrutura uniforme facilita criar KBs novas: copiar um arquivo, trocar o conteúdo.

**Alternatives Rejected:**
1. `mode: 'agent'` para KBs — incorreto; KBs são referência, não agentes que executam tarefas
2. Estrutura diferente para `examples/` — desnecessário; igualdade estrutural é a lição mais valiosa

**Consequences:**
- KBs de exemplo ensinam o padrão apenas pelo exemplo — sem documentação extra necessária

---

### Decision 3: `copilot-instructions.md` com placeholders explícitos e comentários de instrução

| Attribute | Value |
|-----------|-------|
| **Status** | Accepted |
| **Date** | 2026-04-16 |

**Context:** O `copilot-instructions.md` é carregado automaticamente em toda sessão Copilot. É o arquivo mais importante do template. Precisa ser utilizável imediatamente e guiar o time sobre o que customizar.

**Choice:** Usar o padrão `{YOUR_PROJECT_NAME}`, `{YOUR_TECH_STACK}`, etc. com comentários HTML `<!-- instrução de como preencher -->` em cada seção.

**Rationale:** Placeholders em maiúsculas são visualmente óbvios. Comentários HTML ficam invisíveis no Copilot mas aparecem no editor — guia in-line sem poluir o contexto do modelo.

**Alternatives Rejected:**
1. Arquivo completamente em branco com seções vazias — Não guia o time sobre o que escrever
2. Arquivo com exemplos genéricos sem marcação clara — O time pode não perceber que precisa editar

**Consequences:**
- Quem clona precisa de uma busca por `{YOUR_` para encontrar todos os pontos de customização

---

### Decision 4: Deletar KBs antigas, não mover para `examples/`

| Attribute | Value |
|-----------|-------|
| **Status** | Accepted |
| **Date** | 2026-04-16 |

**Context:** As KBs `gcp-reference`, `gemini-reference` e `pydantic-reference` existentes são muito específicas do projeto original (invoice pipeline, GCP, Gemini).

**Choice:** Deletar os 3 arquivos. As KBs de exemplo em `examples/` (spark, mongodb, databricks) assumem o papel de "referência de como criar KBs especializadas".

**Rationale:** Manter GCP/Gemini/Pydantic como exemplos enviaria mensagem errada — o time pensaria que o template é voltado para esse stack específico. As novas KBs de exemplo (spark, mongodb, databricks) são mais neutras e representam domínios comuns.

**Alternatives Rejected:**
1. Mover para `examples/` — Confunde: o usuário verá GCP e pensará que o template é para GCP

---

## File Manifest

| # | Arquivo | Ação | Propósito | Agent | Deps |
|---|---------|------|-----------|-------|------|
| 1 | `.github/copilot-instructions.md` | Reescrever | Template com `{placeholders}` e comentários de instrução | @github-copilot-specialist | None |
| 2 | `.github/prompts/workflow/brainstorm.prompt.md` | Editar | Remover seção "Project Context" com UberEats | @build-agent | None |
| 3 | `.github/prompts/workflow/define.prompt.md` | Editar | Remover referências a projeto específico | @build-agent | None |
| 4 | `.github/prompts/workflow/design.prompt.md` | Editar | Remover referências a projeto específico | @build-agent | None |
| 5 | `.github/prompts/workflow/build.prompt.md` | Editar | Remover referências a projeto específico | @build-agent | None |
| 6 | `.github/prompts/workflow/ship.prompt.md` | Editar | Remover referências a projeto específico | @build-agent | None |
| 7 | `.github/prompts/agents/code-reviewer.prompt.md` | Editar | Remover "UberEats invoice pipeline", "Cloud Run", "Pub/Sub", "BigQuery"; tornar checklist genérico | @build-agent | None |
| 8 | `.github/prompts/agents/python-developer.prompt.md` | Editar | Remover "UberEats invoice processing pipeline" do contexto; manter padrões Python | @build-agent | None |
| 9 | `.github/prompts/agents/test-generator.prompt.md` | Editar | Remover Cloud Run, Pub/Sub, LangFuse, BigQuery, GCS; tornar genérico para qualquer projeto Python | @build-agent | None |
| 10 | `.github/prompts/agents/llm-specialist.prompt.md` | Editar (mínimo) | Verificar e remover refs específicas se houver | @build-agent | None |
| 11 | `.github/prompts/agents/github-copilot-specialist.prompt.md` | Editar (mínimo) | Remover "UberEats invoice processing pipeline" da primeira linha | @build-agent | None |
| 12 | `.github/prompts/agents/extraction-specialist.prompt.md` | Mover | `agents/` → `agents/examples/` | @build-agent | None |
| 13 | `.github/prompts/agents/pipeline-architect.prompt.md` | Mover | `agents/` → `agents/examples/` | @build-agent | None |
| 14 | `.github/prompts/agents/function-developer.prompt.md` | Mover | `agents/` → `agents/examples/` | @build-agent | None |
| 15 | `.github/prompts/agents/dataops-builder.prompt.md` | Mover | `agents/` → `agents/examples/` | @build-agent | None |
| 16 | `.github/prompts/agents/infra-deployer.prompt.md` | Mover | `agents/` → `agents/examples/` | @build-agent | None |
| 17 | `.github/prompts/kb/gcp-reference.prompt.md` | Deletar | Substituída por KBs universais | @build-agent | None |
| 18 | `.github/prompts/kb/gemini-reference.prompt.md` | Deletar | Substituída por KBs universais | @build-agent | None |
| 19 | `.github/prompts/kb/pydantic-reference.prompt.md` | Deletar | Substituída por KBs universais | @build-agent | None |
| 20 | `.github/prompts/kb/python-reference.prompt.md` | Criar | KB universal Python: type hints, dataclasses, patterns, error handling | @build-agent | None |
| 21 | `.github/prompts/kb/testing-reference.prompt.md` | Criar | KB universal Testing: pytest, fixtures, mocking, parametrize | @build-agent | None |
| 22 | `.github/prompts/kb/rest-api-reference.prompt.md` | Criar | KB universal REST API: design, status codes, FastAPI patterns, auth | @build-agent | None |
| 23 | `.github/prompts/kb/data-engineering-reference.prompt.md` | Criar | KB universal Data Engineering: ETL patterns, batch vs streaming, data quality | @build-agent | None |
| 24 | `.github/prompts/kb/examples/spark-reference.prompt.md` | Criar | KB especializada Spark: PySpark DataFrame API, joins, performance | @build-agent | None |
| 25 | `.github/prompts/kb/examples/mongodb-reference.prompt.md` | Criar | KB especializada MongoDB: document design, indexes, aggregation pipeline | @build-agent | None |
| 26 | `.github/prompts/kb/examples/databricks-reference.prompt.md` | Criar | KB especializada Databricks: Delta Lake, Unity Catalog, notebooks, clusters | @build-agent | None |
| 27 | `.github/prompts/README.md` | Reescrever | Refletir nova estrutura: 5 agentes universais + 5 exemplos, 4 KBs + 3 exemplos | @build-agent | 1-26 |
| 28 | Git state | Commitar | Stage + commit arquivos deletados `.claude/` + workflows modificados | @build-agent | None |

**Total de Arquivos:** 28 (9 criar, 11 editar, 5 mover, 3 deletar, 1 git commit)

---

## Agent Assignment Rationale

| Agent | Arquivos | Motivo |
|-------|----------|--------|
| @build-agent | 2–28 | Trabalho de edição de conteúdo Markdown e git — build-agent lida diretamente |
| @github-copilot-specialist | 1 | Especialista em `copilot-instructions.md` e `.prompt.md` files — arquivo mais crítico |

---

## Content Patterns

### Pattern 1: Template de `copilot-instructions.md`

```markdown
# {YOUR_PROJECT_NAME}

<!-- Substitua {YOUR_PROJECT_NAME} pelo nome real do seu projeto -->
<!-- Este arquivo é carregado automaticamente em toda sessão do Copilot -->
<!-- Mantenha em ~200 linhas — cada linha compete pelo context window -->

## What This Project Does

<!-- 2-3 frases descrevendo o que o projeto faz e qual problema resolve -->
{YOUR_PROJECT_DESCRIPTION}

## Technology Stack

<!-- Liste as principais tecnologias. Seja específico: versões importam. -->
| Layer | Technology | Version |
|-------|------------|---------|
| Language | {e.g., Python} | {e.g., 3.11+} |
| Framework | {e.g., FastAPI} | {e.g., 0.104+} |
| Database | {e.g., PostgreSQL} | {e.g., 15} |
| Infrastructure | {e.g., AWS Lambda} | - |

## Coding Standards

<!-- Liste os padrões não-negociáveis do projeto -->
- {YOUR_CODING_STANDARD_1}: e.g., "Type hints required on ALL function signatures"
- {YOUR_CODING_STANDARD_2}: e.g., "Pydantic v2 for ALL structured data"
- {YOUR_CODING_STANDARD_3}: e.g., "Structured JSON logging — no print()"

## Key File Locations

<!-- Ajuda o Copilot a navegar o projeto -->
| What | Where |
|------|-------|
| {Main source} | `{path/to/src/}` |
| {Tests} | `{path/to/tests/}` |
| {Config} | `{path/to/config/}` |

## Domain Vocabulary

<!-- Termos específicos do domínio que o Copilot deve conhecer -->
| Term | Meaning |
|------|---------|
| {YOUR_TERM_1} | {Definition} |
| {YOUR_TERM_2} | {Definition} |

## AgentSpec Workflow

| Phase | Command | Output |
|-------|---------|--------|
| 0 | `/brainstorm` | `BRAINSTORM_{FEATURE}.md` |
| 1 | `/define` | `DEFINE_{FEATURE}.md` |
| 2 | `/design` | `DESIGN_{FEATURE}.md` |
| 3 | `/build` | Code + `BUILD_REPORT.md` |
| 4 | `/ship` | `SHIPPED_{DATE}.md` in archive |

Artifacts: `agentspec/sdd/features/` (active) → `agentspec/sdd/archive/` (shipped)

## Copilot Integration

Specialist agents: `.github/prompts/agents/` — reference via `#file:`
KB references: `.github/prompts/kb/` — use `#file:` for domain patterns
Full usage guide: `.github/prompts/README.md`
```

---

### Pattern 2: Agente universal genericizado

```markdown
---
mode: 'agent'
description: '{Short description — what this agent does}'
---

# {Agent Name}

You are a **{role}** for software projects. {1-2 sentences on scope without mentioning specific project}.

## {Process or Standards section}

{Content without project-specific references}

## What NOT to Do

- Don't hardcode {project-specific thing} — use {generic alternative}
- Don't reference {project-specific system} — describe the pattern generically
```

**Padrão de limpeza para agentes existentes:**
- Substituir `"for the UberEats invoice processing pipeline"` por `"for software projects"`
- Substituir `"Cloud Run functions, Pydantic models, adapters"` por `"Python modules and services"`
- Substituir `"Terraform/Terragrunt infrastructure code"` por `"Infrastructure as Code"`
- Remover imports/fixtures com `ExtractedInvoice`, `LineItem`, `UberEats`
- Substituir exemplos de test com modelos genéricos (`SampleModel`, `OrderItem`)

---

### Pattern 3: KB universal (modo `ask`)

```markdown
---
mode: 'ask'
description: '{Domain} quick reference — {what it covers}'
---

# {Domain} Reference

> Quick reference for {domain} patterns.
> Full KB: `agentspec/kb/{domain}/` (se existir)

## Core Concepts

| Concept | Description |
|---------|-------------|

## Quick Reference

| Pattern | When to Use |
|---------|-------------|

## Code Examples

### Example 1: {Common Pattern}
\`\`\`python
# {brief description}
{copy-paste ready snippet}
\`\`\`

## Common Pitfalls

| Mistake | Fix |
|---------|-----|
```

---

### Pattern 4: KB de exemplo especializada

Estrutura **idêntica** ao Pattern 3, mas com conteúdo da tecnologia específica. O nome do arquivo (`spark-reference.prompt.md`) e o frontmatter `description` são os únicos diferenciadores visíveis.

```markdown
---
mode: 'ask'
description: 'Apache Spark quick reference — PySpark DataFrame API, joins, and performance patterns'
---

# Apache Spark Reference

> Quick reference for PySpark patterns.
> Add to `.github/prompts/kb/` when your project uses Spark.
```

---

## Data Flow (Workflow de Execução do Build)

```text
1. Git cleanup
   └── Stage + commit arquivos deletados de .claude/ e workflows modificados
   │
   ▼
2. copilot-instructions.md
   └── Reescrever como template com {placeholders}
   │
   ▼
3. Workflow prompts (5 arquivos em paralelo)
   └── Remover seções "Project Context" com UberEats/invoice
   │
   ▼
4. Agentes universais (5 arquivos em paralelo)
   └── Genericizar — remover UberEats, Cloud Run, Pub/Sub, BigQuery
   │
   ▼
5. Agentes especializados (5 arquivos em paralelo)
   └── Mover agents/*.prompt.md → agents/examples/*.prompt.md
   │
   ▼
6. KBs antigas (3 arquivos em paralelo)
   └── Deletar gcp-reference, gemini-reference, pydantic-reference
   │
   ▼
7. KBs universais (4 arquivos em paralelo)
   └── Criar python, testing, rest-api, data-engineering
   │
   ▼
8. KBs de exemplo (3 arquivos em paralelo)
   └── Criar spark, mongodb, databricks em kb/examples/
   │
   ▼
9. README.md
   └── Reescrever para refletir nova estrutura completa
   │
   ▼
10. Verificação final
    └── grep para confirmar zero referências específicas
```

---

## Integration Points

| Sistema | Tipo | Notas |
|---------|------|-------|
| GitHub Copilot | Lê `.github/copilot-instructions.md` automaticamente | Carregado em toda sessão |
| GitHub Copilot | Lê `.github/prompts/**/*.prompt.md` via `#file:` ou `/comando` | On-demand |
| VS Code | `.vscode/settings.json` habilita instruction files | Opcional mas recomendado |
| Git / GitHub | Todos os arquivos versionados | Agentes e KBs evoluem com o projeto |

---

## Testing Strategy

| Tipo | Escopo | Como verificar | Critério de sucesso |
|------|--------|----------------|---------------------|
| Grep check | `.github/prompts/` | `grep -r "UberEats\|invoice\|Gemini\|Cloud Run\|LangFuse" .github/prompts/` | Zero matches |
| Placeholder check | `copilot-instructions.md` | `grep "{YOUR_" .github/copilot-instructions.md` | ≥ 5 matches |
| Structure check | `agents/` e `kb/` | `ls .github/prompts/agents/` e `ls .github/prompts/kb/` | Arquivos corretos em cada pasta |
| KB content check | KBs universais | Contar linhas de cada KB | ≥ 50 linhas cada |
| Git state check | Repo | `git status` | "nothing to commit" |

---

## Security Considerations

- Nenhum segredo, API key, ou credencial deve aparecer em nenhum `.prompt.md` — todos são commitados publicamente
- O `copilot-instructions.md` não deve conter URLs internas ou IPs de infraestrutura
- Remover qualquer referência a project IDs GCP ou nomes de buckets específicos que possam ter ficado nos arquivos de workflow

---

## Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-04-16 | design-agent | Initial version |

---

## Next Step

**Ready for:** `/build agentspec/sdd/features/DESIGN_AGENTSPEC_COPILOT_TEMPLATE.md`
