# Arquitetura: Test Agent Loop (SkillOpt)

## Referência

SkillOpt — arXiv:2605.23904 (Microsoft Research, 14.9k stars)
Implementação existente em: `AI TUTOR/data/user/workspace/skills/pinn-executor/references/skillopt/`

## O Problema

Agentes hoje têm skills (prompts + ferramentas + checklists) que são:
- Feitas à mão
- Geradas one-shot
- Evoluídas sem controle

Nenhuma abordagem garante que a skill **melhora consistentemente** com feedback.

## A Solução: SkillOpt

SkillOpt trata a skill como **estado externo do agente** (texto puro) e a otimiza como um otimizador de deep learning faria com pesos:

1. Um modelo **optimizer** separado transforma rollouts (execuções com resultado) em edições bounded (add/delete/replace) na skill
2. Uma edição só é aceita se **melhorar estritamente** o validation score
3. **Edit budget** decrescente funciona como learning rate textual
4. **Rejected-edit buffer** evita ciclos
5. **Slow/meta update** por época estabiliza o treinamento
6. **Zero overhead** em inferência — a skill é texto puro

## O Loop de Testes (5 Etapas)

```
┌─────────────────────────────────────────────────────┐
│                   TEST AGENT LOOP                    │
├──────────┬──────────┬──────────┬──────────┬──────────┤
│  ETAPA 1 │  ETAPA 2 │  ETAPA 3 │  ETAPA 4 │  ETAPA 5 │
│ ROLLOUT  │REFLECTION│   EDIT   │VALIDATION│  MERGE   │
├──────────┼──────────┼──────────┼──────────┼──────────┤
│ Executa  │ Analisa  │ Aplica   │ Re-roda  │ Consolida│
│ N tasks  │ falhas & │ edições  │ tasks    │ skills   │
│ c/ skill │ sucessos │ na skill │ alteradas│ bem-     │
│ atual    │ → gera   │ baseado  │ → só     │ sucedidas│
│          │ insights │ nos      │ aceita   │ no       │
│          │          │ insights │ se >     │ sistema  │
│          │          │          │ anterior │          │
└──────────┴──────────┴──────────┴──────────┴──────────┘
```

## Parâmetros (do SkillOptConfig)

```python
class TestLoopConfig:
    epochs: int = 4
    steps_per_epoch: int = 5
    rollout_batch_size: int = 40
    reflection_minibatch: int = 8
    edit_budget: int = 4
    edit_budget_floor: int = 2
    schedule: str = "cosine"  # constant, cosine, linear, autonomous
    validation_mode: str = "strict"  # só aceita se > current
    edit_mode: str = "patch"  # patch ou rewrite
```

## UI no Chat

Durante a execução, o usuário vê notificações não-intrusivas:

```
[Test Loop em andamento: 3/5 — Editando skill...]
                                         [Expandir logs ▸]
```

Ao clicar em "Expandir logs", abre detalhes:

```
────────────────────────────────────
ROLLOUT (4/40 tasks concluídas)
  ✅ task_001: score 0.87
  ✅ task_002: score 0.92
  ❌ task_003: score 0.31
     → Insight: faltou validação de tipos
  ✅ task_004: score 0.78
────────────────────────────────────
REFLECTION: 3 insights gerados
  1. "Adicionar validação de tipos na entrada"
  2. "Incluir exemplo de caso de borda"
────────────────────────────────────
EDIT: 2 edições aplicadas (budget 4 restante: 2)
────────────────────────────────────
VALIDATION: score 0.81 → 0.89 ✅ Aceito
────────────────────────────────────
```

## Integração com Ariadne

- O **harness** são os testes do NoteBlock ou do projeto
- A **skill** é o prompt + ferramentas + checklists do agente
- O **optimizer** é um LLM separado (pode ser o mesmo modelo com temperatura diferente)
- Os **rollouts** executam dentro do ambiente isolado do NoteBlock

## Próximos Passos

1. Abstrair o core SkillOpt do PINN executor para uma biblioteca genérica
2. Criar o harness padrão para a Ariadne (NoteBlock → task → score)
3. Implementar a UI de notificação com expansão de logs
4. Conectar com o sistema de memória (Mem0) para persistir histórico de edições
