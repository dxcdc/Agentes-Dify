# 🏢 Arquitetura e Integração: Dify vs. Mattermost Agents (CDC)

Este documento estabelece o alinhamento arquitetural, as fronteiras de responsabilidade, a prevenção de conflitos técnicos e o roteiro de adoção integrada entre a plataforma **Dify** e o ecossistema **Mattermost Agents** no CDC.

---

## 1. Posicionamento Estratégico & Leitura para o CDC

Dify e Mattermost Agents **não são a mesma coisa**, mas possuem uma área considerável de sobreposição. O conflito surge quando ambos são configurados para exercer o papel de "cérebro central" da inteligência artificial.

A leitura oficial adotada pelo CDC é:

*   **Dify**: Plataforma central de IA, conhecimento institucional, RAG e automações.
*   **Mattermost Agents**: Camada nativa de interação com IA e inteligência operacional dentro do Mattermost.

### Tabela Comparativa de Funções

| Função | Dify | Mattermost Agents |
| :--- | :--- | :--- |
| **Papel Principal** | Orquestrador de aplicações de IA | Integração nativa de IA no Mattermost |
| **Base de Conhecimento** | Documentos, Markdown (Wiki), PDFs, APIs e bases externas | Mensagens, canais, threads e arquivos do Mattermost |
| **RAG** | Completo, indexado, reranqueado e configurável por aplicação | Busca semântica (pgvector) focada no conteúdo do chat |
| **Workflows** | Fluxos visuais, condições, ferramentas e múltiplas etapas | Agentes, ações nativas da UI e ferramentas MCP |
| **Interface** | API REST, Web App, Webhooks ou Servidor MCP | Canais, mensagens diretas (DMs), menus e barra lateral |
| **Histórico** | Conversas e variáveis mantidas no Chatflow (`conversation_id`) | Conversas com bots e contexto de threads do chat |
| **Provedores de IA** | Gemini, OpenAI, Anthropic, modelos locais e outros | Gemini, OpenAI, Anthropic, APIs compatíveis e outros |
| **Permissões** | Configurações de aplicações e usuários da plataforma Dify | Usuários, equipes, canais e permissões nativas do Mattermost |

---

## 2. Pontos de Conflito Arquitetural & Decisões Recomendadas

Os conflitos não são necessariamente técnicos, mas de arquitetura e governança.

### 2.1. Duas bases de conhecimento com o mesmo conteúdo
*   **Risco**: Colocar a Wiki em Markdown na base vetorial do Dify e simultaneamente na pesquisa semântica do Mattermost Agents produz dois índices separados, dois processos de atualização e respostas inconsistentes para o usuário.
*   **Detalhe Técnico**: A pesquisa semântica do Mattermost Agents utiliza PostgreSQL com `pgvector`, possui status experimental e requer licença compatível. Seu foco natural é a indexação de conversas internas do chat, enquanto o Dify foi projetado para bases institucionais externas.
*   **Decisão Recomendada**: 
    *   **Dify**: Fonte de verdade da Wiki e documentos institucionais oficiais.
    *   **Mattermost Agents**: Fonte de conhecimento das conversas e decisões registradas nos canais do chat.

### 2.2. Dois históricos de conversação (Duplicidade de Memória)
*   **Risco**: Se o Mattermost mantiver o contexto da thread e o Dify mantiver um segundo histórico via `conversation_id`, perguntas antigas podem ser reenviadas ou o modelo pode receber um contexto excessivamente grande e confuso.
*   **Decisão Recomendada**: O Mattermost gerencia a conversa do usuário e o Dify recebe requisições independentes para consultar a Wiki. O Dify não precisa preservar uma segunda conversa longa para cada thread do Mattermost.

### 2.3. Duas conexões diretas com o Gemini (Descentralização de Custos e Credenciais)
*   **Risco**: Conectar ambos os sistemas diretamente ao Gemini gera duas configurações de chaves, dois locais de controle, dois históricos de consumo, prompts institucionais divergentes e dificuldade para estabelecer travas orçamentárias.
*   **Decisão Recomendada**: O **Dify centraliza as credenciais principais da API do Gemini**, concentrando o gerenciamento de modelos, limites de concorrência, RAG, prompts institucionais e logs de auditoria.

