# 💾 Política de Backup 3-2-1 (CDC Receitas)

Este documento estabelece as diretrizes de governança e sustentação para garantir a integridade e a recuperabilidade de todas as receitas e configurações críticas do repositório.

---

## 1. O Padrão de Redundância 3-2-1

Para mitigar o risco de perda catastrófica de dados, todas as receitas, scripts de infraestrutura e prompts do CDC Receitas seguem a regra 3-2-1:

1.  **3 Cópias dos Dados**:
    *   **Cópia Primária**: Repositório central de produção hospedado no GitHub (`dxcdc/Agentes-Dify`).
    *   **Cópia Secundária (Local)**: Clones locais ativos nas máquinas de desenvolvimento dos colaboradores.
    *   **Cópia Terciária (Backup)**: Arquivos de snapshot incrementais exportados periodicamente.
2.  **2 Mídias Diferentes**:
    *   **Armazenamento em Nuvem**: Servidores do GitHub (Git) e cofres em nuvem.
    *   **Dispositivo de Armazenamento Local**: Disco local das máquinas de desenvolvimento ou servidor NAS local da empresa.
3.  **1 Localização Externa (Offsite)**:
    *   Sincronização dos snapshots compactados em um bucket externo seguro em nuvem (ex: AWS S3, Backblaze B2 ou Google Cloud Storage) geograficamente isolado da sede da empresa.

---

## 2. Frequência de Backup e Sincronização

A rotina de backup é automatizada por meio de scripts localizados na pasta `infra/` que seguem o cronograma:

*   **Sincronização com GitHub**: Contínua (a cada Push aprovado no fluxo Git).
*   **Backup Incremental Offsite**: Diário (executado às 02:00 UTC-3 de forma automatizada pelo servidor de sustentação).
*   **Backup Completo (Snapshot de Infraestrutura)**: Semanal (aos domingos).

---

## 3. Segurança e Criptografia do Backup

> [!CAUTION]
> Backups de infraestrutura local podem conter variáveis de ambiente ou configurações de credenciais. Portanto:
> 1. Os snapshots de backup contendo variáveis sensíveis devem ser **criptografados** na origem antes de serem transferidos para o armazenamento offsite.
> 2. Utilize algoritmos de criptografia fortes (como AES-256) com chaves gerenciadas de forma independente (armazenadas em gerenciador de senhas corporativo).

---

## 4. Testes de Recuperação (Drills)

A política de backup só é eficaz se os dados puderem ser restaurados. A equipe de sustentação deve realizar simulações de recuperação a cada **6 meses**, registrando o resultado no histórico de incidentes/postmortem caso ocorram falhas durante a restauração simulada.
