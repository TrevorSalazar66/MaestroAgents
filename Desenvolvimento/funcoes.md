# Especificação Técnica Detalhada das Funções (`funcoes.md`)
## Orquestrador Multi-Agentes de 3 Camadas (LLM Routing & Debate Automation)

---

## 📌 Visão Geral Técnica

Este documento fornece a especificação técnica detalhada de cada módulo, arquivo, classe, função, tipos de dados, schemas Pydantic, endpoints REST, eventos WebSockets, dependências e integrações do **Orquestrador Multi-Agentes de 3 Camadas**.

Ele orienta estritamente a fase de implementação, garantindo adesão total à Clean Architecture, tipagem rigorosa em Python 3.10+ e conformidade com as diretrizes do repositório em `Diretrizes/`.

---

## 💻 Metadata & Perfil de Hospedagem / Dispositivos Alvo

* **Dispositivo Alvo Primário:** PC com Sistema Operacional Linux (Ubuntu/Debian/Fedora).
* **Dispositivo Alvo Secundário:** Dispositivo Mobile Android executando Termux / Python.
* **Linguagem Oficial:** Python 3.10+ (Tipagem estrita via `typing` / `pydantic`).
* **Frontend Web Local:** HTML5 Vanilla, CSS3 Vanilla com variáveis CSS, JavaScript ES6+ assíncrono (WebSockets / SSE).
* **Terminal CLI:** `Rich` + `Prompt-Toolkit`.
* **Banco de Dados / Persistência:** Arquivos `.md` (Padrão Blackboard), arquivos `.json` de configuração e SQLite leve para telemetria.
* **Custo de Operação:** R$ 0,00 (Custo Zero Garantido via Tiers Gratuitos e Modelos Locais).

---

## 🏗️ Mapeamento de Arquivos, Módulos & Funções

---

### 1. Núcleo de Infraestrutura Base & Governança (Fase 1)

#### 1.1. `workspace_manager.py` (Gerenciador de Projetos & Isolamento)
* **Classe:** `WorkspaceManager`
* **Propósito:** Garante o isolamento de pastas por projeto e gerencia a alternância/criação de workspaces ("Ambiente Limpo" em nova pasta vazia), preservando 100% dos logs e históricos de projetos passados intactos sem nunca apagá-los.
* **Funções:**
  * `get_active_project() -> str`: Retorna o nome do projeto atualmente ativo no workspace.
  * `set_active_project(project_name: str) -> bool`: Alterna o projeto ativo e carrega a pasta do projeto correspondente.
  * `create_clean_workspace(project_name: str) -> dict`: Cria a pasta isolada para um novo projeto (`conferencias/<project_name>/`), inicializando um Quadro Branco sem contexto de conversas anteriores daquele app específico (sem deletar logs de projetos passados).
  * `list_projects() -> List[dict]`: Lista todos os projetos cadastrados com metadados (tamanho, data de criação, status).
  * `export_project_archive(project_name: str, target_dir: str) -> str`: Compacta todo o repositório do projeto em um arquivo `.tar.gz` para backup.

#### 1.2. `memoria.py` (Módulo de Persistência Blackboard)
* **Classe:** `BlackboardMemory`
* **Propósito:** Manipulação segura e auditável do arquivo de contexto em Markdown (`projeto_atual.md`).
* **Funções:**
  * `read_recent_context(max_tokens: int = 4000) -> str`: Lê as últimas seções do arquivo `.md` respeitando o limite de tokens especificado.
  * `append_turn_entry(agent_name: str, role: str, content: str, metadata: dict) -> None`: Formata e escreve a resposta de um turno no arquivo `.md` com carimbo de data/hora e metadados de modelo.
  * `get_full_history() -> str`: Retorna o conteúdo completo do arquivo Markdown da conferência ativa.
  * `clear_blackboard() -> None`: Reseta o arquivo `.md` da sessão ativa do projeto atual (preservando backups).

#### 1.3. `gateway.py` (Servidor API Gateway FastAPI)
* **Aplicação:** `FastAPI` + `Uvicorn`
* **Propósito:** Expor a interface HTTP em `localhost:8000` compatível com a API da OpenAI e endpoints de dashboard.
* **Endpoints:**
  * `POST /v1/chat/completions`: Recebe requisições no formato OpenAI (`ChatCompletionRequest`), dispara o motor do Maestro e retorna `ChatCompletionResponse` com cabeçalhos HTTP customizados (`X-Maestro-Layer`, `X-Maestro-RAM-Used`, `X-Maestro-Quota-Reset`).
  * `GET /v1/models`: Lista os modelos disponíveis nas 3 camadas.
  * `GET /dashboard`: Serve os arquivos estáticos da interface SPA Web.
  * `WS /ws/events`: Socket bidirecional para streaming em tempo real do debate, telemetria e logs.

