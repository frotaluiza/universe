# Handoff: plan-sessao-planejamento-2026-07-25

## Metadados
- Sessão: Planejamento Ariadne — NoteBlock UI + Sync Engine + GitHub Discovery
- Data: 2026-07-25
- Projeto: Ariadne
- Tipo: planejamento
- Branch de trabalho: `plan/sessao-planejamento-2026-07-25`
- Último commit: `cd5b9c0` (plan), `d2c292f` (master após merge)
- Remote: `origin → https://github.com/frotaluiza/universe.git`

## Estado do Projeto (Universo)
- Repositório: `C:\Users\frota\.ariadne\universe`
- Remote configurado: sim (origin, em sync)
- `.gitignore` exclui `*.db`, `*.db-shm`, `*.db-wal`

## Estado do Projeto (Ariadne)
- Repositório: `C:\Users\frota\Projetos\Ariadne`
- Remote: **NÃO CONFIGURADO** — `frotaluiza/ariadne` existe no GitHub mas vazio
- Commits locais: 5
- Frontend: `backend/app/static/index.html` (vanilla)
- NoteBlocks backend: `backend/deeptutor_extracted/noteblocks/`
- Git API: `backend/deeptutor_extracted/api/routers/pm_dashboard.py`
- KB: `backend/deeptutor_extracted/knowledge/kb_types.py`
- DB: `~/.ariadne/universe/ariadne.db` (SQLite)

## O que foi feito
1. Repositórios GitHub descobertos (`frotaluiza/universe` + `frotaluiza/ariadne`)
2. Artefatos 09-12 gerados (NoteBlock UI, Sync Engine, Decisões, Plano de Implementação)
3. NoteBlock UI arquitetado: file tree sutil, CodeMirror, git inline, chat inside
4. Sync Engine arquitetado: GitHub→Notion configurável (Universe + Custom)
5. Task list completa de implementação (3 fases, 15 itens)
6. `.gitignore` atualizado (db files removidos)
7. Artefatos 13-14 gerados (handoff system, decisões db/mongo)
8. Handoff real gerado (este arquivo)
9. Commit + push + merge para master

## O que NÃO foi feito (pendente)

### Prioridade Máxima
1. **Configurar remote do Ariadne** — `git remote add origin https://github.com/frotaluiza/ariadne.git && git push -u origin master`
2. **Implementar NoteBlock UI Fase 1** — file tree toggle + CodeMirror
3. **Criar endpoint `GET /api/repos/{repo_id}/tree`**

### Alta
4. Git UI inline (status, diff, stage, commit, push)
5. Chat dentro do NoteBlock (remover flutuante)
6. Botão "Promover para Markdown"

### Média
7. Sync Engine — Modo Universe
8. Sync Engine — Modo Custom
9. Handoff automático no @session agent

### Features Planejadas
10. Transcrição de vídeo (yt-dlp + Whisper)
11. Diário automático
12. VLM Pipeline (Qwen2-VL)
13. Memória Mem0
14. Test Loop SkillOpt
15. MCP Tools

## Caminhos e Arquivos Usados
| Caminho | Descrição |
|---------|-----------|
| `.ariadne/universe/` | Repositório do universo |
| `.ariadne/universe/data/artifacts/` | Todos os artefatos (01-14) |
| `.ariadne/universe/data/handoffs/` | Handoffs de sessão |
| `.ariadne/universe/.gitignore` | Exclui *.db |
| `Projetos/Ariadne/backend/app/static/index.html` | Frontend vanilla |
| `Projetos/Ariadne/backend/deeptutor_extracted/noteblocks/` | NoteBlocks API |
| `Projetos/Ariadne/backend/deeptutor_extracted/noteblocks/models.py` | Core Note/Block models |
| `Projetos/Ariadne/backend/deeptutor_extracted/noteblocks/ws_note.py` | WebSocket tempo real |
| `Projetos/Ariadne/backend/deeptutor_extracted/noteblocks/notion_sync.py` | Note→Notion sync |
| `Projetos/Ariadne/backend/deeptutor_extracted/api/routers/pm_dashboard.py` | Git API existente |
| `Projetos/Ariadne/backend/app/main.py` | FastAPI entry point |
| `Projetos/Ariadne/docs/pitch.md` | Visão do produto |
| `.config/opencode/AGENTS.md` | Guidelines opencode |

## Decisões que Impactam Implementação
1. **File tree toggle sutil** — borda fina 3px fechado, ~180px aberto. Sempre visível.
2. **Tudo colapsável** — file tree, git graph, chat. NoteBlock é o centro.
3. **Chat dentro do NoteBlock** (aba inferior) — não flutuante.
4. **Botões git inline** + versioning graph toggle na aba Git.
5. **Sync Engine desacoplado** — Universe (built-in) + Custom (qualquer repo).
6. **DB não versionado** — só markdown. Export JSON futuro.
7. **MongoDB não agora** — SQLite + JSON export atende.
8. **Handoff obrigatório** ao final de toda sessão.

## Instruções para Próxima Sessão

### Se for EXECUÇÃO (recomendado):
```powershell
cd C:\Users\frota\Projetos\Ariadne
git remote add origin https://github.com/frotaluiza/ariadne.git
git push -u origin master
git checkout -b feat/noteblock-file-tree
```
Ordem de implementação:
a) `router_repos.py` (tree/file endpoints)
b) CodeMirror 6 + file tree toggle no index.html
c) `router_git.py` (endpoints git)
d) Git buttons inline no frontend
e) Versioning graph colapsável
f) Chat dentro do NoteBlock + "Promover para Markdown"
g) Remover chat flutuante antigo

### O que evitar:
- Não versionar `ariadne.db`
- Não recriar o que já existe em `deeptutor_extracted/`
- Preferir CodeMirror 6 (leve) sobre Monaco (pesado)
- Atualizar AGENTS.md depois

## Task List (ordem)
- [ ] Configurar remote do Ariadne + push inicial
- [ ] Criar `backend/app/routers/router_repos.py`
- [ ] Adicionar CodeMirror 6 ao index.html
- [ ] Implementar file tree toggle (CSS + JS)
- [ ] Criar `backend/app/routers/router_git.py`
- [ ] Implementar git buttons inline
- [ ] Implementar versioning graph colapsável
- [ ] Mover chat para dentro do NoteBlock
- [ ] Implementar "Promover para Markdown"
- [ ] Remover chat flutuante antigo
- [ ] Atualizar AGENTS.md
- [ ] Implementar Sync Engine (modo Universe)
- [ ] Implementar Handoff automático
