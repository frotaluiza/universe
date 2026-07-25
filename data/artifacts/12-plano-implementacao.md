# Plano de Implementação — Ariadne

Lista mestra de tudo que foi planejado até agora, organizado por prioridade e fase.

## Legenda

- 🔴 Fase 1 — MVP base (primeiro a implementar)
- 🟡 Fase 2 — Features essenciais
- 🟢 Fase 3 — Expansão e refinamento
- ⚪ Futuro — Ideias para depois do MVP

---

## Fase 1 (🔴) — NoteBlock UI + Core

### NoteBlock com File Tree + Editor
- [ ] File tree toggle sutil (ícone/borda fina, Ctrl+B)
- [ ] CodeMirror 6 integrado (editor markdown)
- [ ] Localização contextual (mostrar onde o doc está na árvore)
- [ ] Abas superiores: NoteBlock | Chat | Git | ⚙️
- [ ] Tudo colapsável (file tree, git graph, chat)

### Git Inline no NoteBlock
- [ ] Botão [📝 status] — mostra arquivos modificados
- [ ] Botão [📊 diff] — diff --stat
- [ ] Botão [➕ stage all] — git add
- [ ] Botão [📤 commit] — input de mensagem + commit
- [ ] Botão [⬆ push] — push para origin
- [ ] Versioning graph colapsável (git log --graph)
- [ ] Indicador de branch atual + ahead/behind

### Chat Inside NoteBlock
- [ ] Chat movido para dentro do NoteBlock (aba inferior)
- [ ] Botão "Promover para Markdown" (seleciona trecho da conversa → vira bloco)
- [ ] Chat colapsável (30-50% da altura quando aberto)
- [ ] Streaming de resposta do Orquestrador
- [ ] Histórico com scroll infinito

### Backend — Repos API
- [ ] `GET /api/repos/{repo_id}/tree` — lista diretório
- [ ] `GET /api/repos/{repo_id}/file` — conteúdo do arquivo
- [ ] `PUT /api/repos/{repo_id}/file` — salva alterações
- [ ] `POST /api/repos/{repo_id}/git/*` — endpoints git
- [ ] Registro de `repo_id` (universe, projeto/{slug}, custom)

---

## Fase 2 (🟡) — Sync Engine + Features Planejadas

### Sync Engine (GitHub → Notion)
- [ ] Model SyncRule (repo, pattern, database, mapping)
- [ ] Model SyncLog (histórico de execuções)
- [ ] CRUD de regras de sync (router)
- [ ] Parser de markdown (frontmatter + body sections)
- [ ] Modo Universe (mapping default Ariadne)
- [ ] Modo Custom (qualquer repo → qualquer database)
- [ ] Property mapping configurável
- [ ] Webhook endpoint (push do GitHub)
- [ ] Sync manual (UI/API)
- [ ] Sync agendado (opcional, cron)
- [ ] Idempotência (hash de conteúdo evita duplicatas)

### Transcrição de Vídeo
- [ ] Download de áudio via yt-dlp
- [ ] Transcrição via Whisper (local)
- [ ] NoteBlock com timestamps
- [ ] Busca temporal (regex + índice)
- [ ] Player de vídeo integrado

### Diário / Markdown Universal
- [ ] Diário automático por projeto/dia
- [ ] Template fixo (reflexões, ideias, decisões, prompts)
- [ ] Botão "Pesquisar" em ideias
- [ ] Qualquer .md abre como NoteBlock

### Imagem → Texto (VLM Pipeline)
- [ ] Qwen2-VL via Ollama (rota principal)
- [ ] Tesseract OCR (fallback)
- [ ] Upload de imagem no chat
- [ ] Resultado textual no contexto do agente

---

## Fase 3 (🟢) — Memória + Skills + Ferramentas

### Memória de Longo Prazo (Mem0)
- [ ] Grafo de memória (nós + arestas)
- [ ] Extração automática de decisões
- [ ] Consolidação multi-sessão
- [ ] Recuperação de contexto
- [ ] Poda de nós obsoletos

### Test Agent Loop (SkillOpt)
- [ ] Core SkillOpt abstraído (optimizer + editor)
- [ ] Harness padrão (NoteBlock → task → score)
- [ ] Notificação não-intrusiva de progresso
- [ ] Histórico de edições no grafo de memória

### MCP Tools
- [ ] Jupyter Notebook (células executáveis no NoteBlock)
- [ ] Git MCP (commit, branch, push via agente)
- [ ] GeoGebra (plotagem matemática)
- [ ] Wolfram Alpha (cálculo simbólico)
- [ ] YouTube (busca + transcrição)

---

## Futuro (⚪)

- OCR de caderno físico espelhado
- Jogos de avaliação (Runner do Conhecimento)
- Modo escuro padrão
- Anti-token-maxxing por design
- Transcrição em tempo real (Moonshine ASR)
- Integração com Telegram/Slack/Discord

---

## Como Usar Esta Lista

Cada checkbox vira uma tarefa no NoteBlock ou no sistema de tarefas do Ariadne. Ao iniciar uma sessão de execução, escolher uma tarefa da Fase 1, criar branch `feat/{nome}` no repositório do Ariadne, e implementar.
