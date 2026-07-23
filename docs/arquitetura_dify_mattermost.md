# 🏢 Arquitetura e Integração: Dify vs. Mattermost Agents

Este documento descreve o alinhamento arquitetural, as fronteiras de responsabilidade e o plano de adoção integrada entre a plataforma **Dify** e o ecossistema nativo **Mattermost Agents** no CDC.

---

## 1. Posicionamento Estratégico

Para evitar conflitos de escopo onde ambos os sistemas tentem atuar como o "cérebro central" da IA, adotamos a seguinte divisão de papéis:

*   **Dify**: A plataforma central de inteligência artificial, gestão de bases de conhecimento institucionais e automações complexas.
*   **Mattermost Agents**: A camada nativa de interação e inteligência contextual do chat dentro do Mattermost.

### Tabela Comparativa de Funções

| Função | Dify | Mattermost Agents |
| :--- | :--- | :--- |
| **Papel Principal** | Orquestrador de aplicações de IA | Integração nativa de IA no Mattermost |
| **Base de Conhecimento** | Documentos, Markdown, PDFs, APIs externas | Mensagens, canais, threads e arquivos do chat |
| **Fluxo de RAG** | Completo, indexado e configurável por app | Busca semântica experimental sobre mensagens |
| **Workflows** | Fluxos visuais, condições, etapas múltiplas | Ações de interface nativas e ferramentas MCP |
| **Interface** | API, Web App, Webhook, MCP | Canais, DMs, menus de contexto e barra lateral |
| **Memória/Histórico** | Conversas e variáveis de Chatflow (`conversation_id`) | Histórico de conversas da thread do Mattermost |
| **Provedores de LLM** | Gemini, OpenAI, Anthropic, Modelos Locais | Gemini, OpenAI, Anthropic, compatíveis |
| **Permissões** | Configurações de acesso de usuários Dify | Nativas de equipes, canais e usuários Mattermost |

---

## 2. Pontos de Conflito e Decisões de Arquitetura

### 2.1. Duplicidade da Base de Conhecimento (Wiki Markdown)
*   **Problema**: Indexar a Wiki no Dify e simultaneamente na busca semântica do Mattermost Agents gera redundância de processamento, índices separados e respostas divergentes.
*   **Decisão Recomendada**: 
    *   **Dify**: Gerencia e indexa a Wiki oficial em Markdown e documentos corporativos estruturados.
    *   **Mattermost Agents**: Indexa o histórico de conversações e decisões tomadas nos canais do chat.

### 2.2. Gerenciamento do Histórico e Memória
*   **Problema**: Tentar manter o histórico de conversas no Chatflow do Dify e na thread do Mattermost ao mesmo tempo pode inflar o contexto enviado ao Gemini ou duplicar mensagens.
*   **Decisão Recomendada**: Para consultas institucionais diretas, o Mattermost gerencia o histórico da conversa e repassa apenas a pergunta final isolada ao Dify. O Dify responderá de forma "stateless" (sem necessidade de persistir logs longos por canal).

### 2.3. Controle de Provedores e Custos (Gemini API)
*   **Problema**: Conectar tanto o Dify quanto o Mattermost Agents diretamente à API do Gemini descentraliza o controle de credenciais, monitoramento de cotas e auditoria de consumo.
*   **Decisão Recomendada**: O **Dify centraliza as credenciais principais da API do Gemini**, aplicando regras de concorrência, limites de uso (Rate Limiting) e logs centralizados de auditoria.

### 2.4. Respostas Duplicadas por Menção
*   **Problema**: Integrar múltiplos bots sem nomes ou escopos definidos pode fazer com que uma menção como `@ia` ative os dois sistemas simultaneamente.
*   **Decisão Recomendada**: Definir escopos e identidades claras:
    *   `@cdc-conhecimento`: Aciona o Dify para responder dúvidas sobre a Wiki e processos.
    *   `@assistente-mattermost`: Usado para resumos de canais, threads e tarefas nativas.
    *   `@suporte-ti`: Aciona automações de infraestrutura e sustentações autorizadas.

---

## 3. Modelo de Arquitetura CDC

```
                       ┌─────────────────────────┐
                       │       Mattermost        │
                       │ canais, usuários, chats │
                       └───────────┬─────────────┘
                                   │
                 ┌─────────────────┴─────────────────┐
                 │                                   │
                 ▼                                   ▼
    ┌────────────────────────┐          ┌────────────────────────┐
    │   Mattermost Agents    │          │          Dify          │
    │                        │          │                        │
    │ Resume canais          │          │ Consulta Wiki Markdown │
    │ Resume threads         │          │ Executa RAG            │
    │ Transcreve reuniões    │          │ Aplica fluxos          │
    │ Pesquisa conversas     │          │ Controla prompts       │
    │ Respeita permissões    │          │ Controla modelos       │
    └────────────────────────┘          └───────────┬────────────┘
                                                   │
                                                   ▼
                                         ┌──────────────────┐
                                         │ Gemini ou outro  │
                                         │ provedor de LLM  │
                                         └──────────────────┘
```

---

## 4. Plano de Adoção em 3 Etapas

Para garantir estabilidade, segurança e viabilidade financeira, a adoção da integração seguirá as seguintes fases:

### 🚀 Etapa 1: Integração por Webhook/Bot (Fase Inicial)
*   **Status**: Implementado.
*   **Fluxo**: `Mattermost ➔ Webhook/Bot ➔ Dify (RAG Wiki) ➔ Gemini`.
*   **Benefício**: Responde a dúvidas institucionais rapidamente enquanto o Dify gerencia o controle de chamadas e segurança de credenciais.

### 🚀 Etapa 2: Instalação do Agents em Homologação (Camada Complementar)
*   **Foco**: Testar o plugin nativo Mattermost Agents de forma isolada.
*   **Atividades**: Habilitar recursos como resumo de canais, transcrição de reuniões e agrupamento de threads. A Wiki **não** deve ser indexada na base semântica do Agents nesta etapa.

### 🚀 Etapa 3: Integração via MCP (Model Context Protocol)
*   **Foco**: Habilitar interoperabilidade quando a licença do Mattermost permitir.
*   **Fluxo**: `Mattermost Agents ➔ MCP ➔ Dify App (RAG) ➔ Gemini`.
*   **Benefício**: O Mattermost Agents oferece a interface nativa para o usuário, mas executa consultas à Wiki chamando dinamicamente a ferramenta MCP exposta pela API do Dify, unificando a fonte de conhecimento corporativo.
