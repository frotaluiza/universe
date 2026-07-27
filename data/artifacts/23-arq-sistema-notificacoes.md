# Arquitetura: Sistema de Notificações

## Visão Geral

Sistema de notificações internas da plataforma Ariadne. Todo evento do fluxo (git, artefatos, features, handoffs) gera uma notificação visível no dashboard e opcionalmente no chat.

## Eventos que Geram Notificação

### Git / Versionamento
| Evento | Severidade | Mensagem |
|--------|-----------|----------|
| Branch criada | info | "🌿 Branch plan/xyz criada no universo" |
| Branch pusheada | info | "⬆ Branch plan/xyz enviada para o GitHub" |
| Branch mergeada (plan→master) | sucesso | "✅ Branch plan/xyz mergeada na master" |
| Branch deletada | info | "🗑 Branch plan/xyz removida" |
| Commit no universo | info | "📝 Commit a1b2c3d: descrição" |
| Push no universo | sucesso | "⬆ Universo sincronizado com GitHub" |
| Commit em criação | info | "📝 Criação 'engine-ai' comitada" |
| Push em criação | sucesso | "⬆ Criação 'engine-ai' pusheada" |

### Artefatos
| Evento | Severidade | Mensagem |
|--------|-----------|----------|
| Artefato detectado | info | "📄 Artefato detectado: 'Decisão: usar FastAPI'" |
| Artefato aprovado | sucesso | "✅ Artefato 'X' aprovado e roteado" |
| Artefato rejeitado | info | "❌ Artefato 'X' rejeitado" |
| Artefato pusheado | sucesso | "⬆ Artefato 'X' commitado no universo" |
| Timer auto-aprovou | info | "⏱ Artefato 'X' auto-aprovado (30s)" |

### Features
| Evento | Severidade | Mensagem |
|--------|-----------|----------|
| Feature sugerida | info | "💡 Nova feature: 'NoteBlock drag & drop'" |
| Feature implementada | info | "🔧 Feature 'X' implementada (commit a1b2c3d)" |
| Feature aguardando teste | **alerta** | "🔴 Feature 'X' aguardando seu teste!" |
| Feature testada OK | sucesso | "🟢 Feature 'X' testada e aprovada!" |
| Feature testada FAIL | **erro** | "🔴 Feature 'X' falhou no teste: motivo" |

### Sessões
| Evento | Severidade | Mensagem |
|--------|-----------|----------|
| Sessão iniciada | info | "🚀 Sessão 'Planejamento' iniciada" |
| Handoff gerado | info | "📋 Handoff da sessão gerado" |
| Sessão finalizada | sucesso | "✅ Sessão 'Planejamento' finalizada" |
| Export de sessão | info | "💾 Sessão exportada para o universo" |

## Modelo

```python
class Notificacao(Base):
    __tablename__ = "notificacoes"

    id = Column(String, primary_key=True)
    projeto_id = Column(String, ForeignKey("projetos.id"), index=True)
    sessao_id = Column(String, nullable=True)
    
    tipo = Column(String, nullable=False, index=True)
    # "git_branch" | "git_commit" | "git_push" | "git_merge" | "git_delete"
    # "artefato_detectado" | "artefato_aprovado" | "artefato_rejeitado" | "artefato_pusheado"
    # "feature_nova" | "feature_implementada" | "feature_aguardando_teste" | "feature_testada"
    # "sessao_iniciada" | "sessao_finalizada" | "handoff_gerado"
    
    severidade = Column(String, default="info")
    # "info" | "sucesso" | "alerta" | "erro"
    
    titulo = Column(String, nullable=False)
    mensagem = Column(Text, default="")
    acao_url = Column(String, nullable=True)  # link pra ação (ex: abrir diff)
    acao_texto = Column(String, nullable=True)  # "Ver diff" | "Testar" | "Abrir"
    
    lida = Column(Boolean, default=False)
    created_at = Column(DateTime)
    read_at = Column(DateTime, nullable=True)
```

## UI

### Badge no Header
```
[📊 Dashboard] [📁 Criações] [📋 Features] [🔔 3]  ← notificações não lidas
```

### Painel de Notificações
Toggle que abre um slide-over:

```
┌──────────────────────────────┐
│  🔔 Notificações       [Limpar]│
├──────────────────────────────┤
│  🔴 Feature 'X' aguardando   │
│     seu teste!     [Testar]  │
│  ── 2 min atrás ──           │
│                              │
│  ✅ Artefato aprovado e      │
│     pusheado no universo     │
│  ── 10 min atrás ──          │
│                              │
│  🌿 Branch plan/xyz criada   │
│     no universo              │
│  ── 1h atrás ──              │
│                              │
│  📝 Commit a1b2c3d no        │
│     universo                 │
│  ── 1h atrás ──              │
└──────────────────────────────┘
```

### Toast / Notificação Emergente
Notificações de severidade `alerta` ou `erro` aparecem como toast no canto:

```
┌──────────────────────┐
│ 🔴 Feature aguardando│
│ teste!               │
│ [Ir para Features]   │
└──────────────────────┘
```

## API

```python
GET    /api/notificacoes                    # lista não lidas (com paginação)
GET    /api/notificacoes?lidas=true         # lista todas
GET    /api/notificacoes/nao-lidas/count    # count pro badge
PUT    /api/notificacoes/{id}/ler           # marcar como lida
PUT    /api/notificacoes/ler-todas          # marcar todas como lidas
POST   /api/notificacoes                    # criar notificação (interno)
```

## Como os Agentes Disparam Notificações

```python
# Exemplo: agente de git após push
def notificar_push_universo(repo: str, commit: str):
    criar_notificacao(
        tipo="git_push",
        severidade="sucesso",
        titulo="Universo sincronizado",
        mensagem=f"Commit {commit[:8]} enviado para o GitHub",
        projeto_id=proj.id
    )

# Exemplo: agente de features quando muda status
def notificar_feature_aguardando_teste(feature: Feature):
    criar_notificacao(
        tipo="feature_aguardando_teste",
        severidade="alerta",
        titulo=f"Feature '{feature.titulo}' aguardando teste",
        mensagem=f"Commit {feature.commit_hash[:8]}",
        acao_url=f"/features/{feature.id}",
        acao_texto="Testar agora",
        projeto_id=feature.projeto_id
    )
```

## Regras de Notificação (configuráveis)

O usuário pode configurar quais eventos geram notificação via toggles:

| Evento | Notificar? | Severidade |
|--------|-----------|------------|
| Branch criada | toggle | info |
| Push no universo | toggle | sucesso |
| Feature aguardando teste | toggle (default: sim) | alerta |
| Artefato detectado | toggle | info |
| Artefato auto-aprovado | toggle | info |
| Handoff gerado | toggle | info |

## Implementação

1. Model `Notificacao` + migration
2. Router `/api/notificacoes`
3. UI: badge no header + slide-over de notificações
4. Toast para severidade alerta/erro
5. Função helper `criar_notificacao()` usada por todos os agentes
6. Integração com cada agente (git, artefatos, features, handoff, sessão)
