# Mudanças

## v0.1.0 (21/10/2026)

Primeira versão pública.

- Skills `auditoria-seguranca`, `auditoria-legal` e `auditoria-site`, com checklists de referência.
- Script `checagem_passiva.py`: HTTPS e HSTS, TLS, headers, CSP, cookies, `<head>`, scripts de terceiros, robots, sitemap, 404, sourcemap, arquivos sensíveis expostos e DNS, em JSON, com `--comparar`.
- Subagente `auditoria-executor` pra parte braçal.
- Regras locais por skill (`regras-locais.md`, fora do git).

Ajustes que vieram da primeira rodada real, num site em produção:

- Caminhos sensíveis consultados por último e resposta de anti-bot marcada como `bloqueado`, não como falha (a hospedagem bloqueia o IP por alguns minutos depois deles).
- Lighthouse antes do script na auditoria completa.
- Aviso pra não aplicar `npm audit fix --force` sem conferir, porque ele pode sugerir downgrade de versão major.
- Escape feito à mão é testado, não só lido.
