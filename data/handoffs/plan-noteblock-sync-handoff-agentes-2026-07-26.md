# Handoff: plan-noteblock-sync-handoff-agentes-2026-07-26

## Metadados
- Sessão: Config Toggles + Agente Criações + Guidelines + Feature Tracking
- Data: 2026-07-26
- Projeto: Ariadne
- Tipo: planejamento
- Branch: `plan/noteblock-sync-handoff-agentes-2026-07-26`
- Último commit: (pendente)
- Branch anterior mergeada: `plan/sessao-planejamento-2026-07-25` → master (`9f012b1`)
- Remote: `origin → https://github.com/frotaluiza/universe.git`

## Estado do Projeto (Universo)
- Repositório: `C:\Users\frota\.ariadne\universe`
- Branch atual: `plan/noteblock-sync-handoff-agentes-2026-07-26`
- Remote: origin configurado e em sync
- Artefatos: 21 gerados (01 a 21)
- Handoffs: 2 gerados (plan-sessao-planejamento + plan-noteblock-sync-handoff-agentes)
- DB: `ariadne.db` (local, não versionado)
- `.gitignore` exclui `*.db`, `*.db-shm`, `*.db-wal`

## Estado do Projeto (Ariadne)
- Repositório: `C:\Users\frota\Projetos\Ariadne`
- Remote: ✅ `origin → https://github.com/frotaluiza/ariadne.git`
- Último commit: `83a42a1 feat: VS Code integration, artifact view modal, auto-approve timer + push to GitHub repos`
- Frontend: `backend/app/static/index.html`
- NoteBlocks: `backend/deeptutor_extracted/noteblocks/`
- DB: `~/.ariadne/universe/ariadne.db`

## O que foi feito
1. Branch `plan/noteblock-sync-handoff-agentes-2026-07-26` criada a partir da master
2. Artefatos 15-21 gerados (7 artefatos novos)
3. Sistema de Config Toggles arquitetado (15)
4. Agente de Commits para Criações arquitetado (16)
5. Guidelines Globais vs Projeto arquitetado (17)
6. Descrição da Sessão + Export opencode.db→ariadne.db arquitetado (18)
7. Dashboard de Criações arquitetado (19)
8. Feature Tracking com ciclo de teste arquitetado (20)
9. Decisões da sessão 3 registradas (21)
10. Handoff atualizado (22)

## O que NÃO foi feito (pendente)

### Prioridade Máxima — Implementar no Ariadne
1. [ ] Adicionar campo `descricao` na tabela `sessoes` (model + migration)
2. [ ] Criar tabela `user_configs` (model + router + UI)
3. [ ] Criar tabela `guidelines` (model + router + agente)
4. [ ] Criar tabela `criacoes` (model + router)
5. [ ] Criar tabela `features` (model + router)
6. [ ] Implementar UI de Config Toggles no dashboard
7. [ ] Implementar aba Criações no dashboard
8. [ ] Implementar aba Features no dashboard
9. [ ] Implementar Agente de Guidelines (invocável via chat)
10. [ ] Implementar Agente de Criações (worker de commits)
11. [ ] Implementar Export opencode.db → ariadne.db
12. [ ] Implementar Feature Tracking (gatilhos automáticos)
13. [ ] Integrar auto-aprovação por timer + score
14. [ ] Implementar Sync Notion (modo mirror + custom)

### Features Planejadas (fases anteriores)
15. [ ] Transcrição de vídeo (yt-dlp + Whisper)
16. [ ] Diário automático por projeto/dia
17. [ ] VLM Pipeline (Qwen2-VL + Tesseract)
18. [ ] Memória Mem0 (grafo)
19. [ ] Test Loop SkillOpt
20. [ ] MCP Tools (Jupyter, Git, GeoGebra)

## Caminhos e Arquivos Usados
| Caminho | Descrição |
|---------|-----------|
| `.ariadne/universe/` | Repositório do universo |
| `.ariadne/universe/data/artefatos/` | 21 artefatos (01-21) |
| `.ariadne/universe/data/handoffs/` | 2 handoffs |
| `.ariadne/universe/ariadne.db` | SQLite do universo (local) |
| `Projetos/Ariadne/backend/app/models/` | Modelos SQLAlchemy |
| `Projetos/Ariadne/backend/app/models/sessao.py` | Model Sessao |
| `Projetos/Ariadne/backend/app/models/artefato.py` | Model Artefato |
| `Projetos/Ariadne/backend/app/static/index.html` | Frontend vanilla |
| `Projetos/Ariadne/backend/deeptutor_extracted/noteblocks/` | NoteBlocks API |
| `.config/opencode/AGENTS.md` | Guidelines opencode |
| `.config/opencode/opencode.json` | Config opencode |

## Decisões que Impactam Implementação
1. **Toggles:** 3 valores (auto/manual/ask), escopo global ou por projeto, project sobrescreve global
2. **Criações:** cada uma com repo próprio, agente dedicado de commits, não mexe no universo
3. **Guidelines:** hierarquia global→projeto, agente invocável via chat pra criar regras
4. **Feature tracking:** ciclo planejada→implementada→aguardando_teste→testada
5. **Auto-aprovação:** timer 30s configurável, score >0.8 auto, 0.5-0.8 ask, <0.5 manual
6. **Sync Notion:** só 2 modos (mirror ou custom), default desligado
7. **Export sessão:** copia do opencode.db pro ariadne.db + JSON versionado
8. **Branch naming:** `{tipo}/{resumo}-{data}`

## Instruções para Próxima Sessão

### Se for EXECUÇÃO (recomendado — implementar no Ariadne):
```powershell
cd C:\Users\frota\Projetos\Ariadne
git checkout -b feat/sistema-configs
```
**Ordem de implementação sugerida:**
1. Campo `descricao` na Sessao (model + migration)
2. Tabela `user_configs` + router + UI de toggles
3. Tabela `guidelines` + router + agente invocável
4. Tabela `criacoes` + router + aba no dashboard
5. Tabela `features` + router + aba no dashboard + gatilhos
6. Export opencode.db → ariadne.db
7. Agente de Criações (worker de commits)
8. Integração feature tracking + agente de criações
9. Auto-aprovação por timer
10. Sync Notion

### O que evitar:
- Recriar o que já existe em `deeptutor_extracted/`
- Versionar `ariadne.db`
- Implementar sem antes verificar os artefatos de arquitetura (15-20)
- Esquecer de atualizar AGENTS.md depois

### Guidelines pro opencode (já incorporar):
- Handoff automático ao final de toda sessão
- Branch name: `{tipo}/{resumo}-{data}`
- "Criações" como nome dos produtos finais
- Commit + push automático no universo
