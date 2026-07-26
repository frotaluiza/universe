# Decisão: DB Tracking, MongoDB e Session Handoff

## 1. Ariadne.db — Não Versionar

**Contexto:** O `ariadne.db` (SQLite do universo) estava sendo versionado no git, causando conflitos de merge e impedindo operações.

**Alternativas:**
| Opção | Prós | Contras |
|-------|------|---------|
| Git LFS | Binário versionado sem diff enorme | Configuração extra, merge ainda problemático |
| Tracking normal | Backup automático | Merge conflict permanente, repo incha |
| **Não versionar + Export JSON** | **Repo leve, sem conflitos, dados legíveis** | **Export precisa ser automatizado** |

**Escolha:** `*.db`, `*.db-shm`, `*.db-wal` no `.gitignore`. Banco fica local. Dados em markdown são versionados.

**Futuro:** Script de export automático ao final de cada sessão: `python -c "export db → data/exports/{slug}.json"`.

## 2. MongoDB — Não Agora

**Contexto:** Avaliação se MongoDB seria melhor que SQLite para leitura de agentes.

| Opção | Prós | Contras |
|-------|------|---------|
| **SQLite + JSON export** | **Zero infra, já funciona, agent-friendly** | **Camada extra de sync** |
| MongoDB | Documentos nativos, queries ricas | Servidor externo, mais complexidade |
| MongoDB local | Documentos nativos | Single-user não justifica |

**Escolha:** Ficar no SQLite. Criar camada de export JSON para agentes. Reavaliar se houver necessidade multi-usuário.

## 3. Session Handoff — Obrigatório ao Final de Toda Sessão

**Contexto:** Sessões do opencode são isoladas — nova sessão não sabe o que foi feito.

**Escolha:** Handoff gerado ao final de cada sessão, salvo em `data/handoffs/{slug}.md`. Próxima sessão carrega o mais recente antes de começar.

**Implementação futura:** O @session agent ou script de finalização gera o handoff automaticamente.

## Files

- `.gitignore` — adicionado `*.db`, `*.db-shm`, `*.db-wal`
- `data/handoffs/plan-sessao-planejamento-2026-07-25.md` — primeiro handoff
- `data/artifacts/13-arquitetura-session-handoff.md` — arquitetura do handoff
- `data/artifacts/14-decisao-db-tracking-mongodb-handoff.md` — esta decisão
