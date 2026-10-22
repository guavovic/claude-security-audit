# Vercel: lançamento

Carregar quando o site é publicado na Vercel. A parte de segurança (variáveis, proteção de prévia, firewall) está na skill `auditoria-seguranca`, em `references/stacks/vercel.md`.

Cada item: **o que verificar**, **evidência**, **não se aplica**.

## Indexação e domínio

- **Só o domínio de produção indexado.** A Vercel manda `X-Robots-Tag: noindex` nas prévias e nos deploys de produção antigos. Prévia em domínio próprio de branch (ex.: `beta.site.com`) **não** recebe o header e pode ir pro Google.
  - *Evidência*: `curl -sSI <url-da-previa> | grep -i x-robots-tag` (1 requisição); no domínio de produção, o mesmo comando **não** deve mostrar `noindex`.
  - *Não se aplica*: projeto sem prévia nem domínio de branch.
- **Um endereço canônico.** O `projeto.vercel.app` de produção responde junto com o domínio próprio e pode virar conteúdo duplicado. O certo é redirecionar o `.vercel.app` pro domínio (Settings > Domains) ou, no mínimo, `canonical` apontando pro domínio em todas as páginas.
  - *Evidência*: 1 requisição em `https://projeto.vercel.app/` lendo status e `Location`; `<link rel="canonical">` no HTML.
  - *Não se aplica*: site sem domínio próprio (o `.vercel.app` é o endereço oficial).
- **`www` e sem `www` no mesmo lugar.** Um dos dois cadastrado como redirecionamento (308/301) pro outro no painel.
  - *Evidência*: `curl -sSI https://www.dominio` e `https://dominio`.
  - *Não se aplica*: nunca, quando há domínio próprio.

## Cache e redirecionamentos

- **Cache certo por tipo de arquivo.** Arquivo com hash no nome (`/_astro/`, `/_next/static/`, `/assets/`) com `Cache-Control` longo e `immutable`; HTML com revalidação, pra versão nova aparecer na hora.
  - *Evidência*: `curl -sSI` numa página e num arquivo do build; bloco `headers` do `vercel.json`.
  - *Não se aplica*: nunca.
- **Redirecionamentos em `vercel.json`, permanentes quando a URL mudou de vez.** `redirects` com `permanent: true` (308) pra URL antiga que tinha visita ou link; `trailingSlash` e `cleanUrls` coerentes com o `canonical` e o sitemap.
  - *Evidência*: `vercel.json` e 1 requisição numa URL antiga.
  - *Não se aplica*: site novo, sem URL antiga.
- **404 com status 404.** Rewrite de SPA (`"source": "/(.*)", "destination": "/"`) faz qualquer caminho responder 200 com a home: ruim pro Google e esconde link quebrado. O script passivo já marca fallback de SPA.
  - *Evidência*: `rewrites` do `vercel.json` e o resultado do script passivo.
  - *Não se aplica*: site sem rewrite geral.

## Monitoramento e limites

- **Analytics e Speed Insights, se usados, ativos em produção.** `@vercel/analytics` e `@vercel/speed-insights` só medem com o componente no layout e o recurso ligado no painel. Uso de analytics também entra na skill `auditoria-legal` (cookies e LGPD).
  - *Evidência*: o componente no layout; requisições de `/_vercel/insights/` no navegador.
  - *Não se aplica*: site que usa outra ferramenta de analytics ou nenhuma.
- **Limite do plano.** Plano Hobby tem limite de deploys por dia e é para uso não comercial. Site de negócio no Hobby é risco de ficar sem deploy no dia do lançamento e de violar os termos.
  - *Evidência*: plano da conta (pergunta ao dono) e tipo do site (etapa 1).
  - *Não se aplica*: portfólio e projeto pessoal sem fim comercial.
- **Rollback conhecido.** Saber promover o deploy anterior (Instant Rollback no painel ou `vercel rollback`) antes do lançamento.
  - *Evidência*: pergunta ao dono; lista de deploys com `vercel ls`.
  - *Não se aplica*: nunca.

## Fontes

- Vercel, Are Vercel Preview Deployments indexed by search engines?: https://vercel.com/kb/guide/are-vercel-preview-deployment-indexed-by-search-engines
- Vercel, Project configuration (`vercel.json`: headers, redirects, rewrites, trailingSlash, cleanUrls): https://vercel.com/docs/project-configuration
- Vercel, Working with domains: https://vercel.com/docs/domains/working-with-domains
- Vercel, Instant Rollback: https://vercel.com/docs/instant-rollback
- Vercel, Limits: https://vercel.com/docs/limits
- Vercel, Fair use guidelines (uso comercial no Hobby): https://vercel.com/docs/limits/fair-use-guidelines
