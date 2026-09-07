# CookieBytes site (Jekyll)

The CookieBytes landing page plus "The Cookie Jar" blog, as a Jekyll site.

## Run locally

```bash
bundle install
bundle exec jekyll serve
# → http://localhost:4000
```

## Add a post

Create `_posts/YYYY-MM-DD-slug.md`:

```yaml
---
layout: post
title: Your title
description: One-sentence excerpt shown on the listing page.
category: tech        # studio | tech | crew (controls tag + accent colors)
emoji: 🍪             # big faded emoji on the listing card
read_time: 5 min read
lede: Intro paragraph shown under the title.
author_emoji: 🍪
author_note: Line shown in the author box at the bottom.
---

Markdown content here.
```

That's it — the listing at `/blog/` updates automatically. Posts get URLs like `/blog/your-title/`.

Optional flourishes inside posts:

```html
<div class="callout">💡 Highlighted tip box (colored per category).</div>
<div class="divider">🍪 🍪 🍪</div>
```

## Add a project to the portfolio

Create `_portfolio/slug.md` — the listing at `/portfolio/` and the case-study page at `/portfolio/slug/` update automatically:

```yaml
---
title: Project name
client: Who it was for · Where
kind: Event website          # short label shown next to the year
year: 2026
status: live                 # live | wip  (wip adds the "Work in progress" tape + progress bar)
order: 1                     # lower = shown first
description: One-sentence summary shown on the listing and as the case-study lede.
site_url: https://example.com/   # leave empty while in progress
tagline: "// optional mono-font flourish next to the buttons"
progress: 55                 # wip only — % on the "baking progress" bar
progress_note: API & store · in development
screenshot: /assets/portfolio/slug/home.png   # real screenshot (preferred) …
mock: bonanca                # … or a CSS mock: bonanca | brindigrafica (anything else = emoji placeholder)
image: /assets/portfolio/slug/poster.jpg      # poster inside the bonanca mock
highlights:
  - Bullet points shown on the listing card.
stack:
  - Next.js
  - Vapor 4
---

Markdown case study (optional). `## Headings`, lists, `<figure>` etc. work as in blog posts.
```

## Structure

- `_layouts/default.html` — head, nav, footer (shared by everything)
- `_layouts/post.html` — article page (meta, lede, author box)
- `_includes/` — nav, footer, cookie logo SVG
- `assets/css/` — `shared.css` (tokens/nav/footer), `home.css`, `blog.css`, `portfolio.css`
- `index.html` — landing page
- `blog/index.html` — post listing
- `_posts/` — the posts
- `_layouts/project.html` — portfolio case-study page
- `_includes/project-mock.html` — browser-frame preview (screenshot or CSS mock)
- `portfolio/index.html` — portfolio listing
- `_portfolio/` — one file per project (collection, see `_config.yml`)
- `assets/portfolio/` — project images

## Deploy

Any static host works with the `_site/` output of `bundle exec jekyll build`.
For GitHub Pages, push the repo and enable Pages — or swap the Gemfile to the
`github-pages` gem (commented line) for exact version parity.

Note: the contact form still needs a Web3Forms access key in `index.html`
(`YOUR_ACCESS_KEY_HERE`).
