# Guidelines: Estratégia de Branches

## Princípio

O versionamento da sessão depende do seu **tipo**:

| Tipo de Sessão | Branch em | Repositório |
|----------------|-----------|-------------|
| Planejamento | Universo do usuário | `.ariadne/universe/` |
| Execução | Repositório do projeto | `Projetos/{projeto}/` |

## Fluxo para Sessões de Planejamento

```
1. Início: perguntar "Esta é uma sessão de planejamento ou execução?"
2. Se PLANEJAMENTO:
   a. git checkout -b plan/{slug-da-sessao} em .ariadne/universe/
   b. Artefatos vão para data/artifacts/
   c. Ao final: commit + push no universo
3. Se EXECUÇÃO:
   a. git checkout -b feat/{slug-da-sessao} no repositório do projeto
   b. Código e artefatos vão para o projeto
   c. Ao final: commit + push no projeto
```

## Nomenclatura de Branches

### Planejamento
```
plan/{descricao-curta}-{data}
Ex: plan/video-testloop-ocr-2026-07-25
```

### Execução
```
feat/{nome-da-feature}
fix/{bug-descricao}
docs/{doc-descricao}
```

## Por que essa separação?

- **Planejamento gera decisões, não código** — o lugar natural é o universo pessoal
- **Execução gera código** — o lugar natural é o repositório do projeto
- O universo é versionado e pode ser consultado depois para entender "por que decidimos X"
- O repositório do projeto fica limpo de artefatos de planejamento

## Implementação no Ariadne

Dentro da plataforma Ariadne, o mesmo fluxo se aplica:

1. Quando o usuário cria uma **Nota de Planejamento** → salva no universo do usuário
2. Quando o usuário cria um **Artefato de Código** → salva no repositório do projeto
3. O Orquestrador pergunta: "Você quer planejar ou executar?"

## Atualizações no AGENTS.md

Adicionar ao AGENTS.md global:

```markdown
## Branch Strategy

Sessões de planejamento criam branches no universo do usuário
(`.ariadne/universe/`). Sessões de execução criam branches no
repositório do projeto. Perguntar ao usuário no início da sessão
qual o tipo, se não estiver explícito.
```
