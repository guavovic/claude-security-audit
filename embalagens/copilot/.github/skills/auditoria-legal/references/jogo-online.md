# Jogo com ranking online

Jogo para navegador, PC ou celular que manda pontuação para um servidor e mostra um ranking público, com ou sem conta, chat, compra dentro do jogo ou prêmio. O risco jurídico vem de quatro lugares: o ranking ser manipulável (e alguém sair prejudicado, principalmente se houver prêmio), os termos não dizerem as regras do jogo, o público ser de crianças e adolescentes, e a mecânica de compra ou prêmio virar caixa de recompensa, sorteio ou aposta.

Conferir o texto oficial antes de citar artigo. Não é parecer jurídico.

## Quando se aplica

- O jogo envia pontuação, tempo ou resultado para um servidor (Supabase, Firebase, API própria, serviço de leaderboard da loja ou de terceiros) e mostra ranking para outros jogadores.
- Há conta, apelido público, chat, troca de itens, compra dentro do jogo ou prêmio para quem fica no topo.

## Quando não se aplica

- Jogo offline ou com recorde só local (salvo no aparelho). Os itens de ranking e anti-trapaça não se aplicam; os de público infantil e direito autoral continuam valendo para o jogo em si.
- Ranking só entre amigos dentro de plataforma que cuida disso (Steam, Game Center, Google Play Games): o projeto ainda responde pelo que envia e pelos termos, mas moderação e conta são da plataforma.

## O que a regra pede

**Ranking íntegro (validação no servidor)**

Nenhuma lei fala de anti-trapaça. O ponto jurídico é outro: ranking manipulado que distribui prêmio ou destaque vira oferta não cumprida (CDC art. 30 e 35, quando há relação de consumo) e quebra o regulamento do próprio concurso. Sem prêmio, é qualidade do produto e reputação, então risco baixo. O mínimo esperado:

- O servidor **não aceita pontuação crua** enviada pelo cliente como verdade: valida limites plausíveis (pontuação máxima por fase, tempo mínimo), exige sessão iniciada pelo servidor e, quando dá, recalcula a partir de eventos ou de replay.
- O cliente não escreve direto na tabela do ranking: regra de acesso (RLS do Supabase, Security Rules do Firebase) bloqueia escrita pública, e a gravação passa por função no servidor.
- Limite de envios por conta e por IP, e jeito de remover pontuação e banir conta.
- Detalhe técnico da validação fica com a skill `auditoria-seguranca`; aqui o achado é o efeito jurídico.

**Termos de uso do jogo**

- Regras de conduta (trapaça, bot, conta compartilhada, apelido ofensivo), o que acontece em caso de violação (remoção do ranking, banimento) e como contestar.
- Se houver prêmio: regulamento próprio, publicado antes, com critério de desempate, data de apuração, como o vencedor é contatado e o que acontece em caso de trapaça descoberta.
- Itens virtuais e moeda do jogo: o que o jogador compra (licença de uso, não propriedade), o que acontece se o jogo acabar, se há reembolso. Cláusula que tira todo direito do consumidor é nula (CDC art. 51).
- Compra dentro do jogo segue `pix-e-gateway.md` e o direito de arrependimento.

**Dados e apelido público**

- O ranking mostra **apelido**, não nome completo nem e-mail. Se mostrar foto, nome ou cidade, isso é dado pessoal publicado: precisa de base legal e o jogador deve poder esconder (LGPD art. 6º, III e art. 18).
- Conta excluída sai do ranking ou fica anônima.

**Público infantil e adolescente**

O jogo é "de acesso provável" por menores quando o tema, o estilo, a divulgação ou a loja indicam isso; jogo casual com visual colorido quase sempre é. Nesse caso:

- **ECA Digital** (Lei 15.211/2025, em vigor desde 17/03/2026; regulamentado pelo Decreto 12.880/2026, com a ANPD como autoridade, Decreto 12.622/2025):
  - **Caixa de recompensa (loot box) é proibida** em jogo direcionado ou de acesso provável por crianças e adolescentes (art. 20). A lei define caixa de recompensa como item ou vantagem **aleatória** comprada **mediante pagamento** sem saber o conteúdo (art. 2º, IV). O Decreto 12.880 manda verificar idade para barrar a funcionalidade para menores, ou oferecer versão sem caixa de recompensa ou desligá-la por padrão, caso em que dispensa a verificação (art. 23).
  - Jogo com **interação entre usuários** (chat, áudio, vídeo, troca de itens) precisa das salvaguardas da Lei 14.852/2024, art. 16: canal de denúncia, retorno ao denunciante, revisão de penalidade e transparência. Por padrão, a interação vem limitada até o consentimento de um responsável (art. 21 e parágrafo único).
  - Sem perfilamento para direcionar publicidade a menores (art. 22). Anúncio personalizado dentro do jogo é achado.
  - Multa de até 10% do faturamento do grupo no Brasil, limitada a R$ 50 milhões por infração (art. 35).
- **Marco Legal dos Jogos Eletrônicos** (Lei 14.852/2024): jogo acessado por menores segue o melhor interesse da criança e mitiga riscos desde a concepção (art. 15); a classificação indicativa considera microtransações (art. 3º, § 2º).
- **Classificação indicativa**: o jogo informa a faixa etária no acesso (ECA Digital, art. 8º, V). Na loja, isso vem do questionário de classificação (IARC); fora da loja, seguir a política do Ministério da Justiça (Portaria MJSP 1.048/2025, que substituiu a 502/2021 e acrescentou o eixo de interatividade; conferir o texto vigente).
- **LGPD**: dado de criança só no melhor interesse e, em regra, com consentimento específico de um responsável (art. 14). Coletar o mínimo: ranking com apelido não precisa de e-mail nem de data de nascimento.

