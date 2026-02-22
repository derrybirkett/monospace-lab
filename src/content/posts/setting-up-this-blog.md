---
title: "Setting Up This Blog"
description: "How Monospace.Labs went from zero to live in under an hour with Astro, the UINUX Blog theme, and GitHub Pages."
date: 2026-02-22
---

This blog exists because I wanted a quiet place to write about what I build. No CMS, no database, no JavaScript framework fighting me. Just Markdown files and a deploy pipeline.

Here's how it came together.

---

## The Stack

- **Astro** — static site generator, zero client-side JS by default
- **UINUX Blog theme** — typography-first, calm design. Newsreader for body text, Inter for headings, 640px content width
- **GitHub Pages** — free static hosting with custom domain support
- **GitHub Actions** — build and deploy on every push to main

The entire site is six components, a CSS file, and a content folder. Nothing else.

---

## The Setup

Started with the Astro blog starter, then swapped in the [UINUX Blog](https://github.com/Get-UINUX/uinux-blog) theme for its focus on readability and restraint. Stripped the nav header, rebranded to Monospace.Labs, and pointed it at `labs.monospace.studio`.

The deploy workflow is straightforward:

```yaml
on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./dist

  deploy:
    needs: build
    uses: actions/deploy-pages@v4
```

Push to main. Build runs. Site deploys. Done.

---

## Custom Domain

GitHub Pages + a CNAME record pointing `labs.monospace.studio` to `derrybirkett.github.io`. GitHub provisions a Let's Encrypt SSL cert automatically. HTTPS enforced.

A `public/CNAME` file in the repo ensures the custom domain survives every deploy.

---

## What I Like About This Setup

**No build complexity.** Astro compiles Markdown to static HTML. No hydration, no client runtime, no bundle optimisation to worry about.

**Content is just files.** Posts are `.md` files with three frontmatter fields: `title`, `description`, `date`. No schemas to maintain, no CMS to log into.

**Deploys are instant.** Push, wait 30 seconds, live. The GitHub Actions workflow takes under a minute.

**The theme stays out of the way.** Serif body text, generous whitespace, no dark mode toggle, no hamburger menu. Just words on a page.

---

## What's Next

More posts. The blog exists to document real projects — infrastructure, automation, agentic systems. The [HomeCloud launch post](/posts/homecloud-launch) was the first. This is the second.

The best publishing system is the one you actually use.
