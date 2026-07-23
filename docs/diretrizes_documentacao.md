# 📖 Diretrizes de Documentação & Governança (CDC Receitas)

Este documento define as normas e princípios para a criação, manutenção e evolução da documentação e das receitas armazenadas no repositório **Receitas**.

---

## 1. Objetivos da Documentação

*   **Preservação de Conhecimento**: Evitar a perda de padrões, scripts e prompts valiosos acumulados ao longo do tempo pela equipe.
*   **Portabilidade & Reuso**: Garantir que qualquer colaborador possa copiar e colar receitas para novos projetos com zero atrito de configuração.
*   **Segurança por Padrão**: Impedir a exposição involuntária de senhas, tokens ou webhooks reais.

---

## 2. Padrão de Tom de Voz: Modelo Híbrido

Adotamos o **Modelo Híbrido de Escrita (Corporativo Amigável)**:

*   **Seções Conceituais / Introdução**: Usar tom acolhedor, contextual e empático. Explicar o "porquê" de cada receita ou padrão existir.
*   **Passo a Passo / Procedimentos**: Usar tom direto, procedimental e altamente escaneável (listas numeradas, negritos em botões e tabelas).

---

## 3. Estrutura Oficial do Repositório

O repositório é composto pelas seguintes pastas e arquivos principais:

```
Receitas/
├── README.md                          # Painel principal com mapa visual Mermaid e índice
├── docs/                              # Governança, infraestrutura e sustentação do repositório
│   ├── diretrizes_documentacao.md     # Este documento (Regras editoriais e ADRs)
│   ├── estrategia_execucao.md         # Estratégia Git, branches e contribuição
│   ├── migration_guide.md             # Guia de clonagem e onboarding em novas máquinas
│   ├── ajuda_infra.md                 # Arquitetura, estrutura de diretórios e comandos rápidos
│   ├── postmortem.md                  # Registro incremental de incidentes e lições aprendidas
│   ├── troubleshooting.md             # Solução de problemas comuns ao executar receitas
│   ├── politica_backup.md             # Política de backup 3-2-1 e sincronização offsite
│   ├── plano_personalizacao.md        # Roteiro de expansão e criação de novas categorias
│   ├── prompt_ia.md                   # Contexto permanente para assistentes de IA no repositório
│   └── governanca_agentes.md          # Papéis, responsabilidades e maturidade dos Agentes de IA
├── prompts/                           # Prompts de sistema e meta-prompts
├── api/                               # Boilerplates de integração de APIs (Python, JS, etc.)
└── infra/                             # Configurações de infraestrutura, backups, DNS e cofres
```

---

## 4. Regra da Alimentação Incremental (Não-Substituição)

> [!IMPORTANT]
> **Alimentação Incremental**: Ao registrar incidentes no `postmortem.md` ou adicionar soluções no `troubleshooting.md`, **nunca apague registros antigos**. As novas entradas devem ser sempre inseridas incrementalmente no topo das listas/tabelas, preservando o histórico para auditoria.

---

## 5. Regras de Segurança & Sanitização

*   **NUNCA** suba arquivos `.env` reais, certificados `.pem` ou senhas no Git.
*   Todos os arquivos de receita devem utilizar variáveis de ambiente (ex: `os.getenv`) ou placeholders explícitos (ex: `<GEMINI_API_KEY>`, `<MATTERMOST_WEBHOOK_URL>`).
*   Utilize o `.gitignore` oficial do repositório para evitar inclusões acidentais.

---

## 6. Registro de Decisões de Arquitetura (ADR)

Abaixo estão listadas as Decisões de Arquitetura tomadas para este repositório:

| ID | Data | Decisão | Motivo | Status |
| :--- | :--- | :--- | :--- | :--- |
| **ADR-001** | 2026-07-21 | Adição de subpastas `infra/`, `prompts/` e `api/` | Organização modular por tipo de recurso reutilizável. | **Aprovado** |
| **ADR-002** | 2026-07-21 | Adoção do modelo de 8 arquivos na pasta `docs/` (agora 9 arquivos com `prompt_ia.md`) | Padronização de governança DevOps da empresa. | **Aprovado** |
| **ADR-003** | 2026-07-22 | Integração de Dify + Mattermost + Gemini | Criação de um assistente de IA conversacional corporativo e operacional com controle orçamentário. | **Aprovado** |
| **ADR-004** | 2026-07-23 | Criação de documento de Governança de IA | Definição clara dos papéis e níveis de maturidade para dois agentes atuantes. | **Aprovado** |
