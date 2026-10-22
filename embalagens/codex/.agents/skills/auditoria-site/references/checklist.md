# Checklist do site

## Funciona

- **Formulários**: enviar de verdade e ver chegar (e-mail, banco, CRM). Validação no campo com mensagem clara, botão bloqueado durante o envio, mensagem de sucesso **e** de erro, página de obrigado (que também serve pra medir conversão), e-mail de confirmação pro usuário quando faz sentido.
- **Sem validação de fachada**: o que o front diz que validou, o back valida também; campo "obrigatório" que aceita vazio é bug.
- **Página 404** própria, com link pra home e busca ou menu; retorna status 404 de verdade (não 200).
- **Página de erro 500** amigável.
- **Todos os links** clicados ou rastreados: internos, externos, âncoras, botões de rede social, `mailto:` e `tel:`.
- Links externos com `rel="noopener"` quando abrem em nova aba.
- Funciona em Chrome, Edge, Firefox e Safari móvel (pelo menos o fluxo principal).

## Domínio e infraestrutura

- **Domínio próprio** com HTTPS válido, `www` e sem `www` redirecionando pro mesmo lugar, HTTP indo pra HTTPS.
- Domínio com renovação automática, registro bloqueado contra transferência e 2FA na conta do registrador.
- **E-mail do domínio**: SPF, DKIM e DMARC (pelo menos `p=quarantine` depois de um tempo em `none`), senão o e-mail do formulário cai no spam e o domínio pode ser falsificado.
- Registro CAA limitando quem emite certificado.

## Velocidade

- Lighthouse mobile com desempenho alto e Core Web Vitals no verde: LCP até 2,5 s, INP até 200 ms, CLS até 0,1.
- **Imagens** em AVIF ou WebP, no tamanho em que aparecem, com `width` e `height` (evita CLS), `loading="lazy"` abaixo da dobra, e a imagem principal com prioridade.
- Fontes locais com `font-display: swap` e só os pesos usados.
- JS e CSS mínimos; scripts de terceiros adiados.
- Cache e compressão (Brotli/gzip) ligados.
- **Teste em rede móvel** lenta e num celular de verdade, não só no PC.

## SEO

- **Title único** por página (até uns 60 caracteres) e **meta description única** (até uns 155), escritos pra pessoa, com a palavra-chave da página.
- Um `h1` por página, títulos em ordem.
- `sitemap.xml` com as páginas indexáveis, atualizado no build, referenciado no `robots.txt`.
- `robots.txt` sem bloquear o site inteiro por engano (comum quando sobra do ambiente de teste) e sem listar rota interna (isso revela, não protege).
- `canonical` em cada página; sem conteúdo duplicado entre domínios.
- URLs legíveis e estáveis (links personalizados curtos, sem parâmetro inútil); redirecionamento 301 quando mudar.
- Dados estruturados (JSON-LD): `Organization` ou `Person`, `WebSite`, `BreadcrumbList`, `FAQPage` quando tem FAQ, `LocalBusiness` pra negócio local, `Product` pra loja.
- `lang="pt-BR"` no html; `hreflang` se tiver outro idioma.
- Texto alternativo nas imagens (ajuda SEO e é obrigação de acessibilidade).
- **Palavras-chave**: cada página mira um termo que as pessoas buscam de verdade, sem encher de repetição.
- **Search Console** configurado, sitemap enviado, e relatório de cobertura sem erro (páginas excluídas sem motivo, 404, "rastreada mas não indexada").

## Prévia social

- **Favicon** em vários tamanhos, `apple-touch-icon`, `manifest` com ícones e `theme-color`.
- `og:title`, `og:description`, `og:image` (1200x630, JPG ou PNG, abaixo de 5 MB, URL absoluta), `og:url`, `twitter:card=summary_large_image`.
- Testar a prévia colando o link no WhatsApp, LinkedIn e Discord.

## Analytics e medição

- Analytics com consentimento (ver skill auditoria-legal) ou ferramenta sem cookie.
- **Eventos** dos pontos que importam: clique no CTA, envio de formulário, página de obrigado, clique em WhatsApp, compra.
- **Funil e desistência**: onde as pessoas abandonam (formulário pela metade, carrinho, cadastro).
- Conferir que os dados chegam (abrir o relatório em tempo real enquanto testa).
- UTM nos links de campanha e no link da bio.

## Monitoramento

- **Monitor de uptime** (UptimeRobot, Better Stack, Vercel) com alerta no celular.
- **Rastreamento de erro** no front e no back (Sentry ou similar), com sourcemap enviado só pra ferramenta.
- Alerta de vencimento de domínio e certificado.

## Conteúdo e conversão (quando se aplica)

- **Rodapé** com contato, redes, links das políticas, CNPJ quando for empresa e ano do ©.
- **CTA** claro acima da dobra; fixo no mobile em landing e negócio.
- **FAQ** com as perguntas que os clientes fazem de verdade.
- **Avaliações e cases reais**, com nome e autorização.
- **Foto real da equipe** pra negócio que vende confiança.
- **Promessa de tempo de resposta** só se for cumprida (vira obrigação pelo CDC).
- **Negócio local**: Google Meu Negócio verificado com o mesmo nome, endereço e telefone do site; mapa e botão "como chegar"; horário certo.
- **Link na bio**: página própria no domínio (não depender só de Linktree) com UTM.
- Canal de opinião e correção (o visitante consegue avisar que algo está errado).
- **Informações atualizadas**: preço, horário, endereço, portfólio, ano.
- Revisão de texto: ortografia, nome da empresa sempre igual, tom consistente.

## Revisão periódica (3 a 6 meses)

Rodar de novo: Lighthouse, links quebrados, Search Console (cobertura e consultas), dependências, certificado e domínio, formulários de ponta a ponta, informações atualizadas, analytics (o que mudou no funil).
