# Cobrança por Pix e gateway de pagamento

Loja, assinatura, venda de curso, doação com recompensa, item dentro de jogo ou app. Este arquivo cobre o que o **lojista** (o dono do site) precisa cumprir na hora de cobrar e de devolver. A regra do Banco Central é dirigida às instituições (o banco, a fintech, o gateway); ela só entra aqui na medida em que muda o que o lojista faz ou promete ao cliente.

Conferir o texto oficial antes de citar artigo. Não é parecer jurídico.

## Quando se aplica

- O site ou app gera QR Code Pix, mostra chave Pix, usa Pix Automático para assinatura, ou cobra por cartão, boleto ou carteira digital por meio de gateway (Mercado Pago, Stripe, PagSeguro, Asaas, Pagar.me, Efí e parecidos).
- Doação que entrega algo em troca (recompensa, acesso) é venda para o CDC.

## Quando não se aplica

- Portfólio, site institucional ou formulário sem cobrança.
- Link de doação sem contrapartida: não há relação de consumo, mas a identificação de quem recebe continua sendo boa prática.
- Venda feita inteira dentro de uma plataforma (loja de aplicativo, marketplace, Hotmart e parecidas): a plataforma cobra e devolve; o lojista ainda responde pela oferta e pelas informações do produto.

## O que a regra pede

**Informação antes de pagar** (CDC, Lei 8.078/1990, e Decreto 7.962/2013)

- Identificação do vendedor em local de destaque: nome empresarial ou nome, CNPJ ou CPF, endereço físico e eletrônico (Decreto 7.962, art. 2º, I e II). O nome que aparece no comprovante do Pix (titular da chave) deve bater com quem o site diz que vende: divergência assusta o cliente e parece golpe.
- Preço total com frete, taxas e qualquer despesa extra discriminada (art. 2º, IV), modalidades de pagamento, prazo de entrega e restrições (art. 2º, V e VI).
- Resumo do contrato antes de pagar, chance de corrigir erro antes de finalizar, confirmação imediata do pedido e contrato disponível para guardar (art. 4º, I a IV).
- **Desconto por meio de pagamento é permitido** (Lei 13.455/2017, art. 1º): o preço no Pix pode ser menor que no cartão. O desconto tem que estar informado em local e formato visíveis (Lei 10.962/2004, art. 5º-A). O contrário também é diferenciação: cobrar mais no cartão é legal, mas precisa estar claro antes do pagamento.
- Segurança do pagamento e dos dados (Decreto 7.962, art. 4º, VII).

**Confirmação, comprovante e atendimento**

- Confirmar o pedido assim que o pagamento é reconhecido, pelo mesmo canal (e-mail ou tela), com número do pedido, itens, valor e forma de pagamento.
- O comprovante do Pix é emitido pelo banco de quem pagou; ele não substitui a confirmação do pedido. Nota fiscal é regra tributária, fora desta skill: só perguntar se o projeto emite.
- Atendimento eletrônico com resposta em até 5 dias (Decreto 7.962, art. 4º, parágrafo único).

**Arrependimento, estorno e cobrança indevida**

- Arrependimento em 7 dias na compra fora do estabelecimento (CDC art. 49), pelo mesmo meio da compra (Decreto 7.962, art. 5º, § 1º), com valores devolvidos de imediato e atualizados (CDC art. 49, parágrafo único).
- **Cartão**: o lojista comunica imediatamente o arrependimento à administradora para que a cobrança não entre na fatura ou seja estornada (Decreto 7.962, art. 5º, § 3º). Na prática, é o reembolso pelo painel ou pela API do gateway.
- **Pix**: o próprio recebedor inicia a devolução, total ou parcial, em quantas partes quiser, pela conta onde recebeu (Regulamento do Pix, anexo à Resolução BCB 1/2020, arts. 40 e 41). A devolução pelo mecanismo do Pix precisa ser iniciada em até **90 dias** da transação original e depende de saldo na conta (art. 41-A). Depois de 90 dias o lojista ainda deve devolver, só que por um Pix novo para a conta de quem pagou. Política de reembolso deve dizer o prazo e o meio.
- Cobrança em quantia indevida (cobrar duas vezes, cobrar valor maior que o anunciado) dá direito à devolução **em dobro**, salvo engano justificável (CDC art. 42, parágrafo único). Pagamento duplicado por Pix é comum quando a tela não confirma: devolver logo.

**Regras do Pix que afetam o lojista**

- **MED (Mecanismo Especial de Devolução)**: se quem pagou contesta por suspeita de fraude ou houve falha operacional, a instituição do recebedor pode bloquear e devolver o valor sem pedir licença ao lojista (Regulamento do Pix, art. 41-B; bloqueio cautelar no art. 39-B). Desde 2025 o MED foi ampliado para rastrear o dinheiro além da primeira conta (Resolução BCB 493/2025, conferir a vigência atual no Banco Central). Consequência para o lojista: venda paga com dinheiro de golpe pode ser desfeita depois da entrega. Não é motivo para negar o arrependimento ao cliente honesto.
- **Pix Automático** (assinatura): quem paga autoriza uma vez no próprio banco e pode **cancelar ou alterar a qualquer momento** pelo banco (Regulamento do Pix, art. 11-Q, § 1º, IV). O site precisa tratar esse cancelamento (encerrar ou suspender a assinatura) e não pode continuar cobrando por outro meio sem nova autorização.
- **Pix Cobrança** (QR Code dinâmico, com ou sem vencimento): valor, vencimento, juros e multa aparecem para quem paga; o que aparece tem que bater com o pedido e com o que foi anunciado.

**Cartão e gateway**

