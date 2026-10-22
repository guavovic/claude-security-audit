# PWA e service worker: lançamento

Carregar quando o site registra service worker ou tem `manifest.webmanifest`. A parte de segurança (resposta logada ou de erro no cache, logout, escopo) está na skill `auditoria-seguranca`, em `references/stacks/pwa.md`.

Primeira pergunta: **o site precisa de service worker?** Portfólio e landing raramente precisam funcionar offline; um SW mal configurado é a causa mais comum de "subi a correção e o usuário ainda vê a versão velha". Se o SW não traz nada (sem offline, sem push, sem instalação), remover é uma recomendação válida, e o relatório diz isso.

Cada item: **o que verificar**, **evidência**, **não se aplica**.

## Atualização de versão

- **Versão nova chega ao usuário.** O navegador só troca de SW quando o arquivo muda (byte a byte) e a versão nova espera todas as abas da antiga fecharem. Se o SW guarda HTML ou JS sem hash, o usuário fica com o site velho até fechar tudo.
  - *Evidência*: no local, publicar build, mudar um texto, publicar de novo e recarregar: DevTools > Application > Service Workers mostra o novo como "waiting" ou já ativo? Em produção, abrir o site, depois do deploy recarregar e ver se o texto mudou.
  - *Não se aplica*: projeto sem SW.
- **Estratégia de troca escolhida de propósito.** Duas saídas corretas: aviso "nova versão, recarregar" (o app manda `SKIP_WAITING` quando o usuário aceita) ou `skipWaiting` automático. O automático faz uma página carregada com a versão antiga ser atendida pelo SW novo, o que quebra se o build mudou os nomes dos arquivos; em site de conteúdo costuma ser aceitável.
  - *Evidência*: `skipWaiting`, `clientsClaim`, `registerType: 'autoUpdate'` ou `'prompt'` (`vite-plugin-pwa`) no código ou config.
  - *Não se aplica*: projeto sem SW.
- **Caches antigos apagados.** No `activate`, o SW apaga os caches que não são da versão atual (Workbox faz isso com `cleanupOutdatedCaches`). Nome de cache com prefixo do site, pra não apagar cache de outro app na mesma origem.
  - *Evidência*: handler de `activate` ou a opção do plugin; DevTools > Cache Storage depois de duas versões (só os da atual devem sobrar).
  - *Não se aplica*: SW que não usa Cache Storage.
- **Número de versão rastreável.** Versão do SW ou do cache que muda a cada deploy (hash do build ou número) e aparece em algum lugar fácil de ler, pra saber qual versão o usuário tem quando ele reclamar.
  - *Evidência*: constante de versão no SW ou o manifesto de precache com hash.
  - *Não se aplica*: projeto sem SW.
- **Arquivo do SW sem cache longo no host.** `sw.js` com `Cache-Control: no-cache` (ou `max-age=0`).
  - *Evidência*: `curl -sSI https://site/sw.js` (1 requisição).
  - *Não se aplica*: projeto sem SW.

## Offline e erro

- **Página offline útil.** Sem rede, a navegação mostra uma página própria ("sem conexão") e não a tela de erro do navegador nem uma página quebrada.
  - *Evidência*: DevTools > Network > Offline e navegar; `navigateFallback` ou rota de fallback no SW.
  - *Não se aplica*: site que não promete funcionar offline.
- **Fallback não esconde 404.** `navigateFallback` responde só sem rede; com rede, URL inexistente ainda devolve a 404 real. Conferir com `navigateFallbackDenylist` para `/api/` e arquivos.
  - *Evidência*: navegar pra uma URL inexistente com o SW ativo e ver status e página.
  - *Não se aplica*: SW sem fallback de navegação.

## Instalação

- **Manifesto completo.** `name`, `short_name`, `start_url`, `display`, `theme_color`, `background_color` e ícones 192x192 e 512x512 (com um `purpose: "maskable"`), referenciado no `<head>`.
  - *Evidência*: o arquivo do manifesto e DevTools > Application > Manifest, que lista os avisos.
  - *Não se aplica*: site que não quer ser instalável (aí o manifesto pode nem existir, e o relatório diz que não precisa).
- **Lighthouse sem aviso de PWA relevante.** Os itens de instalação apareciam numa categoria própria que o Lighthouse removeu; hoje a conferência é pelo painel Manifest do DevTools.
  - *Evidência*: print do painel Manifest.
  - *Não se aplica*: site não instalável.

## Fontes

- web.dev, The service worker lifecycle: https://web.dev/articles/service-worker-lifecycle
- Chrome for Developers, Handling service worker updates with immediacy (workbox-window): https://developer.chrome.com/docs/workbox/handling-service-worker-updates
- Chrome for Developers, Strategies for service worker caching: https://developer.chrome.com/docs/workbox/caching-strategies-overview
- vite-pwa, Register service worker (`autoUpdate` e `prompt`): https://vite-pwa-org.netlify.app/guide/register-service-worker
- MDN, Web app manifests: https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Manifest
- web.dev, Add a web app manifest: https://web.dev/articles/add-manifest
