# Arquitetura: NoteBlock UI com File Tree + Versionamento

## Visão Geral

Bloco de notas inteligente onde o chat vive **dentro** do NoteBlock, não o contrário. O usuário vê o documento ativo dentro da estrutura de pastas do repositório, com versionamento git acessível na própria janela.

## Princípios de UX

1. **Toggle sutil e sempre presente** — o file tree deve ocupar o mínimo espaço possível quando fechado (ícone ou borda fina)
2. **Tudo colapsável** — file tree, git graph, chat — tudo pode ser ocultado com um clique
3. **Localização contextual** — ao abrir um NoteBlock, o usuário vê onde ele está na árvore do repositório
4. **Versionamento inline** — git status, diff, commit e push sem sair do NoteBlock

## Layout

```
┌─────────────────────────────────────────────────────┐
│ [≡] [NoteBlock 📄]  [Chat 💬]  [Git 🌿]  [⚙️]    │ ← abas superiores
├──────────┬──────────────────────────────────────────┤
│ 📁 repo  │                                          │
│ ┣ 📂 src │   # Documento ativo                      │
│ ┃ ┣ main │   ┌─────────────────────────────────┐    │
│ ┃ ┗ utils│   │  Markdown Editor (CodeMirror)    │    │
│ ┣ 📂 docs│   │                                  │    │
│ ┃ ┣ 09-.. │   │  # Título do Documento          │    │
│ ┃ ┗ 10-.. │   │                                  │    │
│ ┣ 📜 .md  │   │  Conteúdo editável...            │    │
│ ┗ 📜 .md  │   │                                  │    │
│          │   └─────────────────────────────────┘    │
│          │                                          │
│ [🔍 filtrar...] │  [📝 status: modified]            │
│          │   [📊 diff] [✅ stage] [📤 commit] [⬆ push]│
├──────────┴──────────────────────────────────────────┤
│ 💬 Chat com Orquestrador (colapsável)              │
│ ┌──────────────────────────────────────────────┐   │
│ │ 👤 "Adicione uma seção sobre..."             │   │
│ │ 🤖 "Claro, adicionando..."                   │   │
│ │ ───────────────────────────────────────      │   │
│ │ [✏️ Digite sua mensagem...] [Enviar]         │   │
│ └──────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘
```

## Aba de Git (colapsável)

### Botões inline
```
[📝 status] [📊 diff] [➕ stage all] [📤 commit...] [⬆ push]
     │           │           │              │           │
     ▼           ▼           ▼              ▼           ▼
  mostra     mostra      stage         abre input   push p/
  arquivos   diff --stat todos         de mensagem  origin
  modificados
```

### Versioning Graph (toggle dentro da aba Git)
```
[🌿 Git ▼] ─── clique expande
┌────────────────────────────────────────────┐
│ * a62690b (HEAD) plan: artefatos de...     │
│ * 6842056 sessao diaria criada             │
│ * 861dbbf feat: state_json column          │
│ * c8c632a import: AI STEM Tutor            │
│                                          │
│ ┌─── main                                │
│ │                                        │
│ * ──► plan/sessao-planejamento-2026-07-25│
│                                          │
│ [🌿 branch: plan/...] [⬆ ahead 3]       │
└────────────────────────────────────────────┘
```

## File Tree Toggle (sutil)

O file tree ocupa ~180px à esquerda quando aberto. Quando fechado, vira um ícone fino na borda:

| Estado | Visual |
|--------|--------|
| **Aberto** | `📁 repo/` com árvore de pastas |
| **Fechado** | `│` traço fino na borda esquerda (3px) |
| **Hover** | O traço expande leve para `📁` |

Toggle: clique no traço / atalho `Ctrl+B`

## Chat Inside NoteBlock

O chat NÃO é uma janela flutuante (como no frontend atual). Ele está **dentro** do NoteBlock, na parte inferior, colapsável:

```
[💬 Chat] ─── clique para expandir/recolher
```

Quando expandido, ocupa 30-50% da altura do NoteBlock, com:
- Histórico da conversa (scroll infinito)
- Input de texto
- Botão "Promover para Markdown" — o que o usuário escolhe da conversa vira bloco no NoteBlock

## Stack

- **Editor:** CodeMirror 6 (leve, extensível, markdown syntax highlight)
- **File Tree:** Componente vanilla JS com lazy-load de diretórios via API
- **Git:** API existente em `deeptutor_extracted/api/routers/pm_dashboard.py` (status, diff, log, branch)
- **WebSocket:** `noteblocks/ws_note.py` já existente para edição em tempo real
- **Chat API:** `/api/orquestrador/process` + WebSocket para streaming

## Integração com o Backend

Novos endpoints necessários:

```
GET  /api/repos/{repo_id}/tree?path=       → lista diretório
GET  /api/repos/{repo_id}/file?path=       → retorna conteúdo
PUT  /api/repos/{repo_id}/file?path=       → salva alterações
POST /api/repos/{repo_id}/git/status       → git status
POST /api/repos/{repo_id}/git/diff         → git diff
POST /api/repos/{repo_id}/git/commit       → git commit
POST /api/repos/{repo_id}/git/push         → git push
GET  /api/repos/{repo_id}/git/log          → git log --graph --oneline
POST /api/repos/{repo_id}/git/branch       → criar/switch branch
```

O `repo_id` pode ser:
- `universe` → aponta para `.ariadne/universe/`
- `projeto/{slug}` → aponta para `Projetos/{slug}/`
- Qualquer caminho registrado pelo usuário

## Implementação (faseada)

### Fase 1 — File Tree + Editor
1. Criar `router_repos.py` com endpoints de árvore e arquivo
2. Implementar CodeMirror 6 no frontend
3. Implementar file tree toggle (ícone sutil na borda)
4. Conectar WebSocket do NoteBlock

### Fase 2 — Git UI
5. Criar `router_git.py` com endpoints git (usa `pm_dashboard.py` existente)
6. Implementar botões inline (status, diff, stage, commit, push)
7. Implementar versioning graph colapsável

### Fase 3 — Chat Inside
8. Mover chat para dentro do NoteBlock como aba inferior
9. Implementar "Promover para Markdown"
10. Remover chat flutuante do frontend antigo
