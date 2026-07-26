# Arquitetura: Sistema de Configurações com Toggles

## Visão Geral

Painel de configurações onde o usuário decide o que é automático vs manual em cada categoria do sistema. Cada toggle tem um default sugerido mas o usuário pode sobrescrever.

## Estrutura

```python
class UserConfig(Base):
    __tablename__ = "user_configs"

    id = Column(String, primary_key=True)
    projeto_id = Column(String, ForeignKey("projetos.id"), nullable=True)  # null = global
    categoria = Column(String, nullable=False, index=True)
    chave = Column(String, nullable=False)
    valor = Column(String, nullable=False)  # "auto" | "manual" | "ask"
    default = Column(String, nullable=False)  # o default do sistema
    updated_at = Column(DateTime)
```

## Categorias e Toggles

### Versionamento (Git)

| Toggle | Opções | Default | Descrição |
|--------|--------|---------|-----------|
| `criar_branch` | auto / ask | ask | Criar branch nova no início da sessão |
| `commit_universo` | auto / ask | auto | Commitar artefatos no universo |
| `push_universo` | auto / ask | auto | Push do universo após commit |
| `merge_plan_main` | auto / ask | ask | Merge da branch plan → main |
| `commit_criacoes` | auto / ask | ask | Commitar alterações nas criações do projeto |
| `versionar_criacao` | auto / ask | ask | Versionar nova criação (repo separado) |

### Handoff

| Toggle | Opções | Default | Descrição |
|--------|--------|---------|-----------|
| `gerar_handoff` | auto / manual | auto | Gerar handoff ao final da sessão |
| `status_handoff` | auto / ask | auto | Definir status (aberto/resolvido) |

### Artefatos (Scanner + Router)

| Toggle | Opções | Default | Descrição |
|--------|--------|---------|-----------|
| `auto_aprovar_alta` | auto / manual | auto | Artefatos com score > 0.8 são roteados direto |
| `auto_aprovar_media` | auto / manual / ask | ask | Score 0.5-0.8: perguntar |
| `auto_aprovar_baixa` | auto / manual | manual | Score < 0.5: só manual |
| `timer_auto_aprovar` | segundos / desligado | 30s | Se não reagir, auto-aprova |

### Sync (GitHub → Notion)

| Toggle | Opções | Default | Descrição |
|--------|--------|---------|-----------|
| `modo_sync` | mirror / custom / desligado | desligado | Mirror exato ou mapping custom |
| `sync_auto` | auto / manual | manual | Sincronizar automaticamente no push? |

### NoteBlock

| Toggle | Opções | Default | Descrição |
|--------|--------|---------|-----------|
| `auto_noteblock` | auto / ask | ask | Criar NoteBlock automaticamente a partir de ideias |
| `chat_dentro_noteblock` | ativado / desativado | ativado | Chat dentro do NoteBlock vs flutuante |

### Sessões

| Toggle | Opções | Default | Descrição |
|--------|--------|---------|-----------|
| `salvar_conversa` | auto / manual | auto | Salvar conversa no conteudo_json |
| `exportar_json_sessao` | auto / manual | auto | Exportar .json da sessão pro git |
| `descricao_automatica` | auto / manual | auto | Gerar descrição longa automaticamente |

## Resolução de Conflitos

1. Config **global** vale para todos os projetos
2. Config **do projeto** sobrescreve a global na mesma chave
3. Se não existe config, usa o **default do sistema**

## API

```python
GET  /api/configs               # lista todas as configs (global + projeto)
GET  /api/configs/{projeto_id}  # configs de um projeto específico
PUT  /api/configs/{id}          # atualiza um toggle
POST /api/configs               # cria nova config (admin)
GET  /api/configs/resolved/{projeto_id}/{chave}  # retorna o valor resolvido
```

## UI

Painel acessível via ⚙️ no dashboard do projeto:

```
┌─────────────────────────────────────┐
│  Configurações                      │
├─────────────────────────────────────┤
│  Versionamento ───┐                 │
│  [✔] Commit universo    │ auto     │
│  [ ] Merge plan→main   │ ask      │
│  [✔] Push universo     │ auto     │
│  ...                               │
├─────────────────────────────────────┤
│  Artefatos ───────────┐            │
│  Score alto (>0.8):   │ [auto ▾]  │
│  Score médio (0.5-8): │ [ask ▾]   │
│  Timer:               │ [30s]     │
└─────────────────────────────────────┘
```
