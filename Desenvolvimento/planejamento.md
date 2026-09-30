# Planejamento Estruturado do Projeto (Faseamento)
## Orquestrador Multi-Agentes de 3 Camadas (LLM Routing & Debate Automation)

---

## 📌 Visão Geral do Faseamento

O desenvolvimento do **Orquestrador Multi-Agentes de 3 Camadas** é organizado em 6 Fases rígidas e sequenciais. O projeto segue estritamente o protocolo de **Qualidade sobre Velocidade**, garantindo que cada fase só seja dada como concluída após passar pelos testes em 3 camadas e ser registrada em log de histórico de ciclo.

---

## 🗺️ Lista de Fases e Tópicos

### 🔵 Fase 1: Infraestrutura Base, Gestão Multi-Projeto & Interfaces
1.1. Configuração do ambiente virtual Python (`venv`) e dependências base.  
1.2. Gerenciador de Espaço de Trabalho e Isolamento Multi-Projeto (`workspace_manager.py`).  
1.3. Módulo de Persistência Blackboard (`memoria.py`).  
1.4. Servidor API Gateway FastAPI compatível com a API da OpenAI (`gateway.py`).  
1.5. Interface de Linha de Comando Interativa no Terminal (`cli.py` / `maestro-cli`).  
1.6. Interface Web Dashboard Local SPA de 3 Colunas (`dashboard.py` / `static/`).  
1.7. Módulo de Telemetria de Hardware & Rastreamento de RAM/VRAM (`telemetria.py`).  
1.8. Gestor de Cotas, Consumo de Tokens & Cronômetros de Reset (`cotas_manager.py`).  
1.9. Gestor Dinâmico de Provedores & Adição Manual de IAs (`gestor_provedores.py`).  
1.10. Gerenciador Avançado de Árvore de Arquivos, Pastas e Backups (`file_manager.py`).  
1.11. Motor de IA Dedicada Externa: Árbitro, Coletor Web & Chat de Suporte (`agente_dedicado.py`).  

### 🟢 Fase 2: Conectores de Inferência Externa (Camada 2 - 9router, Groq & OpenRouter)
2.1. Conector REST para Groq Cloud API (`conector_groq.py`).  
2.2. Conector REST para OpenRouter Free Tier (`conector_openrouter.py`).  
2.3. Integrador do Hub 9router Proxy (`conector_9router.py`).  
2.4. Avaliador de Complexidade de Prompts & Roteador Semântico (`roteador_semantico.py`).  

### 🟡 Fase 3: Automação RPA & Conectores Web Oficiais (Camada 3 - ChatGPT & Gemini Web)
3.1. Infraestrutura base do Playwright Python com Contexto Persistente (`rpa_base.py`).  
3.2. Scraper e Injetor de Prompts para ChatGPT Web (`scraper_chatgpt.py`).  
3.3. Scraper e Injetor de Prompts para Gemini Web (`scraper_gemini.py`).  
3.4. Sistema de Resiliência RPA, Tratamento de Captchas e Mudanças de Layout (`rpa_resilience.py`).  

### 🟠 Fase 4: Inferência Local & Resiliência Offline (Camada 1 - Hermes & Llama.cpp)
4.1. Conector para Servidor Llama.cpp e MLC LLM (`conector_hermes.py`).  
4.2. Monitor de Conectividade de Rede & Disparo Automático Offline (`network_monitor.py`).  
4.3. Indexador Passivo de Conhecimento e RAG Local (`indexador_rag.py`).  

### 🔴 Fase 5: O Maestro Moderador, Ferramentas Especiais & Loop em 7 Turnos
5.1. Motor de Orquestração Principal (`maestro.py`).  
5.2. Implementação do Loop de Debate de 7 Turnos com Refinamento de Código (`debate_engine.py`).  
5.3. Módulo de Gestão de Contingências e Matriz de Fallbacks (Cenários A a E) (`fallback_manager.py`).  
5.4. Ponte de Transmissão de Eventos em Tempo Real para Web UI & CLI (`ui_bridge.py`).  
5.5. Módulo de Intervenção Humana & Controle Manual do Debate (*Override*) (`override_manager.py`).  
5.6. Módulo de Injeção Direta de Chat no Fluxo de Debate (*In-Stream Chat*) (`instream_chat.py`).  
5.7. Sistema de Replay & Testes A/B de LLMs (`replay_engine.py`).  
5.8. Sandbox Isolado de Execução Segura de Código (`code_sandbox.py`).  
5.9. Gerador Automático de Documentação Técnica e Manuais (`docgen.py`).  

