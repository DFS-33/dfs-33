# BRAINSTORM: AgentSpec Framework — Public Template Repository

> Transform the existing project repo into a clean, generic, public AgentSpec Framework template that any team can clone and use with GitHub Copilot, without AgentSpec dependency or project-specific content.

## Metadata

| Attribute | Value |
|-----------|-------|
| **Feature** | AGENTSPEC_FRAMEWORK_TEMPLATE |
| **Date** | 2026-04-16 |
| **Author** | brainstorm-agent |
| **Status** | Draft |
| **Source** | notes/02-business-kickoff.md |

---

## Problem Statement

The AgentSpec framework (SDD workflow, specialist agents, KB, Dev Loop) is currently embedded inside a UberEats invoice processing project. Anyone who clones the repo gets 9 project-specific folders (`src/`, `functions/`, `infra/`, etc.), a `agentspec/` directory tied to AgentSpec CLI, and a CLAUDE.md full of UberEats context. This makes the framework unusable as a standalone template — teams have to manually strip project code, understand what's framework vs. what's project, and figure out what to rename.

**Goal:** Transform this repo into a clean, public AgentSpec Framework template with no project-specific residue, no AgentSpec CLI dependency, and a visible `agentspec/` directory that any team can clone and start using immediately with GitHub Copilot.

---

## Discovery Questions and Answers

| # | Question | Answer |
|---|----------|--------|
| Q1 | Transformar este repo ou criar novo? | Transformar este repo (in-place) |
| Q2 | Renomear "AgentSpec" para quê? | AgentSpec |
| Q3 | Template pessoal, público, ou demo? | Público/open-source — totalmente genérico |
| Q4 | `agentspec/` deve existir no repo clonado? | Não — usar pasta visível `agentspec/` |
| Q5 | Onde vive o conteúdo do framework? | `agentspec/` na raiz (renomear `agentspec/`) |

---

## Approach Explored

### Approach A: Limpeza completa in-place ⭐ Selecionado

**Por quê:** É o único approach que entrega um repo 100% público sem resíduos do UberEats. A pasta `agentspec/` visível é auto-documentada, não tem dependência do AgentSpec CLI, e a estrutura `.github/` (já construída) fica como a camada de integração Copilot.

**O que faz:**
1. Deleta todo código do projeto UberEats (9 pastas + pyproject.toml)
2. Renomeia `agentspec/` → `agentspec/`
3. Remove 5 agentes domain-específicos (`agentspec/agents/domain/`)
4. Reescreve `agentspec/CLAUDE.md` (→ `agentspec/README.md`) como guia genérico do framework
5. Substitui "AgentSpec" → "AgentSpec" em todos os arquivos
6. Genericiza `.github/copilot-instructions.md` (remove stack UberEats)
7. Limpa `agentspec/sdd/archive/` (remove 6 features UberEats)
8. Cria `README.md` público na raiz do repo

**Estrutura resultante:**
```text
repo/
├── README.md                        ← apresentação pública do AgentSpec
├── agentspec/                       ← framework source (renomeado de agentspec/)
│   ├── agents/                      ← 35 agentes genéricos
│   │   ├── ai-ml/                   ← 5 agentes (incl. github-copilot-specialist)
│   │   ├── aws/                     ← 4 agentes
│   │   ├── code-quality/            ← 6 agentes
│   │   ├── communication/           ← 3 agentes
│   │   ├── data-engineering/        ← 8 agentes
│   │   ├── dev/                     ← 2 agentes
│   │   ├── exploration/             ← 2 agentes
│   │   ├── workflow/                ← 6 agentes SDD
│   │   └── _template.md.example    ← template para novos agentes
│   ├── commands/                    ← 13 slash commands
│   ├── kb/                          ← 8 KB domains (estrutura genérica)
│   ├── sdd/                         ← SDD workflow (templates + examples)
│   └── dev/                         ← Dev Loop
└── .github/
    ├── copilot-instructions.md      ← contexto genérico para Copilot
    └── prompts/                     ← integração Copilot (já pronto)
        ├── README.md                ← manual de uso Copilot
        ├── workflow/                ← 5 fases SDD
        ├── agents/                  ← 10 agentes especialistas
        └── kb/                      ← 3 domínios KB
```

**Pros:**
- Repo 100% genérico — qualquer time pode clonar e usar
- Sem dependência AgentSpec CLI
- `agentspec/` é auto-documentado e descobrível
- `.github/` já está pronto (AGENTSPEC_COPILOT_PORT, enviado 2026-04-16)
- Separação clara: `agentspec/` = fonte do framework, `.github/` = integração Copilot

**Cons:**
- Operação destrutiva (deletar código projeto) — irreversível sem git history
- Find & replace em massa requer revisão cuidadosa para não quebrar links internos

### Approach B: Manter domain agents como exemplos (não selecionado)

Igual ao A, mas renomeia os 5 agentes de domínio para exemplos genéricos em vez de deletar.

**Por que não:** Mais trabalho; risco de resíduos UberEats em exemplos; o `_template.md.example` já serve como guia para criar agentes de domínio.

