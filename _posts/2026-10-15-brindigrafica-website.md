---
ref: brindigrafica-website
layout: post
title: Rebuilding brindigrafica.pt — a Next.js storefront on a Swift backend
description: Brindigráfica sells promotional products — t-shirts, caps, pens, the lot. We rebuilt their site as a Next.js storefront backed by a Vapor API, with Auth0 for identity, a backoffice for the team, and supplier catalogue imports. Here's the stack, where it runs, and the parts that bit us.
category: tech
emoji: 🎁
read_time: 9 min read
lede: Brindigráfica needed more than a brochure site. They needed a catalogue, a store, quote requests, customer accounts, and a place for their own team to manage all of it — without pretending to be a company that lives in a web app. This is the write-up of what we built, what it runs on, and the decisions we'd make again (and the one we had to walk back on day one).
author_emoji: 🍪
author_note: Rocky deleted exactly zero production configs during this project, which we're counting as a personal best. Peggy reviewed the data model and said nothing, which is as close to approval as she gets.
---

<figure>
  <img src="/assets/blog/brindigrafica/banner.png" alt="The new brindigrafica.pt storefront, built by CookieBytes">
</figure>

## The brief

[Brindigráfica](https://brindigrafica.pt) is a Portuguese promotional-products company: branded t-shirts, caps, pens, bags, and the thousand other things a company puts its logo on. Their old site did what most sites in that industry do — showed a few photos and a phone number — and the actual business happened over email, phone and spreadsheets.

The ask was to bring that business online, in stages. Not "build an Amazon." A proper product catalogue, a store that customers could use to assemble an order or ask for a quote, customer accounts to track those orders and quotes, and — the part that matters most day to day — a backoffice where Brindigráfica's own team could manage products, orders and users without calling us.

We scoped v1 deliberately: catalogue, cart, orders and quote requests, with **no online payments yet**. Promotional products are quoted, negotiated and invoiced; forcing a checkout flow onto that would have been building the wrong thing well.

<div class="divider">🍪 🍪 🍪</div>

## The stack

The whole project lives in one repo, `Brindigrafica/`, split into `frontend/`, `backend/` and `docs/`. We consolidated it that way early — one place for everything made deployment and the cross-cutting changes (a new field on a product touches both halves) far less painful.

| Layer | What we used |
| --- | --- |
| Frontend | Next.js (App Router), TypeScript |
| API | Server-side Swift — Vapor 4, Fluent ORM |
| Database | PostgreSQL |
| Identity | Auth0 (New Universal Login, Management API) |
| Frontend hosting | Vercel, Node 24, `brindigrafica.pt` + `www` |
| API hosting | <!-- TODO: where does the Vapor API run? (Fly.io / Hetzner / Docker on a VPS / …) --> |

**Why Swift on the server?** Partly because we like it — we build mobile apps, and a backend in the same language we think in on iOS is a real productivity win. But mostly because Vapor is a mature, well-typed framework that produces small, fast binaries, and Fluent gives us a migration-driven schema that lives in version control next to the models. Type safety on the API side caught more bugs at compile time than we'd have found in a week of testing.

**Why Next.js in front?** A storefront needs to render fast and index well — server components and the App Router get us that for the catalogue and product pages — while the cart, account and backoffice are properly interactive. One framework covers both sides without us running two frontends.

<div class="divider">🍪 🍪 🍪</div>

## How the pieces fit

The frontend never talks to the API directly from the browser. Every request goes through a **BFF proxy** inside Next.js: route handlers that hold the session server-side, attach the API token, and forward the call. Tokens never reach the client, the API only ever sees one trusted caller, and the browser-facing surface stays small. We ripped out the mock data layer we'd used during the design phase rather than keeping it as a "fallback" — a fallback that silently serves fake products is a bug waiting to be discovered by a customer.

The API itself is RESTful, English throughout. One small decision that saved us arguments later: query parameters are English even though the product is Portuguese — `?category=`, not `?categoria=`. The UI is translated; the API isn't a UI.

<figure>
  <img src="/assets/blog/brindigrafica/architecture.png" alt="Architecture diagram — browser to Next.js on Vercel, BFF proxy to the Vapor API, PostgreSQL behind it, Auth0 for identity">
  <figcaption>Browser → Next.js (Vercel) → BFF proxy → Vapor API → PostgreSQL. Auth0 sits to the side and is the only thing that ever sees a password.</figcaption>
</figure>

**Accounts** are Auth0 end to end. Customers get an account area with *Encomendas* (orders) and *Orçamentos* (quotes); admins get the backoffice. We branded Auth0's own screens and emails as Brindigráfica — New Universal Login with a page template, plus every email template (welcome, password reset, verification) — and kept all of it as files in the repo with a small script that applies them through the Management API. The branding is code, not something someone clicked together in a dashboard once and nobody can reproduce.

<div class="divider">🍪 🍪 🍪</div>

## The backoffice

This is the part Brindigráfica's team actually lives in. It's a subpage of the frontend at `/backoffice` (we'd called it `/admin` for about a day; the client's word for it won), reusing the exact same API with role checks — no second app, no second deploy, no second set of bugs.

From there the team can:

- **Manage products** — create, edit, publish, deactivate.
- **Manage orders and quote requests** — move them through their lifecycle, see what a customer asked for and when.
- **Manage users** — including adding and inviting new ones. We chose to do invites through the Auth0 Management API: the API creates the Auth0 user and Auth0 sends the branded set-your-password email. We didn't want to own an email pipeline just to send one message, and Auth0 already handles deliverability, expiry and retries better than we'd bother to.

One rule we were careful about: **deleting products**. A product that's been manually created can be permanently deleted — but only if it isn't attached to any order or quote request. Anything with history gets deactivated instead, never deleted, so a quote from March still shows the thing that was quoted in March. Products imported from suppliers can only ever be deactivated. Which brings us to the hard part.

<div class="divider">🍪 🍪 🍪</div>

## The challenges

### 1. We assumed Vapor 5 had shipped. It hadn't.

We started the API on Vapor 5 — new async-first design, cleaner than 4, exactly what we wanted to write. Then we went looking for a Fluent driver and found out Vapor 5 was still in alpha. We'd mistaken "actively discussed" for "released." The fix was straightforward (back to Vapor 4 + Fluent + PostgreSQL, a stable, battle-tested combination) but it's a lesson we'll keep repeating to ourselves: *check the release page, not the blog posts, before you pick a major version.*

### 2. Supplier catalogues are six different shapes

Brindigráfica doesn't manufacture; it sources from suppliers, each with its own catalogue, pricing and — crucially — its own idea of what an API is. We built import connectors for the three we could:

- **Stricker** — structured catalogue import.
- **Cifra** — API with token auth.
- **Valento** — REST, worked out from their manuals.

Three others (Tejo Brinde, Chuvitex, SOL'S) simply have no integration to speak of, so those products stay manual.

The data-model decisions were as important as the connectors. Supplier origin is tracked on every imported product but is **backoffice-only** — a customer never sees who made the pen. We do **no price rounding** on import; whatever the supplier says is what lands in the database, and pricing decisions are made by humans on top of that. And nothing imported goes live automatically: every import lands as unpublished and gets **reviewed before publishing**. A connector that pushed 2,000 products straight onto the public site because one supplier renamed a category would be a very bad morning.

### 3. Keeping secrets off the client

The BFF proxy exists because of this. The first draft of the frontend had the browser calling the API with a bearer token, which is fine for a demo and terrible for a store with customer data. Moving every API call behind Next.js route handlers meant more plumbing, but the token boundary is now exactly one line thick and it's on the server.

### 4. Branding a thing you don't host

Auth0's screens are the first thing a new customer sees, and they looked like Auth0. Classic Universal Login would have let us hand-write HTML, but it's the legacy path. The New Universal Login + page template approach is more constrained and took longer to get looking right, but it's the supported one and it survives Auth0's own updates. We'd rather fight the constraints of the current thing than own a custom HTML page that breaks in a year.

<div class="divider">🍪 🍪 🍪</div>

## Deployment

The **frontend** deploys to Vercel from the repo — production on `brindigrafica.pt` and `www.brindigrafica.pt`, preview URLs for every branch so the client can review a change before it's live. Zero config beyond pointing DNS at it.

The **API** <!-- TODO: fill in — platform, how the Vapor binary is built (Dockerfile?), how Postgres is hosted/backed up, how migrations run on deploy -->.

Auth0 configuration — login page template, email templates, the Management API script that applies them — lives in `docs/` and the repo, so a fresh tenant can be set up from the files alone.

<div class="divider">🍪 🍪 🍪</div>

## What's next

v1 shipped with the store deliberately short of payments. The roadmap from here:

- **Online payments** — once the quote-and-order flow has had real customers on it for a while and we know which products actually make sense to buy off-the-shelf.
- **Calendar integration** — customer bookings synced to Google Calendar, plus an order/production timeline the team can see at a glance.
- **More supplier connectors**, as the remaining suppliers grow APIs — or as we work out how to parse their price lists without one.

<div class="divider">🍪 🍪 🍪</div>

## What we'd say to the next client

Build the backoffice first in your head, even if you build the storefront first in code. Brindigráfica's team doesn't care about server components or Swift concurrency; they care that adding a product takes thirty seconds and that a quote from six months ago still shows what was quoted. Every architectural decision above — the proxy, the deletion rules, the review-before-publish import — came from asking what the *team* needs, not what the *site* needs.

Need a store, a catalogue, or a backend that your team can actually run? [Talk to us](/#contact). We bring the butter.
