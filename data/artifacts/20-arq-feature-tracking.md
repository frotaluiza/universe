# Arquitetura: Feature Tracking — Ciclo de Teste

## Visão Geral

Sistema que acompanha cada feature desde o planejamento até o teste pelo usuário. Integra artefatos, commits e handoffs num ciclo fechado.

## Ciclo de Vida

```
Planejada ──► Implementada ──► Aguardando Teste ──► Testada (OK/FAIL)
    ▲                                                    │
    └────────────────── Revisão ─────────────────────────┘
```

## Modelo

```python
class Feature(Base):
    __tablename__ = "features"

    id = Column(String, primary_key=True)
    projeto_id = Column(String, ForeignKey("projetos.id"), nullable=False)
    sessao_id = Column(String, nullable=True)            # quem planejou
    criacao_id = Column(String, ForeignKey("criacoes.id"), nullable=True)  # qual criação

    titulo = Column(String, nullable=False)
    descricao = Column(Text, default="")
    artefato_id = Column(String, nullable=True)          # artefato que documentou
    commit_hash = Column(String, nullable=True)          # quando implementada
    branch = Column(String, default="")

    status = Column(String, default="planejada")
    # planejada | implementada | aguardando_teste | testada_ok | testada_falhou

    tested_at = Column(DateTime, nullable=True)
    tested_by = Column(String, default="")
    test_notes = Column(Text, default="")

    created_at = Column(DateTime)
    updated_at = Column(DateTime)
```

## Gatilhos Automáticos

| Evento | Ação |
|--------|------|
| Novo artefato com tipo "feature" | Cria Feature(status=planejada) |
| Commit menciona "#feature:{id}" | Feature passa para implementada + commit_hash |
| Agente de criações faz push | Se alguma feature implementada, move para aguardando_teste |
| Usuário marca como testada | Feature vai para testada_ok ou testada_falhou |

## Dashboard

Aba "Features" no projeto:

```
┌─────────────────────────────────────┐
│  Features                     [Nova]│
├─────────────────────────────────────┤
│  🔴 Aguardando Teste (2)           │
│  ┌─────────────────────────────┐   │
│  │ 📌 NoteBlock com file tree  │   │
│  │   Sessão: plan/2026-07-25   │   │
│  │   Commit: a1b2c3d           │   │
│  │   [Aprovar] [Falhou] [Ver Diff] │
│  ├─────────────────────────────┤   │
│  │ 📌 Sync Engine modo Universe│   │
│  │   Sessão: plan/2026-07-25   │   │
│  │   Commit: e4f5g6h           │   │
│  │   [Aprovar] [Falhou] [Ver Diff] │
│  └─────────────────────────────┘   │
│                                     │
│  🟢 Testadas (3)                   │
│  📌 Chat inside NoteBlock  ✔ 2d ago│
│  📌 File tree toggle        ✔ 2d ago│
│  📌 Auto-handoff             ✔ 1d ago│
│                                     │
│  ⚪ Planejadas (5)                 │
│  📌 Transcrição de vídeo           │
│  📌 VLM Pipeline                   │
└─────────────────────────────────────┘
```

## Notificações

- Quando uma feature vai para `aguardando_teste`: notificação no dashboard
- Badge no header: "2 features aguardando teste"
- Pode ser integrado com o chat do Orquestrador

## Integração com Handoff

O handoff inclui a seção:

```markdown
## Features Aguardando Teste
- [ ] NoteBlock com file tree (commit a1b2c3d)
- [ ] Sync Engine modo Universe (commit e4f5g6h)
```

## Chamada no Chat

Usuário: "testei a feature de file tree e funcionou"
Agente: → Atualiza Feature(id=xxx, status=testada_ok, test_notes="funcionou")
→ "Anotado! Feature 'NoteBlock com file tree' marcada como testada ✔"
