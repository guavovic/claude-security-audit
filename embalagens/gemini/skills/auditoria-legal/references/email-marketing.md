# E-mail marketing

Newsletter, aviso de promoção, carrinho abandonado, sequência automática de boas-vindas. Tudo o que é enviado para vender, divulgar ou reengajar entra aqui. E-mail transacional (confirmação de pedido, redefinição de senha, aviso de segurança) não é marketing e não depende de opt-in, mas não pode carregar oferta escondida.

Conferir o texto oficial antes de citar artigo. Não é parecer jurídico.

## Quando se aplica

- O projeto tem formulário de newsletter, "receba novidades", lista de espera ou cadastro com caixa de "quero receber ofertas".
- A loja ou o app manda e-mail de oferta para cliente ou para quem só se cadastrou.
- O projeto usa ferramenta de envio em massa (Mailchimp, Brevo, Resend, SendGrid, RD Station, ConvertKit e parecidas), ou importa lista de contatos.

## Quando não se aplica

- O site só tem formulário de contato e responde quem escreveu, um a um, sobre o que a pessoa perguntou.
- Só existe e-mail transacional.
- Portfólio sem nenhum cadastro.

Diga isso no relatório para ninguém montar política de descadastro que o projeto não precisa.

## O que a regra pede

**LGPD (Lei 13.709/2018)**

- Toda lista precisa de base legal (art. 7º). Para quem nunca comprou nada, a base é o **consentimento** (art. 7º, I): livre, informado, inequívoco e para finalidade determinada (art. 5º, XII). Checkbox **desmarcado**, separado do aceite dos termos.
- Consentimento se revoga a qualquer momento, por procedimento **gratuito e facilitado** (art. 8º, § 5º). Descadastro tem que ser tão simples quanto o cadastro.
- Para quem já é cliente, o **legítimo interesse** (art. 7º, IX e art. 10) pode sustentar oferta de produto parecido com o que a pessoa comprou, desde que ela espere receber, que exista opção de sair em toda mensagem e que o teste de balanceamento esteja registrado. O guia de legítimo interesse da ANPD (fevereiro de 2024) traz o modelo do teste.
- Lista comprada, alugada ou raspada da internet não tem base legal: quem está nela nunca consentiu nem espera o e-mail. Risco alto.
- O titular pode pedir acesso, correção e eliminação (art. 18). Descadastrar não é o mesmo que apagar: a pessoa pode pedir as duas coisas.
- A política de privacidade diz que existe envio de marketing, qual ferramenta envia (é operador, muitas vezes fora do Brasil, então há transferência internacional) e como sair.

**Autorregulação (CAPEM)**

O Código de Autorregulamentação para a Prática de E-mail Marketing (CAPEM) foi subscrito pelo Comitê Gestor da Internet (Resolução CGI.br/RES/2009/001/P, de 06/02/2009) junto com entidades do mercado. Não é lei, mas é a referência de boa prática no Brasil e o que um provedor ou entidade de classe usa para chamar algo de spam. Os pontos centrais, segundo a divulgação oficial da época (o anexo com o texto integral não está publicado no site do CGI.br hoje, então citar como autorregulação, sem número de artigo):

- **Opt-in**: só enviar para quem pediu. O primeiro e-mail não pode ser o pedido de permissão.
- **Soft opt-in**: relação comercial prévia permite o envio, desde que haja descadastro.
- **Opt-out** em toda mensagem, atendido em até 2 dias quando feito pelo link e em até 5 dias úteis por outros meios.
- Remetente identificado, em domínio próprio (não usar Gmail, Hotmail e parecidos para disparo em massa), assunto que não engana.

**Provedores de e-mail**

Desde fevereiro de 2024 o Gmail exige de quem manda mais de 5 mil mensagens por dia: SPF, DKIM e DMARC, descadastro em um clique (cabeçalho `List-Unsubscribe` com `List-Unsubscribe-Post`, RFC 8058) e pedido atendido em até 2 dias. O Yahoo segue regra parecida. Não é lei, mas quem não cumpre cai no spam ou é bloqueado, e a regra técnica coincide com o CAPEM. A parte de DNS é checada pela skill `auditoria-site`.

