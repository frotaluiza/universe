# Arquitetura: Bloco de Notas Diário + Markdown Universal

## Visão Geral

Todo usuário tem um "diário de bordo" por projeto, atualizado diariamente. Qualquer arquivo markdown no universo do usuário — transcrição de vídeo, PDF extraído via RAG, notas externas — abre como um NoteBlock editável.

## Princípios

1. **Consistência:** Todo NoteBlock é um arquivo `.md` no disco
2. **Universalidade:** Qualquer markdown vira NoteBlock (transcrições, OCR, RAG, importação)
3. **Diário automático:** Criado por projeto/dia com template fixo
4. **Ideias clicáveis:** Cada "ideia" no diário tem botão "Pesquisar" que invoca agente

## Estrutura do Diário

```markdown
# Diário - 2026-07-25 - {Nome do Projeto}

## Reflexões do dia
{texto livre do usuário}

## Ideias
- [ ] [Pesquisar] Ideia 1: {descrição}
- [ ] [Pesquisar] Ideia 2: {descrição}

## Decisões
- Decisão X: {contexto}

## Prompts / Comandos
- {comandos executados}
```

## Mecanismo de "Pesquisar"

Quando usuário clica `[Pesquisar]` em uma ideia:
1. Sistema captura o texto da ideia
2. Invoca agente de pesquisa: `WebSearch + Papers (arXiv/HF) + Avaliação crítica`
3. Retorna resultado direto no NoteBlock como sub-bloco recolhível
4. Usuário pode aceitar/rejeitar/promover para o fluxo principal

## Markdown Universal

| Fonte | Origem | Formato Final |
|-------|--------|---------------|
| Transcrição de vídeo | Whisper SRT/VTT | NoteBlock com timestamps |
| PDF extraído | MinerU2.5 / Docling | Markdown estruturado |
| OCR de imagem | Qwen2-VL / Tesseract | Markdown com bounding boxes |
| Importação externa | Arquivo .md qualquer | Cópia para o universo |

## Caminho no Disco

```
.ariadne/universe/data/artifacts/
  daily/
    2026-07-25-{projeto-slug}.md
  transcripts/
    {video-id}.md
  documents/
    {pdf-hash}.md
```

## Integração com o Orquestrador

- O diário alimenta o grafo de memória (Mem0)
- Decisões registradas viram nós no grafo de decisões do projeto
- Ideias não processadas aparecem no dashboard como "pendentes"
