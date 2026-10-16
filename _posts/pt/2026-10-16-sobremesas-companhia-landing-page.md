---
ref: sobremesas-companhia-landing-page
layout: post
title: Uma landing page para a Sobremesas & Companhia
description: Como construímos o sobremesasecompanhia.pt — um site estático em Next.js, em quatro línguas, para uma pequena cozinha de bolos e catering em Santo Tirso, com um formulário de orçamento que vai direto para o WhatsApp. E um primeiro vislumbre do que vem a seguir.
category: tech
emoji: 🎂
read_time: 6 min de leitura
lede: A Sobremesas & Companhia é um casal que faz bolos decorados, bolachas, mesas de doces e catering para casamentos, batizados, comunhões e aniversários, a partir de uma cozinha em Santo Tirso. O trabalho deles vivia no Instagram. Agora vive também em sobremesasecompanhia.pt — eis como o construímos, e uma pista do que estamos a construir a seguir.
author_emoji: 🍪
author_note: Construído com muitas fotografias de bolos no ecrã e nada para comer. O Billy levou isto a peito. Prometeram uma migalha à Cookie, que ainda está à espera.
---

<figure>
  <img src="/assets/blog/sobremesas-companhia/banner.png" alt="A landing page da Sobremesas & Companhia, pela CookieBytes">
</figure>

## O pedido

A Sobremesas & Companhia é pequena por opção. São duas pessoas a tratar de cada encomenda, da primeira mensagem até montar a mesa no próprio dia, para festas de 15 a 200 convidados, com entregas em todo o distrito do Porto e no Minho. Até agora, tudo isso passava pelo Instagram e por mensagens privadas — o que funciona, até ao momento em que alguém quer mandar um link à irmã e perguntar "o que achas destes?"

O objetivo era simples: uma página que mostre o trabalho, explique como se encomenda e torne pedir um orçamento tão fácil como mandar uma mensagem. Sem contas, sem checkout, sem um CMS para andar a vigiar.

<div class="divider">🍪 🍪 🍪</div>

## O que está na página

