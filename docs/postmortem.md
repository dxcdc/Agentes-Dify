# 🪵 Registro Postmortem (CDC Receitas)

Este documento contém o histórico incremental de incidentes, falhas operacionais e lições aprendidas na execução e sustentação das receitas do repositório.

> [!IMPORTANT]
> **REGRA DE OURO - ALIMENTAÇÃO INCREMENTAL**: Nunca apague os incidentes antigos. Ao relatar um novo incidente, insira-o sempre **no topo da tabela ou da lista de registros**, mantendo a ordem cronológica inversa (mais novos primeiro) para fins de auditoria e consulta rápida.

---

## 📅 Histórico de Incidentes

| ID | Data | Descrição do Incidente | Impacto | Causa Raiz | Ação Corretiva |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **PM-002** | 2026-07-22 | Vazamento de credenciais locais de homologação em branch de testes. | Baixo (credenciais de sandbox expiradas). | Comando `git add -f .env` executado por engano por desenvolvedor. | Reforçado o `.gitignore` com regras mais rígidas (`*creds*`, `*secret*`) e criado script de pré-commit para validar arquivos locais. |
| **PM-001** | 2026-07-15 | Falha na execução da receita de sincronização Mattermost. | Notificações de falha no pipeline de IA ficaram inoperantes por 4 horas. | O Mattermost alterou a URL padrão do endpoint de webhooks em sua última atualização de versão. | Atualizada a receita em `api/` para aceitar parâmetros flexíveis de URL e atualizado o manual técnico no [Troubleshooting](file:///home/vier/Documentos/Code/CDC/Agentes%20de%20IA/docs/troubleshooting.md). |

---

## 📝 Modelo de Registro (Template)
Utilize o formato abaixo para novos incidentes que forem registrados no topo da tabela acima:

```markdown
| **PM-[Número]** | AAAA-MM-DD | [Descrição Curta] | [Alto/Médio/Baixo] | [O que causou o problema] | [Como evitar que ocorra novamente] |
```
