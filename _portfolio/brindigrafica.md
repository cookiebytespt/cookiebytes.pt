---
ref: brindigrafica               # shared with the Portuguese version
title: Brindigráfica
client: Promotional products · Portugal
kind: Website, store & API
year: 2026
status: live            # live | wip
order: 2
description: A full rebuild of Brindigráfica's online presence — new website, product catalogue and store, backed by an API written from scratch and a back-office the team runs day to day, now wired into the shop's Google account and five supplier catalogues. Live at brindigrafica.pt.
site_url: https://brindigrafica.pt/
mock: brindigrafica
# screenshot: /assets/portfolio/brindigrafica/home.png
highlights:
  - Next.js storefront with nested categories, colour swatches and a multi-size order grid, printing options priced per job, cart, orders and quote requests.
  - Server-side Swift API (Vapor 4 + Fluent + PostgreSQL) with Auth0 login, kept behind a Next.js proxy so tokens never touch the browser.
  - Back-office for orders, quotes, products and users — including orders taken over the phone and clients who never need a login.
  - Two-way sync with the shop's Google account — contacts, production calendar — and transactional email sent through Gmail in four languages.
  - Supplier catalogue imports (Stricker, Cifra, Valento, Makito, midocean), reviewed before publishing, plus purchase requests back to suppliers — sent straight through the API for midocean.
stack:
  - Next.js
  - Swift · Vapor 4
  - Fluent · PostgreSQL
  - Auth0
  - Google APIs
  - Vercel · Fly.io
  - Back-office
---

Brindigráfica prints logos on things — t-shirts, caps, pens, bags, and everything else a company hands out at a fair. Their existing site showed the products but couldn't take an order, so every quote was a phone call. The rebuild turns the website into the front door of the business — and the back office into the place the team actually runs it from.

## What we built

A **Next.js storefront** with the full catalogue in categories up to three levels deep, a cart, and two ways to buy: a straight order, or a quote request for the custom-printed jobs that need a conversation first. Product pages show colours as swatches with a photo per colour, and a **multi-size grid** lets a customer order 10 S, 25 M and 30 L in one go — priced as one job, with the printing (screen printing, embroidery, laser, DTF) charged once for the lot. Customers get an account area to follow their orders and quotes.

Behind it, an **API in server-side Swift** — Vapor 4 with Fluent on PostgreSQL. Authentication stays on Auth0, and the frontend talks to the API through a Next.js proxy so tokens never reach the browser.

The team gets a **back-office** on top of the same API: orders, quotes, products, printing types and users in one place. Orders taken over the phone go straight in, for an existing customer, a new one who gets an invitation, or a client who'll never need a login at all.

The platform is connected to the shop's **Google account**: contacts sync both ways with the phone, bookings and production milestones live in Google Calendar (move one there and the order follows), and status updates and confirmations go out through Gmail in Portuguese, English, French or Spanish.

**Supplier catalogues** from Stricker, Cifra, Valento, Makito and midocean are imported into a review queue and published by a person, never automatically. From a customer order, the back-office drafts purchase requests to each supplier — and for midocean, sends them straight through its API.

## Where it stands

Live at [brindigrafica.pt](https://brindigrafica.pt/) since October and in daily use. Online payments are still deliberately out — promotional products are quoted and invoiced, so that flow came first. Next: sending email from the shop's own domain and the first midocean price list.

The story so far is on the blog: [the stack and the launch](/blog/brindigrafica-website/), and [part two — orders at the counter, Google and new suppliers](/blog/brindigrafica-whats-new/).
