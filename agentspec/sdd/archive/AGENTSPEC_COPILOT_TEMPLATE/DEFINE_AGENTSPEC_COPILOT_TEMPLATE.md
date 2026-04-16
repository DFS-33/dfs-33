# DEFINE: AgentSpec GitHub Copilot Template

> Genericizar o repo atual em um template AgentSpec + Copilot limpo, sem referências a projeto específico, pronto para ser clonado por qualquer equipe.

## Metadata

| Attribute | Value |
|-----------|-------|
| **Feature** | AGENTSPEC_COPILOT_TEMPLATE |
| **Date** | 2026-04-16 |
| **Author** | define-agent |
| **Status** | ✅ Shipped |
| **Clarity Score** | 14/15 |
| **Source** | `agentspec/sdd/features/BRAINSTORM_AGENTSPEC_COPILOT_TEMPLATE.md` |

---

## Problem Statement

Times de engenharia que querem adotar AgentSpec + GitHub Copilot precisam de um template limpo, sem conteúdo de projeto específico. O repo `btc-zero-prd-claude-code` contém referências hardcodadas a "UberEats", "invoice pipeline", "GCP", "Gemini" e "LangFuse" nos arquivos `.github/prompts/` — tornando-o inutilizável como template genérico sem limpeza manual significativa.

---

## Target Users

| User | Role | Pain Point |
|------|------|------------|
| Time de engenharia | Equipe que quer adotar AgentSpec + Copilot | Precisa clonar um template limpo e começar a trabalhar imediatamente, sem descontaminar conteúdo de outro projeto |
| Tech lead | Responsável por configurar Copilot no projeto | Quer um ponto de partida com boas práticas (agentes, KBs, workflow SDD) já estruturados, mas neutros |

---

## Goals

| Priority | Goal |
|----------|------|
| **MUST** | Remover todas as referências de projeto específico dos arquivos `.github/prompts/` (UberEats, invoice, GCP, Gemini, LangFuse, Rappi, DoorDash) |
| **MUST** | Transformar `copilot-instructions.md` em template com `{placeholders}` claros e instruções de customização |
| **MUST** | Limpar o git state: commitar arquivos deletados de `.claude/` e workflows modificados |
| **MUST** | Criar 4 KBs universais: `python`, `testing`, `rest-api`, `data-engineering` |
| **SHOULD** | Criar 3 KBs de exemplo especializadas: `spark`, `mongodb`, `databricks` em `examples/` |
| **SHOULD** | Separar agentes: universais na raiz + especializados em `examples/` dentro de `.github/prompts/agents/` |
| **COULD** | Adicionar seção "How to Customize" no `README.md` de `.github/prompts/` |

---

## Success Criteria

- [ ] `grep -r "UberEats\|DoorDash\|Rappi\|iFood\|Grubhub\|invoice\|LangFuse\|Gemini\|Cloud Run" .github/prompts/` retorna vazio
- [ ] `copilot-instructions.md` contém ao menos 5 marcadores `{PLACEHOLDER}` com instruções do que substituir
- [ ] Cada um dos 4 agentes universais (`code-reviewer`, `python-developer`, `test-generator`, `llm-specialist`) funciona sem contexto de projeto específico
- [ ] Os 4 arquivos de KB universal existem em `.github/prompts/kb/` com conteúdo mínimo de 50 linhas cada
- [ ] Os 3 arquivos de KB de exemplo existem em `.github/prompts/kb/examples/` demonstrando o padrão de criação
- [ ] `git status` mostra zero arquivos não commitados após o trabalho
- [ ] `agentspec/sdd/features/` não contém artefatos SDD do projeto original (BRAINSTORM_INVOICE_PIPELINE, etc.)

---

## Acceptance Tests

| ID | Scenario | Given | When | Then |
|----|----------|-------|------|------|
| AT-001 | Repo está limpo de referências específicas | Template commitado no branch `feature/copilot` | Executar `grep -r "UberEats" .github/prompts/` | Retorna vazio — zero matches |
| AT-002 | `copilot-instructions.md` é um template utilizável | Repo clonado em novo diretório | Abrir `.github/copilot-instructions.md` | Contém `{YOUR_PROJECT_NAME}`, `{YOUR_TECH_STACK}`, `{YOUR_CODING_STANDARDS}` e instruções de como preencher |
| AT-003 | Agente universal funciona sem projeto específico | Arquivo `python-developer.prompt.md` aberto no Copilot | Solicitar "write a function that parses JSON" | Copilot responde com código Python sem mencionar invoice/UberEats |
| AT-004 | KBs universais têm conteúdo autossuficiente | KB `python-reference.prompt.md` carregada via `#file:` | Perguntar "como faço type hints em Python?" | Resposta baseada no conteúdo da KB, sem depender de contexto de projeto |
| AT-005 | KBs de exemplo demonstram o padrão | Pasta `examples/` existe | Abrir `spark-reference.prompt.md` | Contém estrutura idêntica às KBs universais, mas com conteúdo Spark |
| AT-006 | Git state está limpo | Todas as mudanças concluídas | Executar `git status` | Retorna "nothing to commit, working tree clean" |

