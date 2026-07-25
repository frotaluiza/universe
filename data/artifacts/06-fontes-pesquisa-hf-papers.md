# Fontes de Pesquisa — HuggingFace Trending Papers (Julho 2026)

Registro de todos os papers analisados nesta sessão de planejamento, vinculados ao projeto Ariadne.

## Papers Analisados

### 1. SkillOpt: Executive Strategy for Self-Evolving Agent Skills
- **arXiv:** 2605.23904
- **Autores:** Yifan Yang, Ziyang Gong, Weiquan Huang et al. (Microsoft Research)
- **Tags:** `agent_skills`, `self_improvement`, `optimization`
- **Stars:** 14.9k | **Upvotes:** 262
- **Link:** https://arxiv.org/abs/2605.23904
- **Código:** https://github.com/microsoft/SkillOpt
- **Aplicação:** Fundação do Test Agent Loop na Ariadne
- **Origem:** `huggingface_papers`
- **Tipo de Fonte:** `arquitetura`

### 2. Unlimited OCR Works
- **arXiv:** 2606.23050
- **Autores:** Youyang Yin, Huanhuan Liu et al. (Baidu)
- **Tags:** `ocr`, `document_parsing`, `attention`
- **Stars:** 18.7k | **Upvotes:** 63
- **Link:** https://arxiv.org/abs/2606.23050
- **Código:** https://github.com/baidu/Unlimited-OCR
- **Aplicação:** OCR de documentos longos (R-SWA como mecanismo de atenção)
- **Origem:** `huggingface_papers`
- **Tipo de Fonte:** `referencia`

### 3. MinerU2.5: Decoupled VLM for High-Resolution Document Parsing
- **arXiv:** 2509.22186
- **Autores:** Junbo Niu, Zheng Liu et al. (OpenDataLab)
- **Tags:** `document_parsing`, `vlm`, `ocr`
- **Stars:** 75.6k | **Upvotes:** 176
- **Link:** https://arxiv.org/abs/2509.22186
- **Código:** https://github.com/opendatalab/MinerU
- **Aplicação:** Pipeline principal de RAG de PDFs (coarse-to-fine parsing)
- **Origem:** `huggingface_papers`
- **Tipo de Fonte:** `arquitetura`

### 4. Mem0: Building Production-Ready AI Agents with Scalable Long-Term Memory
- **arXiv:** 2504.19413
- **Autores:** Prateek Chhikara, Dev Khant et al. (Mem0 AI)
- **Tags:** `memory`, `agents`, `graph`
- **Stars:** 61.6k | **Upvotes:** 67
- **Link:** https://arxiv.org/abs/2504.19413
- **Código:** https://github.com/mem0ai/mem0
- **Aplicação:** Memória gráfica de longo prazo para o Orquestrador
- **Origem:** `huggingface_papers`
- **Tipo de Fonte:** `arquitetura`

### 5. DataFlow: LLM-Driven Data Preparation Framework
- **arXiv:** 2512.16676
- **Autores:** Hao Liang, Xiaochen Ma et al. (Peking University)
- **Tags:** `data_preparation`, `pipeline`, `llm`
- **Stars:** 6.89k | **Upvotes:** 225
- **Link:** https://arxiv.org/abs/2512.16676
- **Código:** https://github.com/OpenDCAI/DataFlow
- **Aplicação:** Pipeline de preparação de dados pro RAG do Orquestrador
- **Origem:** `huggingface_papers`
- **Tipo de Fonte:** `referencia`

### 6. ResearchStudio-Idea: Evidence-Grounded Research Ideation
- **arXiv:** 2607.04439
- **Autores:** Qihao Zhao, Yangyu Huang et al. (Microsoft Research)
- **Tags:** `research`, `ideation`, `skills`
- **Stars:** 1.78k | **Upvotes:** 60
- **Link:** https://arxiv.org/abs/2607.04439
- **Código:** https://github.com/microsoft/ResearchStudio
- **Aplicação:** Skill de pesquisa acadêmica assistida (Paper-Search, Scoop-Check, IdeaSpark)
- **Origem:** `huggingface_papers`
- **Tipo de Fonte:** `arquitetura`

### 7. Moonshine: Speech Recognition for Live Transcription
- **arXiv:** 2410.15608
- **Autores:** Nat Jeffries, Evan King et al. (Useful Sensors)
- **Tags:** `asr`, `speech_recognition`, `edge`
- **Stars:** 10.4k | **Upvotes:** 13
- **Link:** https://arxiv.org/abs/2410.15608
- **Código:** https://github.com/usefulsensors/moonshine
- **Aplicação:** Alternativa leve ao Whisper para transcrição de voz (edge devices)
- **Origem:** `huggingface_papers`
- **Tipo de Fonte:** `referencia`

## Mapa de Aplicações

```
SkillOpt ──────────► Test Agent Loop
                        │
Mem0 ◄──────────────────┴──► Grafo de Memória
                        │
MinerU2.5 ◄────────────┴──► RAG PDF
                        │
Unlimited OCR ◄────────┴──► OCR longo
                        │
ResearchStudio ◄────────┴──► Pesquisa Acadêmica
                        │
DataFlow ◄─────────────┴──► Preparação de Dados
                        │
Moonshine ◄────────────┴──► Voz (futuro)
```
