# Arquitetura: Incorporação de Vídeos na Plataforma

## Visão Geral

Sistema para buscar transcrição de vídeos automaticamente, converter em NoteBlock com timestamps, e permitir que o agente localize informações exatas dentro do vídeo durante conversas.

## Stack

- **Download:** `yt-dlp` (extrai áudio/legendas do YouTube)
- **Transcrição:** `whisper.cpp` local (modelo small ou medium, CPU/GPU)
- **Armazenamento:** NoteBlock com estrutura markdown + metadados
- **Busca temporal:** Regex + índice invertido de timestamps

## Fluxo

```
1. Usuário abre vídeo na plataforma
2. Clica em "Buscar Transcrição"
3. Sistema baixa áudio via yt-dlp
4. Whisper transcreve com timestamps palavra a palavra
5. Converte para NoteBlock no formato:

[00:00:15] Introdução ao conceito de...
[00:02:30] A equação fundamental é...
[00:05:45] O experimento mostrou que...

6. NoteBlock aparece como aba ao lado do player
7. Agente indexa os blocos para busca semântica
```

## Estrutura do NoteBlock de Transcrição

```yaml
tipo: video_transcription
video_url: https://youtube.com/watch?v=xxx
video_title: "Título do Vídeo"
duracao: 15:30
idioma: pt-BR
modelo: whisper-small
data_transcricao: 2026-07-25
```

```markdown
## [00:00:00 - 00:00:15] Abertura
Olá pessoal, hoje vamos falar sobre...

## [00:00:15 - 00:02:30] Introdução
O conceito fundamental é...
```

## Agente Conversando sobre o Vídeo

Quando o usuário pergunta:
- "O que ele disse sobre X?"
- Agente busca nos blocos por similaridade semântica + correspondência de timestamp
- Resposta inclui citação: `[Fonte: 00:02:30 - 00:02:45]`

## Implementação Futura

- Transcrição em tempo real (streaming via Moonshine ASR)
- Suporte a múltiplos idiomas
- Detecção automática de falantes (diarização)
- Geração de resumo automático da transcrição

## Dependências

- `yt-dlp` (Python)
- `whisper.cpp` (C++, bindings Python via `whisper-cpp-python`)
- NoteBlock system existente em `deeptutor_extracted/noteblocks/`