### 🟣 Fase 6: Testes em 3 Camadas, Sanitização & Validação Final
6.1. Execução de Testes Unitários de Camada 1 (Validação de Schemas, Telemetria, Sandbox, Interfaces e Sanitização).  
6.2. Execução de Testes de Integração de Camada 2 (Conectores, Playwright, Web UI, CLI, Agente Dedicado e Gateway).  
6.3. Execução de Varredura Sistêmica Crítica de Camada 3 e Simulações de Falha de Rede.  
6.4. Sanitização de Código, Remoção de Mocks e Geração da Documentação Final (`documentacao-final/`).  

---

## 📝 Detalhamento Explicativo dos Tópicos por Fase

### 🔵 Fase 1: Infraestrutura Base, Gestão Multi-Projeto & Interfaces

* **1.1. Configuração do ambiente virtual Python (`venv`) e dependências base:** Inicialização do repositório de desenvolvimento com Python 3.10+, criação de ambiente virtual isolado e especificação dos arquivos `requirements.txt` / `pyproject.toml` contendo bibliotecas essenciais (`fastapi`, `uvicorn`, `pydantic`, `requests`, `playwright`, `httpx`, `rich`, `prompt-toolkit`, `psutil`).
* **1.2. Gerenciador de Espaço de Trabalho e Isolamento Multi-Projeto (`workspace_manager.py`):** Criação da classe de governança que lê a pasta `Diretrizes/` e garante o isolamento rígido entre múltiplos projetos. O módulo cria pastas dedicadas por projeto (`conferencias/<nome-do-projeto>/`) e provê a alternância para uma pasta vazia ("Ambiente Limpo"), garantindo que nenhuma conversa antiga seja misturada, ao mesmo tempo em que preserva 100% dos logs e contextos dos projetos passados intactos sem nunca apagá-los.
* **1.3. Módulo de Persistência Blackboard (`memoria.py`):** Desenvolvimento do gerenciador de arquivo `.md` que atua como "quadro branco" auditável da sessão. Oferece funções seguras para leitura de contexto recente, escrita formatada com marcação de tempo/IA emissora e salvamento incremental no projeto ativo.
* **1.4. Servidor API Gateway FastAPI compatível com a API da OpenAI (`gateway.py`):** Construção da interface HTTP em `localhost:8000` expondo os endpoints padronizados `/v1/chat/completions` e `/v1/models`. Permite que ferramentas de desenvolvimento (como Antigravity IDE, extensões e clientes REST) consumam o orquestrador como se estivessem se comunicando com uma API padrão da OpenAI, injetando cabeçalhos HTTP com metadados de telemetria.
* **1.5. Interface de Linha de Comando Interativa no Terminal (`cli.py` / `maestro-cli`):** Desenvolvimento do ponto de entrada CLI para terminal com a biblioteca `Rich`. Oferece menus de navegação por abas no console, atalhos de teclado (ex: `Ctrl+P` para pausar debate), streaming colorido das IAs, comandos diretos (`--status`, `--telemetry`, `--add-ai`, `--arbitrate`, `--export`) e seleção de projetos.
* **1.6. Interface Web Dashboard Local SPA de 3 Colunas (`dashboard.py` / `static/`):** Criação da interface gráfica no navegador acessível via `http://localhost:8000/dashboard`. Estruturada em HTML5/CSS Vanilla e WebSockets, possui layout de 3 colunas (Árvore de arquivos, Chat do debate ao vivo com injeção *in-stream*, e Quadro Branco `.md` com chat lateral da IA Dedicada) e 5 abas principais: Debate, Telemetria/Cotas, Gerenciador de IAs, Workspaces/Projetos e Replay/Sandbox.
* **1.7. Módulo de Telemetria de Hardware & Rastreamento de RAM/VRAM (`telemetria.py`):** Módulo de monitoramento contínuo dos recursos locais via `psutil` e APIs do Llama.cpp. Calcula a alocação exata de memória RAM e VRAM ocupada pelos modelos locais (Hermes), uso de CPU, latência da primeira resposta (TTFT) e velocidade de geração em tokens por segundo (t/s).
* **1.8. Gestor de Cotas, Consumo de Tokens & Cronômetros de Reset (`cotas_manager.py`):** Serviço encarregado de rastrear o volume de tokens consumidos em cada provedor (Groq, OpenRouter, ChatGPT, Gemini Web). Calcula dinamicamente o percentual de uso e gerencia os cronômetros regressivos até o horário exato de reset de cada limite gratuito.
* **1.9. Gestor Dinâmico de Provedores & Adição Manual de IAs (`gestor_provedores.py`):** Módulo de configuração que permite cadastrar e editar dinamicamente novos provedores REST, adicionar chaves API, registrar novos seletores visuais para scrapers RPA de novas IAs Web e ajustar a prioridade das camadas de fallback.
* **1.10. Gerenciador Avançado de Árvore de Arquivos, Pastas e Backups (`file_manager.py`):** Módulo para explorar, criar, renomear, mover e excluir arquivos/pastas da árvore de desenvolvimento do projeto ativo, além de gerar backups compactados (`.tar.gz`) da sessão.
* **1.11. Motor de IA Dedicada Externa: Árbitro, Coletor Web & Chat de Suporte (`agente_dedicado.py`):** Implementação da IA auxiliar fora da Mesa Redonda. Exerce os papéis de Árbitro para resolver impasses de debate, Coletor para buscar documentação externa em background e Chat de Suporte direto ao usuário.

