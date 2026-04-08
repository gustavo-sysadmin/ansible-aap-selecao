# Ansible AAP — Selecao Deploy

Repositório de automação para deploy rolling com zero downtime em 3 servidores RHEL 8.

## Estrutura

```
roles/
  webserver/        # instala e configura nginx
  deploy/           # copia index.html com conteúdo dinâmico

playbooks/
  precheck.yml        # verifica conectividade e nginx nos 3 hosts
  security-update.yml # yum update --security em todos os hosts
  deploy.yml          # rolling update serial:1 com block/rescue/always
```

## Variáveis esperadas (via Survey no AAP)

| Variável | Descrição | Exemplo |
|---|---|---|
| `app_version` | Versão da aplicação | `2.1.0` |
| `deploy_env` | Ambiente alvo | `dev` / `homolog` / `prod` |

## Comportamento

- **Idempotente** — rodar 2x não causa mudanças desnecessárias
- **Rolling update** — `serial: 1`, para no primeiro host que falhar
- **Rollback automático** — restaura `index.html.bak` em caso de falha
- **Auditoria** — todo deploy registra em `/var/log/deploy_audit.log`
- **Health check** — valida nginx na porta 80 antes de avançar pro próximo host
