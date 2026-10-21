# Placar

Como as skills são medidas e onde ficam os resultados de cada versão.

## Alvos

Aplicações vulneráveis de propósito, rodando só na máquina local (Docker), com a lista oficial das falhas que existem:

| Alvo | O que mede |
|---|---|
| [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/) | Loja moderna em Node e Angular, falhas do OWASP Top 10 e de API |
| [DVWA](https://github.com/digininja/DVWA) | PHP clássico, injeção e upload |
| [OWASP crAPI](https://github.com/OWASP/crAPI) | API com falhas de autorização (BOLA, BFLA, mass assignment) |

Pra frente jurídica e de qualidade, os alvos são sites reais do autor, publicados só depois de corrigidos (ver [casos](casos/)).

## Como pontua

Pra cada alvo, a lista oficial vira o gabarito. Cada rodada registra:

- **Acertos:** falhas do gabarito que a skill achou, com evidência.
- **Falsos positivos:** achados que não são falha de verdade.
- **Fora do alcance:** falhas que só aparecem explorando (a skill confirma controles, não ataca), contadas à parte.
- **Custo:** tokens e tempo da rodada.

Mesmo alvo, mesma versão do alvo, mesmo modelo, pra comparar versões da skill entre si e com ferramentas como OWASP ZAP (baseline), semgrep e Lighthouse.

## Resultados

Ainda sem rodadas publicadas. Cada rodada entra aqui com a versão da skill, a data e o link pro detalhe.

| Versão | Data | Alvo | Acertos | Falsos positivos | Fora do alcance |
|---|---|---|---|---|---|
