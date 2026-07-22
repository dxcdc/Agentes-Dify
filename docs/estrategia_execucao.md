# 🚀 Estratégia de Execução & Fluxo Git (CDC Receitas)

Este documento define o modelo de ramificação (branching), convenções de mensagens de commit e o processo de contribuição de novas receitas no repositório.

---

## 1. Fluxo de Trabalho (Branching Model)

Adotamos uma variação simplificada do **GitHub Flow**, focada em agilidade e revisões rápidas:

1.  **Branch Principal (`main`)**:
    *   A branch `main` contém a versão estável da documentação e das receitas.
    *   Commits diretos na `main` são bloqueados (devem passar por Pull Requests).
2.  **Branches de Trabalho (Feature Branches)**:
    *   Para adicionar ou atualizar qualquer receita ou documento, crie uma branch a partir da `main`.
    *   **Nomenclatura Padrão**:
        *   `feat/<nome-da-receita>`: Para novas receitas ou scripts (ex: `feat/integracao-mattermost`).
        *   `fix/<problema>`: Para correção de bugs em scripts ou receitas (ex: `fix/token-expirado-gemini`).
        *   `docs/<topico>`: Para melhorias exclusivas de documentação (ex: `docs/atualiza-politica-backup`).
        *   `infra/<recurso>`: Para mudanças em scripts de backup, DNS ou cofres (ex: `infra/script-backup-s3`).

---

## 2. Padrão de Commits (Conventional Commits)

Todas as mensagens de commit devem seguir a convenção de Commits Convencionais para manter o histórico limpo e auditável:

```
<tipo>(<escopo>): <descrição curta em letras minúsculas>
```

### Tipos Permitidos
*   **`feat`**: Introdução de uma nova receita, prompt ou boilerplate (ex: `feat(api): adiciona boilerplate python para gemini`).
*   **`fix`**: Correção de algum erro de execução em receita existente (ex: `fix(infra): corrige porta no script de backup`).
*   **`docs`**: Alterações puras de documentação (ex: `docs(governo): atualiza ADR-002 nas diretrizes`).
*   **`refactor`**: Mudanças no código que não alteram o comportamento final (ex: `refactor(prompts): melhora legibilidade do meta-prompt`).
*   **`chore`**: Tarefas rotineiras que não afetam o código ou a documentação (ex: `chore(git): atualiza regras do .gitignore`).

---

## 3. Fluxo de Contribuição e Pull Request (PR)

Para submeter uma nova receita ou alteração à base de conhecimento:

1.  **Criação da Branch**:
    ```bash
    git checkout main
    git pull origin main
    git checkout -b feat/nova-receita-exemplo
    ```
2.  **Desenvolvimento e Sanitização**:
    *   Escreva o código ou documentação seguindo as [Diretrizes de Documentação](file:///home/vier/Documentos/Code/CDC/Agentes%20de%20IA/docs/diretrizes_documentacao.md).
    *   Certifique-se de que nenhum token, chave ou dado pessoal foi incluído no código.
3.  **Commit**:
    ```bash
    git add .
    git commit -m "feat(prompts): adiciona receita de gerador de resumos"
    ```
4.  **Abertura do Pull Request**:
    *   Envie a branch para o servidor remoto e abra o Pull Request direcionado para a `main`.
    *   O PR passará por uma revisão rápida de segurança para garantir a ausência de dados sensíveis antes do merge.
