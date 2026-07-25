# Arquitetura: Memória de Longo Prazo para Agentes (Mem0)

## Referência

Mem0 — arXiv:2504.19413 (Mem0 AI, 61.6k stars)

## O Problema

Agentes LLM têm contexto fixo. Em conversas multi-sessão, o agente esquece:
- Decisões tomadas em sessões anteriores
- Preferências do usuário
- Histórico de raciocínio

## A Solução: Memória Gráfica

Mem0 usa um **grafo de memória** onde cada interação relevante vira um nó conectado:

```
Sessão 1 ──► Decisão A ──► Preferência X
                 │
Sessão 2 ──► Decisão B ──► Preferência Y
                 │
            Conflito detectado ──► Resolução Z
```

### Métricas do Mem0
- **26% melhora** em LLM-as-a-Judge sobre OpenAI
- **91% menor latência (p95)** que full-context
- **90% menos tokens** que reprocessar todo histórico

## Arquitetura na Ariadne

```
NoteBlocks
    │
    ▼
┌──────────────────────────────────┐
│         MEM0 GRAPH ENGINE        │
│                                  │
│  Extrai → Consolida → Recupera  │
│                                  │
│  Nós: decisões, ideias, prompts, │
│        descobertas, preferências │
│  Arestas: causou, contradiz,     │
│           inspira, resolve       │
└──────────────────────────────────┘
    │
    ▼
Orquestrador (contexto enriquecido)
```

## Tipos de Nó no Grafo

| Tipo | Exemplo | Retenção |
|------|---------|----------|
| Decisão arquitetural | "Usar FastAPI em vez de Flask" | Permanente |
| Preferência do usuário | "Prefiro respostas concisas" | Permanente |
| Insight de sessão | "Descobri que X não funciona com Y" | 30 dias |
| Ideia pendente | "E se a gente tentar Z?" | Até ser processada |
| Erro frequente | "Sempre esqueço de validar entrada" | Até ser resolvido |

## Fluxo de Operação

1. **Extração:** Ao final de cada interação, o sistema extrai entidades relevantes
2. **Consolidação:** Novas entidades são conectadas ao grafo existente
3. **Recuperação:** Antes de cada resposta do agente, o grafo consulta nós relevantes
4. **Poda:** Nós com baixa relevância são arquivados após período

## Integração com o Test Loop

O histórico de edições de skills (SkillOpt) também vira nós no grafo:
- Cada edição rejeitada vira um "anti-padrão"
- Cada edição aceita vira uma "melhor prática"
- O optimizer pode consultar o grafo antes de propor edições

## Implementação

```python
# Interface proposta
class MemoriaGraph:
    def add_node(self, tipo: str, conteudo: str, metadados: dict): ...
    def query(self, contexto: str, k: int = 5) -> list[Node]: ...
    def consolidate(self, session_id: str): ...
    def prune(self, older_than: datetime): ...
```

## Armazenamento

- Banco: SQLite (existente em `ariadne.db`)
- Tabelas: `memory_nodes`, `memory_edges`, `memory_sessions`
- Backup: Git (o universo é versionado)
