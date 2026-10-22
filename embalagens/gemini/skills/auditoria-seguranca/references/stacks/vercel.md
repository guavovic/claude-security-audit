# Vercel: segurança

Carregar quando o projeto tem `vercel.json`, pasta `.vercel/`, adaptador `@astrojs/vercel` ou `@vercel/*` no `package.json`, ou o deploy acontece na Vercel. A parte de lançamento (prévia indexada, domínio, cache) está na skill `auditoria-site`, em `references/stacks/vercel.md`.

Cada item: **o que verificar**, **evidência** (como confirmar lendo), **não se aplica** (quando pular e dizer no relatório).

## Variáveis de ambiente

- **Segredo fora de variável pública.** Tudo com prefixo de framework público (`NEXT_PUBLIC_`, `VITE_`, `PUBLIC_`, `NUXT_PUBLIC_`) vai pro navegador, esteja onde estiver cadastrado.
  - *Evidência*: `vercel env ls` (lista nomes, não valores) cruzado com a busca de prefixos públicos de `references/comandos.md`; depois do build, buscar prefixos de chave privada no bundle.
  - *Não se aplica*: nunca. Todo projeto na Vercel passa por aqui.
- **Segredo marcado como Secret (só escrita).** A Vercel separa Config (legível por quem tem acesso ao projeto) e Secret (não dá pra ler depois de salvo). Chave de API, senha de banco e token devem ser Secret.
  - *Evidência*: tela Settings > Environment Variables, coluna de tipo; pedir print ao dono se eu não tiver acesso.
  - *Não se aplica*: projeto sem nenhum segredo (site estático sem formulário nem API).
- **Escopo por ambiente.** Prévia usando a mesma chave de produção (banco, pagamento, e-mail) faz um teste de branch mexer em dado real. O certo é chave separada ou modo de teste no ambiente Preview.
  - *Evidência*: `vercel env ls preview` e `vercel env ls production`, comparando quais nomes existem em cada um; o valor só o dono confirma.
  - *Não se aplica*: projeto com prévia desligada (deploy só da branch principal) ou sem segredo.
- **Mudança de variável exige deploy novo.** A Vercel aplica a variável só nos deploys seguintes. Segredo trocado depois de vazamento continua valendo nos deploys antigos que ainda respondem (URL gerada de deploy anterior).
  - *Evidência*: data da última alteração da variável comparada com a data do deploy em produção (`vercel ls`).
  - *Não se aplica*: quando não houve troca de segredo.
- **`.env` baixado pela CLI fora do git.** `vercel env pull` grava `.env` ou `.env.local` com valores reais.
  - *Evidência*: `git check-ignore -v .env .env.local` e `git ls-files | grep -i "\.env"`.
  - *Não se aplica*: nunca, se a CLI foi usada no projeto.

## Prévias e proteção de deployment

- **Prévias protegidas.** A proteção padrão (Standard Protection, disponível em todos os planos) exige login na Vercel pra abrir prévias e URLs geradas, deixando só o domínio de produção público. Prévia aberta expõe recurso ainda não revisado, rota de debug e variável de Preview.
  - *Evidência*: Settings > Deployment Protection (print do dono) ou, passivo, 1 requisição numa URL de prévia: protegida responde com redirecionamento pro login da Vercel (401/302), aberta responde 200 com a página.
  - *Não se aplica*: projeto com prévia desligada; ou quando a prévia pública é proposital (portfólio estático, sem segredo) e o dono sabe disso. Dizer no relatório que é decisão consciente.
- **Bypass de automação e links compartilháveis sob controle.** "Protection Bypass for Automation" e links compartilháveis furam a proteção. O segredo do bypass é segredo: só em variável do CI, nunca no repositório.
  - *Evidência*: buscar `x-vercel-protection-bypass` e `VERCEL_AUTOMATION_BYPASS_SECRET` no código e nos workflows; listar no painel os links compartilháveis ativos.
  - *Não se aplica*: projeto sem proteção de prévia ou sem teste automatizado contra prévia.
- **URL gerada de produção.** Com Standard Protection, a URL `projeto-hash.vercel.app` de produção também fica protegida, e código que monta URL com `VERCEL_URL` passa a falhar. Sem proteção, deploys antigos seguem acessíveis pela URL gerada, com o código e as dependências da época.
  - *Evidência*: buscar `VERCEL_URL` e `VERCEL_BRANCH_URL` no código; 1 requisição numa URL gerada antiga.
  - *Não se aplica*: site estático sem dado sensível, onde versão antiga não traz risco.
- **Proteger produção inteira só quando faz sentido.** "All Deployments" fecha também o domínio de produção. É exagero pra site público; faz sentido pra ferramenta interna ou beta fechado.
  - *Evidência*: perfil do projeto (etapa 1 da skill).
  - *Não se aplica*: site público. Dizer que não precisa.

## Código servido e erros

- **Sourcemap de produção.** O build não deve publicar `.map` aberto. A Vercel tem "Protected Source Maps", que deixa os `.map` atrás do login da Vercel.
  - *Evidência*: o script passivo já testa sourcemap; no código, `productionBrowserSourceMaps` (Next) ou `build.sourcemap` (Vite/Astro).
  - *Não se aplica*: projeto open source em que o código já é público (o sourcemap não revela nada novo; registrar como info).
- **Headers de segurança configurados.** A Vercel não adiciona CSP, `nosniff` nem `frame-ancestors` sozinha; vêm de `vercel.json` (`headers`), do framework ou do middleware.
  - *Evidência*: bloco `headers` do `vercel.json` e a resposta real lida pelo script passivo.
  - *Não se aplica*: nunca.
- **Logs da função sem segredo.** Runtime Logs ficam visíveis pra todo membro do time.
  - *Evidência*: buscar `console.log` com `req.headers`, `process.env`, token ou body inteiro nas funções e rotas de API.
  - *Não se aplica*: site sem função (estático puro).

## Firewall e abuso

- **Firewall e anti-bot proporcionais.** A Vercel tem firewall de plataforma sempre ligado e regras configuráveis (rate limit, desafio, bloqueio por IP ou caminho). Regra de rate limit faz sentido em login, formulário e rota que custa dinheiro; em site estático é exagero.
  - *Evidência*: aba Firewall do projeto (print do dono) ou `vercel.json`; comportamento observado no anti-bot (a skill já registra o bloqueio depois de consultar `/.env`).
  - *Não se aplica*: site estático sem formulário.
- **Conta e time com 2FA.** Quem entra na conta da Vercel lê variáveis Config, troca domínio e publica código.
  - *Evidência*: pergunta direta ao dono (não dá pra ver de fora).
  - *Não se aplica*: nunca.

## Fontes

- Vercel, Deployment Protection: https://vercel.com/docs/deployment-protection (atualizada em 15/09/2026)
- Vercel, Environment variables: https://vercel.com/docs/environment-variables (atualizada em 17/09/2026)
- Vercel, Config and Secret environment variables: https://vercel.com/docs/environment-variables/sensitive-environment-variables
- Vercel, Protection Bypass for Automation: https://vercel.com/docs/deployment-protection/methods-to-bypass-deployment-protection/protection-bypass-automation
- Vercel, Protected Source Maps: https://vercel.com/docs/deployment-protection/protected-source-maps
- Vercel, Firewall: https://vercel.com/docs/vercel-firewall
