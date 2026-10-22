<div align="center">

# launch-audit

Skills pro Claude Code que auditam site, API ou aplicativo **antes de ir ao ar**, em três frentes: **segurança**, **risco jurídico no Brasil** e **qualidade de lançamento**. Toda conclusão vem com evidência, e o relatório também diz o que o projeto **não precisa**.

![versão](https://img.shields.io/github/v/release/guavovic/launch-audit?style=flat-square&label=vers%C3%A3o) ![licença](https://img.shields.io/github/license/guavovic/launch-audit?style=flat-square&label=licen%C3%A7a)

[Placar](docs/placar.md) • [Mudanças](CHANGELOG.md)

</div>

## O que tem

| Skill | Responde | Cobre |
|---|---|---|
| `auditoria-seguranca` | "O projeto está seguro?" | Segredo exposto (código, histórico do git, bundle do front), controle de acesso (IDOR/BOLA), injeção, XSS, SSRF, CSRF, CORS, sessão e JWT, upload, erro que vaza código, rota de debug, rate limit e anti-bot, dependência vulnerável ou inventada por IA, prompt injection, backup, rollback, logs |
| `auditoria-legal` | "O site pode ser processado?" | LGPD, cookies e rastreamento, política de privacidade, termos, CDC e direito de arrependimento, Decreto 7.962, Marco Civil, ECA Digital, acessibilidade (LBI e WCAG 2.2), direito autoral, publicidade enganosa |
| `auditoria-site` | "O site está pronto?" | Formulários de ponta a ponta, 404, links, HTTPS e e-mail do domínio, Core Web Vitals, SEO técnico, prévia social, analytics e funil, uptime, rastreamento de erro |

Junto vêm um **script de checagem passiva** (Python sem dependências, saída em JSON, compara com a rodada anterior) e o subagente **`auditoria-executor`**, que roda a parte braçal num modelo mais barato.

## Como pensa

- **Proporcional.** Primeiro descobre o tipo de projeto (portfólio, SaaS, loja, API, app com LLM) e monta uma matriz do que se aplica. WAF num portfólio estático ou banner de cookies num site sem rastreador aparecem como "não precisa", com o motivo.
- **Sem chute.** Cada achado tem arquivo e linha, requisição ou saída de comando. O que não deu pra provar vai pra "não verificado".
- **Seguro por padrão.** Teste ativo só em `localhost`. Em produção, só checagem passiva: headers, TLS, DNS, arquivos que não deviam estar públicos. Segredo encontrado nunca é escrito por inteiro.
- **Feito pro Brasil.** A frente jurídica parte da LGPD, das resoluções da ANPD, do CDC e do ECA Digital, e avisa quando é hora de falar com um advogado.

## Instalação

Copie as pastas de `skills/` pra `~/.claude/skills/` e o arquivo de `agents/` pra `~/.claude/agents/`. As skills são chamadas pelo pedido ("audita a segurança desse projeto", "o site tá pronto pra lançar?") ou pelo nome.

Regras só suas (alvos que não podem ser tocados, onde salvar o relatório, qual perfil de navegador usar) vão num `regras-locais.md` dentro da pasta de cada skill. Ele é lido antes de tudo e fica fora do git.

## Limites

Não substitui pentest feito por gente nem parecer jurídico. A skill aponta o que encontrou e o que não conseguiu verificar. Use só em projetos seus ou com autorização por escrito do dono.

## Placar

As skills são medidas contra aplicações vulneráveis de propósito, que têm a lista oficial das falhas, e comparadas com ferramentas conhecidas. Método e resultados em [docs/placar.md](docs/placar.md).

---

Licença MIT · Feito por [Gustavo Victor](https://github.com/guavovic)
