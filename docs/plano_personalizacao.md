# 🗺️ Plano de Personalização & Expansão (CDC Receitas)

Este documento atua como um roteiro (roadmap) para o crescimento do repositório, definindo como novos recursos e categorias de receitas devem ser propostos, criados e padronizados.

---

## 1. Categorias de Expansão Planejadas

Para acompanhar as necessidades de crescimento da equipe de automação e IA, propomos a expansão modular do repositório nas seguintes fases:

### 📅 Fase 1: Governança e Integrações Básicas (Fase Atual)
*   **Foco**: Estruturação dos processos centrais do Git, segurança com `.gitignore`, e modelos iniciais de integração com APIs (Python/JS) e prompts de IA.

### 📅 Fase 2: Orquestração e Bancos de Dados Vetoriais (Próximo Passo)
*   **Foco**: 
    *   Boilerplates para integração com bancos vetoriais (ex: pgvector, Pinecone, Qdrant).
    *   Scripts de infraestrutura em `infra/` para subir o Dify self-hosted via Docker Compose.

### 📅 Fase 3: Cadeias Complexas de Agentes (Multi-Agent System)
*   **Foco**:
    *   Criação de subdiretórios em `prompts/` dedicados a orquestrações de múltiplos agentes agindo cooperativamente.

---
 
## 2. Novos Horizontes de IA: Casos de Uso Multi-utilidade

Para garantir que a nossa infraestrutura baseada na stack **Dify + Mattermost + Gemini** não fique restrita a um único propósito (como responder a dúvidas da Wiki), mapeamos os seguintes casos de uso expandidos para reuso técnico:

### A. Agente de Suporte e RAG (Wiki Operacional)
*   **Finalidade**: Consultar a base de documentos em Markdown do Git para responder perguntas de processos e receitas.
*   **Funcionamento**: Responde em canais públicos ou por mensagem direta ao ser marcado (`@bot-suporte`).

### B. Agente Copiloto de Incidentes (Autopilot de Postmortem)
*   **Finalidade**: Acelerar a criação de relatórios de incidentes.
*   **Funcionamento**: Quando um alerta de falha de sistema é detectado em canais de operação do Mattermost, o agente resume o impacto técnico e formata um rascunho em Markdown seguindo as diretrizes de [postmortem.md](file:///home/vier/Documentos/Code/CDC/Agentes%20de%20IA/docs/postmortem.md), pronto para ser commitado no topo do histórico.

### C. Agente Code Reviewer e Linter de Segurança
*   **Finalidade**: Analisar código e prevenir vazamento de segredos em Pull Requests.
*   **Funcionamento**: Integrado a Webhooks do GitHub, o agente é acionado a cada PR. Ele varre o código à procura de possíveis segredos expostos (como tokens em texto puro que burlaram o `.gitignore`) e escreve avisos automáticos de correção.

### D. Agente Auditor de Backups (3-2-1)
*   **Finalidade**: Garantir a eficácia da política de backup corporativa.
*   **Funcionamento**: Disparado de forma cronometrada (ex: diariamente após as 02:00), o agente analisa os logs de sincronização offsite definidos na [Política de Backup](file:///home/vier/Documentos/Code/CDC/Agentes%20de%20IA/docs/politica_backup.md). Em caso de falhas, notifica imediatamente os canais de infraestrutura com opções de comando para depuração.

### E. Assistente de Onboarding Interativo
*   **Finalidade**: Facilitar a integração de novos colaboradores.
*   **Funcionamento**: Conversa de maneira amigável com desenvolvedores recém-chegados, ajudando a sanar erros de setup local descritos no [Guia de Migração](file:///home/vier/Documentos/Code/CDC/Agentes%20de%20IA/docs/migration_guide.md) de forma interativa.

---

## 3. Como Criar uma Nova Categoria de Receitas

Se a sua equipe precisar incluir um tipo de recurso que não se encaixa em `prompts/`, `api/` ou `infra/`, siga estes passos:

1.  **Abertura de Proposta**: Proponha a nova categoria abrindo uma ADR (Decisão de Arquitetura) no arquivo [Diretrizes de Documentação](file:///home/vier/Documentos/Code/CDC/Agentes%20de%20IA/docs/diretrizes_documentacao.md).
2.  **Criação da Pasta**: Crie a pasta no diretório raiz do repositório.
3.  **Adicionar README de Categoria**: Toda nova pasta raiz deve conter um arquivo `README.md` explicando a finalidade da categoria, pré-requisitos e um exemplo prático de uso rápido.
4.  **Atualização do Mapa Visual**: Atualize o mapa Mermaid em [README.md](file:///home/vier/Documentos/Code/CDC/Agentes%20de%20IA/README.md) na raiz do projeto para refletir a nova estrutura.
