# Arquitetura: Dashboard de Criações

## Visão Geral

Aba no dashboard do projeto que mostra todas as **criações** (produtos finais) do projeto, seu status de versionamento, e opções de gerenciamento.

## Layout

```
┌─────────────────────────────────────────────────────┐
│  [📊 Dashboard] [📁 Criações] [📋 Features] [⚙️]  │ ← abas
├─────────────────────────────────────────────────────┤
│  Criações do Projeto                           [+ Nova] │
├─────────────────────────────────────────────────────┤
│                                                     │
│  📦 engine-ai                          ●●●          │
│  ├─ Tipo: código (Python)                           │
│  ├─ Repo: frotaluiza/engine-ai            [Abrir]   │
│  ├─ Branch: feat/rag [⬆ ahead 2 ↓ behind 0]        │
│  ├─ Último commit: 3h atrás — "feat: adiciona RAG" │
│  ├─ Status: ✅ versionando                          │
│  └─ [Commits ▸] [Diff ▸] [Config ▸]                 │
│                                                     │
│  📦 monografia                          ●●●          │
│  ├─ Tipo: livro (LaTeX)                             │
│  ├─ Repo: frotaluiza/tcc-membranas        [Abrir]   │
│  ├─ Branch: main [✔ sincronizado]                   │
│  ├─ Último commit: 2 dias atrás — "cap 3: revisão" │
│  ├─ Status: ✅ versionando                          │
│  └─ [Commits ▸] [Diff ▸] [Config ▸]                 │
│                                                     │
└─────────────────────────────────────────────────────┘
```

## Funcionalidades

### Lista de Criações
- Cada criação é um card com: nome, tipo, repo, branch, último commit, status
- Ordenação: última modificação primeiro

### Nova Criação
- Botão "+ Nova" abre modal:
  - Nome da criação
  - Tipo (código, livro, site, jogo, tese, vídeo, outro)
  - Repositório: criar novo OU importar existente (URL)
  - Path local (opcional)

### Ações por Criação
| Ação | Descrição |
|------|-----------|
| Abrir | Abre o repositório no VS Code |
| Commits | Histórico de commits (git log --oneline) |
| Diff | Diff do último commit |
| Config | Configurações específicas da criação |
| Commitar | Forçar commit manual |
| Parar de versionar | Remove do tracking |

### Status de Versionamento
| Status | Significado |
|--------|-------------|
| ✅ versionando | Repo configurado e ativo |
| ⏸️ pausado | Versionamento desligado (toggle) |
| ❌ não versionado | Ainda sem repo (só arquivos locais) |
| ⚠️ precisa configurar | Repo remoto não configurado |

### Importar Repositório Existente
- Usuário cola URL do GitHub
- Sistema clona ou associa ao path local
- Registra na tabela `criacoes`

### Criar Repositório Novo
- Sistema pergunta: nome, tipo, público/privado
- Cria no GitHub via API (Composio)
- `git init` local + `git remote add origin`
- Primeiro commit automático

## API

```python
GET    /api/projetos/{id}/criacoes         # lista criações
POST   /api/projetos/{id}/criacoes         # nova criação
PUT    /api/criacoes/{id}                  # atualizar
DELETE /api/criacoes/{id}                  # remover
POST   /api/criacoes/{id}/commit          # forçar commit
POST   /api/criacoes/{id}/import          # importar repo existente
POST   /api/criacoes/{id}/create-repo     # criar repo novo no GitHub
GET    /api/criacoes/{id}/commits         # histórico
GET    /api/criacoes/{id}/diff            # último diff
```

## Integração com Feature Tracking

Quando uma feature tem `commit_hash` preenchido e está como `implementada`, o sistema move automaticamente para `aguardando_teste` e mostra um badge no card da criação:

```
📦 engine-ai                          ●●● (2 features aguardando teste)
```
