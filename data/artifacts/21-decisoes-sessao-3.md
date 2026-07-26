# Decisões — Sessão 3: Config Toggles, Agente de Criações, Guidelines, Feature Tracking

## Contexto

Terceira sessão de planejamento do Ariadne. Refinamento da arquitetura do NoteBlock, definição do sistema de configurações com toggles, criação do agente de commits para criações, hierarquia de guidelines, feature tracking com ciclo de teste, e export de sessões.

## Decisões

### 2026-07-26 — Branch Nova com Resumo

- **Contexto:** A branch anterior `plan/sessao-planejamento-2026-07-25` não refletia o escopo atual
- **Escolha:** Criada `plan/noteblock-sync-handoff-agentes-2026-07-26`
- **Formato:** `{tipo}/{resumo}-{data}` — tipo + resumo dos assuntos + data
- **Regra:** Handoff da branch anterior commitado e mergeado na master antes de criar a nova
- **Origem:** user_input

### 2026-07-26 — Configurações com Toggles

- **Contexto:** Decidir o que é automático vs manual precisa ser configurável
- **Escolha:** Tabela `user_configs` com escopo global ou por projeto, 3 valores: auto / manual / ask
- **Categorias:** versionamento, handoff, artefatos, sync, noteblock, sessoes
- **Resolução:** Project sobrescreve Global, se nenhuma usa default do sistema
- **Origem:** user_input

### 2026-07-26 — Criações = Filhos do Universo

- **Contexto:** Projetos geram produtos finais (código, livro, site, tese, jogo, vídeo)
- **Escolha:** Chamar de **"criações"**. Cada criação tem seu próprio versionamento.
- **Modelo:** Tabela `criacoes` com tipo, repo_path, repo_url, status de versionamento
- **Dashboard:** Aba dedicada mostrando todas as criações do projeto
- **Origem:** user_input

### 2026-07-26 — Agente de Commits para Criações

- **Contexto:** Versionamento de criações não deve ser responsabilidade do universo
- **Escolha:** Worker interno dedicado que monitora alterações e faz commits
- **Escopo:** Só mexe no repositório da criação, nunca no universo
- **Detecção:** Monitora alterações via FileSystemWatcher ou hook
- **Integração:** Reporta status pro universo (último commit, branch)
- **Origem:** user_input

### 2026-07-26 — Guidelines: Globais + Projeto

- **Contexto:** Usuário quer formalizar regras que valem pra todos ou pra um projeto específico
- **Escolha:** Tabela `guidelines` com escopo global/project. Project sobrescreve Global.
- **Agente invocável:** Usuário diz "sempre faça X" e o agente cria a guideline
- **Resolução:** Orquestrador consulta antes de cada decisão automática
- **Origem:** user_input

### 2026-07-26 — Feature Tracking com Ciclo de Teste

- **Contexto:** Acompanhar features desde o planejamento até o teste pelo usuário
- **Ciclo:** Planejada → Implementada → Aguardando Teste → Testada (OK/FAIL)
- **Modelo:** Tabela `features` com status, commit_hash, test_notes
- **Gatilhos:** Commit menciona feature, agente de criações faz push, usuário testa
- **Dashboard:** Aba com seções: Aguardando Teste (🔴), Testadas (🟢), Planejadas (⚪)
- **Origem:** user_input

### 2026-07-26 — Descrição da Sessão

- **Contexto:** Sessão precisa de descrição longa além do resumo curto
- **Escolha:** Campo `descricao` (TEXT) na tabela `sessoes`, gerado automaticamente por LLM
- **Export:** Script exporta dados do SQLite do opencode para o ariadne.db + JSON
- **Origem:** user_input

### 2026-07-26 — Sync Notion: Só 2 Modos

- **Contexto:** Simplificar sync GitHub→Notion
- **Escolha:** Mirror exato (espelhar databases do universo) OU mapping custom
- **Default:** Desligado (usuário ativa quando quiser)
- **Origem:** user_input

### 2026-07-26 — Auto-aprovação por Timer

- **Contexto:** Se usuário não reage à notificação de artefato
- **Escolha:** Timer de 30s (configurável). Se não reagir, segue preferência da config.
- **Score alto (>0.8):** auto-aprova direto
- **Score médio (0.5-0.8):** ask (timer cai pra auto se não reagir)
- **Score baixo (<0.5):** só manual
- **Origem:** user_input

## Arquivos Modificados

- `data/artifacts/15-arq-config-toggles.md` (criação)
- `data/artifacts/16-arq-agente-criacoes.md` (criação)
- `data/artifacts/17-arq-guidelines-hierarquia.md` (criação)
- `data/artifacts/18-arq-sessao-descricao.md` (criação)
- `data/artifacts/19-arq-dashboard-criacoes.md` (criação)
- `data/artifacts/20-arq-feature-tracking.md` (criação)
- `data/artifacts/21-decisoes-sessao-3.md` (criação)
- `data/handoffs/plan-noteblock-sync-handoff-agentes-2026-07-26.md` (criação)
