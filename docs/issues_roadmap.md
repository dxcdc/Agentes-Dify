# 📋 Roteiro de Issues & Backlog de Tarefas (CDC Receitas)

Este documento contém o catálogo de **GitHub Issues** estruturadas e organizadas por etapas de execução para acompanhamento no GitHub do projeto.

---

## 📌 Etapa 1: Implantação Básica e RAG da Wiki

### 🔹 Issue #1: Deploy do Dify Self-Hosted + Proxy Reverso Nginx
*   **Título**: `[Infra] Deploy do Dify Self-Hosted e subdomínio automatiza.cdc.org.br`
*   **Rótulos**: `infraestrutura`, `dify`, `prioridade-alta`
*   **Descrição**:
    ```markdown
    ## 🎯 Objetivo
    Subir os containers do Dify via Docker Compose na VPS e configurar o proxy reverso Nginx para responder com SSL no subdomínio `https://automatiza.cdc.org.br/`.

    ## ⚙️ Tarefas
    - [ ] Configurar variáveis locais no arquivo `.env` a partir do `infra/docker-compose-dify.yml`.
    - [ ] Executar o container: `docker compose -f infra/docker-compose-dify.yml up -d`.
    - [ ] Aplicar o arquivo de configuração `infra/nginx-dify.conf` no Nginx do servidor.
    - [ ] Gerar certificado SSL gratuito via Certbot: `sudo certbot --nginx -d automatiza.cdc.org.br`.
    - [ ] Validar acesso à Console Web do Dify no navegador.
    ```

---

### 🔹 Issue #2: Conexão da Wiki em Markdown ao Dify (Base Vetorial)
*   **Título**: `[Agente] Indexação da Wiki em Markdown no Dify (RAG)`
*   **Rótulos**: `agente-ia`, `rag`, `conhecimento`
*   **Descrição**:
    ```markdown
    ## 🎯 Objetivo
    Conectar o repositório da Wiki ao Dify para permitir respostas automáticas a dúvidas institucionais.

    ## ⚙️ Tarefas
    - [ ] Criar a aplicação no Dify do tipo *Chatbot / Workflow*.
    - [ ] Importar os arquivos Markdown da Wiki no módulo de *Datasets / Conhecimento*.
    - [ ] Configurar o modelo **Gemini 1.5 Flash** (via Google AI Studio) com limite de taxa (Rate Limiting).
    - [ ] Importar e calibrar os prompts de sistema a partir de `docs/prompt_ia.md`.
    - [ ] Realizar testes de perguntas e respostas no console do Dify.
    ```

---

### 🔹 Issue #3: Implantação da Ponte de Webhooks Mattermost ➔ Dify
*   **Título**: `[API] Implantação da Ponte de Webhooks (FastAPI) para Mattermost`
*   **Rótulos**: `api`, `mattermost`, `webhooks`
*   **Descrição**:
    ```markdown
    ## 🎯 Objetivo
    Colocar em produção o script intermediário de webhooks para integrar as mensagens dos canais do Mattermost com a API do Dify.

    ## ⚙️ Tarefas
    - [ ] Fazer o deploy do código contido em `api/webhook-bridge/` na VPS.
    - [ ] Configurar um *Outgoing Webhook* no Mattermost apontando para `http://localhost:8000/webhook`.
    - [ ] Configurar um *Incoming Webhook* no Mattermost e cadastrar a URL nas variáveis de ambiente.
    - [ ] Testar a menção do bot `@cdc-conhecimento` em um canal de testes e verificar a resposta.
    ```

---

## 📌 Etapa 2: Homologação do Mattermost Agents

### 🔹 Issue #4: Testes Isolados do Plugin Mattermost Agents
*   **Título**: `[Agente] Homologação do Mattermost Agents para Resumo de Canais e Threads`
*   **Rótulos**: `mattermost-agents`, `homologacao`
*   **Descrição**:
    ```markdown
    ## 🎯 Objetivo
    Instalar e homologar as funcionalidades nativas do plugin Mattermost Agents em ambiente de testes, sem duplicar a Wiki.

    ## ⚙️ Tarefas
    - [ ] Habilitar o plugin *Mattermost Agents* no console de administração do Mattermost.
    - [ ] Testar a funcionalidade de resumo automático de threads e canais.
    - [ ] Testar a funcionalidade de extração de tarefas de conversas.
    - [ ] Confirmar que o Agents **não** possui a Wiki indexada na sua pesquisa semântica local.
    ```

---

## 📌 Etapa 3: Integração Avançada via MCP

### 🔹 Issue #5: Publicação de Servidor MCP no Dify para o Mattermost Agents
*   **Título**: `[Agente] Integração do Dify como Servidor MCP no Mattermost Agents`
*   **Rótulos**: `mcp`, `integracao-avancada`
*   **Descrição**:
    ```markdown
    ## 🎯 Objetivo
    Publicar a aplicação institucional do Dify como um servidor MCP e registrá-la no Mattermost Agents quando a licença for compatível.

    ## ⚙️ Tarefas
    - [ ] Expor o servidor MCP na aplicação de Conhecimento do Dify.
    - [ ] Cadastrar a URL e o token do MCP no painel de ferramentas do Mattermost Agents.
    - [ ] Autorizar o bot `@assistente-cdc` a invocar a ferramenta de consulta à Wiki via MCP.
    - [ ] Validar a resposta nativa do chat via MCP.
    ```

---

## 📌 Governança e Sustentação

### 🔹 Issue #6: Teste Semestral de Recuperação de Backup (3-2-1)
*   **Título**: `[Infra] Simulação de Recuperação de Backup Offsite`
*   **Rótulos**: `infraestrutura`, `backup`, `governança`
*   **Descrição**:
    ```markdown
    ## 🎯 Objetivo
    Executar o primeiro teste semestral de restauração de dados para validar a [Política de Backup 3-2-1](file:///home/vier/Documentos/Code/CDC/Agentes%20de%20IA/docs/politica_backup.md).

    ## ⚙️ Tarefas
    - [ ] Baixar o snapshot criptografado do bucket externo em nuvem.
    - [ ] Descriptografar e restaurar a base em um ambiente isolado.
    - [ ] Verificar a integridade dos arquivos e segredos.
    - [ ] Registrar o resultado e eventuais lições aprendidas em `docs/postmortem.md`.
    ```