#### 1.4. `cli.py` (Interface de Linha de Comando Interativa - `maestro-cli`)
* **Classe:** `MaestroCLI`
* **Propósito:** Ponto de entrada para o terminal com navegação rica via biblioteca `Rich`.
* **Funções:**
  * `run_cli() -> None`: Inicializa a TUI no console com captura de teclado.
  * `render_dashboard_tui() -> None`: Desenha os painéis divididos no terminal (Debate, Telemetria, Cotas, Chat).
  * `handle_shortcuts(key: str) -> None`: Processa atalhos de teclado (ex: `Ctrl+P` para pausar o debate, `Tab` para trocar abas).
  * `parse_args() -> argparse.Namespace`: Processa argumentos de linha de comando (`--project`, `--status`, `--offline`, `--add-ai`, `--arbitrate`, `--export`).

#### 1.5. `dashboard.py` / `static/` (Interface Web Local SPA 3 Colunas)
* **Estrutura de Arquivos:** `static/index.html`, `static/css/style.css`, `static/js/app.js`
* **Propósito:** Interface visual do navegador em 3 colunas.
* **Funções JS Front-End:**
  * `connectWebSocket()`: Conecta ao endpoint `/ws/events` para receber dados em tempo real.
  * `renderDebateCard(turnData)`: Renderiza os cartões dos 7 turnos do debate na coluna central.
  * `updateTelemetryGauges(metrics)`: Atualiza os velocímetros de RAM, VRAM e latência na aba de Telemetria.
  * `sendInStreamMessage(text, targetAi)`: Envia mensagem direta do usuário para a Mesa Redonda.
  * `interactDedicatedAgent(message)`: Comunica-se com a IA Auxiliar no chat lateral da coluna direita.

#### 1.6. `telemetria.py` (Monitoramento de Hardware Local)
* **Classe:** `HardwareTelemetry`
* **Propósito:** Coleta métricas de consumo do sistema local e do servidor Llama.cpp.
* **Funções:**
  * `get_ram_usage() -> dict`: Retorna uso total, livre e percentual da memória RAM do sistema (`psutil`).
  * `get_vram_usage() -> dict`: Retorna a VRAM alocada pela GPU/Llama.cpp via `nvidia-smi` ou API do servidor local.
  * `get_cpu_percent() -> float`: Retorna a porcentagem de utilização de CPU.
  * `get_telemetry_snapshot() -> dict`: Consolida todas as métricas em um objeto JSON de telemetria.

#### 1.7. `cotas_manager.py` (Gestor de Cotas & Tokens)
* **Classe:** `QuotaManager`
* **Propósito:** Monitorar limites de requisição e calcular tempo de reset.
* **Funções:**
  * `track_usage(provider: str, input_tokens: int, output_tokens: int) -> None`: Incrementa os contadores de uso de um provedor.
  * `get_quota_status(provider: str) -> dict`: Retorna o percentual consumido e o tempo restante para o reset.
  * `get_all_resets() -> List[dict]`: Lista todos os cronômetros de reset ativos.

#### 1.8. `gestor_provedores.py` (Cadastro Dinâmico de IAs)
* **Classe:** `ProviderManager`
* **Propósito:** Permite adicionar e editar provedores de IA sem reiniciar o servidor.
* **Funções:**
  * `register_provider(config: dict) -> bool`: Cadastra uma nova chave API, endpoint REST ou seletor RPA.
  * `update_provider(provider_id: str, data: dict) -> bool`: Otimiza parâmetros de um provedor existente.
  * `list_active_providers() -> List[dict]`: Retorna a lista de todas as IAs ativas em cada camada.

#### 1.9. `file_manager.py` (Explorador de Arquivos do Projeto)
* **Classe:** `ProjectFileManager`
* **Propósito:** Gerencia a árvore de arquivos de desenvolvimento do projeto ativo.
* **Funções:**
  * `list_dir_tree(rel_path: str = "") -> List[dict]`: Retorna a estrutura de diretórios e arquivos em formato de árvore JSON.
  * `read_file_content(file_path: str) -> str`: Lê o texto de qualquer arquivo dentro de `Desenvolvimento/`.
  * `write_file_content(file_path: str, content: str) -> bool`: Salva edições feitas no código pelo usuário ou pelas IAs.

