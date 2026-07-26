# Arquitetura: Session Handoff — Relatório de Plantão

## Visão Geral

Sistema de handoff que permite uma sessão futura (seja humana ou agente) retomar o trabalho exatamente de onde parou. Funciona como um "relatório de plantão": contém estado atual, instruções literais, caminhos de arquivos, decisões pendentes.

## O Problema

Sessões do opencode são isoladas. Uma sessão que começa não sabe:
- O que foi feito na sessão anterior
- Qual branch usar
- Quais arquivos modificar
- Quais decisões já foram tomadas
- Qual o próximo passo concreto

## A Solução: Handoff Automático

```
Fim da sessão
    │
    ▼
Gera handoff.md com:
  ├─ Estado do projeto (branch, commit, remote)
  ├─ O que foi feito (resumo + artefatos)
  ├─ O que NÃO foi feito (pendências)
  ├─ Caminhos usados (paths exatos)
  ├─ Decisões que impactam implementação
  └─ Instruções para próxima sessão
    │
    ▼
Salva em: data/handoffs/{slug}.md
    │
    ▼
Commit + push (versão permanente)
    │
    ▼
Próxima sessão lê o handoff mais recente
```

## Estrutura

```markdown
# Handoff: {slug-da-sessao}

## Metadados
- Sessão: {título}
- Data: {data ISO}
- Projeto: {nome}
- Tipo: {planejamento|execução}
- Branch: {branch}
- Último commit: {hash}
- Remote: {url}

## Estado do Projeto
- Repositório: {path}
- Remote configurado: {sim|não}
- Pendências: {descrição}

## O que foi feito
{lista numerada}

## O que NÃO foi feito (pendente)
{lista numerada}

## Caminhos e Arquivos Usados
| Path | Descrição |

## Decisões que Impactam Implementação
{lista com rationale}

## Instruções para Próxima Sessão
1. {comando literal}
2. {próximo passo}
3. {o que evitar}

## Task List
- [ ] tarefas em ordem
```

## Implementação Futura

```python
class Handoff:
    slug: str
    project_name: str
    session_type: str
    branch: str
    last_commit: str
    remote_url: str | None
    repo_path: str
    done: list[str]
    pending: list[str]
    paths_used: dict[str, str]
    decisions: list[dict]
    instructions: list[str]
    task_list: list[str]

    def to_markdown(self) -> str: ...
    def save(self, base_path: Path = "data/handoffs"): ...
    @classmethod
    def latest(cls, project: str) -> "Handoff | None": ...
```
