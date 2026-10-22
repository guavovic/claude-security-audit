# Firebase: segurança

Carregar quando o projeto tem `firebase.json`, `.firebaserc`, `firestore.rules`, `database.rules.json`, `storage.rules`, ou usa `firebase`, `firebase-admin` ou os SDKs nativos (`google-services.json`, `GoogleService-Info.plist`).

Como no Supabase, o cliente fala **direto com o banco**. Quem protege o dado são as Security Rules (Firestore, Realtime Database, Storage), não o código do app.

Cada item: **o que verificar**, **evidência**, **não se aplica**.

## Chaves e credenciais

- **`apiKey` do Firebase não é segredo; conta de serviço é.** A `apiKey` da config do cliente só identifica o projeto e pode ficar no código. A credencial do Admin SDK (`service-account*.json`, `private_key` em variável) dá acesso total e passa por cima das Rules.
  - *Evidência*: `git ls-files | grep -iE "service-account|firebase-adminsdk"`, busca por `private_key` e `client_email` no código, no histórico e no bundle. Achar a `apiKey` do cliente **não** é achado.
  - *Não se aplica*: nunca.
- **Chaves de API restritas.** As chaves criadas pelo Firebase já vêm limitadas às APIs do Firebase. Chave usada pra API paga de fora do Firebase (Maps, Gemini e outras do Google Cloud) é separada, restrita por API e por origem ou app, e a do Gemini nunca fica no cliente.
  - *Evidência*: Google Cloud Console > APIs e serviços > Credenciais (print do dono): restrição de API e de aplicativo em cada chave; no código, qual chave cada serviço usa.
  - *Não se aplica*: projeto que só usa produtos do Firebase com a chave padrão.

## Security Rules

- **Nada aberto pra todos.** `allow read, write: if true;` (Firestore e Storage) ou `".read": true, ".write": true` (Realtime Database) deixam qualquer um que saiba o ID do projeto ler, alterar e apagar.
  - *Evidência*: ler `firestore.rules`, `storage.rules` e `database.rules.json`; conferir que o arquivo do repositório é o publicado (`firebase.json` aponta pra ele, e a data do último deploy das Rules no console bate).
  - *Não se aplica*: nunca, se o produto é usado.
- **Regra de modo de teste vencida ou pra vencer.** O modo de teste cria uma regra aberta com data limite (`request.time < timestamp.date(...)`). Antes da data, está tudo aberto; depois, tudo fechado e o app quebra.
  - *Evidência*: buscar `request.time <` nas Rules.
  - *Não se aplica*: Rules já reescritas.
- **"Está logado" não basta.** `allow read, write: if request.auth != null;` deixa qualquer usuário logado ler e escrever o dado de todos. O certo é comparar o dono: `request.auth.uid == userId` no caminho, ou `resource.data.ownerId == request.auth.uid`.
  - *Evidência*: cada `allow` com `request.auth != null` sozinho em coleção com dado pessoal.
  - *Não se aplica*: coleção que é de fato compartilhada entre todos os logados (ex.: lista pública de salas), e o relatório diz isso.
- **Escrita não troca o dono nem campos protegidos.** Em `update`, a regra confere que `request.resource.data.ownerId == resource.data.ownerId` e que campos como `role`, `plan` e `credits` não vêm do cliente (`request.resource.data.diff(resource.data).affectedKeys().hasOnly([...])`).
  - *Evidência*: as regras de `create` e `update` das coleções com dono ou papel.
  - *Não se aplica*: coleção sem campo de dono nem de privilégio.
- **Validação de dado nas Rules.** Tipo e tamanho dos campos (`is string`, `size() <`), porque o cliente pode gravar qualquer coisa.
  - *Evidência*: presença de checagem de tipo e tamanho nas regras de escrita.
  - *Não se aplica*: coleção só escrita pelo servidor (Rules com `allow write: if false` e escrita pelo Admin SDK).
- **Herança no Realtime Database.** Ali a regra desce do pai pros filhos e o filho não tira o acesso dado em cima. `.read: true` na raiz abre tudo, mesmo com regra fechada embaixo.
  - *Evidência*: as regras dos níveis de cima de `database.rules.json`.
  - *Não se aplica*: projeto sem Realtime Database (Firestore não herda assim: cada `match` vale só pro próprio caminho).
- **Storage com dono e limite.** Arquivo de usuário em caminho com o `uid` e regra que compara; limite de tamanho (`request.resource.size <`) e tipo (`request.resource.contentType.matches(...)`).
  - *Evidência*: `storage.rules`.
  - *Não se aplica*: projeto sem Storage.
- **Rules testadas.** Testes com o Emulator Suite (`@firebase/rules-unit-testing`) cobrindo permitido e negado. Sem eles, confirmar no local, no emulador, com dois usuários de teste (etapa 3 da skill).
  - *Evidência*: pasta de testes de Rules e o script no `package.json`.
  - *Não se aplica*: protótipo sem dado real; registrar como "não verificado".

## App Check

- **App Check como camada extra, com enforcement ligado onde importa.** Ele confirma que a requisição vem do app de verdade (Play Integrity, App Attest/DeviceCheck, reCAPTCHA Enterprise na web) e protege Firestore, Realtime Database, Storage, Cloud Functions callable, Firebase AI Logic e outros. Não substitui as Rules nem o login: as Rules continuam sendo a proteção do dado.
  - *Evidência*: `initializeAppCheck` no cliente; console do Firebase > App Check, com cada produto em "Enforced" e não só em monitoramento (print do dono); token de debug (`FIREBASE_APPCHECK_DEBUG_TOKEN`, `self.FIREBASE_APPCHECK_DEBUG_TOKEN`) fora do build de produção.
  - *Não se aplica*: obrigatório só quando há custo por uso (Functions, AI Logic, Maps) ou abuso de bot; em app pequeno sem gasto é recomendado, não bloqueante. Enforcement ligado antes de todos os clientes mandarem token quebra versão antiga do app: olhar as métricas antes.

## Cloud Functions

- **Função callable ou HTTP confere quem chama.** `context.auth` (callable) ou verificação do ID token (HTTP); dono do recurso conferido como em qualquer API.
  - *Evidência*: o início de cada função exportada em `functions/`.
  - *Não se aplica*: projeto sem Functions.
- **Segredo da função no Secret Manager.** `defineSecret` ou `firebase functions:secrets:set`, não em `.env` versionado nem em `functions.config()`.
  - *Evidência*: `git ls-files functions/ | grep -i env` e busca de `defineSecret`.
  - *Não se aplica*: função sem segredo.

## Fontes

- Firebase, Avoid insecure rules: https://firebase.google.com/docs/rules/insecure-rules
- Firebase, Learn about using and managing API keys: https://firebase.google.com/docs/projects/api-keys
- Firebase, App Check: https://firebase.google.com/docs/app-check
- Firebase, Rules conditions e validação de dados no Firestore: https://firebase.google.com/docs/firestore/security/rules-conditions
- Firebase, Test your Security Rules (Emulator Suite): https://firebase.google.com/docs/rules/unit-tests
- Firebase, Configure your environment (segredos em Functions): https://firebase.google.com/docs/functions/config-env