**Prêmio para o ranking**

- Prêmio dado por resultado de concurso **exclusivamente** recreativo ou desportivo, sem sorte, sem pagamento para participar e sem obrigação de comprar nada, não precisa de autorização (Lei 5.768/1971, art. 3º, II).
- Se houver taxa de inscrição, compra obrigatória, vantagem paga que ajuda a ganhar ou sorteio, vira promoção comercial que depende de autorização do Ministério da Fazenda (art. 1º) ou pode se aproximar de aposta (Lei 14.790/2023). Nesse caso: **advogado antes de lançar**.
- Prêmio para menor de idade: entregar via responsável.

## Como verificar (com evidência)

1. **Envio de pontuação**: grep por `score`, `leaderboard`, `ranking`, `highscore`, `submitScore`, `insert`, `upsert`, `from('scores')`, `collection('scores')`. Anotar arquivo:linha de onde a pontuação sai do cliente e onde é gravada.
2. **Validação**: existe função no servidor que confere a pontuação? Há limite plausível, sessão, assinatura ou replay? Regra de acesso da tabela bloqueia escrita direta (ler a política RLS ou as Security Rules)?
3. **Teste no local**: só no ambiente local ou de teste do usuário, tentar enviar pontuação absurda (por exemplo, valor máximo do tipo) pela API, com a conta de teste dele. Evidência: requisição e resposta. Nunca em produção nem em ranking com jogadores reais.
4. **Termos e regulamento**: links no jogo e na página; prints. Cobrem conduta, banimento, contestação, itens virtuais, prêmio?
5. **Ranking público**: print do que aparece de cada jogador. Só apelido?
6. **Público**: tema, arte, divulgação, classificação na loja. Perguntar ao usuário a faixa etária esperada.
7. **Caixa de recompensa**: grep por `loot`, `gacha`, `chest`, `crate`, `random`, `drop`, `roll`, `pack`, `bau`, perto de código de compra. Comprar com dinheiro (ou com moeda comprada com dinheiro) algo aleatório é caixa de recompensa.
8. **Chat e interação**: grep por `chat`, `message`, `friend`, `trade`, `voice`. Tem denúncia, bloqueio, filtro, controle do responsável e padrão restrito para menores?
9. **Anúncios**: SDK de anúncio (`admob`, `unityads`, `ironsource`, `applovin`) com personalização ligada para público infantil?

## O que recomendar

| Achado | Risco | Correção |
|---|---|---|
| Caixa de recompensa paga em jogo de acesso provável por menor | Alto | Remover, desligar por padrão ou verificar idade de forma confiável (Decreto 12.880, art. 23) |
| Prêmio com taxa, compra ou sorte sem autorização | Alto | Advogado; ou tornar o concurso gratuito e só por desempenho |
| Ranking com prêmio que aceita pontuação do cliente sem validar | Alto | Validar no servidor, bloquear escrita direta, regulamento com regra de trapaça |
| Chat aberto para menor sem denúncia nem controle do responsável | Alto | Salvaguardas da Lei 14.852, art. 16, interação limitada por padrão |
| Publicidade personalizada para público infantil | Alto | Desligar personalização (anúncio contextual) |
| Ranking sem prêmio aceitando pontuação crua | Baixo | Validar no servidor (ver `auditoria-seguranca`) |
| Ranking mostra nome, e-mail ou foto sem escolha do jogador | Médio | Só apelido, opção de esconder |
| Sem termos, ou termos sem regra de conduta e banimento | Médio | Termos com conduta, sanção e contestação |
| Classificação indicativa não informada fora da loja | Baixo | Mostrar a faixa etária no acesso |

**Advogado**: prêmio em dinheiro ou bem, qualquer compra dentro de jogo voltado a menor, chat entre desconhecidos com menores, ou jogo com faturamento. Perguntas para levar: se o concurso se enquadra no art. 3º, II da Lei 5.768, se a mecânica de compra é caixa de recompensa pela definição do ECA Digital, qual verificação de idade é aceita.

## Fontes

- ECA Digital, Lei 15.211/2025 (arts. 2º, 8º, 20, 21, 22, 35 e 41-A): <https://www.planalto.gov.br/ccivil_03/_ato2023-2026/2025/lei/L15211.htm>
- Decreto 12.880/2026 (regulamenta o ECA Digital, art. 23 sobre caixas de recompensa): <https://www.planalto.gov.br/ccivil_03/_ato2023-2026/2026/Decreto/D12880.htm>
- Decreto 12.622/2025 (ANPD como autoridade do ECA Digital): <https://www.planalto.gov.br/ccivil_03/_ato2023-2026/2025/Decreto/D12622.htm>
- Marco Legal dos Jogos Eletrônicos, Lei 14.852/2024 (arts. 3º, 15 e 16): <https://www.planalto.gov.br/ccivil_03/_ato2023-2026/2024/lei/L14852.htm>
- Lei 5.768/1971 (distribuição gratuita de prêmios, arts. 1º e 3º): <https://www.planalto.gov.br/ccivil_03/leis/l5768.htm>
- Lei 14.790/2023 (apostas de quota fixa): <https://www.planalto.gov.br/ccivil_03/_ato2023-2026/2023/lei/l14790.htm>
- CDC, Lei 8.078/1990: <https://www.planalto.gov.br/ccivil_03/leis/l8078compilado.htm>
- LGPD, Lei 13.709/2018: <https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm>
- Ministério da Justiça, classificação indicativa: <https://www.gov.br/mj/pt-br/assuntos/seus-direitos/classificacao-1>
