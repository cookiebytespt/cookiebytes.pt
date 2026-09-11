# CookieBytes site (Jekyll)

The CookieBytes landing page plus "The Cookie Jar" blog, as a Jekyll site — in English (default, at `/`) and Portuguese (at `/pt/`).

## Run locally

```bash
bundle install
bundle exec jekyll serve
# → http://localhost:4000
```

## Languages (EN default, PT under /pt/)

English lives at the site root; Portuguese mirrors it under `/pt/`. Everything a visitor
reads comes from one of two places:

- **UI strings** (nav, footer, home page, listings, buttons, form messages) — `_data/i18n/en.yml`
  and `_data/i18n/pt.yml`. Same keys in both files; edit the copy there, not in the HTML.
- **Content** (posts and case studies) — one file per language, paired by a shared `ref:`.

| | English | Portuguese |
| --- | --- | --- |
| Home / Work / Blog pages | `index.html`, `portfolio/index.html`, `blog/index.html` | `pt/index.html`, `pt/portfolio/index.html`, `pt/blog/index.html` |
| Posts | `_posts/*.md` → `/blog/:title/` | `_posts/pt/*.md` → `/pt/blog/:title/` |
| Projects | `_portfolio/*.md` → `/portfolio/:name/` | `_portfolio/pt/*.md` → `/pt/portfolio/:name/` |

The page files are just front matter + `{% include pages/home.html %}` (etc.); the markup lives
once in `_includes/pages/`. `lang` is set automatically by folder (see `defaults` in `_config.yml`).

The EN · PT switch in the nav (and the language link in the footer) jumps to the page with the
same `ref` in the other language, and falls back to that language's home when there's no
translation. Pages with a translation also get `hreflang` alternates in `<head>`.
Contact-form submissions include a `language` field, and PT ones get "(PT)" in the subject.

**Adding a translated post:** write `_posts/2026-08-01-my-post.md` with `ref: my-post`, then
`_posts/pt/2026-08-01-o-meu-artigo.md` with the same `ref: my-post` (Portuguese slug is fine).
Links inside PT posts should point at PT pages, e.g. `[Fale connosco](/pt/#contact)`.

**Adding a translated project:** same idea — `_portfolio/slug.md` and `_portfolio/pt/slug.md`
with the same `ref:`. Keep non-text fields (`status`, `order`, `progress`, `site_url`, `mock`,
`image`, `stack`…) in sync in both files.

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

Add `ref: your-slug` too, so the Portuguese version (in `_posts/pt/`, same `ref`) can be linked — see *Languages* above. The listing at `/blog/` updates automatically. Posts get URLs like `/blog/your-title/`.

Optional flourishes inside posts:

```html
<div class="callout">💡 Highlighted tip box (colored per category).</div>
<div class="divider">🍪 🍪 🍪</div>
```

## Add a project to the portfolio

Create `_portfolio/slug.md` (and its Portuguese twin `_portfolio/pt/slug.md` with the same `ref`) — the listing at `/portfolio/` and the case-study page at `/portfolio/slug/` update automatically:

```yaml
---
ref: slug                    # shared with the Portuguese version
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

- `_layouts/default.html` — head (hreflang, per-page CSS), nav, footer (shared by everything)
- `_includes/i18n.html` — sets `lang`, `t` (strings), `home_url`, `alt_url` for the current page
- `_includes/lang-switch.html` — the EN · PT toggle
- `_includes/pages/` — markup for home, portfolio listing and blog listing (both languages)
- `_data/i18n/` — UI strings per language
- `_layouts/post.html` — article page (meta, lede, author box)
- `_includes/` — nav, footer, cookie logo SVG
- `assets/css/` — `shared.css` (tokens/nav/footer), `home.css`, `blog.css`, `portfolio.css`
- `index.html` / `pt/index.html` — landing page
- `blog/index.html` / `pt/blog/index.html` — post listing
- `_posts/` — the posts (`_posts/pt/` for Portuguese)
- `_layouts/project.html` — portfolio case-study page
- `_includes/project-mock.html` — browser-frame preview (screenshot or CSS mock)
- `portfolio/index.html` / `pt/portfolio/index.html` — portfolio listing
- `_portfolio/` — one file per project (collection, see `_config.yml`; `_portfolio/pt/` for Portuguese)
- `assets/portfolio/` — project images

## Deploy

Any static host works with the `_site/` output of `bundle exec jekyll build`.
For GitHub Pages, push the repo and enable Pages — or swap the Gemfile to the
`github-pages` gem (commented line) for exact version parity.

Note: the contact form still needs a Web3Forms access key in `_includes/pages/home.html`
(`YOUR_ACCESS_KEY_HERE`).
