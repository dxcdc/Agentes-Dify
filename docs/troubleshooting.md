# 🔍 Resolução de Problemas / Troubleshooting (CDC Receitas)

Este guia prático fornece soluções para os problemas mais recorrentes encontrados durante a configuração ou execução de receitas e integrações de API.

> [!IMPORTANT]
> **REGRA DE OURO - ALIMENTAÇÃO INCREMENTAL**: Caso encontre um novo erro e sua respectiva solução, adicione-o **no topo da lista** abaixo, preservando as entradas antigas e a facilidade de busca pelos problemas mais recentes.

---

## 🛠️ Erros Comuns e Soluções

### 🚨 Erro: Variável de Ambiente Vazia (`None` ou `KeyError`)
*   **Contexto**: O script falha imediatamente indicando que chaves como `GEMINI_API_KEY` ou `MATTERMOST_WEBHOOK_URL` não foram encontradas.
*   **Causa**: O script não conseguiu acessar o arquivo `.env` local ou as variáveis não foram exportadas para o terminal atual.
*   **Solução**:
    1.  Verifique se o arquivo `.env` foi criado na pasta de execução do script.
    2.  No Linux/macOS, carregue as variáveis antes de rodar o script usando:
        ```bash
        export $(grep -v '^#' .env | xargs)
        ```
    3.  Confirme se não há espaços em branco ao redor do sinal `=` no arquivo `.env`.

### 🚨 Erro: `ModuleNotFoundError: No module named '...'`
*   **Contexto**: Falha ao tentar executar boilerplates em Python.
*   **Causa**: O pacote necessário não está instalado no sistema ou o ambiente virtual (`venv`) não foi ativado.
*   **Solução**:
    1.  Ative o ambiente virtual:
        ```bash
        source .venv/activate
        ```
    2.  Instale os pacotes requeridos indicados no manual da receita correspondente (ex: `pip install requests google-generativeai`).

### 🚨 Erro: `Permission denied (publickey)` ao fazer Git Clone ou Git Push
*   **Contexto**: O Git falha ao se comunicar com o repositório remoto no GitHub.
*   **Causa**: Sua chave SSH pública não está cadastrada na sua conta do GitHub ou o agente SSH não está rodando.
*   **Solução**:
    1.  Verifique se você possui chaves geradas em `~/.ssh/`.
    2.  Copie o conteúdo de `~/.ssh/id_rsa.pub` (ou similar) e adicione-o em **Settings -> SSH and GPG Keys** no seu painel do GitHub.

---

## 📝 Modelo para Novos Erros
Ao documentar um erro resolvido, utilize a estrutura abaixo e insira-a no topo da seção **Erros Comuns e Soluções**:

```markdown
### 🚨 Erro: [Nome do Erro ou Mensagem do Terminal]
*   **Contexto**: [Em qual cenário o erro se manifesta]
*   **Causa**: [Por que o erro acontece]
*   **Solução**:
    1.  [Passo 1 para resolver]
    2.  [Passo 2 para resolver]
```
