# Checklist legal

Referências: LGPD (Lei 13.709/2018), guia orientativo de cookies da ANPD (2022), Resolução CD/ANPD 2/2022 (pequeno porte), 15/2024 (incidente) e 18/2024 (encarregado), CDC (Lei 8.078/1990), Decreto 7.962/2013 (comércio eletrônico), Marco Civil da Internet (Lei 12.965/2014), Lei Brasileira de Inclusão (Lei 13.146/2015, art. 63), ECA Digital (Lei 15.211/2025, em vigor desde 17/03/2026), Lei de Direitos Autorais (9.610/1998), Lei de Propriedade Industrial (9.279/1996), Código do CONAR. Conferir o texto oficial antes de citar artigo.

Temas com arquivo próprio, abertos só quando o perfil pede: `email-marketing.md` (newsletter e oferta por e-mail), `pix-e-gateway.md` (cobrança, preço, estorno e devolução), `gdpr.md` (público na União Europeia) e `jogo-online.md` (ranking, público infantil, loot box e prêmio).

## LGPD

- **Só dados necessários** (minimização): cada campo do formulário tem uso claro. CPF, telefone, data de nascimento só se forem usados de fato.
- **Base legal** de cada tratamento identificada (consentimento, execução de contrato, legítimo interesse, obrigação legal). Consentimento só quando é a base certa, e aí livre, informado e revogável.
- **Checkbox de consentimento** nunca pré-marcado; newsletter separada do aceite dos termos.
- **Direitos do titular**: canal pra acesso, correção, exclusão, portabilidade e revogação, com prazo de resposta que dá pra cumprir. Excluir conta deve excluir ou anonimizar de verdade.
- **Encarregado (DPO)**: identidade e contato divulgados no site. Agente de pequeno porte pode ser dispensado de indicar, mas precisa manter canal de comunicação com o titular.
- **Incidente**: comunicar ANPD e titulares em até 3 dias úteis após saber que afetou dado pessoal (6 dias úteis pra pequeno porte); registrar todo incidente por 5 anos. Projeto deve ter isso no plano de recuperação.
- **Transferência internacional**: Analytics, Vercel, Supabase, OpenAI etc. ficam fora do Brasil; a política deve dizer.
- **Retenção**: por quanto tempo cada dado fica e o que acontece depois.
- **Dado sensível** e **dado de criança** exigem cuidado reforçado; criança: no melhor interesse e, em regra, com consentimento de um responsável.

## Cookies e rastreamento

- Inventário de cookies e scripts de terceiros, separados em essenciais e não essenciais (analytics, marketing, personalização).
- **Nada não essencial carrega antes do consentimento.** Conferir na aba de rede, não só no código.
- Banner com **aceitar e recusar no mesmo nível** (recusar não pode ser escondido), opção de configurar por categoria e jeito de mudar a escolha depois (link no rodapé).
- **Google Analytics**: usar Consent Mode v2 com padrão negado, ou trocar por ferramenta sem cookie (Plausible, Umami, Vercel Analytics) quando o projeto é simples. Neste caso, muitas vezes nem precisa de banner.
- Fonte do Google, vídeo do YouTube e mapa embutidos também mandam dado pra terceiro: carregar local, usar modo sem cookie (`youtube-nocookie.com`) ou carregar só ao clicar.
- Pixel da Meta, Hotjar, Clarity: só com consentimento de marketing ou análise.

## Documentos

- **Política de privacidade**: quem é o controlador, quais dados, pra quê, base legal, com quem compartilha, transferência internacional, retenção, direitos e como exercer, contato do encarregado, data da última atualização. **Tem que bater com o que o site faz.**
- **Termos de uso**: regras de uso, conta, conteúdo do usuário, limitação de responsabilidade razoável (cláusula abusiva é nula no CDC), foro, mudanças nos termos.
- **Política de troca e reembolso**: obrigatória na prática pra quem vende.
- Todos acessíveis no rodapé de todas as páginas e no momento da coleta (perto do botão de enviar).

