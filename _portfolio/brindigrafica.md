---
ref: brindigrafica               # shared with the Portuguese version
title: Brindigráfica
client: Promotional products · Portugal
kind: Website, store & API
year: 2026
status: live            # live | wip
order: 2
description: A full rebuild of Brindigráfica's online presence — new website, product catalogue and store, backed by an API written from scratch and a back-office the team runs day to day. Live at brindigrafica.pt.
site_url: https://brindigrafica.pt/
mock: brindigrafica
# screenshot: /assets/portfolio/brindigrafica/home.png
highlights:
  - Next.js frontend with catalogue, cart, orders and quote requests; customer area for orders and quotes.
  - Server-side Swift API (Vapor 4 + Fluent + PostgreSQL) with Auth0 login, kept behind a Next.js proxy so tokens never touch the browser.
  - Back-office for orders, products and users, reusing the same API.
  - Supplier catalogue imports (Stricker, Cifra, Valento), reviewed in the back-office before anything is published.
stack:
  - Next.js
  - Swift · Vapor 4
  - Fluent · PostgreSQL
  - Auth0
  - Vercel
  - Back-office
---

Brindigráfica prints logos on things — t-shirts, caps, pens, bags, and everything else a company hands out at a fair. Their existing site showed the products but couldn't take an order, so every quote was a phone call. The rebuild turns the website into the front door of the business.

## What we built

A **Next.js storefront** with the full catalogue, filters by category, a cart, and two ways to buy: a straight order, or a quote request for the custom-printed jobs that need a conversation first. Customers get an account area to follow their orders and quotes.

Behind it, an **API in server-side Swift** — Vapor 4 with Fluent on PostgreSQL. Authentication stays on Auth0, and the frontend talks to the API through a Next.js proxy so tokens never reach the browser.

The team gets a **back-office** on top of the same API: orders, products and users in one place, including inviting new users, plus supplier catalogue imports that land unpublished and get reviewed before they go live.

## Where it stands

The first stage is live at [brindigrafica.pt](https://brindigrafica.pt/): catalogue, cart, orders, quote requests, customer accounts and the back-office. Online payments were deliberately left out of v1 — promotional products are quoted and invoiced, so that flow came first. Next on the list: payments, Google Calendar bookings with a production timeline, and more supplier connectors.

The full story — stack, decisions and the parts that bit us — is in the [blog post](/blog/brindigrafica-website/).