#### 1.10. `agente_dedicado.py` (IA Auxiliar Externa ao Fluxo)
* **Classe:** `DedicatedAgentEngine`
* **Propósito:** Gerencia a IA dedicada que atua como Árbitro, Coletor de Informações e Chat de Suporte.
* **Funções:**
  * `arbitrate_conflict(debate_context: str, contradiction_summary: str) -> str`: Analisa discordâncias entre IAs no debate e emite a decisão técnica de desempate.
  * `collect_web_info(query: str) -> str`: Executa busca em segundo plano e retorna resumo formatado para injeção no `.md`.
  * `handle_support_chat(user_message: str) -> str`: Responde a perguntas diretas do usuário na barra lateral.

---

### 2. Conectores de Inferência Externa (Camada 2 - Fase 2)

#### 2.1. `conector_groq.py` (Groq Cloud API)
* **Classe:** `GroqConnector`
* **Função:** `send_prompt(model: str, messages: List[dict], timeout: float = 10.0) -> dict`: Envia chamadas de altíssima velocidade para a API da Groq Cloud (Llama 3.3 70B, Mixtral).

#### 2.2. `conector_openrouter.py` (OpenRouter Free Tier)
* **Classe:** `OpenRouterConnector`
* **Função:** `send_prompt(model: str, messages: List[dict]) -> dict`: Faz requisições para os modelos gratuitos da OpenRouter (DeepSeek-R1/V3, Qwen 2.5 Coder).

#### 2.3. `conector_9router.py` (Hub 9router Proxy)
* **Classe:** `NineRouterProxy`
* **Função:** `route_request(prompt: str, preferred_tier: str) -> dict`: Gerencia a rotação automática de chaves e endpoints dentro do 9router.

#### 2.4. `roteador_semantico.py` (Classificador de Requisições)
* **Classe:** `SemanticRouter`
* **Função:** `classify_prompt(prompt: str) -> str`: Classifica a intenção do prompt em `TRIVIAL`, `MEDIA` ou `COMPLEXA` com base na contagem de tokens e análise de intenção.

---

### 3. Automação RPA Web Oficial (Camada 3 - Fase 3)

#### 3.1. `rpa_base.py` (Infraestrutura Playwright)
* **Classe:** `PlaywrightRPABase`
* **Função:** `get_persistent_context(user_data_dir: str)`: Abre instância do Chromium preservando a sessão logada do usuário.

#### 3.2. `scraper_chatgpt.py` (ChatGPT Web RPA)
* **Classe:** `ChatGPTScraper`
* **Função:** `send_prompt_web(prompt: str) -> str`: Injeta prompt no campo de texto do ChatGPT Web e extrai a resposta final via DOM.

#### 3.3. `scraper_gemini.py` (Gemini Web RPA)
* **Classe:** `GeminiScraper`
* **Função:** `send_prompt_web(prompt: str) -> str`: Injeta prompt no site do Google Gemini Web e raspa a resposta gerada.

#### 3.4. `rpa_resilience.py` (Resiliência & Captcha)
* **Classe:** `RPAResilienceHandler`
* **Função:** `handle_visual_error(exception: Exception) -> bool`: Detecta mudanças de layout ou desafios visuais e sinaliza fallback imediato para o Maestro.

---

### 4. Inferência Local & Resiliência Offline (Camada 1 - Fase 4)

#### 4.1. `conector_hermes.py` (Servidor Llama.cpp Local)
* **Classe:** `HermesLocalConnector`
* **Função:** `send_prompt(prompt: str, params: dict) -> dict`: Envia requisições ao servidor Llama.cpp em `localhost:8080` (modelos Hermes / Gemma 2B).

#### 4.2. `network_monitor.py` (Monitor de Rede)
* **Classe:** `NetworkMonitor`
* **Função:** `check_online_status() -> bool`: Efetua pings periódicos para verificar se o sistema deve operar online ou no Modo Offline Local.

#### 4.3. `indexador_rag.py` (RAG Local de Memória & Aprendizado Global)
* **Classe:** `LocalRAGIndexer`
* **Função:** `index_session(markdown_filepath: str) -> None`: Converte logs de debates finalizados de todos os projetos em embeddings vetoriais globais. Permite que o modelo local Hermes acumule aprendizado técnico inter-projetos sem misturar o histórico de conversas passadas entre aplicativos.

---

