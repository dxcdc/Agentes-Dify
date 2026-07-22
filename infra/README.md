# ⚙️ Configurações de Infraestrutura (CDC Receitas)

Esta pasta centraliza os scripts de automação de infraestrutura, rotinas de backup offsite e configurações de segurança e DNS do ambiente CDC Receitas.

---

## 1. Diretrizes para Scripts de Infraestrutura

Todo script ou arquivo de configuração hospedado nesta pasta deve seguir as seguintes premissas:

1.  **Modularidade**: Prefira scripts autocontidos e comentados.
2.  **Segurança e Cofres**:
    *   **NUNCA** guarde chaves SSH privadas, senhas de banco ou chaves de criptografia diretamente nesta pasta.
    *   Utilize ferramentas de cofres locais (como Bitwarden CLI, Vault ou variáveis do ambiente do servidor de CI/CD) para injetar credenciais no momento da execução.
3.  **Idempotência**: Scripts de configuração (ex: setup do Docker ou redes) devem ser idempotentes, ou seja, podem ser executados múltiplas vezes sem quebrar o estado atual da infraestrutura.

---

## 2. Conteúdo Esperado nesta Pasta

*   **Sincronização e Backups**: Scripts de sincronização incremental offsite (ex: executando o Restic ou rclone).
*   **Docker / Containers**: Configurações de orquestração local (ex: `docker-compose.yml` para subida do Dify self-hosted).
*   **Políticas de DNS e Proxy**: Arquivos de configuração de servidores Web/Proxy (ex: Nginx, Caddy) para exposição segura das APIs.