### Approach C: Mínimo (não selecionado)

Deletar código + find & replace. Não reescreve CLAUDE.md nem genericiza conteúdo.

**Por que não:** CLAUDE.md ainda falaria de faturas e GCP — confuso para quem clonar publicamente.

---

## YAGNI — O que NÃO faremos

| Feature Removida | Motivo |
|-----------------|--------|
| Criar novos agentes de domínio para substituir os 5 deletados | `_template.md.example` já documenta como criar novos |
| Reescrever todo conteúdo KB em profundidade (concepts/, patterns/) | Padrões técnicos (GCP, Spark, etc.) são genéricos o suficiente; atualizar só índices e quick-reference |
| CI/CD para o template repo | Não há código para testar — só markdown e configuração |
| Portar os 30 agentes restantes para Copilot | Fase 2 — já documentado no SHIPPED AGENTSPEC_COPILOT_PORT |
| Site de documentação | README.md é suficiente para MVP público |
| Remover `.git/` history | Preservar histórico; quem clonar pode fazer `git clone --depth 1` |

---

## Escopo da Transformação

### O que será DELETADO

| Item | Motivo |
|------|--------|
| `src/` | Código UberEats invoice extractor |
| `functions/` | Cloud Run functions específicas do projeto |
| `gen/` | Gerador de invoices sintéticas |
| `tests/` | Smoke tests do pipeline UberEats |
| `infra/` | Terraform/Terragrunt do projeto |
| `design/` | Docs de arquitetura UberEats |
| `notes/` | Atas de reunião do projeto |
| `archive/` | ZIPs de versões antigas |
| `pyproject.toml` | Config Python do projeto |
| `agentspec/agents/domain/` | 5 agentes UberEats-específicos |
| `agentspec/sdd/archive/` | 6 features UberEats arquivadas |

### O que será RENOMEADO

| De | Para |
|----|------|
| `agentspec/` (diretório) | `agentspec/` |
| `agentspec/CLAUDE.md` | `agentspec/README.md` |

### O que será GENERICIZADO (find & replace + reescrita)

| Item | Mudança |
|------|---------|
| Todas as ocorrências de "AgentSpec" | → "AgentSpec" |
| `agentspec/README.md` (ex-CLAUDE.md) | Reescrito como guia genérico do framework |
| `.github/copilot-instructions.md` | Remove stack UberEats; vira template genérico |
| `.github/prompts/agents/` | Remove tipos UberEats (ExtractedInvoice, PipelineMessage) |
| `agentspec/kb/` índices e quick-reference | Remove exemplos UberEats; usa exemplos genéricos |
| `agentspec/sdd/features/` | Limpar (deve estar vazio no template) |
| `agentspec/sdd/reports/` | Limpar (deve estar vazio no template) |

### O que será CRIADO

| Item | Descrição |
|------|-----------|
| `README.md` (raiz) | Apresentação pública do AgentSpec Framework: o que é, estrutura, como começar, como contribuir |

---

## Draft Requirements

1. **R-001** — O repo deve poder ser clonado sem nenhum resíduo de projeto UberEats (zero menções a faturas, GCP pipeline específico, Cloud Run functions, BigQuery schema de invoices)
2. **R-002** — Toda menção a "AgentSpec" (produto Anthropic) deve ser substituída por "AgentSpec"
3. **R-003** — O framework deve residir em `agentspec/` (pasta visível na raiz), não em `agentspec/`
4. **R-004** — A integração Copilot (`.github/`) deve ser genericizada — sem menção a UberEats ou tipos específicos do projeto
5. **R-005** — Um `README.md` público na raiz deve apresentar o framework, explicar a estrutura, e guiar o primeiro uso
6. **R-006** — O framework deve manter todos os 35 agentes genéricos, 13 comandos, 8 KB domains (estrutura), SDD templates, e Dev Loop intactos
7. **R-007** — `agentspec/sdd/features/`, `agentspec/sdd/reports/`, e `agentspec/sdd/archive/` devem estar vazios (prontos para o primeiro projeto do usuário)
8. **R-008** — A operação deve ser realizada no branch `main` existente, preservando o git history

---

## Riscos

| Risco | Impacto | Mitigação |
|-------|---------|-----------|
| Find & replace quebra links internos (ex: `#file:agentspec/`) | Médio | Fazer replace em duas passagens: diretório `agentspec/` → `agentspec/` primeiro, depois "AgentSpec" → "AgentSpec" |
| Deletar `domain/` remove agentes que eram referenciados em arquivos `.github/` | Baixo | Os 10 agentes em `.github/prompts/agents/` são cópias independentes — não dependem de `domain/` |
| Conteúdo residual em KB patterns/ | Baixo | Patterns técnicos (GCP, Spark, etc.) são genéricos; só índices têm contexto UberEats |

---

## Próximo Passo

**Pronto para:** `/define agentspec/sdd/features/BRAINSTORM_AGENTSPEC_FRAMEWORK_TEMPLATE.md`