---

## Out of Scope

- Projeto fictício de exemplo preenchido (o time que clonar preenche com seu próprio projeto)
- KB de Docker (removida por ser muito específica de infra — pode ser adicionada depois)
- Criação de novo repositório separado (o trabalho é no repo atual)
- Modificar agentes dentro de `agentspec/agents/` (já são genéricos — fora de escopo)
- Modificar workflows de CI/CD além do necessário para remover referências específicas
- MCP integration para Copilot (futuro — já documentado no brainstorm anterior do projeto)

---

## Constraints

| Type | Constraint | Impact |
|------|------------|--------|
| Git | Trabalhar no branch `feature/copilot` existente | Não criar branches novos |
| Repositório | Não criar novo repositório separado | Modificar o repo atual in-place |
| Estrutura | Manter a estrutura `agentspec/` + `.github/prompts/` como está | Só o conteúdo muda, não os caminhos |
| Escopo | Não tocar em `agentspec/agents/` (já genérico) | Trabalho concentrado em `.github/prompts/` e `agentspec/kb/` |

---

## Technical Context

| Aspect | Value | Notes |
|--------|-------|-------|
| **Deployment Location** | `.github/prompts/` + `agentspec/sdd/features/` | Edições de conteúdo, sem deploy de infraestrutura |
| **KB Domains** | Nenhum externo necessário | Criar KBs do zero com base na estrutura de `agentspec/kb/pydantic/` como referência |
| **IaC Impact** | None | Sem recursos de infraestrutura envolvidos |

**Arquivos a modificar:**

| Arquivo / Pasta | Ação | Detalhe |
|-----------------|------|---------|
| `.github/copilot-instructions.md` | Reescrever como template | Substituir conteúdo específico por `{placeholders}` |
| `.github/prompts/workflow/*.prompt.md` | Editar (5 arquivos) | Remover referências a UberEats/invoice |
| `.github/prompts/agents/*.prompt.md` | Editar (universais) + mover (especializados para `examples/`) | Genericizar + reorganizar |
| `.github/prompts/kb/` | Substituir (3 arquivos) + criar (4 novos) | Remover gcp/gemini/pydantic; criar python/testing/rest-api/data-engineering |
| `.github/prompts/kb/examples/` | Criar (3 arquivos) | spark, mongodb, databricks |
| `.github/prompts/README.md` | Editar | Atualizar para refletir nova estrutura |
| `agentspec/sdd/features/` | Verificar e limpar | Remover artefatos SDD do projeto original se existirem |
| Git state | Commitar pendências | Arquivos deletados de `.claude/` + workflows modificados |

---

## Assumptions

| ID | Assumption | If Wrong, Impact | Validated? |
|----|------------|------------------|------------|
| A-001 | Os agentes universais (`code-reviewer`, `python-developer`, `test-generator`, `llm-specialist`) já têm conteúdo suficientemente genérico para reutilização com edições mínimas | Precisaria reescrever do zero — mais esforço | [x] Validado — leitura do `python-developer.prompt.md` confirma que só o contexto de projeto precisa ser removido |
| A-002 | Os 5 arquivos de workflow SDD em `.github/prompts/workflow/` têm o processo correto — só precisam de limpeza de contexto específico | Teria que revisar o workflow inteiro | [x] Validado — `brainstorm.prompt.md` tem o processo correto, só menciona UberEats no "Project Context" |
| A-003 | A estrutura de `agentspec/kb/pydantic/` serve como referência suficiente para criar as novas KBs universais | Precisaria de mais pesquisa para estruturar as KBs | [ ] Não validado — verificar no início do Design |

---

## Clarity Score Breakdown

| Element | Score (0-3) | Notes |
|---------|-------------|-------|
| Problem | 3 | Específico: repo com conteúdo hardcodado, arquivos identificados, branch definido |
| Users | 3 | 2 personas com roles e pain points concretos |
| Goals | 3 | MUST/SHOULD/COULD definidos com ações específicas |
| Success | 2 | Critérios mensuráveis, mas "conteúdo útil standalone" das KBs é subjetivo — mitigado pelo critério de 50 linhas mínimas |
| Scope | 3 | Out-of-scope explícito e confirmado no brainstorm |
| **Total** | **14/15** | Pronto para Design |

---

## Open Questions

Nenhuma — pronto para Design.

---

## Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-04-16 | define-agent | Initial version from BRAINSTORM_AGENTSPEC_COPILOT_TEMPLATE.md |

---

## Next Step

**Ready for:** `/design agentspec/sdd/features/DEFINE_AGENTSPEC_COPILOT_TEMPLATE.md`
