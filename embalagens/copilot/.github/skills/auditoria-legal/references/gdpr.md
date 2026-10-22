# GDPR: site com público europeu

O Regulamento Geral de Proteção de Dados da União Europeia (Regulamento (UE) 2016/679, GDPR, ou RGPD na versão portuguesa) alcança empresa de fora da UE em certos casos. A LGPD foi inspirada nele, então boa parte do que o projeto já faz para a LGPD serve. Este arquivo diz quando o GDPR entra e o que muda.

Conferir o texto oficial antes de citar artigo. Não é parecer jurídico.

## Quando se aplica

Pelo art. 3º, n.º 2, o GDPR vale para quem não tem estabelecimento na UE quando trata dados de pessoas que **estão na UE** e:

- **(a) oferece bens ou serviços a elas**, pagos ou gratuitos. O considerando 23 dá os sinais: site em idioma de país da UE com opção de comprar nesse idioma, preço em euro, envio para a Europa, menção a clientes ou usuários na UE, campanha paga mirando países da UE.
- **(b) monitora o comportamento delas** dentro da UE: rastreamento para criar perfil, publicidade direcionada, análise de comportamento ligada a pessoa identificável (considerando 24).

Exemplos que entram: loja que entrega em Portugal, app em inglês com assinatura em euro, jogo com servidor e ranking abertos a jogadores europeus e divulgado para eles, SaaS que aceita cliente europeu.

## Quando não se aplica

- O site só está **acessível** da Europa. Acesso por si só não basta (considerando 23): site em português, voltado ao Brasil, preço em real, sem envio para fora e sem campanha lá fora não oferece serviço à UE.
- Portfólio pessoal em inglês sem coleta nem rastreamento de perfil. Inglês sozinho não indica público europeu.
- Visitante europeu ocasional num site brasileiro. Nesse caso a LGPD continua valendo (a LGPD alcança quem oferece serviço a pessoas no Brasil ou coleta dado aqui, art. 3º).

Caso de dúvida (Analytics comum, sem perfil nem publicidade, com visita europeia relevante): registrar como "provavelmente não se aplica" e explicar o motivo. Aplicar o padrão mais protetor de cookies costuma resolver os dois lados.

## O que muda em relação à LGPD

| Tema | LGPD | GDPR |
|---|---|---|
| Representante | Encarregado (DPO) com dispensa para agente de pequeno porte | Quem é de fora da UE e cai no art. 3º, n.º 2 nomeia **representante na UE** (art. 27), salvo tratamento ocasional, sem dado sensível em larga escala e de baixo risco |
| Encarregado (DPO) | Regra geral, com dispensa para pequeno porte | Só obrigatório nos casos do art. 37 (monitoramento sistemático em larga escala, dado sensível em larga escala, órgão público) |
| Bases legais | 10 hipóteses (art. 7º), incluindo proteção do crédito | 6 hipóteses (art. 6º); não existe base de proteção do crédito |
| Cookies | Guia da ANPD (2022), consentimento para o não essencial | Diretiva ePrivacy (2002/58/CE, art. 5º, n.º 3) e lei de cada país: consentimento **prévio** para tudo que não é estritamente necessário, inclusive analytics na maioria dos países |
| E-mail marketing | LGPD e autorregulação (ver `email-marketing.md`) | Diretiva ePrivacy, art. 13: opt-in; exceção só para cliente e produto semelhante, com saída na coleta e em cada mensagem |
| Idade para consentir | Criança até 12 anos: consentimento de um responsável (art. 14, § 1º); ECA Digital para menores de 18 | Serviço online oferecido diretamente a criança: consentimento próprio a partir de 16 anos, cada país pode baixar até 13 (art. 8º) |
| Incidente | ANPD e titulares em até 3 dias úteis (Resolução CD/ANPD 15/2024) | Autoridade de controle em até **72 horas** (art. 33); titulares sem demora quando o risco é alto (art. 34) |
| Registro das operações | Recomendado, simplificado para pequeno porte | Obrigatório (art. 30), com exceção para menos de 250 pessoas quando o tratamento é ocasional e de baixo risco |
| Multa | Até 2% do faturamento no Brasil, limitada a R$ 50 milhões por infração | Até 20 milhões de euros ou 4% do faturamento mundial, o que for maior (art. 83, n.º 5) |
| Reclamação | ANPD | Autoridade de controle do país do titular |

