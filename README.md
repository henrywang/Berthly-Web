# berthly.net

Marketing site and blog for [Berthly](https://github.com/henrywang/Berthly), the native macOS
GUI for Apple's `container` tool. Built with [Astro](https://astro.build), deployed on Vercel.

## Develop

```sh
npm install
npm run dev      # http://localhost:4321
npm run build    # static output in dist/
```

## Publish a blog post

Add an `.mdx` file to `src/content/blog/`:

```mdx
---
title: "Post title"
description: "One-sentence summary shown in lists, RSS, and meta tags."
date: 2026-07-12
tags: [devlog]   # design | devlog | release | news
---

Markdown body…
```

Push to `main` and Vercel deploys it. RSS, sitemap, tag pages, and the home-page
"Latest posts" section update automatically.

## Design system

`design-system/` holds the HTML design cards (also synced to the "Berthly Web" project on
claude.ai/design) and `og.svg`, the source for the social-preview image. Icons come from the
app repo at `Berthly/design/icon`. The overall design and build plan is in [PLAN.md](PLAN.md).