O [sobremesasecompanhia.pt](https://sobremesasecompanhia.pt/) é um único scroll em cinco partes:

- **Abertura** — o que fazem e para que ocasiões, com o botão de orçamento em destaque.
- **Criações** — uma galeria de 83 fotografias, filtrável por ocasião (casamentos, batizados, comunhões, aniversários, sobremesas, bolachas, catering, decoração). Mostra doze de cada vez, para que o primeiro ecrã seja uma amostra de tudo e não uma parede de bolos.
- **Como encomendar** — três passos: conte-nos o seu evento, receba uma proposta à medida, nós tratamos do dia.
- **Onde estamos** — a cozinha em Santo Tirso e as localidades onde entregam.
- **Pedir orçamento** — o formulário, e um link direto para o WhatsApp para quem prefere simplesmente falar.

<figure>
  <img src="/assets/blog/sobremesas-companhia/creations-pt.jpg" alt="A secção Criações, com os emblemas de ocasião por cima de uma grelha de fotografias">
  <figcaption>Escolha uma ocasião e veja trabalhos anteriores. Toque outra vez para ver tudo.</figcaption>
</figure>

### O design vem dos bolos

Não inventámos uma marca — eles já tinham uma, no logótipo e nas capas dos destaques do Instagram. A paleta vem diretamente dali: branco-açúcar, azul-pó, castanho-chocolate e um rosa-merengue para a secção de orçamento. Os títulos usam Young Serif e o texto Figtree.

O único pormenor de assinatura é a **borda recortada** que se vê nos limites das secções e nos emblemas das ocasiões. É a borda de uma renda de papel para bolos, que já usavam nas capas dos destaques, e é feita só com CSS, com uma máscara de gradiente radial — sem imagens, por isso fica nítida em qualquer tamanho e não pesa nada a carregar.

### As fotografias

A galeria junta oito imagens do feed do Instagram com 75 dos originais deles. Cada original foi exportado com 1400px no máximo, renomeado por ocasião (`casamento-bolo-orquideas.jpg` em vez de `IMG_4821.jpg`) e **limpo de dados EXIF e GPS** — muitas destas fotografias foram tiradas em casa de clientes e em quintas, e isso não é algo que um site público deva andar a publicar sem ninguém dar por isso. Cada fotografia tem uma legenda escrita, que serve também de texto alternativo.

<div class="divider">🍪 🍪 🍪</div>

## Com que foi construído

| Peça | Escolha |
| --- | --- |
| Framework | Next.js 16 (App Router), React 19, TypeScript |
| Estilos | Tailwind CSS 4, com os tokens de design num único ficheiro CSS |
| Tipos de letra | Young Serif + Figtree, alojados no próprio site via Fontsource |
| Resultado | Exportação estática — HTML, CSS e imagens simples |
| Alojamento | GitHub Pages, publicado pelo GitHub Actions a cada push para `main` |

A linha importante é a quarta. Não há servidor. O `next build` produz uma pasta de ficheiros estáticos, e isso é o site inteiro. Carrega depressa, não pode ir abaixo porque uma base de dados foi abaixo, e não custa nada a alojar. O Next.js ganha o seu lugar pela experiência de desenvolvimento — componentes, conteúdo tipado, um build a sério — e não por algo que tenha de correr em produção.

### Um formulário de orçamento sem backend

A forma óbvia de fazer um formulário de contacto é enviá-lo para algum lado: um serviço de email, uma API de formulários, uma base de dados. Saltámos tudo isso, porque os clientes deles já lhes falam pelo WhatsApp.

O formulário pede o essencial — ocasião, data, número de convidados, o que procuram (bolo, bolachas, mesa de doces, catering), uma nota livre, nome e contacto. Ao submeter, junta tudo numa mensagem arrumada e abre o WhatsApp com ela **já escrita**, endereçada a eles. O cliente revê e carrega em enviar. O casal recebe um pedido completo na app onde já vive, em vez de um "olá, quanto custa um bolo?" que leva quatro mensagens a desembaraçar.

Sem fornecedor de formulários, sem filtro de spam para afinar, sem uma caixa de correio que ninguém vê. Se o WhatsApp não estiver configurado, recorre a um email já preenchido.

<figure style="max-width: 360px;">
  <img src="/assets/blog/sobremesas-companhia/quote-form-pt.png" alt="O formulário de orçamento: ocasião, data, convidados, opções de produto, nome e contacto">
  <figcaption>Preencha o essencial; o WhatsApp abre com a mensagem pronta a enviar.</figcaption>
</figure>

### Quatro línguas, verificadas no build

Nem todos os clientes, nem todos os convidados que ajudam a escolher o bolo, leem português, por isso o site está em **português, inglês, espanhol e francês**. O português é o idioma por omissão em `/`; os outros são cópias estáticas completas em `/en/`, `/es/` e `/fr/`, com um seletor de língua no cabeçalho e links `hreflang` para que os motores de busca mostrem a versão certa.

Todo o texto da página vive num dicionário por língua, e os de inglês, espanhol e francês são tipados contra o português. Se faltar traduzir uma frase, **o build falha** — por isso uma página meio traduzida não pode ir para o ar por engano. As legendas da galeria também têm as quatro línguas, e a mensagem de WhatsApp que o formulário escreve sai na língua em que o visitante está a ler.

<div class="divider">🍪 🍪 🍪</div>

## O que vem a seguir: a plataforma

A landing page é a porta de entrada. Por trás dela, começámos a trabalhar em algo maior para a Sobremesas & Companhia: **uma plataforma para gerir o próprio negócio**, para lá do que uma página estática consegue fazer.

Não a vamos descrever aqui — merece um artigo só dela, e vai tê-lo quando houver algo real para mostrar. Para já: a landing page foi construída para poder crescer até lá sem ser deitada fora, e vai saber dela primeiro aqui no blog.

<div class="divider">🍪 🍪 🍪</div>

## Espreite

O site está no ar em [sobremesasecompanhia.pt](https://sobremesasecompanhia.pt/), e o código é público em [`cookiebytespt/sobremesas-companhia-landing`](https://github.com/cookiebytespt/sobremesas-companhia-landing). Se está a planear um casamento no norte, já sabe onde está o bolo.

Tem um pequeno negócio que vive nas mensagens privadas? [Fazemos sites como este](/pt/#contact) — rápidos, multilingues e ligados à forma como já trabalha.
