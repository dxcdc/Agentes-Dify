# 💬 Central de Prompts (CDC Receitas)

Esta pasta é dedicada a armazenar e documentar os **prompts de sistema, personas e meta-prompts** validados para uso em produção.

---

## 1. Diretrizes de Organização

Ao adicionar um novo prompt a esta pasta:

1.  **Criação de Arquivo**: Salve o prompt como um arquivo `.txt` ou `.md`.
2.  **Identificação e Metadados**: Adicione um cabeçalho curto contendo:
    *   **Nome do Prompt**: Nome claro da sua função.
    *   **Modelo de LLM Alvo**: Para qual modelo o prompt foi otimizado (ex: Gemini 1.5 Pro, Claude 3.5 Sonnet).
    *   **Autor/Equipe**: Quem criou/mantém o prompt.
    *   **Data de Atualização**: Data da última validação em produção.
3.  **Placeholders de Variáveis**: Use a convenção de chaves duplas `{{VARIAVEL}}` ou colchetes angulares `<VARIAVEL>` para indicar campos dinâmicos que o Dify ou sua aplicação irá preencher.

---

## 2. Exemplo de Estruturação de Arquivo de Prompt

```markdown
# Persona: Assistente de Suporte Tecnico CDC

> **Otimizado para**: Gemini 1.5 Flash / Pro
> **Autor**: Equipe de Sustentação
> **Data**: 2026-07-22

Você é um agente de IA especialista em resolver incidentes técnicos da infraestrutura CDC...
Sempre responda de forma concisa.

### Contexto Adicional:
- Banco de dados: PostgreSQL
- Webhook: {{WEBHOOK_URL}}
```
