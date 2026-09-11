---
ref: brindigrafica               # shared with the Portuguese version
title: Brindigráfica
client: Promotional products · Portugal
kind: Website, store & API
year: 2026
status: wip             # live | wip
order: 2
description: A full rebuild of Brindigráfica's online presence — new website, product catalogue and store, backed by an API written from scratch and a back-office for the team.
site_url:               # no public URL yet — leave empty while in progress
progress: 55            # % shown on the "baking progress" bar (wip only)
progress_note: API & store · in development
mock: brindigrafica
# screenshot: /assets/portfolio/brindigrafica/home.png
highlights:
  - Next.js frontend with catalogue, cart, orders and quote requests; customer area for orders and quotes.
  - Server-side Swift API (Vapor 4 + Fluent + PostgreSQL) with Auth0 login, kept behind a Next.js proxy so tokens never touch the browser.
  - Back-office for orders, products and users, reusing the same API.
  - Customer bookings with Google Calendar sync and an order/production timeline.
stack:
  - Next.js
  - Swift · Vapor 4
  - Fluent · PostgreSQL
  - Auth0
  - Google Calendar
  - Back-office
---

Brindigráfica prints logos on things — t-shirts, caps, pens, bags, and everything else a company hands out at a fair. Their existing site showed the products but couldn't take an order, so every quote was a phone call. The rebuild turns the website into the front door of the business.

## What we're building

A **Next.js storefront** with the full catalogue, filters by category, a cart, and two ways to buy: a straight order, or a quote request for the custom-printed jobs that need a conversation first. Customers get an account area to follow their orders and quotes.

Behind it, an **API in server-side Swift** — Vapor 4 with Fluent on PostgreSQL. Authentication stays on Auth0, and the frontend talks to the API through a Next.js proxy so tokens never reach the browser.

The team gets a **back-office** on top of the same API: orders, products and users in one place, plus bookings that sync to Google Calendar and a production timeline for each job.

## Where it stands

The API and the store are in active development; online payments are deliberately out of v1 — catalogue, cart, orders and quotes ship first. This page will get a link the day it goes live.
