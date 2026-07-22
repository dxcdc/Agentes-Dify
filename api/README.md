# 🔌 Boilerplates de API (CDC Receitas)

Esta pasta abriga os modelos e scripts de integração com APIs externas (ex: Gemini API, Mattermost Webhooks, Dify API) prontos para reuso com zero atrito de configuração.

---

## 1. Diretrizes para Criação de Integrações

Para que um boilerplate de API seja considerado elegível e portável:

1.  **Isolamento**: Cada integração deve estar contida em sua própria subpasta (ex: `api/gemini-python/`).
2.  **Gerenciamento de Dependências**:
    *   Para **Python**: Inclua um arquivo `requirements.txt`.
    *   Para **Node.js**: Inclua um arquivo `package.json`.
3.  **Segurança Absoluta (Sem Chaves Físicas)**:
    *   O código de integração deve ler segredos obrigatoriamente através de variáveis de ambiente.
    *   Adicione um arquivo de exemplo `.env.example` na pasta da receita para guiar a configuração local do desenvolvedor (ex: `api/gemini-python/.env.example`).
4.  **Tratamento de Erros**:
    *   Adicione tratamento para falhas de rede, limites de taxa de API (rate limit) e variáveis não configuradas.

---

## 2. Estrutura Padrão Recomendada de Subpasta

```
api/minha-integracao/
├── README.md             # Guia rápido de execução da receita
├── .env.example          # Modelo de variáveis de ambiente
├── requirements.txt      # Dependências (se Python)
└── app.py                # Código-fonte principal da integração
```
