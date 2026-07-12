# berthly.net — Design & Plan

Marketing + blog site for **Berthly**, the native macOS GUI for Apple's
[`container`](https://github.com/apple/container). Hosted on Vercel at
`berthly.net`.

Two jobs, from RAW.md:

1. **Convert** — introduce the app's features and turn visitors into users.
2. **Publish** — a blog for design write-ups, devlog (issues & fixes),
   release announcements, and news.

---

## 1. Positioning & voice

- **One-liner (hero):** *"Apple's `container`, finally with a face."* or
  *"The Docker Desktop experience for Apple's container tool."*
- **Audience:** macOS developers on Apple Silicon who use (or are curious
  about) Apple's `container` CLI and miss a GUI. They know Docker Desktop /
  OrbStack; the pitch is instantly familiar.
- **Voice:** developer-to-developer, concrete, no marketing fluff. The app's
  privacy story ("everything stays on your Mac, no telemetry") is a selling
  point — the *website* must live up to it (see §5, no trackers).
- **Primary CTA:** **Download for macOS** → latest GitHub release asset
  (dmg). Secondary CTA: **View on GitHub**.

## 2. Tech stack

| Choice | What | Why |
| --- | --- | --- |
| Framework | **Astro 5** | Content-first: built-in Markdown/MDX content collections, RSS, sitemap; ships ~zero JS so the site is instant; first-class Vercel adapter. (Next.js would also work, but it's heavier than this site needs and Astro's authoring flow for a Markdown blog is simpler.) |
| Styling | **Tailwind CSS 4** | Fast to build the design system in §3; dark mode via `prefers-color-scheme` + toggle. |
| Content | **MDX files in the repo** (`src/content/blog/*.mdx`) | You write posts in your editor and `git push` — no CMS to run. Frontmatter: `title, date, description, tags, cover?`. |
| Code blocks | Shiki (built into Astro) | Devlog posts will be full of Swift/shell snippets; needs great highlighting in light *and* dark. |
| Rendering | Fully static (SSG) | No server code needed anywhere. Vercel serves it from the edge. |
| Analytics | None, or Vercel Web Analytics (cookieless) at most | Matches the app's privacy stance. |

## 3. Visual design

The site should feel like a sibling of the app: **native-macOS, calm, blue,
nautical**.

### Brand tokens

From `Berthly/design/icon` (mark = dock bollard with mooring line):

```
--brand:        #2A6FDB   (brand blue — links, CTA, accents)
--brand-bright: #3F82EC   (gradient partner / hover)
--rope:         #8FB6F0   (mid tint — borders, secondary accents)
--water:        #B9D4FB   (light tint — backgrounds, section washes)
```

- **Light theme:** white/near-white background, `#B9D4FB`-tinted section
  bands ("water"), near-black text, brand-blue CTAs.
- **Dark theme:** deep navy background (~`#0B1526`/`#0F1B30`, i.e. the brand
  blue's dark relatives — not plain gray), tints used sparingly for glow and
  borders. Both themes required; developers live in dark mode.
- **Typography:** system stack (`-apple-system, "SF Pro", Inter, sans-serif`)
  so the site literally renders in the same face as the app on a Mac; mono is
  `ui-monospace, "SF Mono", "JetBrains Mono"`.
- **Texture:** a subtle "waterline" motif — the horizontal band from the icon
  — as a section divider (a flat tinted wave/line, not a heavy illustration).
- **Imagery:** real app screenshots (light + dark), lightly framed in a
  macOS-style window with shadow. No stock art.

### Iconography assets → `public/`

| Source (in `Berthly/design/icon`) | Use on site |
| --- | --- |
| `berthly-favicon-512.svg` | `favicon.svg` (+ 32/16 PNG fallbacks) |
| `berthly-touch-512.png` | `apple-touch-icon.png` |
| `berthly-dock-1024.png` | hero/nav logo mark, OG-image ingredient |
| `berthly-menubar-36.svg` | small monochrome glyph (footer, dividers) |

OG image (1200×630): dark-navy card, icon left, "Berthly — a native macOS
GUI for Apple's container" right. One static file for the site + a generated
variant per blog post (title baked in) via `@vercel/og` or a build-time
satori step — v1 can ship with the single static image.

### Page inventory & layout

```
/            Home (landing)
/blog        Blog index (reverse-chron, tag filter)
/blog/[slug] Post
/tags/[tag]  Posts by tag
/changelog   (optional later — releases can just be a blog tag at v1)
/rss.xml     RSS feed        /sitemap.xml    404 page
```

**Home page, top to bottom:**

1. **Nav** — icon + "Berthly", links: Features · Blog · GitHub, small
   Download button, theme toggle.
2. **Hero** — headline + one-liner, big screenshot of the main window,
   `Download for macOS` + `View on GitHub` buttons, small print:
   "Free & open source · Apache-2.0 · Apple Silicon, macOS 26+".
3. **"What it is" strip** — one sentence explaining Apple `container` and
   that Berthly is its missing GUI (many visitors won't know the CLI yet;
   this section also catches "apple container gui" search intent).
4. **Feature grid** (from the app README, one screenshot or glyph each):
   Images · Containers + live logs · Machines/VMs · Networks & volumes ·
   Registries · Integrated terminal (SwiftTerm) · Command palette ·
   Menu-bar presence.
5. **Privacy section** — "Everything stays on your Mac": no telemetry,
   Keychain-only credentials, open code. This is a differentiator; give it
   its own section, not a footnote.
6. **Latest from the blog** — 3 most recent posts.
7. **Footer** — GitHub, RSS, license, report-an-issue, SECURITY.md link.

**Blog post page:** ~68ch measure, big readable type, Shiki code blocks,
post date + tags, prev/next links, RSS autodiscovery. Tags (fixed small set):
`design`, `devlog`, `release`, `news`.

## 4. Build plan

**Phase 0 — Scaffold** (~½ day)
- `git init`; `npm create astro@latest` (empty template) + Tailwind 4 +
  MDX + sitemap + RSS integrations; Vercel project connected to the repo.
- Copy icon assets per the table above; verify favicon set.

**Phase 1 — Design system & shell** (~½ day)
- Tokens from §3 as CSS variables; light/dark themes + toggle.
- `BaseLayout` (head/meta/OG), Nav, Footer, container widths, waterline
  divider component, buttons.

**Phase 2 — Home page** (~1 day)
- All seven sections above. Needs **app screenshots** (light + dark of the
  main window, plus 2–3 feature close-ups) — capture from the running app;
  this is the only asset that doesn't already exist.
- "Download" button resolves to the latest GitHub release dmg (static link
  to `releases/latest` is fine for v1).

**Phase 3 — Blog** (~1 day)
- Content collection schema; index, post, and tag pages; Shiki themes for
  light/dark; RSS + sitemap; two seed posts (e.g. "Introducing Berthly" and
  the icon-design story — `design/icon/README.md` is already half a post).

**Phase 4 — Polish & SEO** (~½ day)
- OG/meta on every page, JSON-LD (`SoftwareApplication` on home,
  `BlogPosting` on posts), 404 page, Lighthouse pass (target ≥95 across the
  board — static Astro should get there by default), responsive pass
  (375px → 1440px), keyboard/contrast a11y check.

**Phase 5 — Launch** (~½ day, mostly waiting on DNS)
- Register `berthly.net`, add to Vercel, apex + `www` → apex redirect,
  HTTPS auto. Production deploy from `main`; preview deploys per PR.
- Add the site URL to the app README and GitHub repo metadata.

**Later / not v1:** per-post generated OG images, `/changelog` page fed by
GitHub Releases API, sparkle-style appcast if the app ever self-updates,
localized pages.

## 5. Principles / guardrails

- **No trackers, no cookies, no third-party requests.** Fonts are system,
  JS is near-zero, everything self-hosted. The privacy page of the app and
  the behavior of the site must tell the same story.
- **Blog is files in git.** No database, no CMS, no auth — nothing to
  maintain.
- **Screenshots are the marketing.** Budget real time for good captures;
  they matter more than any copy.
- **Site stays boring on the inside** so publishing a post is: write `.mdx`,
  `git push`, done.
