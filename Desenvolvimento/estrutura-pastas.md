# Governança de Arquitetura & Estrutura Viva de Pastas (`estrutura-pastas.md`)

---

## 📌 Regra Viva & Restritiva

Este documento é o guia oficial de governança de arquitetura viva para a pasta `Desenvolvimento/`. 
Tanto os desenvolvedores humanos quanto os assistentes de IA (Antigravity IDE / Gemini) DEVEM consultar este documento antes de criar qualquer novo arquivo ou subdiretório. Nenhuma alteração de estrutura é permitida sem alinhamento formal neste documento.

---

## 📂 Árvore Oficial de Diretórios de `Desenvolvimento/`

```text
Desenvolvimento/
├── idealizacao.md                  # Tríade 1: Conceito Humano & GDD do Orquestrador
├── planejamento.md                 # Tríade 2: Faseamento Estruturado (6 Fases)
├── funcoes.md                      # Tríade 3: Especificação Técnica Detalhada
├── estrutura-pastas.md            # Governança Viva de Arquitetura (Este Arquivo)
├── tarefas.md                      # Registro de Tarefas Ativas da Fase/Subfase Atual
│
├── conferencias/                   # Padrão Blackboard: Memória e Quadro Branco (.md)
│   └── <nome-do-projeto>/          # Diretório isolado por projeto
│       └── projeto_atual.md        # Quadro Branco vivo sincronizado do projeto
│
├── docs/                           # Documentações Internas de Desenvolvimento
│   ├── historico-ciclos/           # Logs auditáveis de cada subfase concluída
│   └── incidencias-e-refatoracoes/ # Logs de incidentes e análise de custo de refatoração
│
├── src/                            # Código-Fonte Principal (Python 3.10+)
│   ├── core/                       # Núcleo de Governança e Orquestração
│   │   ├── workspace_manager.py    # Isolamento de projetos e Clean Slate
│   │   ├── memoria.py              # Leitura/escrita do Quadro Branco (.md)
│   │   ├── maestro.py              # Motor principal de orquestração do fluxo
│   │   ├── debate_engine.py        # Máquina de estados do debate em 7 turnos
│   │   └── fallback_manager.py     # Gestão dos 5 cenários de contingência
│   │
│   ├── api/                        # Servidor HTTP Gateway & Interfaces de Conexão
│   │   ├── gateway.py              # Servidor FastAPI compatível com OpenAI REST
│   │   ├── dashboard.py            # Servidor da interface Web SPA Local (3 colunas)
│   │   └── ui_bridge.py            # Transmissor WebSockets/SSE para Web UI & CLI
│   │
│   ├── cli/                        # Interface de Linha de Comando no Terminal
│   │   └── cli.py                  # Ponto de entrada CLI interativo Rich (`maestro-cli`)
│   │
│   ├── conectores/                 # Conectores de Inferência das 3 Camadas
│   │   ├── camada1_hermes/         # Conector Llama.cpp / MLC LLM local
│   │   │   └── conector_hermes.py
│   │   ├── camada2_apis/           # Conectores REST externos
│   │   │   ├── conector_groq.py
│   │   │   ├── conector_openrouter.py
│   │   │   ├── conector_9router.py
│   │   │   └── roteador_semantico.py
│   │   └── camada3_web/            # Scrapers e automação RPA Playwright
│   │       ├── rpa_base.py
│   │       ├── scraper_chatgpt.py
│   │       ├── scraper_gemini.py
│   │       └── rpa_resilience.py
│   │
│   ├── agentes_especiais/          # Agentes de IA Auxiliares & Ferramentas
│   │   ├── agente_dedicado.py      # IA Árbitro, Coletora Web & Chat de Suporte
│   │   ├── instream_chat.py        # Injeção direta de chat no fluxo do debate
│   │   ├── override_manager.py     # Controle manual humano (pausa, troca, edição)
│   │   ├── replay_engine.py        # Replay de conferências & Testes A/B
│   │   ├── code_sandbox.py         # Sandbox seguro de execução de código
│   │   └── docgen.py               # Gerador automático de documentação técnica
│   │
│   └── servicos/                   # Serviços Utilitários & Telemetria
│       ├── telemetria.py           # Monitor de hardware (RAM, VRAM, CPU, ms, t/s)
│       ├── cotas_manager.py        # Monitor de consumo de tokens e reset
│       ├── gestor_provedores.py    # Cadastro dinâmico de IAs e chaves
│       ├── file_manager.py         # Gerenciador visual de árvore de arquivos
│       ├── network_monitor.py      # Monitor de conectividade de rede (offline trigger)
│       └── indexador_rag.py        # Indexador local de memória RAG
│
├── static/                         # Assets do Dashboard Web SPA (Local)
│   ├── index.html                  # HTML5 do Dashboard (Layout de 3 Colunas)
│   ├── css/
│   │   └── style.css               # Estilização Vanilla CSS
│   └── js/
│       ├── app.js                  # Lógica assíncrona principal
│       ├── debate_view.js          # Componente do chat do debate ao vivo
│       ├── telemetry_view.js       # Componente de telemetria e cotas
│       └── file_tree.js            # Componente do explorador de arquivos
│
├── tests/                          # Suíte de Testes em 3 Camadas
│   ├── camada1_unitarios/          # Testes unitários dos módulos e schemas
│   ├── camada2_integração/         # Testes de integração entre conectores, gateway e UI
│   └── camada3_sistemica/          # Testes de estresse, simulação de falha e pentest
│
└── documentacao-final/             # Documentação de Produção (Gerada ao Fim do Projeto)
```