---

### 🟢 Fase 2: Conectores de Inferência Externa (Camada 2 - 9router, Groq & OpenRouter)

* **2.1. Conector REST para Groq Cloud API (`conector_groq.py`):** Implementação de cliente HTTP assíncrono direcionado à API da Groq Cloud, explorando modelos de altíssima velocidade (Llama 3.3 70B, Mixtral). Inclui controle de timeout rigoroso e tratamento de headers de limites de requisição.
* **2.2. Conector REST para OpenRouter Free Tier (`conector_openrouter.py`):** Desenvolvimento de conector para acesso aos modelos do tier gratuito da OpenRouter (DeepSeek-R1/V3, Qwen 2.5 Coder). O conector mapeia modelos disponíveis e efetua o parse padronizado de respostas.
* **2.3. Integrador do Hub 9router Proxy (`conector_9router.py`):** Módulo responsável por interagir com a infraestrutura do 9router. Gerencia a rotação automática de endpoints, chaveamento de proxies e seleção do modelo alternativo mais rápido em caso de instabilidade de um provedor específico.
* **2.4. Avaliador de Complexidade de Prompts & Roteador Semântico (`roteador_semantico.py`):** Algoritmo de classificação de requisições. Analisa o prompt recebido aplicando regras rápidas (contagem de tokens, presença de código/regex) e uma verificação semântica leve para categorizar a tarefa em: trivial, média ou complexa, definindo a rota inicial de processamento.

---

### 🟡 Fase 3: Automação RPA & Conectores Web Oficiais (Camada 3 - ChatGPT & Gemini Web)

* **3.1. Infraestrutura base do Playwright Python com Contexto Persistente (`rpa_base.py`):** Construção da camada de automação de navegador usando Playwright em Python. Configura a inicialização com `launch_persistent_context` apontando para o diretório de dados de usuário (`user_data_dir`), preservando cookies de autenticação e evitando verificações de login repetitivas.
* **3.2. Scraper e Injetor de Prompts para ChatGPT Web (`scraper_chatgpt.py`):** Script automatizado especializado em interagir com o site do ChatGPT. Localiza dinamicamente a área de texto, insere o prompt, aciona o envio, monitora o término da geração via DOM e extrai o texto gerado de forma limpa.
* **3.3. Scraper e Injetor de Prompts para Gemini Web (`scraper_gemini.py`):** Script especializado para a interface web do Google Gemini. Realiza injeção de texto, aguarda a finalização do streaming da resposta e extrai os blocos Markdown formatados.
* **3.4. Sistema de Resiliência RPA, Tratamento de Captchas e Mudanças de Layout (`rpa_resilience.py`):** Módulo de proteção contra falhas visuais. Detecta timeouts de renderização, presença de seletores ausentes (decorrentes de atualização da interface) ou desafios CAPTCHA, emitindo sinalizador de falha para que o Maestro efetue o desvio automático.

---

### 🟠 Fase 4: Inferência Local & Resiliência Offline (Camada 1 - Hermes & Llama.cpp)

* **4.1. Conector para Servidor Llama.cpp e MLC LLM (`conector_hermes.py`):** Cliente de comunicação local focado no modelo Hermes (Gemma 2B / Llama 3.2 3B quantizado). Conecta-se ao servidor local `localhost:8080` via REST com baixa latência e consumo otimizado de recursos.
* **4.2. Monitor de Conectividade de Rede & Disparo Automático Offline (`network_monitor.py`):** Serviço em background que monitora a saúde das interfaces de rede e a conectividade com a internet. Em caso de perda de sinal ou falha de DNS, dispara o sinalizador de chaveamento imediato para o Modo Offline na Camada 1.
* **4.3. Indexador Passivo de Conhecimento e RAG Local (`indexador_rag.py`):** Módulo que lê os arquivos `.md` dos debates concluídos de todos os projetos e gera embeddings vetoriais locais acumulados. Permite que o Hermes aprenda técnicas e soluções acumuladas inter-projetos sem carregar logs de conversas antigas.