**Público europeu**: na União Europeia o marketing por e-mail segue a Diretiva 2002/58/CE (art. 13): opt-in, com exceção só para cliente e produto semelhante, com opção de sair no momento da coleta e em cada mensagem. Ver `gdpr.md`.

**Criança e adolescente**: e-mail de oferta para menor depende de consentimento de um responsável quando for criança (LGPD art. 14, § 1º) e não pode usar perfilamento para publicidade (ECA Digital, Lei 15.211/2025, art. 22). Projeto nessa situação: recomendar advogado.

## Como verificar (com evidência)

1. **Formulário**: print do campo de inscrição. A caixa vem desmarcada? Está separada do aceite dos termos? Diz o que vai ser enviado e com que frequência? Tem link para a política perto do botão?
2. **Código**: grep por `newsletter`, `subscribe`, `mailchimp`, `brevo`, `sendinblue`, `resend`, `sendgrid`, `rdstation`, `convertkit`, `audience`, `list_id`, `marketing`, `optin`, `opt_in`. Anotar arquivo:linha de onde o contato entra na lista e se o consentimento é gravado (data, origem, texto aceito). Sem registro do consentimento não há como provar que ele existe.
3. **Checkbox pré-marcado**: grep por `checked`, `defaultChecked`, `defaultValue={true}` perto do campo de marketing.
4. **Mensagem real**: pedir ao usuário um e-mail de marketing recebido (ou mandar um de teste para uma caixa dele). Conferir remetente, domínio, link de descadastro visível, endereço ou contato do remetente, cabeçalhos `List-Unsubscribe` e `List-Unsubscribe-Post` (em "mostrar original" no Gmail).
5. **Descadastro de ponta a ponta**: clicar no link numa caixa de teste do usuário, confirmar que não pede login nem senha, e conferir na ferramenta (ou no banco local) que o contato saiu da lista. Nunca descadastrar contato real.
6. **Origem da lista**: perguntar de onde vieram os contatos. Importação de planilha sem registro de consentimento é achado.
7. **Política**: a política de privacidade cita o envio, a ferramenta e o jeito de sair?

## O que recomendar

| Achado | Risco | Correção |
|---|---|---|
| Lista comprada ou raspada, ou envio para quem nunca pediu | Alto | Parar o envio, apagar a lista, recomeçar com opt-in |
| Checkbox pré-marcado ou atrelado ao aceite dos termos | Alto | Caixa própria, desmarcada, com texto claro |
| Sem descadastro, ou descadastro que pede login ou não funciona | Alto | Link em toda mensagem, um clique, efeito em até 2 dias |
| Consentimento não registrado | Médio | Gravar data, origem, IP e texto aceito |
| Política não cita marketing nem a ferramenta | Médio | Atualizar a política |
| Sem `List-Unsubscribe` ou disparo de domínio gratuito | Médio | Configurar na ferramenta, enviar de domínio próprio com SPF, DKIM e DMARC |
| Oferta enfiada em e-mail transacional | Baixo | Separar os dois tipos de envio |

Sugerir **confirmação dupla** (double opt-in: a pessoa clica num link para confirmar) quando a lista cresce por formulário público: prova o consentimento e barra cadastro de terceiros.

## Fontes

- LGPD, Lei 13.709/2018: <https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm>
- ANPD, Guia orientativo: hipóteses legais de tratamento, legítimo interesse (02/2024): <https://www.gov.br/anpd/pt-br/centrais-de-conteudo/materiais-educativos-e-publicacoes>
- Resolução CGI.br/RES/2009/001/P (subscreve o código de autorregulação de e-mail marketing): <https://www.cg.org.br/resolucoes/documento/2009/CGI.br_Resolucao_2009_001.pdf>
- ECA Digital, Lei 15.211/2025: <https://www.planalto.gov.br/ccivil_03/_ato2023-2026/2025/lei/L15211.htm>
- Google, diretrizes para remetentes de e-mail: <https://support.google.com/a/answer/81126>
- RFC 8058, descadastro em um clique: <https://www.rfc-editor.org/rfc/rfc8058>
- Diretiva 2002/58/CE, art. 13: <https://eur-lex.europa.eu/legal-content/PT/TXT/?uri=CELEX:32002L0058>
