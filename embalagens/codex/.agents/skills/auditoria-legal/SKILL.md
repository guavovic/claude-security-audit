---
name: "auditoria-legal"
description: "Audita os riscos jurídicos de um site ou aplicativo no Brasil pra não ser processado nem multado: LGPD (base legal, só dados necessários, direitos do titular, encarregado, incidente), aviso e preferência de cookies, scripts de rastreamento e Google Analytics, política de privacidade, termos de uso, política de troca e reembolso e direito de arrependimento (CDC), dados legais da empresa no rodapé (Decreto 7.962), Marco Civil (guarda de logs), ECA Digital (público infantil), acessibilidade (LBI e WCAG: alt nas imagens, contraste, teclado), direitos autorais de imagem e fonte, marca, e promessa ou depoimento falso. Diz o que se aplica ao tipo de projeto e o que não. Use quando o usuário pedir \"o site pode ser processado?\", \"tá dentro da LGPD?\", \"precisa de política de privacidade / termos / aviso de cookies?\", \"auditoria jurídica\", \"conformidade\", \"acessibilidade do site\", ou antes de lançar algo que coleta dado ou vende."
---

<!-- Arquivo gerado por ferramentas/gerar_embalagens.py a partir de skills/ e agents/. Não edite à mão: mude a fonte e rode o script de novo. -->

> `<pasta-desta-skill>` é a pasta onde está este SKILL.md. As três skills ficam lado a lado, então `<pasta-desta-skill>/../auditoria-seguranca` é a pasta da skill de segurança.

# Auditoria legal

**Regras locais:** se existir `regras-locais.md` nesta pasta, leia antes de tudo. Ele guarda as regras de quem usa a skill (alvos proibidos, onde salvar o relatório, qual navegador usar, como abrir issue) e vale acima do padrão daqui.

Objetivo: achar o que pode virar **processo, multa, reclamação no Procon ou derrubada do site**, provar com evidência (URL, print, arquivo:linha) e dizer como corrigir. Também dizer **o que não se aplica**, pra ninguém gastar tempo com documento que o projeto não precisa.

**Não é parecer jurídico.** Projeto que vende, guarda dado sensível, atende criança ou fatura de verdade: o relatório recomenda revisão por advogado e lista as perguntas pra levar. Texto de política nunca é inventado como se fosse padrão legal: é rascunho marcado pra revisão.

Data de referência das regras: outubro de 2026. Antes de citar artigo ou prazo que pode ter mudado (resolução da ANPD, regulamentação do ECA Digital), confira no site oficial (planalto.gov.br, gov.br/anpd).

## 1. Perfil do projeto

Descubra pelo código e pelo site no ar:

1. **Quem opera**: pessoa física (portfólio, projeto pessoal), MEI/empresa, cliente do usuário.
2. **O que coleta**: formulário, cadastro, login social, newsletter, upload, localização, dado sensível (saúde, religião, biometria, origem racial, orientação sexual), dado de criança ou adolescente.
3. **O que roda no navegador**: Analytics, Pixel da Meta, Hotjar/Clarity, Tag Manager, chat, vídeo embutido, fontes externas, mapa, captcha. Cada um é um terceiro recebendo dado.
4. **Se vende**: produto, assinatura, serviço, doação, afiliado.
5. **Público**: geral, profissional, ou provável de ter menor de idade (jogo, app escolar, rede social).

Matriz (vai no relatório, com o "não precisa" explicado):

| Item | Portfólio sem coleta | Site com formulário | App com login | Loja / venda | Público infantil |
|---|---|---|---|---|---|
| Política de privacidade | se tiver analytics | sim | sim | sim | sim, reforçada |
| Aviso e preferência de cookies | só se usar cookie não essencial | idem | idem | idem | idem |
| Termos de uso | - | recomendado | sim | sim | sim |
| Troca, reembolso, arrependimento | - | - | se cobrar | sim | se cobrar |
| Dados legais da empresa | - | se for empresa | se for empresa | sim | se for empresa |
| Canal do titular / encarregado | contato simples | sim | sim | sim | sim |
| Guarda de logs (Marco Civil) | - | se fins econômicos | sim | sim | sim |
| ECA Digital | - | - | - | - | sim |
| Acessibilidade | sim | sim | sim | sim | sim |
| Direito autoral de imagem, fonte, música | sim | sim | sim | sim | sim |

## 2. Verificação

Parte braçal (Lighthouse, script passivo, npm audit, buscas no código, axe no navegador): delegue ao subagente `auditoria-executor` se a ferramenta tiver subagente; se não tiver, ou se ele estiver desligado, você mesmo faz a parte braçal com as mesmas regras dele (só o alvo pedido, nada que grave em produção, segredo com só 4 caracteres à mostra). Ao delegar, passe o alvo, o que verificar, onde salvar e o caminho completo do script e de `references/comandos.md` da skill `auditoria-seguranca` (ele não tem como descobrir sozinho onde as skills foram instaladas). Eu fico com perfil, análise, severidade e relatório.

Siga `references/checklist.md`. Ferramentas:

- **Navegador**: Playwright (o perfil indicado nas regras locais, se houver). Abrir o site com armazenamento limpo, **sem aceitar nada**, e listar cookies e requisições a terceiros feitas antes do consentimento. Depois recusar e conferir de novo. Depois aceitar.
- **Script passivo** (`<pasta-desta-skill>/../auditoria-seguranca/scripts/checagem_passiva.py`): os itens do grupo `legal` (alt, scripts de terceiros, rastreadores, links de privacidade/termos/cookies) são o ponto de partida.
- **Código**: grep por `gtag`, `googletagmanager`, `fbq`, `clarity`, `hotjar`, `analytics`, scripts externos, fontes do Google carregadas de fora, campos de formulário.
- **Acessibilidade**: axe-core injetado na página pelo Playwright, mais conferência manual de navegação por teclado (Tab, Shift+Tab, Enter, Esc) e foco visível.
- **Imagens e fontes**: de onde vieram (pasta do projeto, histórico do git, banco de imagens) e se a licença permite o uso.

## 3. Relatório

Salve em `auditorias/<projeto>-legal-AAAA-MM-DD.md` (na raiz do projeto auditado, ou onde as regras locais mandarem), com o JSON da checagem passiva ao lado.

1. **Veredito em uma linha.**
2. **Perfil e matriz**, com o que não se aplica.
3. **Achados** por risco:

| Risco | Exemplo |
|---|---|
| Alto | Coleta sem política, rastreio antes do consentimento, venda sem dados da empresa ou sem arrependimento, imagem sem licença, depoimento inventado, dado sensível sem base legal |
| Médio | Política genérica que não bate com o que o site faz, cookie banner sem opção de recusar, formulário pedindo dado que não usa |
| Baixo | Link de política difícil de achar, data de atualização ausente |
| Acessibilidade | Separado, com a regra WCAG de cada achado |

Cada achado: **o quê**, **evidência**, **norma** (lei e artigo quando souber com segurança), **correção**.

4. **Perguntas pro advogado**, quando houver.
5. **Rascunhos** (política, termos, aviso de cookies) só se o usuário pedir, adaptados ao que o site faz de verdade, marcados "rascunho, revisar".

## 4. Depois

- Ofereça issues pros riscos Altos. Correção de código (banner, rodapé, alt, contraste) só com ok.
- Revisão a cada 3 a 6 meses, ou quando entrar ferramenta nova de rastreio, formulário novo ou venda. Ofereça agendar (se a ferramenta tiver agendamento) se o usuário quiser recorrente.
