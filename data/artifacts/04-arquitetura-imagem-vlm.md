# Arquitetura: Envio de Imagens para o Agente (VLM Pipeline)

## O Problema

O modelo de linguagem do agente pode não ter capacidade de visão (vision). Como o usuário envia imagens (fotos de caderno, prints de tela, diagrams, gráficos) e o agente "entende"?

## Solução: Pipeline em Camadas

```
Imagem do usuário
       │
       ▼
┌──────────────────┐
│   Rota 1: VLM    │  Qwen2-VL 7B (Ollama local)
│   (primário)     │  ~6GB VRAM, 4096×4096 nativo
└────────┬─────────┘         │
         │                   ▼
         │         Texto extraído + descrição
         │                   │
         ▼                   ▼
┌──────────────────┐
│   Rota 2: OCR    │  Tesseract (fallback se VLM
│   (fallback)     │  indisponível)
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│   Prompt final   │  Texto estruturado enviado
│   para o LLM     │  ao agente como contexto
└──────────────────┘
```

## Decisão: Qwen2-VL Local

**Escolhido sobre alternativas:**

| Modelo | VRAM | OCR Multilíngue | Offline | License |
|--------|------|-----------------|---------|---------|
| **Qwen2-VL 7B** | ~6GB | ✅ Líder CJK | ✅ | Apache 2.0 |
| Llama 3.2 Vision 11B | ~8GB | ⚠️ Inglês only | ✅ | Llama 2 Community |
| GPT-4o | N/A | ✅ | ❌ Cloud | Proprietária |
| LLaVA 1.6 | ~6GB | ⚠️ Inferior CJK | ✅ | Apache 2.0 |

## Fluxo Detalhado

```
1. Usuário faz upload da imagem
2. Sistema verifica se Qwen2-VL está disponível (Ollama rodando)
3. Se sim: envia imagem para Qwen2-VL com prompt:
   "Extraia todo o texto desta imagem. Preserve a estrutura."
4. Se não: fallback para Tesseract OCR
5. Resultado textual é inserido no contexto do agente como:
   
   [Imagem: nome_do_arquivo.png]
   ---
   {texto extraído}
   ---

6. Agente conversa sobre o conteúdo normalmente
```

## Instalação do Qwen2-VL

```powershell
ollama pull qwen2-vl:7b
# ~6GB, roda em RTX 4060+ ou Apple Silicon 16GB+
```

## Frameworks de Apoio

- **`vision-local-context`** (GitHub agent-wrangler) — pipeline OCR + layout extraction + scene inference → LLM-ready context
- **SmolDocling** (IBM, 256M params) — conversão multimodal de documentos, extremamente leve
- **Docling** (IBM, 63.7k stars) — pipeline completo document → markdown

## Casos de Uso

1. **Foto de caderno físico:** OCR do texto manuscrito → NoteBlock
2. **Print de tela:** Extração de UI + texto → contexto pro agente
3. **Gráfico/Diagrama:** VLM descreve o gráfico → agente usa a descrição
4. **Documento escaneado:** MinerU2.5 (quando disponível) → layout-aware parsing
5. **Prova/Exercício:** Foto da questão → agente resolve

## Métricas de Performance

- Qwen2-VL 7B: ~2-5s por imagem (GPU)
- Tesseract: ~0.5-1s por imagem (CPU)
- Precisão OCR documentos: Qwen2-VL > 95%, Tesseract ~85-90%
