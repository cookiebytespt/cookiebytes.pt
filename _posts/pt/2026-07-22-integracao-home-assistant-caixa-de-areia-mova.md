---
ref: mova-litter-box-home-assistant-integration
layout: post
title: Construímos uma integração de Home Assistant para a caixa de areia dos nossos gatos
description: A MOVA MeowgicPod só fala com a sua própria app na cloud, sem API documentada. Eis como fizemos engenharia inversa ao protocolo, adivinhámos os IDs das ações e lançámos quatro versões em dois dias — e o que ainda está no roadmap.
category: tech
emoji: 🐾
read_time: 10 min de leitura
lede: A caixa de areia autolimpante MOVA MeowgicPod do nosso escritório não tem integração oficial com o Home Assistant, nem API local, nem documentação — só um protocolo na cloud que a app MOVAhome fala. Por isso, o Billy fez-lhe engenharia inversa. Este é o devlog — o protocolo, o trabalho de detetive por trás dos botões de ação e tudo o que foi lançado da v0.1.0 à v0.4.0.
author_emoji: 🐾
author_note: Testado no terreno pelo Rocky e pela Peggy, que usam o dispositivo várias vezes por dia e nos avisariam de certeza se algo avariasse. A Cookie supervisionou a uma distância segura, como lhe compete.
---

<figure>
  <img src="/assets/blog/mova-litter-box/banner.png" alt="Integração MOVA Litter Box para Home Assistant, pela CookieBytes">
</figure>

## O problema

Todos os projetos de domótica começam da mesma forma: há algo em casa que se recusa a falar com o Home Assistant, e alguém na CookieBytes decide que isso é inaceitável.

Desta vez foi a **MOVA MeowgicPod LR10 Prime** — a caixa de areia autolimpante que o Rocky e a Peggy reclamaram como o seu trono pessoal no escritório. É um equipamento genuinamente engenhoso. Só não tem integração oficial com o Home Assistant e só comunica com o mundo através da cloud da MOVA e da app MOVAhome. Sem API local, sem MQTT, sem documentação pública, nada para onde apontar o Home Assistant.