- Número de cartão e código de segurança não podem ser guardados pelo site. É exigência do padrão PCI DSS (contrato com bandeira e gateway, não lei) e base da segurança que o Decreto 7.962 pede. Checkout hospedado ou tokenização pelo gateway tiram o site desse escopo.
- O gateway é operador de dado pessoal (LGPD): a política de privacidade cita quem processa o pagamento e se há transferência internacional.

## Como verificar (com evidência)

1. **Página de produto e checkout**: prints com preço, frete, desconto do Pix e condições, antes de pagar. O desconto aparece antes da escolha do meio de pagamento? O total final bate com o anunciado?
2. **Rodapé ou página de identificação**: nome, CNPJ ou CPF, endereço. Comparar com o titular da chave Pix (se a chave aparece na tela ou no QR Code estático, o nome sai no app do banco ao pagar).
3. **Fluxo de pagamento no código**: grep por `pix`, `qrcode`, `brcode`, `copia e cola`, `txid`, `webhook`, `stripe`, `mercadopago`, `pagseguro`, `asaas`, `pagarme`, `efi`, `checkout`, `refund`, `estorno`, `devolu`. Conferir:
   - Pedido só vira "pago" por confirmação **no servidor** (webhook assinado ou consulta à API do PSP), nunca pela tela do cliente nem por print de comprovante enviado pelo cliente.
   - QR Code dinâmico gerado por pedido (valor e identificador próprios) em vez de chave fixa com valor digitado à mão.
   - Nenhum campo de cartão chega ao servidor do site: grep por `card_number`, `cardNumber`, `cvv`, `cvc`, `pan`, e por log de corpo de requisição no checkout.
   - Webhook trata pagamento repetido (mesmo `txid` ou `endToEndId`) sem duplicar pedido.
4. **Reembolso**: existe rota ou processo documentado para devolver Pix e estornar cartão? Quem faz, em quanto tempo? Pedir ao usuário o passo a passo real. Testar só em ambiente de teste do gateway (sandbox), nunca com pagamento real.
5. **Assinatura**: grep por `subscription`, `recorr`, `pix automatico`, `cancel`. O site trata o aviso de cancelamento vindo do banco ou do gateway? Cancelar pelo site é tão fácil quanto assinar?
6. **Confirmação**: print da tela e do e-mail de confirmação de um pedido de teste (sandbox).
7. **Política de troca e reembolso**: cita prazo, meio de devolução (Pix, estorno no cartão) e o que acontece depois de 90 dias no Pix?

## O que recomendar

| Achado | Risco | Correção |
|---|---|---|
| Vende sem identificar CNPJ ou CPF e endereço | Alto | Dados no rodapé e no checkout |
| Pedido marcado como pago pela tela do cliente ou por comprovante enviado | Alto | Confirmar só por webhook assinado ou consulta à API do PSP |
| Site guarda número de cartão ou CVV, ou loga o corpo do checkout | Alto | Checkout hospedado ou tokenização, apagar o que foi gravado (ver `auditoria-seguranca`) |
| Sem meio de exercer arrependimento ou sem processo de reembolso | Alto | Botão ou canal claro, devolução pelo mesmo meio, processo escrito |
| Total no fim maior que o anunciado (taxa surpresa) | Alto | Mostrar o total desde a página do produto |
| Desconto do Pix não informado antes, ou preço diferente sem aviso | Médio | Exibir o preço por meio de pagamento |
| Titular da chave Pix diferente de quem vende, sem explicação | Médio | Usar chave da empresa ou explicar o intermediador |
| Pix Automático sem tratar cancelamento | Médio | Escutar o evento de cancelamento e encerrar a assinatura |
| Webhook sem idempotência (pedido duplicado) | Médio | Deduplicar pelo identificador da transação |
| Política de reembolso sem prazo ou meio | Baixo | Completar a política |

**Advogado**: assinatura com renovação automática, venda internacional, marketplace com repasse a terceiros (split) ou faturamento relevante. Perguntas para levar: regras de cancelamento e reembolso da assinatura, responsabilidade em fraude contestada pelo MED depois da entrega, se o modelo de repasse exige autorização do Banco Central.

## Fontes

- CDC, Lei 8.078/1990: <https://www.planalto.gov.br/ccivil_03/leis/l8078compilado.htm>
- Decreto 7.962/2013 (comércio eletrônico): <https://www.planalto.gov.br/ccivil_03/_ato2011-2014/2013/decreto/d7962.htm>
- Lei 13.455/2017 (preço diferente por meio de pagamento): <https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2017/lei/l13455.htm>
- Lei 10.962/2004 (informação de preço, art. 5º-A): <https://www.planalto.gov.br/ccivil_03/_ato2004-2006/2004/lei/l10.962.htm>
- Decreto 5.903/2006 (regulamenta a informação de preço): <https://www.planalto.gov.br/ccivil_03/_ato2004-2006/2006/decreto/d5903.htm>
- Resolução BCB 1/2020 e Regulamento do Pix (texto consolidado, versão 43): <https://www.bcb.gov.br/estabilidadefinanceira/exibenormativo?tipo=Resolu%C3%A7%C3%A3o%20BCB&numero=1>
- Resolução BCB 493/2025 (aprimora o MED): <https://www.bcb.gov.br/estabilidadefinanceira/exibenormativo?tipo=Resolu%C3%A7%C3%A3o%20BCB&numero=493>
- Banco Central, Guia de implementação do MED: <https://www.bcb.gov.br/content/estabilidadefinanceira/pix/Guia_MED.pdf>
- LGPD, Lei 13.709/2018: <https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm>
- PCI Security Standards Council (PCI DSS): <https://www.pcisecuritystandards.org/>
