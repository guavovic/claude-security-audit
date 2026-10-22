# PWA e service worker: segurança

Carregar quando o projeto registra service worker (`navigator.serviceWorker.register`, `sw.js`, `service-worker.js`, Workbox, `vite-plugin-pwa`, `@vite-pwa/astro`, `next-pwa`, `serwist`) ou tem `manifest.webmanifest`. A parte de lançamento (atualização de versão, offline, manifesto) está na skill `auditoria-site`, em `references/stacks/pwa.md`.

O risco central: o Cache Storage do service worker **não segue `Cache-Control`**. O que o SW decide guardar fica lá até o próprio SW apagar, inclusive resposta com dado do usuário ou resposta de erro.

```bash
grep -rnE "serviceWorker\.register|workbox|registerRoute|caches\.open|cache\.put|precache" --exclude-dir={node_modules,dist,.git} .
```

Cada item: **o que verificar**, **evidência**, **não se aplica**.

## O que vai pro cache

- **Resposta autenticada fora do cache compartilhado.** Rota de API com dado do usuário (`/api/me`, perfil, pedidos) não deve casar com regra de cache do SW. Em computador compartilhado, o próximo usuário vê o dado do anterior offline.
  - *Evidência*: lista de rotas do `registerRoute` (ou do `runtimeCaching` do plugin) cruzada com as rotas logadas da etapa 1; no local, logado, abrir DevTools > Application > Cache Storage e conferir que nenhuma entrada é resposta logada.
  - *Não se aplica*: site sem login.
- **Logout limpa o que é do usuário.** Ao sair, o app apaga os caches com dado pessoal (`caches.delete`) ou envia `Clear-Site-Data` na resposta do logout. O valor `"storage"` desse header remove registros de service worker, IndexedDB e `localStorage`; o MDN não garante que ele limpe o Cache Storage, então o `caches.delete` explícito é o que confirma.
  - *Evidência*: código do logout; no local, Cache Storage depois de sair.
  - *Não se aplica*: site sem login, ou SW que não guarda nada logado (item anterior confirmado).
- **Resposta de erro não é guardada.** Com Workbox, `NetworkFirst` e `StaleWhileRevalidate` guardam por padrão status 200 **e 0** (resposta opaca, de outra origem, cujo status real o SW não enxerga). Uma opaca pode ser um 500 ou uma página de bloqueio do anti-bot servida pra sempre. `CacheFirst` guarda só 200 por padrão. SW escrito à mão costuma guardar qualquer coisa: precisa conferir `response.ok` antes do `cache.put`.
  - *Evidência*: `CacheableResponsePlugin` com `statuses` em cada rota; em SW manual, a linha antes de cada `cache.put`.
  - *Não se aplica*: SW que só faz precache dos arquivos do build.
- **Navegação (HTML) não fica presa.** HTML servido com `CacheFirst` mantém a versão velha da página, que pode apontar pra script antigo com falha já corrigida.
  - *Evidência*: estratégia usada pra `request.mode === 'navigate'` ou pro `navigateFallback`.
  - *Não se aplica*: site sem SW de navegação (só cache de imagem e fonte).

## Escopo e origem

- **Escopo do SW.** Um SW na raiz controla a origem inteira. Se o mesmo domínio serve painel admin ou API, eles passam pelo SW também.
  - *Evidência*: caminho do arquivo registrado e opção `scope`; header `Service-Worker-Allowed` se houver.
  - *Não se aplica*: domínio com um único app.
- **SW servido do próprio domínio e sem cache longo.** O arquivo do SW vem da mesma origem (exigência do navegador) e o host não deve mandar `Cache-Control` longo nele, pra correção de segurança chegar rápido. Navegadores atuais ignoram o cache HTTP na checagem do script principal, mas os `importScripts` ainda seguem `updateViaCache`.
  - *Evidência*: 1 requisição no arquivo do SW em produção, lendo `Cache-Control`; opção `updateViaCache` no `register`.
  - *Não se aplica*: projeto sem SW.
- **Recurso de terceiro no cache.** Guardar script de CDN de terceiro no SW prolonga a vida de uma versão comprometida. Preferir versão fixa com `integrity` e não guardar no runtime cache.
  - *Evidência*: rotas do `registerRoute` com domínio externo.
  - *Não se aplica*: SW que só guarda a própria origem.

## Push e mensagens

- **Push com chave VAPID privada só no servidor.** A chave pública vai no cliente; a privada nunca.
  - *Evidência*: busca por `VAPID` e `web-push` no código e no bundle.
  - *Não se aplica*: PWA sem notificação.
- **`postMessage` confere a origem.** Handler de `message` no SW ou na página que executa ação a partir do conteúdo recebido confere `event.origin` ou o cliente de origem.
  - *Evidência*: `addEventListener('message'` no código.
  - *Não se aplica*: SW sem troca de mensagem.

## Fontes

- Chrome for Developers, workbox-cacheable-response (critérios padrão por estratégia): https://developer.chrome.com/docs/workbox/modules/workbox-cacheable-response
- Chrome for Developers, Strategies for service worker caching: https://developer.chrome.com/docs/workbox/caching-strategies-overview
- web.dev, The service worker lifecycle: https://web.dev/articles/service-worker-lifecycle
- MDN, Clear-Site-Data: https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Clear-Site-Data
- MDN, ServiceWorkerContainer.register (`scope`, `updateViaCache`): https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerContainer/register
