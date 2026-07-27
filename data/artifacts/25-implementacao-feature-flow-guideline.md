# Feature #1: Feature Flow Guidelines (Global → Por Projeto)

## Ciclo de Vida Seguido

1. **Branch**: `feat/feature-flow-guideline` (de `master` no Ariadne) + `plan/noteblock-sync-handoff-agentes-2026-07-26` (universo)
2. **Tarefas**: guideline model + router + seed + AGENTS.md + testes
3. **Implementar**: Model `Guideline`, CRUD `/api/guidelines`, resolve hierarquia, integração Orquestrador
4. **Testar**: Servidor rodou na porta 8008, 6 testes executados via `test_guidelines.py` — todos passaram
5. **Aprovação**: Usuário visualizou os resultados, aprovou
6. **Merge + Delete**: feat/feature-flow-guideline → master, branch deletada

## O que foi implementado

### Ariadne Backend

| Arquivo | O que faz |
|---------|-----------|
| `models/guideline.py` | Modelo `Guideline` + 24 defaults |
| `routers/guidelines.py` | CRUD + resolve + seed |
| `services/orquestrador/engine.py` | Orquestrador consulta guidelines |
| `main.py` + `__init__.py` | Integração |

### Guidelines Globais Criadas (24)

| Categoria | Chave | Regra |
|-----------|-------|-------|
| versionamento | criar_branch | ask |
| versionamento | commit_universo | auto |
| versionamento | push_universo | auto |
| versionamento | merge_plan_main | ask |
| versionamento | deletar_branch_merge | auto |
| versionamento | commit_criacoes | ask |
| versionamento | feature_flow | **branch+tarefas+implementar+testar+merge+delete** |
| versionamento | check_ports_before_feature | **sim** |
| versionamento | feature_approval_mode | **manual_implementation** |
| versionamento | feature_approval_tests | **agent_executed** |
| handoff | gerar_handoff | auto |
| artefatos | auto_aprovar_alta | auto |
| artefatos | auto_aprovar_media | ask |
| artefatos | auto_aprovar_baixa | manual |
| artefatos | timer_aprovar | 30 |
| sync | modo_sync | desligado |
| sync | auto_sync | manual |
| noteblock | auto_noteblock | ask |
| sessoes | salvar_conversa | auto |
| sessoes | exportar_json_sessao | auto |
| sessoes | descricao_automatica | auto |
| notificacoes | notificar_git | sim |
| notificacoes | notificar_artefatos | sim |
| notificacoes | notificar_features | sim |

### Hierarquia de Resolução

```
1. Guideline do projeto (se existir e ativa)
2. Guideline global (se existir e ativa)
3. Default do sistema (hardcoded)
```

### AGENTS.md — Novas Regras (13-16)

- Feature flow: branch → tarefas → implementar → testar → merge → delete
- Checar portas antes de feature (session-registry + portas locais)
- Aprovação de implementação é manual (diferente de aprovação automática de artefatos)
- Testes executados pelo agente (agente executa testes e mostra pro usuário)

## Resultado dos Testes

| # | Teste | Resultado |
|---|-------|-----------|
| 1 | GET /api/health | 200 OK |
| 2 | POST /api/guidelines/seed | 201 - 23 guidelines |
| 3 | GET /api/guidelines/globais | 23 encontradas |
| 4 | GET /resolved/../feature_flow | retornou global |
| 5 | POST /api/guidelines (projeto) | 201 - criada |
| 6 | GET /resolved/{projeto}/feature_flow | projeto sobrescreveu global ✅ |

## Próximo Passo

Feature #2: Tabela `features` no Ariadne (model + router + migration)
