# Decisões — Sessão 2: NoteBlock UI + Sync Engine + GitHub Discovery

## Contexto

Segunda sessão de planejamento do Ariadne. Início com descoberta dos repositórios no GitHub (frotaluiza), definição de prioridades para implementação, e refinamento de duas features principais: NoteBlock UI com file tree + git, e Sync Engine GitHub→Notion.

## Decisões

### 2026-07-25 01:50 — GitHub Repos Descobertos

- **Contexto:** Precisávamos saber se os repositórios do universo e Ariadne estavam no GitHub
- **Descoberta:** Ambos existem como privados:
  - `frotaluiza/universe` — default branch `plan/sessao-planejamento-2026-07-25`
  - `frotaluiza/ariadne` — vazio (criado mas sem push)
- **Remote local:** Universo já tem `origin` configurado e em sync (7 commits, mesma HEAD)
- **Ariadne:** 5 commits locais, sem remote configurado
- **Origem:** github_api (Composio)

### 2026-07-25 02:10 — Sync Não é de Sessões, é GitHub → Notion

- **Contexto:** Eu propus sync via @session (opencode → Notion)
- **Correção:** O sync desejado é **GitHub → Notion** — os markdowns no repositório (artefatos, decisões, sessões) devem virar páginas no Notion
- **Stack:** GitHub API (ler arquivos) + Notion API (criar/atualizar páginas)
- **Origem:** user_input

### 2026-07-25 02:15 — Sync Engine Deve Ser Configurável

- **Contexto:** O sync não pode ser amarrado ao universo Ariadne
- **Decisão:** O usuário pode personalizar o que vai pra onde — dois modos:
  - **Modo Universe** (built-in): mapping default artefatos→docs, sessões→sessões
  - **Modo Custom:** qualquer repo GitHub → qualquer database Notion
- **Origem:** user_input

### 2026-07-25 02:20 — NoteBlock com File Tree + Git

- **Contexto:** Quando o usuário é redirecionado para um NoteBlock, precisa ver onde está na estrutura de pastas
- **Decisão:**
  - File tree toggle sutil (ícone fino na borda quando fechado, ~180px quando aberto)
  - Toggle sempre aparece (deve ser o mais sutil possível, ocupando espaço mínimo)
  - Git inline: botões (status, diff, stage, commit, push) + versioning graph colapsável
  - Tudo colapsável: file tree, git graph, chat
- **Stack:** CodeMirror 6 (editor), API pm_dashboard.py (git), ws_note.py (WebSocket)
- **Origem:** user_input

### 2026-07-25 02:25 — Chat Vive Dentro do NoteBlock

- **Contexto:** O pitch diz "chat existe dentro do bloco de nota, não o contrário"
- **Decisão:** Chat vai na parte inferior do NoteBlock, aba colapsável, com botão "Promover para Markdown"
- **Impacto:** Remove o chat flutuante atual (ícone no canto inferior direito)
- **Origem:** user_input + pitch.md

### 2026-07-25 02:30 — Prioridade de Implementação

1. NoteBlock UI (file tree + editor + git)
2. Sync Engine (modo Universe primeiro, depois Custom)
3. Integração NoteBlock ↔ Sync Engine

### 2026-07-25 02:35 — Branch Strategy Confirmada

- Planejamento → branch no universo (`plan/sessao-planejamento-2026-07-25`)
- Execução → branch no repositório do projeto
- Esta sessão gerou 3 novos artefatos + atualização de task list

## Papers Analisados (nova rodada)

Nenhum paper novo — os papers do HuggingFace foram todos registrados no artefato 06.

## Fontes Registradas

| Fonte | Tipo | Origem |
|---|---|---|
| GitHub API (Composio) | ferramenta | descoberta dos repos |
| pitch.md (Ariadne) | documento | visão do NoteBlock |
| freeCodeCamp tools | artigo | MCP tools (ref. 07) |

## Arquivos Modificados

- `data/artefatos/09-arquitetura-noteblock-ui.md` (criação)
- `data/artefatos/10-arquitetura-sync-engine.md` (criação)
- `data/artefatos/11-decisoes-sessao-2.md` (criação)
