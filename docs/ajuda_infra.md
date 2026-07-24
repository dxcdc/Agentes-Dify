# 🛠️ Ajuda de Infraestrutura (CDC Receitas)

Este documento apresenta a arquitetura conceitual das receitas de automação e fornece comandos rápidos (cheat sheets) para sustentação local e operação da infraestrutura.

---

## 1. Visão Geral da Arquitetura

O ecossistema de receitas funciona sob um modelo modular integrado de três partes:

1.  **Orquestradores de IA (`prompts/`)**: Contém os arquivos de texto estruturados que ditam as regras de comportamento para os agentes Dify ou outras LLMs.
2.  **Pontes de Conexão (`api/`)**: Códigos escritos em Python ou JavaScript que consomem dados do ambiente e os enviam/recebem das LLMs ou do Mattermost.
3.  **Ambiente e Segurança (`infra/`)**: Garante que o ecossistema permaneça no ar (infraestrutura) e que os segredos permaneçam em cofres seguros locais ou remotos.

---

## 2. Estrutura Detalhada de Diretórios

A distribuição de responsabilidade técnica de cada diretório raiz é a seguinte:

*   `/prompts`: Todos os arquivos de texto com extensão `.txt` ou `.md`. Contém subpastas para agentes específicos e meta-prompts.
*   `/api`: Contém pacotes autocontidos. Cada subpasta deve incluir seu próprio arquivo de dependências (ex: `requirements.txt` ou `package.json`).
*   `/infra`: Armazena scripts de infraestrutura, incluindo agendamentos de backup, configurações de DNS, e receitas de orquestração de Docker se aplicável.

---

## 3. Comandos Rápidos e Cheat Sheet

### 3.1. Teste de Leitura de Variáveis locais
Para verificar se as variáveis do seu `.env` local estão sendo devidamente interpretadas no terminal:

```bash
# Exportar variáveis do .env para a sessão atual do Bash (Linux/macOS)
export $(grep -v '^#' .env | xargs)

# Exibir valor de teste
echo $GEMINI_API_KEY
```

### 3.2. Script Rápido para Ativação de Ambiente (Python)
Gere e execute ambientes virtuais isolados rapidamente:

```bash
# Executar na raiz do projeto onde há dependências
python3 -m venv .venv && source .venv/activate && pip install --upgrade pip
```

### 3.3. Simulação de Execução de Script de API
Use este comando simples para testar a comunicação com uma API usando `curl` (usando variáveis de ambiente locais):

```bash
curl -X POST "$MATTERMOST_WEBHOOK_URL" \
  -H "Content-Type: application/json" \
  -d '{"text": "Teste de infraestrutura CDC Receitas com sucesso!"}'
```

---

## 4. Integração com Dify e Mattermost (Webhooks)

Para interligar o Mattermost ao Dify, configuramos uma estrutura de Webhooks (de Entrada e Saída) e variáveis de ambiente.

### 4.1. Variáveis de Ambiente Necessárias (Cofre/VPS)
*   `DIFY_API_KEY`: Token de autenticação do agente criado no Dify (tipo *Chatbot* ou *Agent*).
*   `DIFY_API_URL`: URL base da API do Dify (ex: `http://localhost:5001/v1` ou `https://api.dify.ai/v1`).
*   `MATTERMOST_WEBHOOK_URL`: URL do Incoming Webhook gerada no console do Mattermost.

### 4.2. Payloads de Comunicação

#### A. Envio do Mattermost para o Dify (ou Ponte de API)
O Gatilho de Saída (Outgoing Webhook) do Mattermost envia uma requisição `POST` com os seguintes campos em `application/x-www-form-urlencoded` ou `application/json`:
```json
{
  "channel_id": "haw78a78ih78h19h0a",
  "channel_name": "suporte-ia",
  "text": "como restaurar o backup offsite?",
  "user_id": "uid12345",
  "user_name": "colaborador.cdc"
}
```

#### B. Chamada de API do Dify (Bloqueante)
Para interagir com o agente do Dify via API corporativa, o payload de requisição deve ser enviado para `POST /chat-messages`:
*   **Headers**:
    *   `Authorization: Bearer <DIFY_API_KEY>`
    *   `Content-Type: application/json`
*   **Body**:
```json
{
  "inputs": {},
  "query": "como restaurar o backup offsite?",
  "response_mode": "blocking",
  "user": "colaborador.cdc",
  "conversation_id": ""
}
```

#### C. Retorno para o Mattermost
Para publicar a resposta gerada pelo Gemini + Dify de volta no canal do Mattermost, envie um `POST` para a `MATTERMOST_WEBHOOK_URL`:
```json
{
  "username": "Assistente CDC Receitas",
  "icon_emoji": "robot",
  "text": "Olá! Para restaurar o backup offsite, siga o procedimento contido na seção 4 do guia de migração: [Guia de Migração](url_aqui)..."
}
```

---

## 5. Automação CI/CD & GitHub Actions

O repositório conta com uma rotina de automação em [.github/workflows/automatizar_issues.yml](file:///home/vier/Documentos/Code/CDC/Agentes%20de%20IA/.github/workflows/automatizar_issues.yml):

*   **Disparo Automático**: É acionado a cada `push` efetuado na branch `main` ou via execução manual no GitHub (`workflow_dispatch`).
*   **Gestão de Tarefas (Issues)**: Utiliza a ferramenta GitHub CLI para ler as especificações de [docs/issues_roadmap.md](file:///home/vier/Documentos/Code/CDC/Agentes%20de%20IA/docs/issues_roadmap.md) e cadastrar as tarefas na aba *Issues* do GitHub.
*   **Verificação Antiduplicação**: O script verifica a existência prévia de cada Issue pelo título para evitar cadastros repetidos em commits subsequentes.


