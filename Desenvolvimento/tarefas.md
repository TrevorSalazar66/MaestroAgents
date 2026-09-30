# Registro de Tarefas do Ciclo Ativo (`tarefas.md`)
## Fase 1: Infraestrutura Base, Gestão Multi-Projeto & Interfaces

---

## 📌 Status do Ciclo: Em Inicialização

Este documento rastreia e gerencia a execução das tarefas da **Fase 1**. Cada item é marcado como concluído `[x]` somente após o código ser implementado e validado nos testes de Camada 1.

---

## 📋 Lista de Tarefas da Fase 1

- [ ] **1.1. Ambiente & Dependências Base:** Configuração do ambiente virtual Python 3.10+ (`venv`) e criação do `requirements.txt` / `pyproject.toml` com todas as bibliotecas base (`fastapi`, `uvicorn`, `pydantic`, `requests`, `playwright`, `httpx`, `rich`, `prompt-toolkit`, `psutil`).
- [ ] **1.2. Workspace & Clean Slate (`src/core/workspace_manager.py`):** Implementação da classe `WorkspaceManager` para isolamento rígido de projetos e inicialização de ambientes limpos em `conferencias/<projeto>/`.
- [ ] **1.3. Persistência Blackboard (`src/core/memoria.py`):** Implementação da classe `BlackboardMemory` para leitura e escrita formatada do histórico em Markdown (`projeto_atual.md`).
- [ ] **1.4. API Gateway OpenAI Compatible (`src/api/gateway.py`):** Criação do servidor FastAPI com os endpoints `/v1/chat/completions` e `/v1/models`.
- [ ] **1.5. Terminal CLI (`src/cli/cli.py`):** Desenvolvimento do utilitário `maestro-cli` em Python com a biblioteca `Rich` para navegação por abas no console e atalhos de teclado (`Ctrl+P`).
- [ ] **1.6. Dashboard Web SPA 3 Colunas (`src/api/dashboard.py` & `static/`):** Construção da interface visual gráfica no navegador com layout de 3 colunas (Árvore de arquivos, Chat do debate ao vivo, Quadro Branco `.md` + IA Dedicada).
- [ ] **1.7. Telemetria de Hardware (`src/servicos/telemetria.py`):** Implementação do monitor de consumo de RAM, VRAM, CPU, latência (ms) e velocidade de geração (t/s).
- [ ] **1.8. Gestor de Cotas & Reset (`src/servicos/cotas_manager.py`):** Implementação do rastreador de consumo de tokens por provedor e cronômetros regressivos de reset.
- [ ] **1.9. Gestor Dinâmico de Provedores (`src/servicos/gestor_provedores.py`):** Módulo para cadastro e edição manual de chaves API, endpoints REST e seletores RPA.
- [ ] **1.10. Gerenciador de Arquivos & Backups (`src/servicos/file_manager.py`):** Módulo para navegação visual e manipulação de arquivos na pasta `Desenvolvimento/`, com gerador de backups `.tar.gz`.
- [ ] **1.11. Motor de IA Dedicada Externa (`src/agentes_especiais/agente_dedicado.py`):** Implementação do assistente fora do fluxo (Árbitro para impasses, Coletor de dados web/locais e Chat de suporte ao usuário).

---

## 🧪 Validação Exigida para Conclusão da Fase 1

1. **Testes de Camada 1 (Unitários):** Validação de schemas Pydantic, leitor/escritor de `.md`, gerenciador de workspace e cálculo de telemetria de RAM/VRAM.
2. **Log de Histórico de Ciclo:** Registro formal do encadeamento da Fase 1 em `docs/historico-ciclos/log_fase_1.md`.
3. **Commit Git:** Commit formal da Fase 1 na branch ativa.
