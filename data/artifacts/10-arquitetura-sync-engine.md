# Arquitetura: Sync Engine — GitHub → Notion Configurável

## Visão Geral

Motor de sincronização que leva arquivos markdown de qualquer repositório GitHub para qualquer database Notion. O usuário configura regras de sync: "arquivos `.md` do repo X que correspondem ao padrão Y vão para a database Z".

Dois modos de operação:
1. **Modo Universe** (built-in) — mapping default para o ecossistema Ariadne
2. **Modo Custom** — usuário configura seus próprios pipelines

## Princípios

1. **Configurável** — o usuário decide o que vai pra onde
2. **Não intrusivo** — o sync não interfere no fluxo de trabalho do usuário
3. **Rastreável** — cada sync registra timestamp, arquivos processados, resultado
4. **Idempotente** — executar o mesmo sync duas vezes não cria duplicatas

## Estrutura do Sync Rule

```python
class SyncRule:
    id: str                          # UUID
    nome: str                        # "Universo → Notion"
    modo: Literal["universe", "custom"]

    # Origem (GitHub)
    repo_owner: str                  # "frotaluiza"
    repo_name: str                   # "universe"
    branch: str                      # "main"
    file_pattern: str                # "data/artefatos/*.md" ou "**/*.md"

    # Destino (Notion)
    notion_token_vault: str          # chave no cofre de secrets
    notion_database_id: str          # database ID no Notion
    property_mapping: dict           # { "notion_prop": "md_field" }

    # Comportamento
    auto_sync: bool                  # sync automático no push?
    last_sync_at: datetime | None
    sync_count: int
```

## Fluxo do Sync

```
[Evento] Push no GitHub OU manual "Sync now"
    │
    ▼
1. Parse rule → qual repo + qual database
    │
    ▼
2. Fetch arquivos do repo via GitHub API (ou clone local)
    │
    ▼
3. Parse cada .md → extrai frontmatter + body
    │
    ▼
4. Map para propriedades do Notion conforme property_mapping
    │
    ▼
5. Cria/atualiza página no Notion (idempotente via hash do conteúdo)
    │
    ▼
6. Registra no log de sync (timestamp, arquivos, status)
```

## Diagrama de Componentes

```
┌─────────────────────────────────────────────────────────┐
│                     SYNC ENGINE                          │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────┐ │
│  │  SyncRule     │   │  SyncParser  │   │  NotionWriter│ │
│  │  (models.py)  │   │  (parser.py) │   │  (writer.py) │ │
│  └──────┬───────┘   └──────┬───────┘   └──────┬───────┘ │
│         │                  │                  │          │
│         ▼                  ▼                  ▼          │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────┐ │
│  │  SyncRouter   │   │  GitHubFetch │   │  SyncLogger  │ │
│  │  (CRUD rules) │   │  (fetcher.py)│   │  (logger.py) │ │
│  └──────┬───────┘   └──────┬───────┘   └──────────────┘ │
│         │                  │                             │
└─────────┼──────────────────┼─────────────────────────────┘
          │                  │
          ▼                  ▼
   ┌──────────┐       ┌──────────┐
   │  Notion  │       │  GitHub  │
   │  API     │       │  API     │
   └──────────┘       └──────────┘
```

## Modo Universe (built-in)

Mapping padrão para o ecossistema Ariadne, ativado automaticamente:

| Padrão de arquivo | Database Notion | Mapping |
|---|---|---|
| `data/artefatos/*.md` | Documentacao de Projetos.Arquitetura | título → Nome, body → Arquitetura |
| `data/sessoes/*.md` | Sessões opencode (2026) | título → Sessão, body → rich_text |
| `data/decisoes/*.md` | Projetos (campo Notas/Decisões) | título + contexto |

Usuário pode sobrescrever qualquer regra do modo Universe.

## Modo Custom

Usuário cria regras arbitrárias:

```
Sync Rule: "Meu TCC → Notion TCC"
  Repo: frotaluiza/TCC---Distilação-de-Membranas
  Pattern: "capitulos/*.md"
  Database: <database_id_do_tcc>
  Mapping:
    "# Título" → "Nome do Capítulo"
    "## Resumo" → "Resumo (rich_text)"
    "data: " → "Data"
```

## Triggers

### 1. Webhook (push do GitHub)
```
POST /api/sync/webhook/github
  Recebe push event do GitHub
  → Match com SyncRules (repo + branch)
  → Executa sync para cada rule matching
```

### 2. Manual (UI/API)
```
POST /api/sync/run/{rule_id}
  Executa sync imediatamente
  Retorna progresso (streaming SSE)
```

### 3. Agendado (opcional)
```
Job periódico (cron: a cada 6h)
  → Para cada regra com auto_sync=True
  → Verifica se repo teve novos commits desde last_sync_at
  → Se sim, executa sync
```

## Property Mapping

O coração do sync é o mapeamento entre markdown e Notion:

```python
# Exemplo de property_mapping
{
    "title": {                    # Notion property name
        "from": "frontmatter",    # ou "body_line", "regex"
        "key": "titulo",          # campo no frontmatter
        "type": "title"           # tipo Notion
    },
    "Resumo (curto)": {
        "from": "body_regex",
        "pattern": r"^## Resumo\n(.+)$",
        "type": "rich_text"
    },
    "Data": {
        "from": "frontmatter",
        "key": "data",
        "type": "date"
    }
}
```

## Registro de Sync

```sql
CREATE TABLE sync_log (
    id TEXT PRIMARY KEY,
    rule_id TEXT NOT NULL,
    started_at TIMESTAMP NOT NULL,
    finished_at TIMESTAMP,
    status TEXT,              -- running / success / error
    files_processed INTEGER,
    pages_created INTEGER,
    pages_updated INTEGER,
    error_message TEXT,
    FOREIGN KEY (rule_id) REFERENCES sync_rules(id)
);
```

## Considerações de Segurança

- Notion tokens armazenados em cofre (env ou vault criptografado)
- GitHub tokens via OAuth (já conectado via Composio/GitHub App)
- Sync só acessa repositórios que o usuário deu acesso
- Histórico de sync auditável

## Priorização para Implementação

1. ⭐ Models (`SyncRule`, `SyncLog`) + CRUD router
2. ⭐ Parser de markdown (frontmatter + body sections)
3. ⭐ Modo Universe (mapping default)
4. ⭐ Integração GitHub API + Notion API
5. ⭐ Webhook endpoint (push do GitHub)
6. ⭐ Modo Custom (UI para criar regras)
7. ⭐ Agendador periódico (cron)
8. ⭐ Logs e auditoria no dashboard

## Relação com NoteBlock

O NoteBlock é o editor. O Sync Engine é o publicador:
- Usuário escreve/edita no NoteBlock
- Quando satisfeito, clica "Sync para Notion"
- Sync Engine pega o arquivo, aplica a regra, envia para a database configurada
