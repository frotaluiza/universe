# Arquitetura: Guidelines — Globais vs Projeto

## Visão Geral

Sistema de regras que o Orquestrador (e agentes internos) consultam antes de cada decisão automática. O usuário pode formalizar "leis" que valem para todos os projetos ou para um específico.

## Hierarquia de Resolução

```
1. Carrega todas as GLOBAIS ativas
2. Carrega todas as PROJETO do projeto atual
3. PROJETO sobrescreve GLOBAL na mesma categoria+chave
4. Se nenhuma: usa o default do sistema (hardcoded)
```

## Modelo

```python
class Guideline(Base):
    __tablename__ = "guidelines"

    id = Column(String, primary_key=True)
    escopo = Column(String, nullable=False)       # "global" | "project"
    projeto_id = Column(String, ForeignKey("projetos.id"), nullable=True)  # null se global
    categoria = Column(String, nullable=False)    # "versionamento", "sync", "artefatos", "handoff"...
    chave = Column(String, nullable=False)        # "auto_commit", "auto_push", "timer_aprovar"
    regra = Column(Text, nullable=False)          # texto completo da regra
    ativa = Column(Boolean, default=True)
    criado_em = Column(DateTime)
    atualizado_em = Column(DateTime)
```

## Agente de Guidelines

Invocado quando o usuário diz algo como:
- "Sempre faça commit automático no universo"
- "Nunca faça push sem eu confirmar"
- "Para o projeto TCC, sempre pergunte antes de commitar"

O agente:
1. Interpreta a intenção (LLM)
2. Mapeia para categoria + chave
3. Cria/atualiza a Guideline
4. Confirma com o usuário: "Entendi. A partir de agora, {regra} para {escopo}. Correto?"

## API

```python
GET    /api/guidelines                  # lista todas
GET    /api/guidelines?escopo=global    # filtradas
GET    /api/guidelines/{projeto_id}     # de um projeto
POST   /api/guidelines                  # criar
PUT    /api/guidelines/{id}             # atualizar
DELETE /api/guidelines/{id}             # desativar
GET    /api/guidelines/resolved/{projeto_id}/{chave}  # regra resolvida
```

## Integração com o Orquestrador

```python
class Orquestrador:
    def _get_guideline(self, projeto_id: str, chave: str) -> str:
        # 1. Tenta project
        # 2. Tenta global
        # 3. Retorna default
        ...
```

## Guidelines Recomendadas (Globais)

| Categoria | Chave | Regra |
|-----------|-------|-------|
| versionamento | criar_branch | ask |
| versionamento | commit_universo | auto |
| versionamento | push_universo | auto |
| versionamento | merge_plan_main | ask |
| handoff | gerar_handoff | auto |
| artefatos | auto_aprovar_alta | auto |
| artefatos | timer_segundos | 30 |
| sync | modo_sync | desligado |
| sync | auto_sync | manual |
| noteblock | auto_criar | ask |
| sessoes | descricao_automatica | auto |
| sessoes | exportar_json | auto |
| nomenclatura | nome_criacoes | "criações" |

## UI

```
┌─────────────────────────────────────┐
│  Guidelines                         │
├─────────────────────────────────────┤
│  🌐 Globais                         │
│  [✔] Commit universo automático     │
│  [ ] Merge plan→main: perguntar     │
│  ...                                │
├─────────────────────────────────────┤
│  📁 Projeto: Ariadne                │
│  [✔] Handoff automático             │
│  [✔] Timer auto-aprovar: 30s       │
│  [ ] Sync Notion: mirror            │
│  [+ Nova Regra]                     │
└─────────────────────────────────────┘
```

## No Chat

Usuário: "Sempre pergunte antes de commitar minhas criações"
Agente: → cria Guideline(escopo=global, chave=commit_criacoes, valor="ask")
→ "Entendido! Vou sempre perguntar antes de commitar criações a partir de agora."
