---
ref: brindigrafica      # shared with the English version (_portfolio/brindigrafica.md)
title: Brindigráfica
client: Produtos promocionais · Portugal
kind: Site, loja e API
year: 2026
status: live            # live | wip
order: 2
description: Uma reconstrução completa da presença online da Brindigráfica — novo site, catálogo de produtos e loja, com uma API escrita de raiz e um back-office que a equipa usa no dia a dia. No ar em brindigrafica.pt.
site_url: https://brindigrafica.pt/
mock: brindigrafica
# screenshot: /assets/portfolio/brindigrafica/home.png
highlights:
  - Frontend em Next.js com catálogo, carrinho, encomendas e pedidos de orçamento; área de cliente para acompanhar encomendas e orçamentos.
  - API em Swift do lado do servidor (Vapor 4 + Fluent + PostgreSQL) com login Auth0, protegida por um proxy Next.js para que os tokens nunca cheguem ao browser.
  - Back-office para encomendas, produtos e utilizadores, a reutilizar a mesma API.
  - Importação dos catálogos de fornecedores (Stricker, Cifra, Valento), revista no back-office antes de qualquer produto ser publicado.
stack:
  - Next.js
  - Swift · Vapor 4
  - Fluent · PostgreSQL
  - Auth0
  - Vercel
  - Back-office
---

A Brindigráfica imprime logótipos em coisas — t-shirts, bonés, canetas, sacos e tudo o mais que uma empresa oferece numa feira. O site antigo mostrava os produtos, mas não aceitava encomendas, por isso cada orçamento era um telefonema. A reconstrução transforma o site na porta de entrada do negócio.

## O que construímos

Uma **loja em Next.js** com o catálogo completo, filtros por categoria, carrinho e duas formas de comprar: uma encomenda direta ou um pedido de orçamento para os trabalhos personalizados que precisam primeiro de uma conversa. Os clientes têm uma área pessoal para acompanhar encomendas e orçamentos.

Por trás, uma **API em Swift do lado do servidor** — Vapor 4 com Fluent sobre PostgreSQL. A autenticação fica no Auth0 e o frontend fala com a API através de um proxy Next.js, para que os tokens nunca cheguem ao browser.

A equipa tem um **back-office** assente na mesma API: encomendas, produtos e utilizadores num só sítio, incluindo convidar novos utilizadores, além da importação dos catálogos de fornecedores, que entram por publicar e são revistos antes de irem para o site.

## Em que ponto está

A primeira fase está no ar em [brindigrafica.pt](https://brindigrafica.pt/): catálogo, carrinho, encomendas, pedidos de orçamento, contas de cliente e o back-office. Os pagamentos online ficaram deliberadamente de fora da v1 — os brindes são orçamentados e faturados, por isso esse fluxo veio primeiro. A seguir: pagamentos, marcações no Google Calendar com cronologia de produção e mais ligações a fornecedores.

A história completa — stack, decisões e as partes que nos deram luta — está no [artigo do blog](/pt/blog/site-brindigrafica/).
