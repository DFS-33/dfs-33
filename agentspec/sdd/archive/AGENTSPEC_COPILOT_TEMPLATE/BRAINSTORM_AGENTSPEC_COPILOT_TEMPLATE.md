# BRAINSTORM: AgentSpec GitHub Copilot Template

> Exploratory session to clarify intent and approach before requirements capture

## Metadata

| Attribute | Value |
|-----------|-------|
| **Feature** | AGENTSPEC_COPILOT_TEMPLATE |
| **Date** | 2026-04-16 |
| **Author** | brainstorm-agent |
| **Status** | Ready for Define |

---

## Initial Idea

**Raw Input:** Criar um template AgentSpec voltado para GitHub Copilot, sem nenhum conteúdo relacionado a projeto específico (ex: UberEats, invoice pipeline, GCP).

**Context Gathered:**
- O repo já possui integração Copilot em `.github/prompts/` com 5 workflows SDD, 10 agentes e 3 KBs — mas todos com referências hardcodadas ao pipeline de invoices UberEats
- O `agentspec/` foi recentemente renomeado de `.claude/` (commit `68e2368`) — estrutura já é genérica na maioria
- Branch `feature/copilot` tem 30+ arquivos deletados de `.claude/` não commitados e 6 workflows modificados pendentes
- O `copilot-instructions.md` ainda referencia o stack específico do projeto (GCP, Gemini, Pydantic v2)
- Os `.prompt.md` de agentes como `python-developer` e `brainstorm` hardcodam "UberEats invoice processing pipeline"

**Technical Context Observed (for Define):**

| Aspect | Observation | Implication |
|--------|-------------|-------------|
| Likely Location | `.github/prompts/` + `agentspec/` | Modificar in-place, não criar novo repo |
| Relevant KB Domains | Nenhum específico | Criar KBs universais do zero |
| IaC Patterns | N/A | Sem infraestrutura envolvida |

---

## Discovery Questions & Answers

| # | Question | Answer | Impact |
|---|----------|--------|--------|
| 1 | O que você quer dizer com "template voltado para GitHub Copilot"? | Template 100% genérico, sem referências a projeto específico | Remover todo conteúdo UberEats/GCP/Gemini/invoice |
| 2 | O que o template deve conter ao ser clonado? | Copilot + AgentSpec completo, tudo genérico | Manter a estrutura atual mas genericizar todo conteúdo |
| 3 | Os agentes Copilot devem ser universais ou especializados? | Universais como base + especializados como exemplos de como criar os seus | Separar em pasta raiz (universais) e `examples/` (especializados) |
| 4 | O que fazer com as KB domains? | Substituir por KBs universais | Criar: python, testing, rest-api, data-engineering |
| 5 | Tem material de referência externo? | Não — o próprio repo atual é a referência | Genericizar o que existe, não reescrever do zero |

---

## Sample Data Inventory

| Type | Location | Count | Notes |
|------|----------|-------|-------|
| Arquivos `.prompt.md` existentes | `.github/prompts/` | 19 | Base para genericização |
| Agentes AgentSpec | `agentspec/agents/` | 40 | Já genéricos, manter |
| KB domains existentes | `agentspec/kb/` | 8 | Específicos do projeto original |
| Workflows SDD existentes | `.github/prompts/workflow/` | 5 | Precisam ter referências de projeto removidas |

**Como os samples serão usados:**
- `.prompt.md` existentes como base para os arquivos genéricos (editar, não reescrever)
- KBs de `agentspec/kb/pydantic/` como referência de estrutura para novas KBs universais

---

## Approaches Explored

### Approach A: Genericizar o repo atual ⭐ Recommended

**Description:** Trabalhar no branch `feature/copilot` — remover referências de projeto específico, substituir KBs, limpar `.prompt.md` files e transformar `copilot-instructions.md` em template com placeholders.

**Pros:**
- Aproveita estrutura madura que já funciona
- Git history preservado
- É cirurgia, não reescrita — menor esforço
- Claude Code + Copilot compartilham os mesmos artefatos SDD

**Cons:**
- Requer atenção para não deixar rastros em comentários ou exemplos de código

**Why Recommended:** O repo já tem a estrutura certa. O trabalho é de limpeza, não de construção.

