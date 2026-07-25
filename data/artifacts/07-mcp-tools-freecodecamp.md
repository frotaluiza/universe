# MCP Tools: Integração das Ferramentas do freeCodeCamp

## Referência

"Open Source Tools Every STEM Student Should Know About" — freeCodeCamp, Jun 2026
https://www.freecodecamp.org/news/open-source-tools-every-stem-student-should-know-about/

## Ferramentas e Aplicação como MCP Tools

### 1. Jupyter Notebook
**Uso:** NoteBlock interativo com código executável
**MCP Tool:** `execute_jupyter_cell(code: str, kernel: str = "python3") -> str`
**Casos:** Cálculos numéricos, simulações, visualização de dados, análise exploratória
**Stack:** `jupyter-client` + `nbformat` + kernel local

### 2. Git + GitHub
**Uso:** Versionamento de NoteBlocks e artefatos
**MCP Tool:** `git_commit(message: str, files: list[str])`, `git_branch(name: str)`, `git_push()`
**Casos:** Salvar versão de um NoteBlock, criar branch de planejamento, sincronizar artefatos
**Stack:** GitPython ou subprocess git

### 3. VS Code
**Uso:** Edição avançada de markdown e código
**MCP Tool:** `vscode_open(file: str)`, `vscode_diff(file_a: str, file_b: str)`
**Casos:** Usuário quer editar um NoteBlock no VS Code, comparar versões

### 4. Blender
**Uso:** Visualização científica 3D
**MCP Tool:** `blender_render(script: str, output: str)`
**Casos:** Gerar visualização 3D a partir de dados ou equações
**Stack:** `bpy` (Blender Python API) via subprocess

### 5. OBS Studio
**Uso:** Gravação de sessões de estudo/apresentação
**MCP Tool:** `obs_record(scene: str, duration: int)`
**Casos:** Gravar explicação do usuário, criar videoaulas

### 6. GeoGebra
**Uso:** Visualização matemática interativa
**MCP Tool:** `geogebra_plot(equation: str, range: tuple)`
**Casos:** Plotar funções, explorar transformações, visualizar geometria

## Priorização para Implementação

| Prioridade | Ferramenta | Justificativa |
|------------|------------|---------------|
| 🔴 Alta | Jupyter Notebook | Mais versátil para STEM, integração direta com NoteBlocks |
| 🔴 Alta | Git + GitHub | Essencial para o fluxo de versionamento do universo |
| 🟡 Média | GeoGebra | Específico para matemática, acionável via Wolfram MCP |
| 🟢 Baixa | VS Code | NoteBlock já tem editor embutido |
| 🟢 Baixa | Blender | Caso de uso nichado (visualização 3D) |
| 🟢 Baixa | OBS Studio | Caso de uso nichado (gravação) |

## Arquitetura MCP

```
NoteBlock ──► Ação do usuário ──► MCP Router
                                     │
                  ┌──────────────────┼──────────────────┐
                  ▼                  ▼                  ▼
           Jupyter MCP          Git MCP           GeoGebra MCP
                  │                  │                  │
                  ▼                  ▼                  ▼
           kernel local        universe/.git      plot image
```

## Implementação (Jupyter como primeira MCP)

```python
# MCP Tool: Jupyter
@mcp.tool()
async def execute_jupyter(code: str, kernel: str = "python3") -> str:
    """
    Executa código Python em um kernel Jupyter.
    Retorna output (stdout + stderr + display data).
    """
    km = KernelManager(kernel_name=kernel)
    km.start_kernel()
    client = km.client()
    # ... execução, captura de output, timeout de 30s
    return output
```
