# Arquitetura: Agente de Features — Ciclo de Vida

## Visão Geral

Funcionário interno do universo dedicado exclusivamente ao ciclo de vida das features. Pode ser invocado pelo usuário no chat ou chamado automaticamente pelo assistente. Gerencia o estado de cada feature desde a sugestão até o teste.

## Invocação

### Pelo usuário
```
"quero sugerir uma feature nova"
"qual o status das features?"
"testei a feature X"
"marca feature Y como falhou"
```

### Pelo assistente (Orquestrador)
Quando detecta que um artefato descreve uma nova funcionalidade, chama o agente automaticamente:
```python
agente_features.criar(
    titulo="NoteBlock com file tree",
    descricao="...",
    projeto_id=proj.id,
    artefato_id=art.id,
    sessao_id=sess.id
)
```

## Modelo (já definido em 20, complemento)

```python
class Feature(Base):
    # ... campos já definidos
    invocacoes = Column(Integer, default=0)  # quantas vezes foi mencionada
    ultima_menção = Column(DateTime)
    tags = Column(Text, default="[]")  # ["ux", "backend", "prioritario"]
```

## Comportamento no Chat

```
Usuário: "quero sugerir uma feature"
Agente: "Claro! Me fala o nome e uma breve descrição."
Usuário: "Noteblock com suporte a arrastar blocos"
Agente: → Cria Feature(titulo="Drag & drop no NoteBlock", status=planejada)
        → "Feature registrada! Qual a prioridade? (alta/média/baixa)"
        → "Quer associar a alguma criação existente?"
```

```
Usuário: "qual o status das features?"
Agente: → Query features do projeto
        → Mostra resumo:
        "📊 Features do Ariadne:
         🔴 2 aguardando teste
         🟢 3 testadas
         ⚪ 5 planejadas
         
         🔴 Aguardando você:
         • NoteBlock file tree (commit a1b2c3d)
         • Sync Engine modo Universe (commit e4f5g6h)"
```

## Dashboard

O agente alimenta a aba Features que já definimos:

```
┌─────────────────────────────────────┐
│  Features                     [Nova]│
├─────────────────────────────────────┤
│  🔴 Aguardando Teste (2)           │
│  📌 NoteBlock com file tree        │
│     Commit: a1b2c3d  [Aprovar] [Diff]
│  📌 Sync Engine modo Universe      │
│     Commit: e4f5g6h  [Aprovar] [Diff]
│                                     │
│  🟢 Testadas (3)                   │
│  📌 Chat inside NoteBlock  ✔ 2d ago│
│                                     │
│  ⚪ Planejadas (5)                 │
│  📌 Transcrição de vídeo           │
│     [Iniciar Implementação]        │
└─────────────────────────────────────┘
```

## Gatilhos Automáticos

| Evento | Ação do Agente |
|--------|---------------|
| Commit menciona `feat: #feature:{id}` | Move para `implementada`, salva commit_hash |
| Agente de criações faz push | Checa features implementadas sem push → `aguardando_teste` |
| Artefato aprovado com tipo "feature" | Cria nova feature se não existir similar |
| Handoff gerado | Inclui seção de features pendentes |
| Usuário marca como testada | Move para `testada_ok` ou `testada_falhou` |

## API

```python
# Endpoints para o agente
POST /api/features/agente/criar       # criado pelo agente
POST /api/features/agente/atualizar   # atualizado pelo agente
GET  /api/features/agente/status      # resumo pra mostrar no chat

# Endpoints pro dashboard
GET    /api/features
GET    /api/features?status=aguardando_teste
PUT    /api/features/{id}/testar      # usuário marca como testada
```

## Integração com Notificações

Quando o agente de features muda o status, dispara notificação:
- Feature nova planejada → notificação info
- Feature implementada → notificação "aguardando teste" (prioridade)
- Feature testada_ok → notificação sucesso
- Feature testada_falhou → notificação alerta