---

### Approach B: Criar novo repo do zero

**Description:** Criar repositório limpo com só o que o template precisa.

**Pros:**
- Zero risco de conteúdo residual

**Cons:**
- Retrabalho total — joga fora estrutura que já funciona
- Sem benefício real frente à Approach A

---

## Selected Approach

| Attribute | Value |
|-----------|-------|
| **Chosen** | Approach A |
| **User Confirmation** | 2026-04-16 |
| **Reasoning** | Genericizar o que existe é mais eficiente e preserva o trabalho já feito |

---

## Key Decisions Made

| # | Decision | Rationale | Alternative Rejected |
|---|----------|-----------|----------------------|
| 1 | Agentes universais na raiz + especializados em `examples/` | Quem clona vê o padrão de uso imediatamente | Todos no mesmo nível (confunde o que é base vs. extensão) |
| 2 | KBs genéricas: python, testing, rest-api, data-engineering | Válidas para qualquer projeto | Docker (muito específico de infra) |
| 3 | KBs de exemplo: spark, mongodb, databricks | Mostram como criar KB especializada para stack de dados | Manter gcp/gemini/langfuse (muito específicas do projeto original) |
| 4 | `copilot-instructions.md` vira template com `{placeholders}` | O time que clonar sabe exatamente o que customizar | Deixar genérico sem marcadores (confuso) |

---

## Features Removed (YAGNI)

| Feature Suggested | Reason Removed | Can Add Later? |
|-------------------|----------------|----------------|
| Projeto fictício de exemplo (opção c) | Mais trabalho; o time vai apagar antes de usar | Yes |
| KB de Docker | Muito específico de infra; substituído por data-engineering | Yes |
| Criar novo repositório separado | Retrabalho sem benefício real | No |

---

## Incremental Validations

| Section | Presented | User Feedback | Adjusted? |
|---------|-----------|---------------|-----------|
| Approach A vs B | ✅ | Confirmou Approach A | No |
| Estrutura final do template | ✅ | Ajustou KBs: removeu Docker, adicionou data-engineering + spark/mongodb/databricks como exemplos | Yes |

---

## Suggested Requirements for /define

### Problem Statement (Draft)
O repo `btc-zero-prd-claude-code` deve ser transformado em um template AgentSpec genérico para GitHub Copilot, sem nenhuma referência ao projeto original (UberEats/invoice/GCP), que qualquer equipe possa clonar e usar imediatamente.

### Target Users (Draft)

| User | Pain Point |
|------|------------|
| Time de engenharia | Quer adotar AgentSpec + Copilot sem ter que limpar conteúdo de outro projeto |
| Tech lead | Quer um template que sirva de ponto de partida para configurar Copilot no seu stack |

### Success Criteria (Draft)

- [ ] Nenhuma referência a "UberEats", "invoice", "Gemini", "GCP", "LangFuse", "Rappi", "DoorDash" nos arquivos de template Copilot
- [ ] `copilot-instructions.md` contém `{placeholders}` claros para customização
- [ ] Agentes universais funcionam sem contexto de projeto específico
- [ ] KBs universais (python, testing, rest-api, data-engineering) têm conteúdo útil standalone
- [ ] KBs de exemplo (spark, mongodb, databricks) demonstram como criar KBs especializadas
- [ ] `agentspec/sdd/features/` não contém artefatos do projeto original
- [ ] Git state limpo: arquivos deletados de `.claude/` commitados, workflows commitados

### Constraints Identified

- Trabalhar no branch `feature/copilot` existente
- Não criar novo repositório
- Manter a estrutura `agentspec/` + `.github/prompts/` como está

### Out of Scope (Confirmed)

- Projeto fictício de exemplo
- KB de Docker
- Criação de novo repositório separado
- Modificar os agentes dentro de `agentspec/agents/` (já são genéricos)

---

## Session Summary

| Metric | Value |
|--------|-------|
| Questions Asked | 5 |
| Approaches Explored | 2 |
| Features Removed (YAGNI) | 3 |
| Validations Completed | 2 |

---

## Next Step

**Ready for:** `/define agentspec/sdd/features/BRAINSTORM_AGENTSPEC_COPILOT_TEMPLATE.md`
