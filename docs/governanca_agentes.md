# 👥 Definição de Papéis e Maturidade de Agentes de IA

Este documento estabelece as regras de governança, definição de papéis e níveis de maturidade para os agentes de Inteligência Artificial integrados ao ecossistema do **CDC Receitas**.

A governança é essencial para garantir que os agentes atuem de forma complementar, segura e transparente, evitando conflitos de escopo ou ações redundantes.

---

## 1. Definição dos Dois Agentes de IA

Definimos as responsabilidades e fronteiras técnicas para os dois agentes principais atuando no ecossistema:

### 🤖 Agente 1: Assistente de RAG & Base de Conhecimento (Wiki)
*   **Foco Principal**: Responder dúvidas operacionais, técnicas e de processos corporativos.
*   **Fontes de Dados**: Lê a base de dados em Markdown (Wiki) sincronizada via Git e orquestrada no Dify.
*   **Interface Principal**: Mattermost via menções diretas (ex: `@assistente-wiki`).
*   **Nível de Acesso**: Leitura apenas (Read-only). Não executa comandos que alterem arquivos ou servidores.
*   **Propósito no CDC**: Acelerar o onboarding de desenvolvedores e evitar buscas manuais em documentos internos.

### 🤖 Agente 2: Copiloto de Operações (Sustentação & Automações)
*   **Foco Principal**: Auxiliar nas tarefas de DevOps, infraestrutura e monitoramento preventivo.
*   **Fontes de Dados**: Logs do sistema, scripts em `infra/` e status dos pipelines de CI/CD.
*   **Gatilhos de Execução**: Tarefas agendadas (Cron) e Webhooks de eventos (ex: falhas de backups, alertas de erros).
*   **Nível de Acesso**: Escrita e execução supervisionada (Write/Execute com aprovação humana).
*   **Propósito no CDC**: Manter a estabilidade ambiental (auditoria 3-2-1), propor rascunhos de incidentes em [postmortem.md](file:///home/vier/Documentos/Code/CDC/Agentes%20de%20IA/docs/postmortem.md) e realizar o Code Review automático de Pull Requests.

---

## 2. Matriz de Níveis de Maturidade de IA

Para planejar a evolução técnica e mitigar riscos, classificamos a autonomia dos nossos agentes em quatro níveis:

| Nível | Classificação | Descrição | Status Agente 1 | Status Agente 2 |
| :--- | :--- | :--- | :--- | :--- |
| **Nível 1** | **Reativo** | Responde a comandos fixos sem contexto histórico ou semântico. | Superado | Superado |
| **Nível 2** | **Contextual** | Utiliza RAG e busca semântica em base vetorial para apoiar decisões humanas. | **Atual (Produção)** | **Atual (Produção)** |
| **Nível 3** | **Integrado** | Executa ações em outros sistemas ativado por webhooks e eventos (ex: envia alertas de falha). | Planejado | **Atual (Produção)** |
| **Nível 4** | **Autônomo** | Orquestração multi-agente (IA aciona IA) com intervenção humana apenas para auditoria de segurança. | Longo Prazo | Planejado |

---

## 3. Diretrizes de Governança e Evolução

Para manter a integridade operacional com múltiplos agentes:

1.  **Segregação de Chaves de API**: Cada agente deve usar um token de API (Dify e Gemini) exclusivo. Nunca compartilhe a mesma `DIFY_API_KEY` para ambos, garantindo auditoria limpa de logs.
2.  **Aprovação Humana Obrigatória (Human-in-the-Loop)**: O *Agente 2* pode sugerir correções de código e scripts de infraestrutura, mas a sua aplicação efetiva em ambiente de produção deve obrigatoriamente passar por aprovação manual de um desenvolvedor via Pull Request.
3.  **Avaliação Contínua e Calibração**: 
    *   **Feedback Explicito**: Utilizar reações de emojis no Mattermost (👍/👎) para quantificar a assertividade das respostas da IA.
    *   **Atualização por Git**: Correções no comportamento ou nas instruções da IA devem ser versionadas em [docs/prompt_ia.md](file:///home/vier/Documentos/Code/CDC/Agentes%20de%20IA/docs/prompt_ia.md) ou nos arquivos correspondentes na pasta `prompts/`.
