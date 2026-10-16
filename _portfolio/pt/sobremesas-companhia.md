---
ref: sobremesas-companhia   # shared with the English version (_portfolio/sobremesas-companhia.md)
title: Sobremesas & Companhia
client: Bolos, mesas de doces e catering · Santo Tirso
kind: Landing page e plataforma de gestão
year: 2026
status: wip             # live | wip
order: 3
description: Uma landing page em quatro línguas para uma pequena cozinha de bolos e catering, com um formulário de orçamento que vai direto para o WhatsApp — e, por trás, uma plataforma para gerir o negócio, já no forno.
site_url: https://sobremesasecompanhia.pt/
progress_note: Landing page no ar · plataforma em desenvolvimento
mock: sobremesas
highlights:
  - Galeria com 83 criações, filtrável por ocasião — casamentos, batizados, comunhões, aniversários e mais.
  - Formulário de orçamento sem backend — escreve o pedido e abre o WhatsApp com a mensagem pronta a enviar.
  - Português, inglês, espanhol e francês, com um build que falha se ficar algum texto por traduzir.
  - Identidade tirada do próprio logótipo e das capas do Instagram, até à borda rendada de naperon desenhada só em CSS.
stack:
  - Next.js 16
  - Tailwind CSS 4
  - Exportação estática
  - GitHub Pages
  - PT / EN / ES / FR
  - WhatsApp
---

A Sobremesas & Companhia é um casal que faz bolos decorados, bolachas, mesas de doces e catering para casamentos, batizados, comunhões e aniversários, a partir de uma cozinha em Santo Tirso. O trabalho deles vivia no Instagram e nas mensagens privadas — o que funciona, até ao momento em que alguém quer mandar um link à irmã e perguntar "o que achas destes?"

## Primeira fase: a landing page

O [sobremesasecompanhia.pt](https://sobremesasecompanhia.pt/) é um único scroll estático: o que fazem, uma galeria de trabalhos filtrável por ocasião, como encomendar, onde entregam e um formulário de orçamento. Sem servidor, sem contas, sem CMS para tomar conta — o `next build` gera HTML, CSS e imagens, publicados no GitHub Pages a cada push.

O formulário pede o essencial — ocasião, data, convidados, o que procuram — e abre o WhatsApp com o pedido já escrito, para que o casal receba um pedido completo na app onde já vive. O site está em quatro línguas, tipadas contra o dicionário português, para que uma página meio traduzida nunca chegue a sair.

A história completa está no [artigo do blog](/pt/blog/site-sobremesas-companhia/).

## Segunda fase: a plataforma

A landing page é a porta de entrada. Por trás dela, estamos a construir **uma plataforma para gerir o próprio negócio**, para lá do que uma página estática consegue fazer. Vai ter o seu próprio caso de estudo quando houver algo real para mostrar.
