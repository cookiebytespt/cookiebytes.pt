---
ref: brindigrafica      # shared with the English version (_portfolio/brindigrafica.md)
title: Brindigráfica
client: Produtos promocionais · Portugal
kind: Site, loja e API
year: 2026
status: live            # live | wip
order: 2
description: Uma reconstrução completa da presença online da Brindigráfica — novo site, catálogo de produtos e loja, com uma API escrita de raiz e um back-office que a equipa usa no dia a dia, agora ligados à conta Google da loja e a cinco catálogos de fornecedores. No ar em brindigrafica.pt.
site_url: https://brindigrafica.pt/
mock: brindigrafica
# screenshot: /assets/portfolio/brindigrafica/home.png
highlights:
  - Loja em Next.js com categorias dentro de categorias, amostras de cor e grelha de vários tamanhos, impressão com preço por trabalho, carrinho, encomendas e pedidos de orçamento.
  - API em Swift do lado do servidor (Vapor 4 + Fluent + PostgreSQL) com login Auth0, protegida por um proxy Next.js para que os tokens nunca cheguem ao browser.
  - Back-office para encomendas, orçamentos, produtos e utilizadores — incluindo encomendas feitas por telefone e clientes que nunca precisam de acesso.
  - Sincronização nos dois sentidos com a conta Google da loja — contactos e calendário de produção — e emails enviados pelo Gmail em quatro línguas.
  - Importação dos catálogos de fornecedores (Stricker, Cifra, Valento, Makito, midocean), revista antes de publicar, e pedidos de encomenda aos fornecedores — enviados diretamente pela API no caso da midocean.
stack:
  - Next.js
  - Swift · Vapor 4
  - Fluent · PostgreSQL
  - Auth0
  - Google APIs
  - Vercel · Fly.io
  - Back-office
---

A Brindigráfica imprime logótipos em coisas — t-shirts, bonés, canetas, sacos e tudo o mais que uma empresa oferece numa feira. O site antigo mostrava os produtos, mas não aceitava encomendas, por isso cada orçamento era um telefonema. A reconstrução transforma o site na porta de entrada do negócio — e o back-office no sítio de onde a equipa o gere.

## O que construímos

Uma **loja em Next.js** com o catálogo completo em categorias até três níveis, carrinho e duas formas de comprar: uma encomenda direta ou um pedido de orçamento para os trabalhos personalizados que precisam primeiro de uma conversa. As páginas de produto mostram as cores como amostras, com fotografia por cor, e uma **grelha de vários tamanhos** deixa encomendar 10 S, 25 M e 30 L de uma vez — com o preço de um só trabalho e a impressão (serigrafia, bordado, laser, DTF) cobrada uma vez pelo conjunto. Os clientes têm uma área pessoal para acompanhar encomendas e orçamentos.

Por trás, uma **API em Swift do lado do servidor** — Vapor 4 com Fluent sobre PostgreSQL. A autenticação fica no Auth0 e o frontend fala com a API através de um proxy Next.js, para que os tokens nunca cheguem ao browser.

A equipa tem um **back-office** assente na mesma API: encomendas, orçamentos, produtos, tipos de impressão e utilizadores num só sítio. As encomendas feitas por telefone entram diretamente, para um cliente existente, para um novo que recebe um convite, ou para um cliente que nunca vai precisar de acesso.

A plataforma está ligada à **conta Google** da loja: os contactos sincronizam nos dois sentidos com o telemóvel, as marcações e os marcos de produção vivem no Google Calendar (mudou lá, a encomenda acompanha) e os estados e confirmações saem pelo Gmail em português, inglês, francês ou espanhol.

Os **catálogos dos fornecedores** Stricker, Cifra, Valento, Makito e midocean entram numa fila de revisão e são publicados por uma pessoa, nunca automaticamente. A partir da encomenda de um cliente, o back-office prepara os pedidos de encomenda a cada fornecedor — e, no caso da midocean, envia-os diretamente pela API.

## Em que ponto está

No ar em [brindigrafica.pt](https://brindigrafica.pt/) desde outubro e em uso todos os dias. Os pagamentos online continuam de fora de propósito — os brindes são orçamentados e faturados, por isso esse fluxo veio primeiro. A seguir: enviar email a partir do domínio da loja e a primeira lista de preços da midocean.

A história até agora está no blog: [a stack e o lançamento](/pt/blog/site-brindigrafica/) e [a parte dois — encomendas ao balcão, Google e novos fornecedores](/pt/blog/brindigrafica-novidades/).
