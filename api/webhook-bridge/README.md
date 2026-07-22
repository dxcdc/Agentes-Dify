# 🔌 Ponte de Integração: Mattermost ➔ Dify (FastAPI Boilerplate)

Esta receita fornece o boilerplate de uma API intermediária (bridge) escrita em Python com **FastAPI**. 

Ela é necessária porque o Mattermost envia requisições em formato de formulário ou JSON simples (sem o cabeçalho de autorização do Dify), enquanto o Dify exige um payload JSON específico contendo o Token de Autorização da API.

---

## 1. Funcionamento da Ponte

```
[Mattermost Outgoing Webhook] 
       ➔ (Envia: user_name, text)
              ➔ [webhook-bridge (FastAPI)]
                     ➔ (Traduz e autoriza com Bearer Token)
                            ➔ [Dify Chat-Messages API] ➔ [Gemini 1.5 Flash]
              ➔ [webhook-bridge (FastAPI)] ➔ (Formata retorno para o chat)
       ➔ [Mattermost Incoming Webhook] (Exibe a resposta do bot)
```

---

## 2. Código-Fonte Recomendado (`bridge.py`)

Abaixo está o boilerplate completo para implantação rápida.

```python
import os
import requests
from fastapi import FastAPI, Form, HTTPException
from pydantic import BaseModel

app = FastAPI(title="Mattermost-Dify Bridge")

# Configurações lidas do .env
DIFY_API_KEY = os.getenv("DIFY_API_KEY")
DIFY_API_URL = os.getenv("DIFY_API_URL", "https://api.dify.ai/v1")
MATTERMOST_WEBHOOK_URL = os.getenv("MATTERMOST_WEBHOOK_URL")

class MattermostPayload(BaseModel):
    channel_name: str
    text: str
    user_name: str

@app.post("/webhook")
async def handle_webhook(
    user_name: str = Form(...),
    text: str = Form(...),
    channel_name: str = Form(...)
):
    """
    Recebe o gatilho de saída (Outgoing Webhook) do Mattermost.
    Traduz o formato e encaminha para a API do Dify.
    """
    if not DIFY_API_KEY or not MATTERMOST_WEBHOOK_URL:
        raise HTTPException(status_code=500, detail="Configurações ausentes no servidor.")

    # 1. Preparar chamada para o Dify
    dify_headers = {
        "Authorization": f"Bearer {DIFY_API_KEY}",
        "Content-Type": "application/json"
    }
    
    dify_payload = {
        "inputs": {},
        "query": text,
        "response_mode": "blocking",
        "user": user_name,
        "conversation_id": "" # Pode ser persistido se quiser manter o histórico por canal
    }

    try:
        # 2. Enviar pergunta ao Dify
        response = requests.post(
            f"{DIFY_API_URL}/chat-messages",
            headers=dify_headers,
            json=dify_payload,
            timeout=30
        )
        response.raise_for_status()
        dify_data = response.json()
        bot_response = dify_data.get("answer", "Desculpe, não consegui processar sua resposta.")

        # 3. Retornar resposta formatada ao Mattermost (Incoming Webhook)
        mattermost_payload = {
            "username": "Assistente CDC Receitas",
            "icon_emoji": "robot",
            "text": bot_response
        }
        
        requests.post(MATTERMOST_WEBHOOK_URL, json=mattermost_payload, timeout=10)
        
        return {"status": "success"}

    except Exception as e:
        # Registrar falhas no console para auditoria rápida
        print(f"[ERRO] Falha na integração: {e}")
        return {"status": "error", "detail": str(e)}
```

---

## 3. Como Executar Localmente

### Passo 1: Configurar Variáveis locais (`.env`)
Crie um arquivo `.env` nesta pasta com suas credenciais:
```env
DIFY_API_KEY=app-sua_chave_do_dify_aqui
DIFY_API_URL=https://api.dify.ai/v1
MATTERMOST_WEBHOOK_URL=https://mattermost.seu-dominio.com/hooks/seu-webhook-id
```

### Passo 2: Instalar Dependências e Rodar o Servidor
```bash
pip install fastapi uvicorn requests python-multipart
uvicorn bridge:app --host 0.0.0.0 --port 8000
```

Seu webhook intermediário estará ouvindo em `http://sua-vps-ip:8000/webhook`. Configure esta URL no Outgoing Webhook do Mattermost.