**Transferência de dados**: em 26/01/2026 a Comissão Europeia reconheceu o Brasil como país com proteção adequada, e a ANPD reconheceu a UE na Resolução CD/ANPD 32/2026. Dado que sai da UE para o projeto no Brasil não precisa de cláusula contratual padrão. Isso não muda o resto: se o dado vai depois para servidor nos EUA (Vercel, Supabase, OpenAI), vale a regra de transferência para lá (EU-US Data Privacy Framework ou cláusulas padrão), e o projeto continua obrigado a cumprir o GDPR em si.

## Como verificar (com evidência)

1. **Sinais de público europeu**: idioma e seletor de país (print), moeda (grep por `EUR`, `€`, `currency`), opções de envio ou de país no checkout, `hreflang` com `-PT`, `-ES`, `-DE`, `-FR` etc., campanhas pagas mirando a Europa (perguntar ao usuário), dados do Analytics por país (perguntar, nunca acessar a conta sem ele).
2. **Monitoramento**: lista de rastreadores já levantada no checklist de cookies. Pixel de publicidade ou ferramenta de perfil ativa para visitante europeu é sinal da alínea (b).
3. **Cookies com geolocalização europeia**: se o banner muda por país, testar com o navegador emulando local e idioma de um país da UE (Playwright: `locale` e `geolocation`). Nada não essencial antes do aceite, recusar no mesmo nível que aceitar.
4. **Política**: existe versão no idioma do público europeu? Cita base legal do GDPR para cada uso, representante na UE (se exigido), direito de reclamar à autoridade de controle, transferências?
5. **Formulários e idade**: serviço aberto a menores na UE pede confirmação de idade e consentimento de responsável abaixo de 16 (ou da idade do país)?
6. **Incidente**: o plano de resposta prevê 72 horas para a UE, além dos prazos da ANPD?

## O que recomendar

| Achado | Risco | Correção |
|---|---|---|
| Oferece à UE e carrega rastreador antes do consentimento | Alto | Bloquear não essenciais até o aceite, recusar no mesmo nível |
| Oferece à UE sem política que cubra o GDPR | Alto | Política com bases do GDPR, direitos, autoridade de controle e transferências |
| Cai no art. 3º, n.º 2 sem representante na UE e sem enquadrar a exceção | Médio | Avaliar a exceção do art. 27, n.º 2 ou contratar serviço de representante |
| Plano de incidente só com o prazo da ANPD | Médio | Incluir as 72 horas e a autoridade competente |
| E-mail marketing para europeu sem opt-in | Médio | Ver `email-marketing.md` |
| Política só em português para público que não lê português | Baixo | Versão no idioma do público |

Quando o GDPR não se aplica, dizer isso no relatório e por quê (é o caso mais comum dos projetos brasileiros).

**Advogado**: venda recorrente para a UE, dado sensível, público infantil europeu ou volume grande. Perguntas para levar: se cabe a exceção do representante, qual autoridade é a principal, se o Analytics configurado conta como monitoramento.

## Fontes

- Regulamento (UE) 2016/679 (GDPR), arts. 3º, 6º, 8º, 27, 30, 33, 34, 37, 83 e considerandos 23 e 24: <https://eur-lex.europa.eu/legal-content/PT/TXT/?uri=CELEX:32016R0679>
- Diretiva 2002/58/CE (ePrivacy), arts. 5º e 13: <https://eur-lex.europa.eu/legal-content/PT/TXT/?uri=CELEX:02002L0058-20091219>
- Comissão Europeia, decisões de adequação (Brasil, 26/01/2026): <https://commission.europa.eu/law/law-topic/data-protection/international-dimension-data-protection/adequacy-decisions_en>
- ANPD, Brasil e União Europeia reconhecem adequação mútua: <https://www.gov.br/anpd/pt-br/assuntos/noticias/brasil-e-uniao-europeia-reconhecem-adequacao-mutua-em-protecao-de-dados-pessoais>
- ANPD, transferência internacional de dados (Resolução CD/ANPD 32/2026): <https://www.gov.br/anpd/pt-br/assuntos/assuntos-internacionais/transferencia-internacional-de-dados>
- LGPD, Lei 13.709/2018: <https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm>
- EDPB, diretrizes 3/2018 sobre o alcance territorial (art. 3º): <https://www.edpb.europa.eu/our-work-tools/our-documents/guidelines/guidelines-32018-territorial-scope-gdpr-article-3-version_pt>