---

### 🔴 Fase 5: O Maestro Moderador, Ferramentas Especiais & Loop em 7 Turnos

* **5.1. Motor de Orquestração Principal (`maestro.py`):** O cérebro central do sistema. Une a API Gateway, o gerenciador de workspace, os conectores e a lógica de debate. Coordena a atribuição de papéis a cada IA e controla o estado global da sessão.
* **5.2. Implementação do Loop de Debate de 7 Turnos com Refinamento de Código (`debate_engine.py`):** Execução da máquina de estados do debate:
  1. *Turno 1:* Expansão de Conceito.
  2. *Turno 2:* Crítica Arquitetural.
  3. *Turno 3:* Síntese de Consenso.
  4. *Turno 4:* Geração Inicial de Código.
  5. *Turno 5:* Code Review & Pentest Lógico.
  6. *Turno 6:* Refatoração & Otimização Final.
  7. *Turno 7:* Loop de Feedback de Erros de Testes.
* **5.3. Módulo de Gestão de Contingências e Matriz de Fallbacks (`fallback_manager.py`):** Implementação das regras operacionais dos 5 cenários definidos na idealização (Cenário A: Nominal; Cenário B: Falha RPA; Cenário C: Rate Limit Camada 2; Cenário D: Modo Offline Total; Cenário E: Requisição Trivial).
* **5.4. Ponte de Transmissão de Eventos em Tempo Real para Web UI & CLI (`ui_bridge.py`):** Módulo de comunicação bidirecional via WebSockets / SSE responsável por transmitir cada turno do debate, métricas de telemetria, contagem de tokens e status dos conectores ao vivo tanto para o Dashboard Web quanto para o Terminal CLI.
* **5.5. Módulo de Intervenção Humana & Controle Manual do Debate (*Override*) (`override_manager.py`):** Sistema de controle em tempo real que escuta comandos do usuário vindos do Dashboard Web ou da CLI (`Ctrl+P`). Permite pausar o debate entre turnos, editar manualmente a resposta de uma IA, forçar a escolha da próxima IA ou alternar imediatamente para o modo 100% offline.
* **5.6. Módulo de Injeção Direta de Chat no Fluxo de Debate (`instream_chat.py`):** Permite a injeção de prompts do usuário como mensagens ativas no meio da Mesa Redonda, tratando marcações `@` para direcionamento específico de IAs.
* **5.7. Sistema de Replay & Testes A/B de LLMs (`replay_engine.py`):** Mecanismo para carregar e reprisar conferências passadas alterando parâmetros de modelo para comparação visual de código.
* **5.8. Sandbox Isolado de Execução Segura de Código (`code_sandbox.py`):** Ambiente controlado que compila e executa trechos de código gerados nos Turnos 4 e 6, alimentando o Turno 7 com relatórios reais de erros/sucessos de execução.
* **5.9. Gerador Automático de Documentação Técnica e Manuais (`docgen.py`):** Compilador que transforma o repositório, o histórico de debates e os artefatos em manuais finais (README, PDF, HTML e OpenAPI specs).

---

### 🟣 Fase 6: Testes em 3 Camadas, Sanitização & Validação Final

* **6.1. Execução de Testes Unitários de Camada 1 (Validação de Schemas, Telemetria, Sandbox, Interfaces e Sanitização):** Testes automatizados focados nos conectores individuais, módulo de telemetria de RAM/VRAM, gestor de cotas, rotas do Dashboard Web, comandos CLI, sandbox de execução e integridade do salvamento no `.md`.
* **6.2. Execução de Testes de Integração de Camada 2 (Conectores, Playwright, Web UI, CLI, Agente Dedicado e Gateway):** Validação das pontes de comunicação direta entre a API Gateway, o Dashboard Web SPA de 3 colunas, a CLI, a automação Playwright, a IA Dedicada (Árbitro/Coletor), a API da Groq/OpenRouter e o servidor Llama.cpp local.
* **6.3. Execução de Varredura Sistêmica Crítica de Camada 3 e Simulações de Falha de Rede:** Testes de estresse sistêmicos simulando interrupção abrupta de internet, queda de navegadores RPA, esgotamento de cotas de API e intervenções manuais (*Override*) para garantir zero travamentos no gateway.
* **6.4. Sanitização de Código, Remoção de Mocks e Geração da Documentação Final (`documentacao-final/`):** Limpeza total de arquivos temporários e dados mockados, seguida da geração da documentação final unificada em `documentacao-final/` contendo `README.md`, `API_DOCS.md`, `MANUAL_USUARIO.md` e `ARQUITETURA_FINAL.md`.
