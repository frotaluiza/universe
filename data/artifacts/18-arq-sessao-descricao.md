# Arquitetura: Descrição da Sessão + Export opencode.db → ariadne.db

## 1. Descrição Longa na Sessão

A Sessao model já tem `resumo` (curto, ~80 chars) e `palavras_chave`. Falta uma descrição longa do conteúdo.

```python
class Sessao(Base):
    __tablename__ = "sessoes"

    # ... campos existentes
    resumo = Column(Text, default="")       # ~80 chars, gerado automaticamente
    descricao = Column(Text, default="")     # ~500 chars, descrição do conteúdo
    palavras_chave = Column(Text, default="")
```

**Como é gerada:**
- **Automático:** LLM resume a conversa do `conteudo_json` ao final da sessão
- **Manual:** Usuário pode editar no dashboard
- **Toggle:** `descricao_automatica` (auto/manual)

## 2. Export opencode.db → ariadne.db

O opencode tem seu próprio SQLite (`~/.config/opencode/state.v2.db` ou similar). O `ariadne.db` do universo é diferente. Precisamos copiar os dados de um pro outro.

### O que copiar

| Tabela opencode | Tabela ariadne.db | Quando |
|-----------------|-------------------|--------|
| `session` | `sessoes` | Final da sessão |
| `session.message` | `mensagens` | Final da sessão |
| Metadados da sessão | `sessoes.resumo, descricao, palavras_chave` | Final da sessão |

### Fluxo

```
1. Fim da sessão opencode
2. Script consulta SQLite do opencode:
     SELECT id, slug, title, time_updated
     FROM session ORDER BY time_updated DESC LIMIT 1
3. Mapeia para ariadne.db:
     - slug → slug
     - title → titulo
     - time_updated → data
4. Se já existe (mesmo slug): atualiza
5. Se não existe: cria
6. Exporta mensagens da sessão do opencode
7. Salva no conteudo_json da sessoes
8. Gera JSON em data/sessoes/{slug}.json (versionado)
```

### Script de Export

```python
def export_opencode_session(slug: str):
    # Lê do SQLite do opencode
    opencode_db = "~/.config/opencode/state.v2.db"
    # Conecta, consulta session + messages
    # Mapeia para o schema do ariadne.db
    # Salva em sessoes
    # Exporta JSON para data/sessoes/{slug}.json
```

### Trigger

- Automático: ao final de toda sessão (toggle `exportar_json = auto`)
- Manual: botão "Exportar Sessão" no dashboard

### Estrutura do JSON exportado

```json
{
  "slug": "plan-sessao-planejamento-2026-07-25",
  "titulo": "Planejamento Ariadne",
  "data": "2026-07-25T01:00:00Z",
  "resumo": "Planejamento de 5 features...",
  "descricao": "Sessão completa de planejamento...",
  "palavras_chave": "noteblock, sync, handoff, git",
  "mensagens": [
    {"role": "user", "content": "..."},
    {"role": "assistant", "content": "..."}
  ],
  "artefatos_gerados": ["09", "10", "11", "12"],
  "handoff_id": "uuid-do-handoff"
}
```
