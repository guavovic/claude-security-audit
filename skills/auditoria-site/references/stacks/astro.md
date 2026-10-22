# Astro: lançamento

Carregar quando o site é feito em Astro (`astro.config.*`). A parte de segurança (endpoints, `checkOrigin`, ilhas, `set:html`) está na skill `auditoria-seguranca`, em `references/stacks/astro.md`.

Cada item: **o que verificar**, **evidência**, **não se aplica**.

## Estático ou servidor

- **Renderizar no servidor só o que precisa.** O Astro é estático por padrão e o próprio guia diz que `output: 'server'` não traz recurso a mais, só muda o padrão. Página que é igual pra todo mundo (home, sobre, blog) prerenderizada carrega mais rápido e não gasta função.
  - *Evidência*: `output` e `adapter` no config; lista de arquivos com `prerender = false` (ou, em `output: 'server'`, sem `prerender = true`).
  - *Não se aplica*: site sem adaptador (tudo estático).
- **Cache da página sob demanda.** Página renderizada no servidor e igual pra todos pode mandar `Cache-Control` com `s-maxage` pra CDN guardar; página personalizada não.
  - *Evidência*: `Astro.response.headers.set('Cache-Control', ...)` nas páginas sob demanda e a resposta real.
  - *Não se aplica*: site estático.

## SEO

- **`site` definido e sitemap gerado.** `@astrojs/sitemap` precisa de `site` no config e gera `sitemap-index.xml` + `sitemap-0.xml`. Rota dinâmica renderizada no servidor **não** entra sozinha: precisa de `customPages` ou de sitemap próprio.
  - *Evidência*: config (`site`, `integrations`); `dist/sitemap-*.xml` depois do build; `robots.txt` apontando pro `sitemap-index.xml` (não pro `sitemap-0.xml`).
  - *Não se aplica*: nunca, se o site quer aparecer no Google.
- **`filter` tira o que não deve ser indexado.** Página de obrigado, rascunho, 404 e área logada fora do sitemap.
  - *Evidência*: opção `filter` e as URLs presentes no XML gerado.
  - *Não se aplica*: site sem essas páginas.
- **`canonical` e `og:url` a partir de `Astro.site`.** Montar com `new URL(Astro.url.pathname, Astro.site)`, pra prévia e produção não apontarem pra domínios errados.
  - *Evidência*: componente de `<head>` do layout.
  - *Não se aplica*: nunca.
- **`trailingSlash` coerente.** A opção `trailingSlash` do config, o `canonical`, o sitemap e o host (`vercel.json`) usam a mesma forma de URL; senão o Google vê duas URLs por página.
  - *Evidência*: config, uma URL do sitemap e 1 requisição com e sem barra final.
  - *Não se aplica*: site de uma página só.

## Velocidade

- **Imagens pelo `<Image />` ou `<Picture />`.** Os componentes de `astro:assets` geram formato moderno, `width`/`height` e `loading="lazy"`; `<img>` cru com arquivo de `public/` não passa por otimização.
  - *Evidência*: `grep -rn "<img " src/` e o peso das imagens de `public/`.
  - *Não se aplica*: site sem imagem de conteúdo.
- **Imagem principal com prioridade.** A imagem do LCP (geralmente o topo) sem `loading="lazy"` e com prioridade (`priority` no componente nas versões que têm, ou `loading="eager"` + `fetchpriority="high"`).
  - *Evidência*: o componente do topo e o elemento LCP apontado pelo Lighthouse.
  - *Não se aplica*: página sem imagem acima da dobra.
- **Ilha com a diretiva mais tardia que funciona.** `client:load` só no que precisa responder na hora; menu e carrossel abaixo da dobra com `client:visible` ou `client:idle`; componente que não tem interação, sem diretiva nenhuma.
  - *Evidência*: `grep -rnE "client:(load|idle|visible|media|only)" src/` e o JS total no Lighthouse.
  - *Não se aplica*: site sem framework de UI.
- **Fontes locais.** Fonte do Google baixada e servida do próprio site (ou pela API de fontes do Astro, quando disponível na versão) com `font-display: swap` e só os pesos usados.
  - *Evidência*: `<link>` pra `fonts.googleapis.com` no layout ou `@font-face` local.
  - *Não se aplica*: site só com fonte do sistema.

## Páginas de erro

- **`src/pages/404.astro` existe** e o host serve com status 404.
  - *Evidência*: o arquivo e o resultado do script passivo (que já detecta fallback de SPA).
  - *Não se aplica*: nunca.
- **`500.astro` em site com servidor.** Página de erro amigável pra falha em rota sob demanda.
  - *Evidência*: o arquivo `src/pages/500.astro`.
  - *Não se aplica*: site estático.

## Fontes

- Astro, On-demand rendering: https://docs.astro.build/en/guides/on-demand-rendering/
- Astro, @astrojs/sitemap: https://docs.astro.build/en/guides/integrations-guide/sitemap/
- Astro, Configuration reference (`site`, `trailingSlash`, `output`): https://docs.astro.build/en/reference/configuration-reference/
- Astro, Images: https://docs.astro.build/en/guides/images/
- Astro, Front-end frameworks (diretivas `client:*`): https://docs.astro.build/en/guides/framework-components/
- Astro, Custom 404 and 500 pages: https://docs.astro.build/en/basics/astro-pages/#custom-404-error-page