### 2.4. Respostas duplicadas nos canais por menções ambíguas
*   **Risco**: Uma menção genérica como `@ia` pode ser capturada tanto pelo webhook do Dify quanto pelo bot do Mattermost Agents, gerando duas respostas e duplo consumo de tokens.
*   **Decisão Recomendada**: Atribuir nomes e finalidades distintas para cada agente:
    *   `@cdc-conhecimento`: Consulta documentos da Wiki indexados no Dify.
    *   `@assistente-mattermost`: Resume canais, threads e tarefas do chat.
    *   `@suporte-ti`: Executa ferramentas técnicas e automações autorizadas.

---

## 3. Modelo de Arquitetura Recomendado para o CDC

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

### Exemplos de Aplicação por Responsabilidade

#### Mattermost Agents (Cuida do ecossistema de comunicação):
*   *"Resuma as mensagens que não li neste canal."*
*   *"Quais tarefas foram definidas nesta thread?"*
*   *"Transcreva e resuma a reunião do time."*
*   *"Procure onde discutimos o cadastro de fornecedores no chat."*

#### Dify (Cuida do conhecimento institucional):
*   *"Como cadastrar fornecedor no ONGSYS?"*
*   *"Qual é o fluxo de aprovação de uma requisição?"*
*   *"Quais documentos são necessários para prestação de contas?"*
*   *"Explique a política de backup do Moodle."*

---

## 4. Integração via MCP (Model Context Protocol) & Advertências Técnicas

### 4.1. Conexão Elegante via MCP (Futuro)
É possível integrar os dois sistemas sem duplicação:

```
Mattermost Agents ──(MCP)──➔ Aplicação Dify ──➔ Wiki/RAG ──➔ Gemini
```

1. Criamos no Dify a aplicação **CDC — Conhecimento Institucional**.
2. Vinculamos essa aplicação à base da Wiki em Markdown.
3. Publicamos a aplicação como um **Servidor MCP**.
4. Registramos a URL do MCP no Mattermost Agents.
5. Quando o usuário pergunta sobre documentação (ex: `@assistente-cdc Como funciona uma requisição devolvida?`), o Mattermost Agents identifica a necessidade e chama a ferramenta do Dify.

> [!IMPORTANT]
> **Limitação de Licenciamento**: O suporte a MCP e ferramentas avançadas do Mattermost Agents depende de edição/licença comercial. Caso o ambiente não possua essa licença, a integração por Webhook/API do Dify continua sendo a solução recomendada.

### 4.2. Por que NÃO configurar o Dify como "OpenAI Compatible" diretamente no Agents
O Mattermost Agents aceita provedores com API compatível com a OpenAI. No entanto, uma aplicação do Dify expõe uma API REST própria (com identidade de usuário, aplicação e sessão) que **não deve ser presumida como substituto 1:1 de um endpoint da OpenAI**. Usar o Dify no campo "OpenAI Compatible" diretamente pode quebrar a conversação e perder citações e RAG.

---

## 5. Roteiro de Adoção em 3 Etapas

1.  **Etapa 1 (Atual - Planejada)**:
    *   `Mattermost ➔ Webhook/Bot ➔ Dify ➔ RAG da Wiki ➔ Gemini`.
    *   Atende às consultas institucionais mantendo o Dify como centro de controle.
2.  **Etapa 2 (Homologação do Mattermost Agents)**:
    *   Instalar o plugin Agents exclusivamente para funções nativas (resumo de threads/canais e transcrição). **Não cadastrar a Wiki no Agents**.
3.  **Etapa 3 (Integração Avançada via MCP)**:
    *   Quando a licença permitir, publicar as aplicações do Dify como servidores MCP para o Mattermost Agents consumir.

---

## 📜 Resumo Executivo da Arquitetura do CDC

> A plataforma **Dify** foi adotada como o orquestrador central de aplicações de inteligência artificial do CDC, responsável pela integração com provedores de modelos, organização das bases de conhecimento institucionais (Wiki), fluxos de RAG, prompts, limites de execução e automações.
> 
> O **Mattermost** permanece como a principal interface de comunicação. A integração inicial ocorre por meio de bots/webhooks que encaminham solicitações ao Dify.
> 
> O plugin **Mattermost Agents** atuará como camada complementar exclusiva para funções nativas da plataforma de chat (resumo de canais/threads, extração de tarefas e transcrição). Ele não substituirá a base de conhecimento do Dify nem manterá uma segunda cópia da Wiki.
> 
> Quando tecnicamente disponível, as aplicações do Dify serão expostas ao Mattermost Agents via protocolo MCP. O arranjo oficial define o **Dify como o cérebro institucional** e o **Mattermost Agents como a interface inteligente do chat**.
