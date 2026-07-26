# Arquitetura: Agente de Commits para Criações

## Visão Geral

Worker interno do universo dedicado a versionar as **criações** do projeto — os produtos finais que o projeto gera (código, livro LaTeX, site, jogo executável, etc). Age apenas no repositório da criação, não no universo.

## Conceito

- **Universo:** cérebro — versiona artefatos, decisões, sessões, handoffs
- **Criações:** filhos do universo — os entregáveis do projeto, versionados separadamente
- **Agente de Criações:** worker que monitora alterações nas criações e faz commits

## Modelo

```python
class Criacao(Base):
    __tablename__ = "criacoes"

    id = Column(String, primary_key=True)
    projeto_id = Column(String, ForeignKey("projetos.id"), nullable=False)
    nome = Column(String, nullable=False)
    tipo = Column(String, nullable=False)  # "codigo", "livro", "site", "jogo", "tese", "video", "outro"
    repo_path = Column(String, nullable=True)   # caminho local
    repo_url = Column(String, nullable=True)    # GitHub
    repo_exists = Column(Boolean, default=False)  # já tem repo?
    versionando = Column(Boolean, default=False)  # sendo versionado?
    created_at = Column(DateTime)
    ultimo_commit = Column(String, default="")
```

## Fluxo

```
Criação é modificada (via NoteBlock/editor/chat)
    │
    ▼
Agente de Criações detecta mudanças
    │
    ├─ Se repo_path não existe:
    │   → Pergunta: "Criar repositório para esta criação?"
    │   → Se sim: git init + remote (ou pergunta URL)
    │
    ├─ Se repo_path existe:
    │   → git add -A
    │   → git commit -m "feat: {descrição auto-gerada}"
    │   → git push
    │
    ▼
Registra no feature tracking (se aplicável)
Atualiza ultimo_commit na tabela criacoes
```

## Integração com o Universo

O agente de criações NÃO mexe no universo. Ele:
- Só opera no repositório da criação
- Usa o contexto do universo pra saber o que commitar (mensagem descritiva)
- Reporta status pro universo (último commit, branch, ahead/behind)

## Dashboard

Na aba "Criações" do projeto:
```
┌─────────────────────────────────────┐
│  Criações do Projeto                │
├─────────────────────────────────────┤
│  📦 engine-ai (código)              │
│     branch: feat/rag [⬆ ahead 2]   │
│     último: 3h atrás                │
│     [Abrir] [Commits]               │
│                                     │
│  📦 monografia (livro LaTeX)        │
│     branch: main  [✔ sync]          │
│     último: 2 dias atrás            │
│     [Abrir] [Commits]               │
│                                     │
│  [+ Nova Criação]                   │
└─────────────────────────────────────┘
```

## Tipos de Criação e Comportamento

| Tipo | Extensões | Estratégia de Commit |
|------|-----------|---------------------|
| código | .py, .js, .ts, .java, .cpp | Commits por feature/módulo |
| livro/tese | .tex, .md | Commits por capítulo/seção |
| site | .html, .css, .js, .astro | Commits por página/componente |
| jogo | .unity, .godot, .py | Commits por mecânica/cena |
| video | .mp4, .mov, projeto .aep | Commits do arquivo de projeto |

## Configuração

- O usuário decide se quer versionar a criação (toggle)
- Pode apontar repo existente ou criar novo
- Pode ter múltiplas criações por projeto
- Cada criação tem seu próprio agente de commits (ou o mesmo worker gerencia todas)