## Venda online (CDC e Decreto 7.962)

- **Identificação do vendedor** em local de destaque: nome empresarial, CNPJ (ou CPF), endereço físico e eletrônico.
- Características do produto, preço total com taxas e frete, condições de pagamento, prazo de entrega, restrições.
- **Direito de arrependimento**: 7 dias a partir da compra ou do recebimento, com devolução integral, e meio fácil de exercer (art. 49 do CDC).
- Resumo do contrato antes da compra, confirmação imediata do pedido, contrato disponível pra guardar.
- Atendimento eletrônico com resposta em até 5 dias.
- Assinatura: cancelar tem que ser tão fácil quanto assinar.

## Publicidade, promessas e depoimentos

- **Sem promessa falsa** ou que não dá pra provar ("o mais rápido do Brasil", "resultado garantido", "resposta em 1 hora" sem cumprir). É publicidade enganosa (CDC art. 37).
- **Depoimentos e avaliações reais**, com autorização da pessoa; nunca inventados, nunca de banco de imagem com nome fictício.
- **Sem selo falso** (segurança, certificação, "verificado") e sem validação de fachada.
- Logo de cliente ou parceiro só com autorização.
- Preço de "de/por" verdadeiro.

## Marco Civil

- Provedor de aplicação com fins econômicos guarda **registro de acesso** (IP, data e hora) por 6 meses, em ambiente seguro (art. 15).
- Conteúdo de terceiros: ter canal pra denúncia e remoção.

## ECA Digital (quando houver público infantil provável)

- Produto direcionado a menor ou de acesso provável por menor tem deveres de proteção desde a concepção, configurações mais protetivas por padrão, ferramentas de supervisão por responsáveis e verificação de idade confiável para conteúdo impróprio (autodeclaração não basta).
- Sem perfilamento pra publicidade direcionada a criança.
- Multas altas (até 10% do faturamento no Brasil, limitado a R$ 50 milhões por infração). Projeto nessa situação: recomendar advogado.

## Propriedade intelectual

- **Imagem**: própria, de banco com licença (guardar o comprovante) ou com licença livre compatível (atenção a "uso não comercial" e atribuição obrigatória). Imagem achada no Google não é livre.
- **Imagem gerada por IA**: conferir os termos da ferramenta e não imitar personagem ou marca de terceiro.
- **Fonte, ícone, música, vídeo, sprite**: licença permite o uso (inclusive comercial, se for o caso).
- **Foto de pessoa** (equipe, cliente): autorização de uso de imagem.
- **Marca**: nome do projeto não colide com marca registrada no mesmo ramo (busca no INPI).
- Rodapé com © e ano; atribuições exigidas pelas licenças.

## Acessibilidade (LBI art. 63, referência técnica WCAG 2.2 AA)

- **Texto alternativo** em toda imagem com significado; `alt=""` em imagem decorativa.
- **Contraste** mínimo 4,5:1 em texto normal, 3:1 em texto grande e componentes.
- **Teclado**: tudo funciona sem mouse, ordem de foco lógica, foco visível, sem armadilha de foco, modal fecha com Esc.
- Formulário com `label` ligado ao campo, erro descrito em texto (não só cor) e anunciado.
- Idioma da página (`lang="pt-BR"`), títulos em hierarquia, links com texto que faz sentido sozinho.
- Vídeo com legenda; nada piscando mais de 3 vezes por segundo; respeitar `prefers-reduced-motion`.
- Zoom até 200% sem quebrar; alvo de toque com pelo menos 24x24 px.
- Formas de validar: axe-core (automático pega cerca de um terço), teclado manual, leitor de tela (NVDA) nos fluxos principais.

## Contato e conteúdo

- Informações atualizadas (endereço, telefone, horário, preço). Informação velha vira problema no CDC.
- Revisão de texto (erro de português em contrato e política tira credibilidade e pode gerar ambiguidade).