### 5. O Maestro, Ferramentas Especiais & Orquestração (Fase 5)

#### 5.1. `maestro.py` (Cérebro Central)
* **Classe:** `MaestroOrchestrator`
* **Função:** `process_user_request(request_data: dict) -> dict`: Recebe a solicitação do usuário, avalia o estado das 3 camadas e coordena a execução do debate.

#### 5.2. `debate_engine.py` (Motor do Debate em 7 Turnos)
* **Classe:** `DebateEngine`
* **Função:** `execute_7_turn_loop(initial_prompt: str) -> dict`: Executa sequencialmente a máquina de estados dos 7 turnos (Expansão $\rightarrow$ Crítica $\rightarrow$ Síntese $\rightarrow$ Código Inicial $\rightarrow$ Review $\rightarrow$ Refatoração $\rightarrow$ Feedback de Testes).

#### 5.3. `fallback_manager.py` (Gestor de Contingências)
* **Classe:** `FallbackManager`
* **Função:** `apply_contingency(scenario_id: str, error_details: dict) -> dict`: Aplica as regras operacionais dos Cenários A, B, C, D ou E em caso de falha de qualquer componente.

#### 5.4. `ui_bridge.py` (Ponte de Eventos em Tempo Real)
* **Classe:** `UIBridge`
* **Função:** `broadcast_event(event_type: str, payload: dict) -> None`: Transmite eventos do debate, logs e telemetria instantaneamente via WebSockets para o Dashboard Web e para a CLI.

#### 5.5. `override_manager.py` (Controle Manual Humano)
* **Classe:** `OverrideManager`
* **Funções:**
  * `pause_debate() -> None`: Pausa a execução do debate entre turnos.
  * `substitute_agent(turn_number: int, new_agent_id: str) -> None`: Altera a IA responsável por um determinado turno.
  * `edit_turn_output(turn_number: int, new_text: str) -> None`: Altera o texto gerado por uma IA antes que ele seja repassado para o próximo turno.

#### 5.6. `instream_chat.py` (Injeção de Mensagens no Debate)
* **Classe:** `InStreamChatHandler`
* **Função:** `inject_user_message(turn_number: int, message: str, target_ai: str = None) -> None`: Injeta a instrução enviada pelo usuário diretamente no fluxo da Mesa Redonda.

#### 5.7. `replay_engine.py` (Sistema de Replay & Testes A/B)
* **Classe:** `ReplayEngine`
* **Função:** `load_and_replay_session(session_id: str, alternative_models: dict) -> dict`: Reprisa um debate anterior trocando modelos para comparação visual.

#### 5.8. `code_sandbox.py` (Execução Segura de Código)
* **Classe:** `CodeSandbox`
* **Função:** `execute_generated_code(code_snippet: str, language: str) -> dict`: Compila e executa o código gerado em um ambiente isolado, alimentando o Turno 7 com o relatório de saída/erros de execução.

#### 5.9. `docgen.py` (Gerador de Documentação)
* **Classe:** `DocGenExporter`
* **Função:** `generate_final_docs(project_name: str) -> dict`: Compila os arquivos de código e o histórico do debate gerando documentos finais formatados (PDF, HTML, README.md e OpenAPI specs).

---

## 🔄 Fluxo de Dados & Tipagem de Schemas (Pydantic)

### Schema de Entrada do Gateway (`ChatCompletionRequest`):
```python
from pydantic import BaseModel
from typing import List, Optional

class Message(BaseModel):
    role: str
    content: str
    name: Optional[str] = None

class ChatCompletionRequest(BaseModel):
    model: str
    messages: List[Message]
    temperature: Optional[float] = 0.7
    project_name: Optional[str] = "default"
    mode: Optional[str] = "debate" # "debate", "direct", "offline"
```

### Schema de Evento de Telemetria (`TelemetrySnapshot`):
```python
class TelemetrySnapshot(BaseModel):
    timestamp: str
    ram_usage_mb: float
    ram_percent: float
    vram_usage_mb: float
    cpu_percent: float
    active_layer: int # 1, 2 ou 3
    active_model: str
    active_provider: str
    tokens_per_second: float
    time_to_first_token_ms: float
```

---

## 🎯 Protocolo de Validação & Conclusão da Tríade

Com a revisão rigorosa dos conceitos de preservação de contexto e gestão multi-projeto nos 3 arquivos (`idealizacao.md`, `planejamento.md`, `funcoes.md`), a **Tríade de Especificação** está 100% alinhada à sua visão de arquitetura.
