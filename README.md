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

## Structure

- `_layouts/default.html` — head, nav, footer (shared by everything)
- `_layouts/post.html` — article page (meta, lede, author box)
- `_includes/` — nav, footer, cookie logo SVG
- `assets/css/` — `shared.css` (tokens/nav/footer), `home.css`, `blog.css`
- `index.html` — landing page
- `blog/index.html` — post listing
- `_posts/` — the posts

## Deploy

Any static host works with the `_site/` output of `bundle exec jekyll build`.
For GitHub Pages, push the repo and enable Pages — or swap the Gemfile to the
`github-pages` gem (commented line) for exact version parity.

Note: the contact form still needs a Web3Forms access key in `index.html`
(`YOUR_ACCESS_KEY_HERE`).
