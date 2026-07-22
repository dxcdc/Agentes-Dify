# 📦 Guia de Migração & Onboarding (CDC Receitas)

Este documento descreve o processo passo a passo para clonar, configurar e testar o repositório **CDC Receitas** em uma nova máquina de desenvolvimento.

---

## 1. Pré-requisitos do Sistema

Antes de iniciar, certifique-se de que sua máquina possui as ferramentas básicas instaladas:

*   **Git** (versão 2.30 ou superior)
*   **Python** (versão 3.10 ou superior) - para boilerplates e scripts de automação.
*   **Node.js & npm** (LTS) - para execução de ferramentas JavaScript.

---

## 2. Clonagem do Repositório

Para clonar o repositório de forma segura utilizando SSH:

```bash
git clone git@github.com:dxcdc/Agentes-Dify.git
cd Agentes-Dify
```

---

## 3. Configuração do Ambiente Local

As receitas deste repositório utilizam variáveis de ambiente para chaves de API e outros dados sensíveis. Siga as instruções abaixo para configurá-las sem expor chaves reais ao controle de versão.

### Passo 1: Criar o Arquivo de Variáveis Local
NUNCA edite diretamente arquivos que vão ao Git com suas chaves. Crie um arquivo `.env` na raiz do repositório ou nas pastas específicas onde for executar as receitas:

```bash
# Exemplo de criação rápida
touch .env
```

### Passo 2: Definir suas Credenciais Locais
Adicione as variáveis de ambiente necessárias no arquivo `.env`. Veja o exemplo abaixo:

```env
# Configurações do Gemini API
GEMINI_API_KEY=sua_chave_real_aqui

# Configurações de Notificações
MATTERMOST_WEBHOOK_URL=https://exemplo.com/hooks/seu_token_real
```

> [!WARNING]
> O arquivo `.env` está no nosso `.gitignore` e não será enviado para o servidor Git remoto. Nunca force o commit de arquivos `.env` (`git add -f .env`).

### Passo 3: Criar um Ambiente Virtual Python (se aplicável)
Para receitas escritas em Python na pasta `api/` ou scripts em `infra/`:

```bash
# Criar ambiente virtual
python -m venv .venv

# Ativar no Linux/macOS
source .venv/activate

# Ativar no Windows (Prompt de Comando)
# .venv\Scripts\activate.bat

# Instalar dependências se houver algum requirements.txt
pip install -r requirements.txt
```

---

## 4. Teste de Validação Rápida

Para garantir que o seu ambiente foi configurado corretamente, realize uma execução de teste simples na receita escolhida. 

Se encontrar problemas de importação de variáveis ou erros de execução do Git, consulte o documento de [Troubleshooting](file:///home/vier/Documentos/Code/CDC/Agentes%20de%20IA/docs/troubleshooting.md).
