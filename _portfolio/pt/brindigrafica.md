---
ref: brindigrafica      # shared with the English version (_portfolio/brindigrafica.md)
title: Brindigráfica
client: Produtos promocionais · Portugal
kind: Site, loja e API
year: 2026
status: wip             # live | wip
order: 2
description: Uma reconstrução completa da presença online da Brindigráfica — novo site, catálogo de produtos e loja, com uma API escrita de raiz e um back-office para a equipa.
site_url:               # no public URL yet — leave empty while in progress
progress: 55            # % shown on the "baking progress" bar (wip only)
progress_note: API e loja · em desenvolvimento
mock: brindigrafica
# screenshot: /assets/portfolio/brindigrafica/home.png
highlights:
  - Frontend em Next.js com catálogo, carrinho, encomendas e pedidos de orçamento; área de cliente para acompanhar encomendas e orçamentos.
  - API em Swift do lado do servidor (Vapor 4 + Fluent + PostgreSQL) com login Auth0, protegida por um proxy Next.js para que os tokens nunca cheguem ao browser.
  - Back-office para encomendas, produtos e utilizadores, a reutilizar a mesma API.
  - Marcações de clientes sincronizadas com o Google Calendar e uma cronologia de encomenda e produção.
stack:
  - Next.js
  - Swift · Vapor 4
  - Fluent · PostgreSQL
  - Auth0
  - Google Calendar
  - Back-office
---

A Brindigráfica imprime logótipos em coisas — t-shirts, bonés, canetas, sacos e tudo o mais que uma empresa oferece numa feira. O site antigo mostrava os produtos, mas não aceitava encomendas, por isso cada orçamento era um telefonema. A reconstrução transforma o site na porta de entrada do negócio.

## O que estamos a construir

Uma **loja em Next.js** com o catálogo completo, filtros por categoria, carrinho e duas formas de comprar: uma encomenda direta ou um pedido de orçamento para os trabalhos personalizados que precisam primeiro de uma conversa. Os clientes têm uma área pessoal para acompanhar encomendas e orçamentos.

Por trás, uma **API em Swift do lado do servidor** — Vapor 4 com Fluent sobre PostgreSQL. A autenticação fica no Auth0 e o frontend fala com a API através de um proxy Next.js, para que os tokens nunca cheguem ao browser.

A equipa tem um **back-office** assente na mesma API: encomendas, produtos e utilizadores num só sítio, além de marcações que sincronizam com o Google Calendar e uma cronologia de produção para cada trabalho.

## Em que ponto está

A API e a loja estão em desenvolvimento ativo; os pagamentos online ficam deliberadamente de fora da v1 — catálogo, carrinho, encomendas e orçamentos saem primeiro. Esta página vai ter um link no dia em que o site for lançado.
