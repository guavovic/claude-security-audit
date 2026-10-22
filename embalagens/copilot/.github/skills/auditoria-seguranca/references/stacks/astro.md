# Astro: segurança

Carregar quando o projeto tem `astro.config.*` ou `astro` nas dependências. A parte de lançamento (sitemap, imagens, prerender e cache) está na skill `auditoria-site`, em `references/stacks/astro.md`.

Primeiro passo: **descobrir o que roda no servidor**. Astro é estático por padrão (`output: 'static'`); só vira servidor com adaptador e com `output: 'server'` ou `export const prerender = false` em alguma rota. Site 100% estático pula quase tudo daqui e fica com os itens de segredo no build e HTML injetado.

```bash
grep -nE "output|adapter|security|env:" astro.config.*
grep -rnE "prerender\s*=\s*(false|true)" src/
grep -rnE "server:defer" src/
```

Cada item: **o que verificar**, **evidência**, **não se aplica**.

## Segredos e variáveis

- **Só `PUBLIC_` vai pro cliente, mas o build estático grava valores no HTML.** Em página prerenderizada, qualquer variável lida no frontmatter vira texto no HTML gerado, mesmo sem prefixo `PUBLIC_`.
  - *Evidência*: depois do `astro build`, buscar prefixos de chave privada e nomes de variáveis em `dist/` (ver `references/comandos.md`); conferir no código onde `import.meta.env.<NOME>` aparece em página sem `prerender = false`.
  - *Não se aplica*: projeto sem nenhum segredo.
- **`astro:env` com o tipo certo.** No schema de `env` do config, segredo deve ser `context: 'server', access: 'secret'` (fica fora do bundle). `context: 'client'` é sempre público; não existe segredo de cliente.
  - *Evidência*: bloco `env.schema` em `astro.config.*` e os imports de `astro:env/client` e `astro:env/server` no código.
  - *Não se aplica*: projeto que não usa `astro:env` (aí vale o item anterior).

## Rotas no servidor (SSR e endpoints)

- **Endpoint com controle de acesso.** Cada arquivo em `src/pages/**` que exporta `GET`, `POST`, `PUT`, `PATCH` ou `DELETE` e roda no servidor é uma API: confere sessão e dono do recurso como qualquer rota (A01 do checklist geral).
  - *Evidência*: listar `grep -rlE "export (const|async function) (GET|POST|PUT|PATCH|DELETE)" src/pages`; em cada um, mostrar a linha que lê a sessão e o filtro pelo dono.
  - *Não se aplica*: endpoints prerenderizados (geram arquivo estático no build, como `rss.xml` e `sitemap`), que não recebem requisição.
- **Middleware cobre o que deveria.** `src/middleware.*` costuma ser onde fica a checagem de login. Rota prerenderizada **não passa** pelo middleware em tempo de requisição.
  - *Evidência*: ler o `onRequest` e a lista de caminhos protegidos; cruzar com as rotas que têm `prerender = false`.
  - *Não se aplica*: projeto sem login.
- **CSRF pelo `checkOrigin`.** `security.checkOrigin` vem ligado por padrão (desde a 4.9) e devolve 403 em POST, PUT, PATCH e DELETE de outra origem com tipo de formulário, só nas páginas renderizadas sob demanda.
  - *Evidência*: `security.checkOrigin` ausente ou `true` no config; se estiver `false`, procurar outra proteção (token ou cookie `SameSite`).
  - *Não se aplica*: site sem rota que grava, ou API autenticada só por header (Bearer), sem cookie.
- **`allowedDomains` atrás de proxy.** Quando o app confia em `X-Forwarded-Host` pra montar URL (link de redefinição de senha, redirect), `security.allowedDomains` deve listar os domínios reais. Sem a lista, o Astro ignora o header, o que é o comportamento seguro.
  - *Evidência*: config; buscar `Astro.url`, `request.url` e `X-Forwarded-Host` em código que gera link enviado por e-mail.
  - *Não se aplica*: site estático, ou SSR que não monta URL absoluta.
- **Cookies de sessão com atributos.** `Astro.cookies.set` aceita `httpOnly`, `secure`, `sameSite`; o padrão não liga tudo.
  - *Evidência*: cada `cookies.set(` no código, com as opções passadas.
  - *Não se aplica*: projeto sem cookie de sessão.
- **Actions validadas.** Em `src/actions/`, cada action tem `input` com schema (Zod) e confere a sessão no `handler`; action é endpoint público por definição.
  - *Evidência*: ler `src/actions/index.*`.
  - *Não se aplica*: projeto sem Actions.

## Ilhas

- **Props de ilha de cliente são públicas.** Tudo passado pra um componente com `client:*` é serializado no HTML. Não passar objeto inteiro do banco, token ou campo interno.
  - *Evidência*: buscar `client:(load|idle|visible|media|only)` e ler as props de cada uso.
  - *Não se aplica*: site sem ilha de cliente.
- **Ilha de servidor (`server:defer`).** O Astro cifra as props, que vão na URL de uma requisição GET (ou POST, se passar de 2048 bytes). A ilha é uma rota própria: confere a sessão de novo, não confia que "só aparece pra quem está logado". Com deploy em várias regiões ou CDN guardando página antiga, definir `ASTRO_KEY` fixa (gerada por `astro create-key`) como segredo.
  - *Evidência*: componentes com `server:defer`, a checagem de sessão dentro deles e a presença de `ASTRO_KEY` nas variáveis (só o nome).
  - *Não se aplica*: projeto sem `server:defer`.
- **Cache da ilha personalizada.** Resposta de ilha com dado do usuário não pode sair com `Cache-Control: public`.
  - *Evidência*: headers definidos em `Astro.response.headers` dentro da ilha e a resposta real no local.
  - *Não se aplica*: ilha com conteúdo igual pra todo mundo.

## HTML injetado e CSP

- **`set:html` e Markdown.** `set:html` não escapa. Conteúdo vindo de usuário, CMS ou API externa precisa de sanitização antes.
  - *Evidência*: `grep -rn "set:html" src/` e a origem de cada valor.
  - *Não se aplica*: `set:html` só com conteúdo do próprio repositório (ex.: SVG ou JSON-LD gerado no build), o que é seguro e deve ser dito.
- **CSP do Astro.** A partir da 6.0 há `security.csp`, que gera hashes dos scripts e estilos do próprio site; não funciona no `astro dev`. Em projeto que já define CSP no host (ex.: `vercel.json`), conferir que as duas não se contradizem.
  - *Evidência*: config e o header real lido pelo script passivo.
  - *Não se aplica*: versão do Astro anterior à 6.0 (CSP fica no host).

## Fontes

- Astro, Configuration reference (`output`, `security.checkOrigin`, `security.allowedDomains`, `security.csp`): https://docs.astro.build/en/reference/configuration-reference/
- Astro, On-demand rendering: https://docs.astro.build/en/guides/on-demand-rendering/
- Astro, Server islands: https://docs.astro.build/en/guides/server-islands/
- Astro, Environment variables: https://docs.astro.build/en/guides/environment-variables/
- Astro, Actions: https://docs.astro.build/en/guides/actions/
- Astro, Template directives (`set:html`): https://docs.astro.build/en/reference/directives-reference/
