# Supabase: segurança

Carregar quando o projeto usa `@supabase/supabase-js`, `@supabase/ssr`, pasta `supabase/` (migrations, functions) ou URL `*.supabase.co`.

O ponto que decide tudo: o navegador fala **direto com o banco** pela API do Supabase usando a chave pública. Quem protege o dado é o Postgres (grants e RLS), não o código do front. Um `select` que o app "não faz" pode ser feito por qualquer pessoa com a chave pública, que está no bundle por definição.

Cada item: **o que verificar**, **evidência**, **não se aplica**.

## Chaves

- **Só a chave pública no cliente.** Pública: `sb_publishable_...` (nova) ou `anon` (legada, JWT que começa com `eyJ`). Privada: `sb_secret_...` (nova) ou `service_role` (legada). A privada usa o papel `service_role`, que ignora o RLS: no navegador, é banco aberto.
  - *Evidência*: buscar `sb_secret_`, `service_role` e `SERVICE_ROLE` no código do front e no bundle (`dist/`, `.next/`), e conferir que nenhuma variável com prefixo público (`NEXT_PUBLIC_`, `VITE_`, `PUBLIC_`) guarda a privada. Mostrar só os 4 primeiros caracteres.
  - *Não se aplica*: nunca.
- **Chave privada só onde precisa.** Servidor, Edge Function, job agendado. O cliente criado com ela não deve ser reaproveitado pra atender pedido de usuário sem filtrar pelo dono, porque ali não há RLS.
  - *Evidência*: cada `createClient(` com a chave privada e as queries feitas por ele.
  - *Não se aplica*: projeto que não usa chave privada.
- **Chave legada desligada depois da migração.** Criar as chaves novas não revoga `anon` e `service_role`; elas seguem ativas até serem desativadas em Settings > API Keys. O Supabase anunciou o fim das chaves legadas pra fim de 2026.
  - *Evidência*: perguntar ao dono ou ver print da tela de API Keys; no código, qual formato é usado.
  - *Não se aplica*: projeto criado já com as chaves novas.

## RLS e grants

- **RLS ligado em toda tabela de schema exposto** (`public` e qualquer outro listado em Settings > API > Exposed schemas). Tabela sem RLS em schema exposto é leitura e escrita pra quem tem a chave pública, se o grant existir.
  - *Evidência* (leitura, no SQL Editor ou `psql` com o banco local do `supabase start`):
    ```sql
    select schemaname, tablename, rowsecurity
    from pg_tables where schemaname = 'public' order by 2;
    ```
    O Security Advisor do painel (Database > Advisors) também aponta "RLS disabled in public".
  - *Não se aplica*: tabela em schema não exposto.
- **Policy por operação e por papel.** Cada policy tem `to authenticated` (ou `anon`, quando público de propósito), filtra pelo dono com `auth.uid()` e trata o `null` de quem não está logado. `UPDATE` precisa de `using` **e** `with check`, senão o usuário troca o dono da linha.
  - *Evidência*:
    ```sql
    select tablename, policyname, cmd, roles, qual, with_check
    from pg_policies where schemaname = 'public' order by 1, 3;
    ```
    Marcar como achado: `qual = 'true'` em tabela com dado pessoal, policy sem `roles` definido, `UPDATE` com `with_check` vazio.
  - *Não se aplica*: tabela de conteúdo público só de leitura (ex.: catálogo), com policy de `select` aberta e sem policy de escrita. Isso é correto e deve ser dito.
- **Grants conferidos, não só policies.** Grant decide se o papel pode fazer a operação; policy decide em quais linhas. Em projetos que ainda dão todos os privilégios a `anon` e `authenticated` em tabela nova, uma tabela com RLS mas sem policy fica fechada, e sem RLS fica aberta.
  - *Evidência*: `select grantee, table_name, privilege_type from information_schema.role_table_grants where table_schema = 'public' and grantee in ('anon','authenticated');`
  - *Não se aplica*: nunca, se há tabela em schema exposto.
- **Views e funções.** View criada pelo `postgres` ignora o RLS: no Postgres 15 ou mais novo, usar `security_invoker = true`. Função `security definer` em schema exposto roda com o dono e passa por cima do RLS: precisa de `set search_path = ''` e nomes qualificados, e de preferência fica fora do schema exposto.
  - *Evidência*:
    ```sql
    select relname, reloptions from pg_class where relkind = 'v' and relnamespace = 'public'::regnamespace;
    select proname, prosecdef, proconfig from pg_proc where pronamespace = 'public'::regnamespace and prosecdef;
    ```
  - *Não se aplica*: projeto sem view nem função no schema exposto.
- **RLS em migration versionada.** Policy criada só pelo painel some quando alguém recria o banco. O certo é tudo em `supabase/migrations/`.
  - *Evidência*: `grep -rlE "enable row level security|create policy" supabase/migrations/` comparado com a lista de tabelas.
  - *Não se aplica*: projeto sem pasta `supabase/` (gerenciado só pelo painel): registrar como risco de operação, não de acesso.
- **Teste de permissão.** Testes pgTAP em `supabase/tests/` cobrindo permitido e negado por papel (`supabase test db`) são a evidência mais forte. Sem eles, confirmar no local com dois usuários de teste (etapa 3 da skill).
  - *Não se aplica*: protótipo sem dado real; registrar como "não verificado".

## Storage, Auth e Edge Functions

- **Bucket privado por padrão.** Bucket público serve qualquer arquivo pela URL. Arquivo de usuário (documento, foto pessoal) fica em bucket privado com policy em `storage.objects` e download por URL assinada.
  - *Evidência*: `select id, public from storage.buckets;` e as policies de `storage.objects` em `pg_policies`.
  - *Não se aplica*: projeto sem Storage, ou bucket só com arquivo público do site (logo, imagem de post).
- **Auth configurado.** Confirmação de e-mail ligada quando o cadastro libera algo; URLs de redirecionamento permitidas restritas ao domínio real (sem curinga amplo); provedores OAuth que não são usados, desligados.
  - *Evidência*: `supabase/config.toml` (`[auth]`, `site_url`, `additional_redirect_urls`, `enable_confirmations`) ou print do painel.
  - *Não se aplica*: projeto sem Supabase Auth.
- **Edge Function confere o JWT.** Função com `verify_jwt = false` é pública; só faz sentido pra webhook que confere assinatura própria.
  - *Evidência*: `supabase/config.toml` (`[functions.<nome>]`) e o código que lê o usuário.
  - *Não se aplica*: projeto sem Edge Functions.
- **Backup de acordo com o plano.** O que o Supabase guarda e por quanto tempo muda com o plano, e o plano gratuito pausa projeto parado. Pra projeto com dado real, conferir o que o plano cobre e se há cópia fora do provedor (item de Operação do checklist geral).
  - *Evidência*: plano do projeto, página Database > Backups (print do dono) e rotina de `supabase db dump` ou `pg_dump`, se existir.
  - *Não se aplica*: protótipo sem dado de usuário.

## Fontes

- Supabase, Row Level Security: https://supabase.com/docs/guides/database/postgres/row-level-security
- Supabase, Understanding API keys: https://supabase.com/docs/guides/api/api-keys
- Supabase, Securing your API (schemas expostos): https://supabase.com/docs/guides/api/securing-your-api
- Supabase, Storage access control: https://supabase.com/docs/guides/storage/security/access-control
- Supabase, Database Advisors: https://supabase.com/docs/guides/database/database-advisors
- Supabase, Testing your database (pgTAP): https://supabase.com/docs/guides/database/testing