Por isso o Billy — Diretor de Disrupção, com três pivots e dois rebrands no currículo — foi lá e construiu uma na mesma: [`homeassistant-mova-litter-box`](https://github.com/cookiebytespt/homeassistant-mova-litter-box), uma integração personalizada para a MeowgicPod (`mova.litterbox.q2504w`). Este artigo é o devlog: como funciona realmente o protocolo na cloud, como adivinhámos os IDs das ações não documentadas e o que foi lançado ao longo de quatro versões em apenas dois dias. Vai haver mais artigos à medida que o roadmap abaixo for sendo cumprido — este ainda está longe de estar terminado.

<div class="divider">🍪 🍪 🍪</div>

## Como é que fala realmente com a caixa

A MOVA é uma submarca da Dreame, e a MeowgicPod usa a cloud IoT da Dreame em vez de algo local — a app e a caixa nunca falam diretamente na rede local. Todos os pedidos vão para `https://eu.iot.mova-tech.com:13267`, exatamente o mesmo host e a mesma API que a app MOVAhome para iPhone usa; o nosso cliente limita-se a fazer-se passar por essa app (a mesma string de user-agent, o mesmo token de basic auth) em vez de falar diretamente com a caixa.

**O login** é um password grant ao estilo OAuth: a password é convertida num hash MD5 com um salt fixo, enviada por POST para `/dreame-auth/oauth/token`, e a cloud devolve um access token e um refresh token, para não termos de voltar a autenticar em cada sondagem.

**Ler e escrever estado** segue a convenção [MIoT](https://iot.mi.com/new/doc/design/spec/overall) que os dispositivos do ecossistema Xiaomi usam — cada capacidade é endereçada como `siid.piid` (ID de serviço, ID de propriedade) em vez de campos com nome. O estado é `2.1`, a build do firmware é `1.4`, o modo de limpeza vive no serviço 3, e por aí fora. Três formatos de pedido cobrem tudo, todos enviados através de um único endpoint, `/device/sendCommand`:

| Intenção | method | formato |
| --- | --- | --- |
| Ler um valor | `get_properties` | `[{did, siid, piid}, ...]` |
| Escrever um valor | `set_properties` | `[{did, siid, piid, value}, ...]` |
| Iniciar um ciclo | `action` | `{did, siid, aiid, in: [...]}` |

É esta a superfície toda: a sondagem é uma varredura `get_properties` a cada 60 segundos, mudar um interruptor no Home Assistant é uma chamada `set_properties`, e carregar em «Iniciar limpeza» é uma chamada `action` com um ID de serviço e um ID de ação (`aiid`). Simples, quando se conhece o formato — e só o conhecíamos graças a [`EvotecIT/homeassistant-dreamelawnmower`](https://github.com/EvotecIT/homeassistant-dreamelawnmower), uma integração de Home Assistant para um corta-relvas Dreame na *mesma* cloud. Foi o código cliente dessa integração que nos disse que o protocolo existia e, mais ou menos, como falá-lo. O que não nos podia dizer era que números significavam o quê numa caixa de areia — essa parte era connosco.

Todas as propriedades que a cloud reporta têm lugar no Home Assistant, mapeadas ou não — as que ainda não descodificámos continuam a aparecer como sensores de diagnóstico em bruto, em vez de desaparecerem em silêncio:

<figure>
  <img src="/assets/blog/mova-litter-box/diagnostic.png" alt="O cartão Diagnóstico no Home Assistant — janelas de não incomodar, horário de limpeza, build do firmware, número de série e sensores de propriedades em bruto desativados">
  <figcaption>Horário e janelas de não incomodar descodificados, mais seis sensores de propriedades em bruto, desativados por defeito, para o que ainda não identificámos.</figcaption>
</figure>

<div class="divider">🍪 🍪 🍪</div>

## A parte sem documentação: encontrar os IDs das ações

Ler o estado foi a metade fácil. A caixa responde de bom grado a `get_properties` para tudo nos serviços 1–3, por isso o enum de estado, os sensores e as definições saíram bastante depressa de uma varredura de propriedades. Mas *iniciar* um ciclo — Iniciar limpeza, Esvaziar resíduos, Nivelar areia — precisa de uma chamada `action`, e as ações são invisíveis a uma varredura de propriedades. Não havia lista em lado nenhum. A MOVA não publica uma, e a API na cloud não está acessível a partir do CI para simplesmente ir experimentando de forma automática.

Por isso tratámos o assunto como um problema de investigação antes de tocar em hardware real — está no repositório, na íntegra, em [`ACTIONS_RESEARCH.md`](https://github.com/cookiebytespt/homeassistant-mova-litter-box/blob/main/ACTIONS_RESEARCH.md). A versão curta:

O cliente de referência do corta-relvas tinha um mapa de ações explícito — `START_MOWING = siid 5, aiid 1`, `STOP = siid 5, aiid 2`, `DOCK = siid 5, aiid 3`, e por aí fora, todos agrupados em números baixos num serviço dedicado. Isso deu-nos uma hipótese estrutural em vez de um palpite com sorte: os ciclos são iniciados chamando uma ação, nunca escrevendo na propriedade de estado (que é só de leitura), e os IDs de ações para comportamentos relacionados tendem a agrupar-se no serviço a que pertencem. Seguiram-se duas teorias concorrentes — ações no mesmo serviço do estado (`2`), ou separadas num serviço próprio, como no corta-relvas (`4` ou `5`) — e ordenámos os pares `siid`/`aiid` candidatos para cada ciclo por grau de confiança antes de testar o que quer que fosse.

<div class="callout">🔬 Cada candidato foi testado da mesma forma cuidadosa: dispositivo em standby, nenhum gato perto da unidade, uma ação de cada vez, app MOVAhome aberta para o caso de ser preciso abortar a meio do ciclo. <code>tools/mova_probe.py --action siid aiid</code> dispara a chamada e mostra a transição de estado resultante — se o <code>2.1</code> se mover como a hipótese previa, o palpite estava certo.</div>

A realidade ficou a meio caminho: não era a hipótese favorita. As ações de limpar/esvaziar/nivelar/pausar/retomar/parar vivem afinal no **serviço 3** — o serviço das definições, nem o do estado, nem um serviço dedicado a ações. Bom lembrete de que um palpite bem fundamentado continua a ser um palpite; ou se confirma em hardware real, ou não se lança. As seis ações estão agora confirmadas e verificadas pela transição de estado que efetivamente produzem, e é por isso que os botões funcionam de forma fiável em todos os tipos de ciclo, e não «provavelmente».

<div class="divider">🍪 🍪 🍪</div>

## Da v0.1.0 à v0.4.0, em dois dias

Assim que o transporte e o mapa de propriedades existiam, o resto andou depressa. O detalhe completo está no [CHANGELOG](https://github.com/cookiebytespt/homeassistant-mova-litter-box/blob/main/CHANGELOG.md); em traços gerais:

**v0.1.0 — primeira versão.** Cliente da cloud com login e renovação de token, config flow (inicie sessão com a sua conta MOVAhome e escolha o dispositivo), o enum de estado completo, sensores binários, switches, selects e sensores de diagnóstico para todas as propriedades que a cloud reporta — 27 entidades a partir de um só dispositivo. Ainda sem botões de ação; esses precisavam da investigação acima.

**v0.2.0 — controlo total do dispositivo.** Iniciar limpeza, Esvaziar resíduos, Nivelar areia, Pausar, Retomar e Parar, todos confirmados em hardware real no serviço 3. E ainda três serviços de controlo genéricos (`send_action`, `set_property`, `refresh`) para mexer em qualquer coisa diretamente a partir do Home Assistant — muito úteis precisamente para este tipo de engenharia inversa.

<figure style="max-width: 320px;">
  <img src="/assets/blog/mova-litter-box/controls.png" alt="O cartão Controlos no Home Assistant — modo de limpeza e os botões Esvaziar resíduos, Nivelar areia, Pausar, Retomar, Iniciar limpeza e Parar">
  <figcaption>As seis ações confirmadas, ativas como botões.</figcaption>
</figure>

**v0.3.0 — conheça os gatos.** O dispositivo regista internamente as idas à caixa; esta versão descodifica esse registo de eventos em Peso do último gato, Hora da última visita, duração da visita e contagem de visitas nas últimas 24 horas. Pode dar nome aos seus gatos e definir o peso habitual de cada um nas opções da integração, e as visitas são atribuídas ao peso mais próximo — assim, as visitas do Rocky e as da Peggy aparecem separadas, em vez de num único sensor indiferenciado de «aconteceu um gato».

<div style="display: flex; gap: 16px; flex-wrap: wrap;">
  <figure style="flex: 1; min-width: 240px; max-width: 320px;">
    <img src="/assets/blog/mova-litter-box/sensors.png" alt="O cartão Sensores no Home Assistant — Último gato: Rocky, Peso do último gato: 5,99 kg, última visita e contagem de visitas em 24 h por gato, para o Rocky e a Peggy">
    <figcaption>O Rocky e a Peggy, acompanhados em separado pelo peso correspondente.</figcaption>
  </figure>
  <figure style="flex: 1; min-width: 240px; max-width: 320px;">
    <img src="/assets/blog/mova-litter-box/activity.png" alt="O registo de Atividade no Home Assistant com o histórico datado das visitas do Rocky e da Peggy à caixa de areia">
    <figcaption>As mesmas visitas, como histórico com data e hora.</figcaption>
  </figure>
</div>

**v0.3.1 — ícone da marca.**

<figure style="max-width: 160px; margin-left: 0;">
  <img src="/assets/blog/mova-litter-box/brand-icon.png" alt="O ícone da marca MOVA Litter Box — uma cara de gato em forma de bolacha, submetido ao home-assistant/brands">
</figure>

Um ícone de app a sério, recortado segundo as especificações e submetido ao [`home-assistant/brands`](https://github.com/home-assistant/brands) — o repositório partilhado que fornece os pequenos logótipos que aparecem ao lado de cada integração na interface do HA e do HACS. Só vai aparecer lá quando esse PR for aceite, já que o ícone é servido pela CDN central de marcas e não vem incluído na própria integração.

**v0.4.0 — controlo do nivelamento.** A MeowgicPod consegue ajustar a forma como nivela a areia depois de um ciclo — ao centro, ou desviada para a esquerda ou para a direita em três intensidades diferentes, sete posições no total. As sete foram confirmadas no dispositivo real (propriedade `3.21`), expostas como uma entidade select, e mudá-la dispara o mesmo ciclo de renivelamento que a app.

<figure style="max-width: 320px;">
  <img src="/assets/blog/mova-litter-box/configuration.png" alt="O cartão Configuração no Home Assistant, com o novo select de nivelamento em Centrado, ao lado do bloqueio para crianças, luz dos botões, som dos botões, atraso da limpeza e definições do spray">
  <figcaption>O nivelamento, ativo no cartão Configuração — em Centrado, ao lado das restantes definições.</figcaption>
</figure>

Isso também resolveu, sem alarde, uma das questões em aberto desde o primeiro dia: a `3.21` era uma das cinco propriedades por mapear no roadmap original. Agora está identificada — faltam quatro.

<div class="divider">🍪 🍪 🍪</div>

## O que vem a seguir

Quatro versões depois, [o roadmap](https://github.com/cookiebytespt/homeassistant-mova-litter-box#-roadmap) ainda tem itens a sério:

- **Consumíveis** — vida restante e ações de reposição para o filtro de purificação do ar, o líquido desodorizante e o saco de resíduos. Planeado.
- **Atualizações de firmware** — expor a verificação de atualizações de firmware como uma entidade `update` do Home Assistant, em vez de um sensor de versão estático. Planeado.
- **Unidade de peso** — alternar entre kg e lb para corresponder à definição da app. Planeado.
- **As últimas propriedades por mapear** — `2.2`, `2.6`, `3.2` e `3.12` continuam por identificar e só aparecem como sensores de diagnóstico em bruto. Em investigação.
- **Nível de areia e estado do contentor de resíduos** — sensores realmente úteis que ainda não temos, bloqueados até descobrirmos onde o dispositivo os reporta.
- **Atualizações push por MQTT** — substituir a sondagem de 60 segundos por algo que reaja de imediato. Por enquanto, ainda só uma ideia.

Se também tem uma MOVA MeowgicPod e quer ajudar a fechar algum destes pontos, o repositório inclui uma ferramenta de sondagem só de leitura (`tools/mova_probe.py`, com um modo `--watch` que regista as alterações em tempo real enquanto usa a app) — executá-la e abrir uma issue com o resultado é-nos genuinamente útil.

<div class="divider">🍪 🍪 🍪</div>

## Experimente

A integração instala-se através do [HACS](https://hacs.xyz/) como repositório personalizado — as instruções completas, incluindo botões de um clique para o HACS e para o config flow, estão no [README](https://github.com/cookiebytespt/homeassistant-mova-litter-box). Inicie sessão com a sua conta MOVAhome, escolha o dispositivo e ele começa a reportar.

<figure style="max-width: 300px;">
  <img src="/assets/blog/mova-litter-box/device-info.png" alt="O cartão de informações do dispositivo no Home Assistant, com mova.litterbox.q2504w da MOVA, firmware 1.7.18_1113 e a entrada MOVA Litter Box (MeowgicPod) com o respetivo ícone">
  <figcaption>O aspeto final depois de configurada — com ícone e tudo.</figcaption>
</figure>

Ainda é cedo — quatro versões em dois dias é um sprint, não uma meta — e é essa a ideia de ir lançando pelo caminho em vez de esperar que esteja «pronto». Sem afiliação à MOVA ou à Dreame, use por sua conta e risco, e há mais desta série a caminho à medida que o roadmap acima for avançando.

Tem um dispositivo que se recusa a entender-se com o Home Assistant? [Fazemos instalações e integrações](/pt/#contact) — caixas de areia incluídas, pelos vistos.
