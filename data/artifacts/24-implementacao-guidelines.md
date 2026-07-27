# Implementação: Guidelines Hierárquicas (Global → Projeto)

## Commit
```
2335cd7 feat: sistema de guidelines hierárquicas (global → projeto)
Branch: feat/guidelines-hierarquia
Repo: https://github.com/frotaluiza/ariadne
```

## O que foi feito

### 1. Model — `backend/app/models/guideline.py`

Tabela `guidelines` com:

| Campo | Tipo | Descrição |
|-------|------|-----------|
| id | String (PK) | UUID |
| escopo | String | "global" \| "project" |
| projeto_id | String (FK → projetos.id) | Null se global |
| categoria | String | versionamento, sync, artefatos, handoff, sessoes, noteblock, notificacoes |
| chave | String | auto_commit, timer_aprovar, modo_sync... |
| regra | Text | "auto" \| "manual" \| "ask" \| texto livre |
| ativa | Boolean | True por default |
| created_at / updated_at | DateTime | Timestamps |

20 guidelines default definidas (`GUIDELINE_DEFAULTS` + `DEFAULT_GUIDELINES`):
- **versionamento**: criar_branch(ask), commit_universo(auto), push_universo(auto), merge_plan_main(ask), deletar_branch_merge(auto), commit_criacoes(ask)
- **handoff**: gerar_handoff(auto)
- **artefatos**: auto_aprovar_alta(auto), auto_aprovar_media(ask), auto_aprovar_baixa(manual), timer_aprovar(30)
- **sync**: modo_sync(desligado), auto_sync(manual)
- **noteblock**: auto_noteblock(ask)
- **sessoes**: salvar_conversa(auto), exportar_json_sessao(auto), descricao_automatica(auto)
- **notificacoes**: notificar_git(sim), notificar_artefatos(sim), notificar_features(sim)

### 2. Router — `backend/app/routers/guidelines.py`

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | /api/guidelines | Lista todas (filtro por escopo/projeto_id) |
| GET | /api/guidelines/globais | Só globais |
| GET | /api/guidelines/projeto/{id} | Só de um projeto |
| GET | /api/guidelines/resolved/{proj_id}/{chave} | **Resolve hierarquia** |
| GET | /api/guidelines/{id} | Guideline específica |
| POST | /api/guidelines | Criar |
| PUT | /api/guidelines/{id} | Atualizar |
| DELETE | /api/guidelines/{id} | Deletar |
| POST | /api/guidelines/seed | Inserir 20 defaults (se não existirem) |

**Função auxiliar exportada:** `get_guideline_value(db, projeto_id, chave)` → resolve e retorna o valor final.

### 3. Integração no Orquestrador — `engine.py`

```python
def process(self, request, db=None):
    # ...
    if db:
        modo_sync = self.get_guideline(db, ctx.projeto_id, "modo_sync")
        auto_aprovar = self.get_guideline(db, ctx.projeto_id, "auto_aprovar_alta")
        logger.info("Guidelines [projeto=%s]: modo_sync=%s auto_aprovar_alta=%s",
                    ctx.projeto_id, modo_sync, auto_aprovar)
```

### 4. Arquivos alterados

| Arquivo | Ação |
|---------|------|
| `backend/app/models/guideline.py` | +criado |
| `backend/app/routers/guidelines.py` | +criado |
| `backend/app/models/__init__.py` | +import Guideline |
| `backend/app/main.py` | +include_router guidelines |
| `backend/app/services/orquestrador/engine.py` | +get_guideline + log |

## Pendente

- Agente de Guidelines invocável via chat (interpretar intenção → criar/atualizar guideline)
- UI de guidelines no frontend
- Testes unitários
