---
ref: sobremesas-companhia   # shared with the Portuguese version
title: Sobremesas & Companhia
client: Cakes, dessert tables & catering · Santo Tirso
kind: Landing page & business platform
year: 2026
status: wip             # live | wip
order: 3
description: A four-language landing page for a two-person cake and catering kitchen, with a quote form that lands straight in WhatsApp — and, behind it, a platform to run the business, now in the oven.
site_url: https://sobremesasecompanhia.pt/
progress_note: Landing page live · platform in development
mock: sobremesas
highlights:
  - Gallery of 83 creations, filterable by occasion — weddings, christenings, communions, birthdays and more.
  - Quote form with no backend — it writes the request and opens WhatsApp with the message ready to send.
  - Portuguese, English, Spanish and French, with a build that fails if a string is left untranslated.
  - Brand lifted from their own logo and Instagram covers, down to a scalloped doily edge drawn in pure CSS.
stack:
  - Next.js 16
  - Tailwind CSS 4
  - Static export
  - GitHub Pages
  - PT / EN / ES / FR
  - WhatsApp
---

Sobremesas & Companhia is a couple making decorated cakes, cookies, dessert tables and catering for weddings, christenings, communions and birthdays, from a kitchen in Santo Tirso. Their work lived on Instagram and in direct messages — which works, right up until someone wants to send a link to their sister and ask "what do you think of these?"

## Stage one: the landing page

[sobremesasecompanhia.pt](https://sobremesasecompanhia.pt/) is a single, static scroll: what they make, a gallery of past work filterable by occasion, how ordering works, where they deliver, and a quote form. No server, no accounts, no CMS to babysit — `next build` produces plain HTML, CSS and images, deployed to GitHub Pages on every push.

The quote form asks for the essentials — occasion, date, guests, what they're after — and opens WhatsApp with the request already written, so the couple gets a complete brief in the app they already live in. The site is in four languages, typed against the Portuguese dictionary so a half-translated page can't ship.

The full write-up is in the [blog post](/blog/sobremesas-companhia-landing-page/).

## Stage two: the platform

The landing page is the front door. Behind it, we're building **a platform to run the business itself**, beyond what a static page can do. It'll get its own case study once there's something real to show.
