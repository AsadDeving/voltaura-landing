<!-- GSD:project-start source:PROJECT.md -->

## Project

**Voltaura's Transformator — Landing Page**

A trilingual (Uzbek Latin, Uzbek Cyrillic + Russian) landing page for Voltaura's Transformator, a transformer business in Andijan, Uzbekistan, operating since 2010. The business repairs transformers and manufactures and sells transformer products. The page turns visitors (factories, utilities/builders, farms and small businesses) into phone calls, Telegram chats and workshop visits.

**Core Value:** A visitor instantly understands what Voltaura does (repair, produce, sell transformers), trusts it (since 2010, certificates, CIS partners) and can contact the business in one tap.

### Constraints

- **Languages**: Uzbek (Latin + Cyrillic) + Russian — local buyers and CIS partners
- **Contact**: Call, Telegram and visit only — user-selected goals
- **Style**: Clean corporate — user choice
- **Hosting**: Static-friendly, deployable on a custom domain

<!-- GSD:project-end -->

<!-- GSD:stack-start source:research/STACK.md -->

## Technology Stack

## Headline Recommendation

## Recommended Stack

### Core Technologies

| Technology | Version (verified 2026-10-07) | Purpose | Why Recommended | Confidence |
|------------|-------------------------------|---------|-----------------|------------|
| Astro | 7.3.6 | Static site generator, routing, i18n, image pipeline | Ships zero JS by default, so the best Lighthouse/LCP baseline for a content page. Has first-class i18n routing, a build-time image pipeline (sharp), a stable Fonts API and built-in SVG imports. Static output works on any host, including Vercel. Needs Node >= 22.12. | HIGH |
| Tailwind CSS | 4.3.3 | Styling (white/navy corporate theme) | v4 is CSS-first: define the navy palette in `@theme` in CSS, with no JS config. Only used classes are emitted, so the CSS payload is tiny. Mobile-first breakpoints match the brief. Same utility approach the user likely knows from React work. | HIGH |
| @tailwindcss/vite | 4.3.3 | Tailwind integration | Official Astro install path is the Vite plugin, not `@astrojs/tailwind`, which is the old v3-era integration. Peer range covers Vite 5 to 8, and Astro 7 uses Vite 8. | HIGH |
| TypeScript | 6.0.3 (pin `~6.0`) | Typing for i18n dictionaries and config | **Do not use 7.x yet.** `typescript@7.0.2` is on npm, but `@astrojs/check@0.9.10` declares peer `^5 \|\| ^6`. Pin 6 to avoid peer conflicts. | HIGH |
| Node.js | 24 LTS (>= 22.12 required) | Build runtime | Astro 7 engines field requires >= 22.12. Local Node is already v24.16. Set the same version in the host's build settings. | HIGH |

### Internationalization (no library)

| Approach | Version | Purpose | Why Recommended | Confidence |
|----------|---------|---------|-----------------|------------|
| Astro built-in i18n routing (`i18n` in `astro.config.mjs`) | part of Astro 7 | `/` = Uzbek, `/ru/` = Russian | `defaultLocale: "uz"`, `locales: ["uz","ru"]`, `routing.prefixDefaultLocale: false`. Pure static HTML per language, no client-side language detection or runtime i18n library. Both languages are crawlable as separate URLs, which is what SEO needs. `getRelativeLocaleUrl()` builds switcher links. | HIGH |
| Typed dictionaries `src/i18n/uz.ts` and `ru.ts` plus a tiny `t(locale, key)` helper | hand-written (~20 lines) | Translate UI strings and section copy | A one-page site has about 100 strings. i18next, react-intl and Paraglide are pure overhead here. Typing `ru` as `typeof uz` makes a missing translation a compile error via `astro check`. | HIGH |
| `@astrojs/sitemap` | 3.7.4 | `sitemap-index.xml` with hreflang alternates | Its `i18n` option (`defaultLocale` + `locales` map such as `{ uz: 'uz-UZ', ru: 'ru-UZ' }`) emits `xhtml:link rel="alternate" hreflang` entries automatically. Requires `site` set in the Astro config. | HIGH |

### Fonts

| Technology | Version | Purpose | Why | Confidence |
|------------|---------|---------|-----|------------|
| Astro Fonts API with `fontProviders.fontsource()` | built into Astro 7 (stable) | Self-hosted, preloaded, subsetted webfont with auto fallback metrics | Downloads at build time and self-hosts, so no third-party Google Fonts request and no extra DNS/TLS round trip on slow mobile networks. Handles fallback font metrics to cut CLS. | HIGH |
| Inter variable (via Fontsource) | `@fontsource-variable/inter` 5.3.0 as the source | Body and headings | Subsets `latin` + `cyrillic`, `weights: ["400 700"]`. **Verified**: the Fontsource `latin` subset's unicode-range includes `U+02BB-02BC`, so Uzbek o'/g' render correctly. Cyrillic is needed for Russian. Skip `latin-ext`/`cyrillic-ext` to save bytes. | HIGH |

### Images

| Technology | Version | Purpose | Why | Confidence |
|------------|---------|---------|-----|------------|
| Astro `<Picture />` and `<Image />` (`astro:assets`) | built into Astro 7 | AVIF + WebP + JPEG fallback, responsive `srcset`/`sizes`, intrinsic width/height | Runs **at build time** on local images in `src/assets/`, so output is plain static files on the CDN. No runtime image service, no per-transformation billing and no host lock-in. Set `image.layout: 'constrained'` globally (docs: the `layout` prop auto-generates `srcset` and `sizes`). | HIGH |
| sharp | 0.35.5 (comes with Astro as an optional dependency) | Image encoder | Astro's default image service. With pnpm, install it explicitly. | HIGH |
- Hero image: `loading="eager"` plus `fetchpriority="high"`, AVIF/WebP, max ~1600px wide. It is the LCP element.
- All below-the-fold photos (workshop, products, certificates): lazy by default (Astro default).
- Certificates/licenses: show a thumbnail and open the full-size image on tap (a plain `<a>` to a larger optimized file or CSS `:target` lightbox). Avoid lightbox JS libraries.
- Keep the originals in the repo at a reasonable size (<= 3000px, not raw phone photos at 12 MP) to keep builds fast.
- Logo: SVG, imported directly (Astro supports `import Logo from '../assets/logo.svg'` as a component), inlined, 0 extra requests.

### Hosting / Infrastructure

| Technology | Version | Purpose | Why | Confidence |
|------------|---------|---------|-----|------------|
| **Cloudflare (Workers static assets, or Pages) + Cloudflare DNS** | wrangler 4.148.0 (only if deploying via CLI) | Static hosting, CDN, DNS, free TLS | Cloudflare has a **Tashkent PoP**, and holds ~88% of the CDN market in Uzbekistan, so assets are served from inside the region. The free plan allows commercial use and has no bandwidth charge. Static Astro needs no adapter: deploy `dist/`. Cloudflare says to prefer Workers (static assets) for new projects over Pages. | MEDIUM (Tashkent PoP and market share are from third-party sources. Commercial-use terms were not confirmed on the official pages, so check Cloudflare ToS before launch.) |
| Vercel **Pro** (alternative) | `@astrojs/vercel` 11.0.12 is NOT needed for static | Static hosting if the user wants the Vercel workflow | **Vercel Hobby is non-commercial personal use only** (official Hobby plan page). A business landing page therefore needs Pro ($20/user/month). A static Astro site deploys on Vercel with no adapter. Vercel's regional edge near Uzbekistan was not verified. | HIGH (Hobby restriction), LOW (regional latency) |
| Git (GitHub) + host's Git integration | n/a | CI/CD: push to main, automatic build and deploy, preview URLs | Matches the user's CI/CD habit with zero config. | HIGH |

### Supporting Libraries

| Library | Version | Purpose | When to Use | Confidence |
|---------|---------|---------|-------------|------------|
| `schema-dts` | 2.1.0 (dev dependency, types only) | Type-check hand-written JSON-LD | Use for the `LocalBusiness`/`Organization` JSON-LD object. It adds zero runtime bytes. Build the object in a `.astro` component and emit it as `<script type="application/ld+json" set:html={JSON.stringify(data)} />`. | HIGH |
| Inline SVG icons (hand-copied from Lucide or Tabler) | n/a | Phone, Telegram, map pin, shield, factory icons | Store as `.svg` files in `src/assets/icons/` and import. Zero runtime, zero deps. | HIGH |
| `astro-icon` + `@iconify-json/lucide` | 1.2.0 / 1.2.140 | Icon sets resolved at build time | Optional convenience. `astro-icon` declares no Astro peer range, and its compatibility with Astro 7 was not verified, so test before adopting. Otherwise use inline SVG. | MEDIUM |
| `astro-seo` | 1.2.0 | `<head>` meta/OG helper | **Optional, usually skip.** A 30-line `SEO.astro` component gives the same result with full control over per-locale title, description, canonical, hreflang and OG tags. | MEDIUM |
| `@astrojs/react` | match Astro 7 | React islands | **Not in v1.** Add only if a quote form or calculator arrives (currently out of scope). | HIGH |
- Call: `<a href="tel:+998XXXXXXXXX">` with a full international number. Put the phone in a sticky bottom bar on mobile.
- Telegram: `<a href="https://t.me/USERNAME" rel="noopener">`, optionally with a prefilled `?text=` (per-locale message). Check the exact Telegram username/phone.
- Google Map: the standard Google Maps embed `<iframe loading="lazy" referrerpolicy="no-referrer-when-downgrade" title="...">` at 40.7568, 72.3380, placed near the bottom of the page. Add a visible "Open in Google Maps" link (`https://www.google.com/maps/search/?api=1&query=40.7568,72.3380`) so the contact still works if the iframe is blocked or slow. Optional: a click-to-load facade (static map image, load the iframe on tap) if PageSpeed flags the iframe.
- Structured data: `LocalBusiness` (or a more specific `Organization` + `LocalBusiness` combination) with `name`, `telephone`, `address` (Andijan, UZ), `geo` (40.7568, 72.3380), `foundingDate: 2010`, `areaServed`, `sameAs` (Telegram), `inLanguage`, one JSON-LD block **per language page**.

### Analytics

| Technology | Purpose | Why | Confidence |
|------------|---------|-----|------------|
| **Yandex Metrika** | Primary: visits, goals, session replay and heatmaps | Free. In Uzbekistan it is the most-used analytics tool among sites (24.5% vs Google Analytics 19.4% per BuiltWith). Yandex search has ~21% mobile share in Uzbekistan, so Yandex Webmaster is worth registering as well. Heatmaps/Webvisor show whether visitors tap Call and Telegram. | MEDIUM |
| **Google Analytics 4** (+ Google Search Console) | Secondary: Google is ~75% of mobile search in Uzbekistan | Free. Needed anyway for Search Console, and for Google Ads later. | MEDIUM-HIGH |
| Custom goal events on `tel:` and `t.me` link clicks | Measure the actual success metric (calls, Telegram messages) | Add `data-goal="call"` and `data-goal="telegram"` attributes plus one ~15-line script that fires `ym(ID,'reachGoal',...)` and `gtag('event',...)`. This is the only JS the page should need beyond a mobile menu. | HIGH |

### Development Tools

| Tool | Version | Purpose | Notes |
|------|---------|---------|-------|
| `@astrojs/check` | 0.9.10 | Type-check `.astro` files and dictionaries in CI | Peer is `typescript ^5 \|\| ^6`, so pin TypeScript 6. Run `astro check && astro build` in the build command. |
| Prettier + `prettier-plugin-astro` | 3.9.9 / 0.14.x (check at install) | Formatting | `prettier-plugin-astro` was not version-verified here, so use whatever `npm i -D` resolves. |
| Lighthouse (CLI) or PageSpeed Insights | lighthouse 13.5.0 | Performance budget | Test on **mobile preset with slow 4G throttling**. Targets: LCP < 2.5 s, CLS < 0.1, total JS < 30 KB, total page weight < 500 KB above the fold. |
| pnpm or npm | current | Package manager | pnpm needs `sharp` installed explicitly. npm avoids that. Pick npm for the least friction. |

## Installation

# Scaffold

# Core

# Dev dependencies

## Alternatives Considered

| Recommended | Alternative | When to Use Alternative |
|-------------|-------------|-------------------------|
| Astro 7 | Next.js 16.4 (App Router, `output: 'export'`) | If the site will grow into a real app (auth, dashboard, ordering) and you want one codebase. It always ships a React runtime, which is a real cost on slow mobile networks for a brochure page. |
| Astro 7 | Vite + React 18 SPA | Never for this. Client-rendered HTML hurts SEO and first paint. Requires prerendering workarounds. |
| Astro 7 | Plain HTML/CSS | If nobody will edit the site later. You lose i18n routing and image tooling, so you give up the maintainability. |
| Tailwind 4 | Plain CSS / CSS Modules | If you dislike utilities. Fine at this scale, but you give up the design-token consistency Tailwind gives for free. |
| Built-in Astro i18n + dictionaries | i18next / react-i18next / Paraglide / astro-i18next | Only for 4+ locales, plural rules, or CMS-managed translations. Overkill for 2 languages and ~100 strings. |
| Cloudflare | Vercel Pro | If the client/dev values the Vercel dashboard, previews and workflow and accepts $20/month. |
| Cloudflare | Netlify | No advantage for this case. Same static capability, no regional edge advantage. |
| Cloudflare | Local VPS in Uzbekistan (nginx) | Only if the client requires in-country hosting for legal or latency reasons. You lose the CDN, so it is slower for most visitors, and you take on server maintenance. |
| Yandex Metrika + GA4 | Plausible / Umami / Matomo | If privacy-first is a requirement. They need paid hosting or a subscription and have no heatmaps. |
| Hand-written JSON-LD | Yoast/SEO plugins, CMS | Not applicable. There is no CMS. |
| Static content in files | Headless CMS (Sanity, Decap, Strapi) | v2, if the client wants to edit text and products without a developer. Out of scope per PROJECT.md. |

## What NOT to Use

| Avoid | Why | Use Instead |
|-------|-----|-------------|
| Create React App | Deprecated and unmaintained. Client-rendered, slow first paint. | Astro |
| Vercel Hobby plan for production | Official terms: non-commercial, personal use only. The client is a business. | Cloudflare free, or Vercel Pro |
| Runtime image optimization (Vercel Image Optimization, `next/image` on server) | Billed per transformation, adds latency and host lock-in for a site that has about 15 fixed images. | Build-time `<Picture>` |
| Google Fonts `<link>` (CDN) | Extra DNS + TLS connection on mobile, a render-blocking stylesheet, and a privacy concern. | Astro Fonts API with self-hosted Fontsource |
| `@astrojs/tailwind` integration | Legacy integration for Tailwind 3. Not the current install path. | `@tailwindcss/vite` |
| Tailwind 3 / `tailwind.config.js` | Superseded. v4 is CSS-first. | Tailwind 4 `@theme` |
| TypeScript 7 (for now) | `@astrojs/check` peer range excludes it. | TypeScript `~6.0` |
| Client-side language detection / JS redirect on `/` | Hurts SEO, causes flashes and hides one language from crawlers. | Separate static URLs per language + hreflang + a visible switcher |
| Auto-redirecting by browser `Accept-Language` | Google can't see both versions, and bots get one language. | Same as above. Let users choose. |
| Heavy JS libs: jQuery, Bootstrap JS, sliders (Swiper/Slick), AOS/animation libs, lightbox libs | Large JS and layout shift on slow phones, with no conversion benefit. | CSS (`scroll-snap`, `details/summary`, `:target`, `prefers-reduced-motion`-aware transitions) |
| Google Maps JavaScript API / Leaflet | Needs an API key, billing and a big JS payload. | Plain Maps embed iframe (lazy) + a direct link |
| Carousel hero with auto-play | Hurts LCP and mobile data. | One static hero image |
| Quote form + backend in v1 | Out of scope per PROJECT.md, adds spam, privacy and maintenance. | `tel:` and `t.me` links. Revisit in v2 (Telegram bot or Formspree-type service). |
| Auto-translated (machine) Uzbek copy shipped unreviewed | Technical terms (transformer ratings, kVA, oil-filled, dry-type) must be exact. A bad translation hurts trust. | Native-speaker review of both languages |

## Stack Patterns by Variant

- Add Astro content collections (Markdown/JSON, per-locale) first, then a Git-based CMS (Decap/Keystatic).
- Because it keeps the site static and fast, and avoids a database.
- Prefer a Telegram bot webhook (a Cloudflare Worker or Vercel Function) that forwards submissions to the sales chat, plus `@astrojs/react` only for the form island.
- Because the business already lives in Telegram, and there is no email inbox to manage.
- Register in Yandex Webmaster, verify the domain, and submit the sitemap. Add the Yandex Business (Yandex Maps) listing alongside the Google Business Profile.
- Because Yandex still has ~21% of Uzbek mobile search.
- Add `"en"` to `locales`, create `en.ts`, and add a `/en/` page. No architecture change.
- Because the built-in i18n routing and typed dictionaries are locale-count agnostic.

## Version Compatibility

| Package A | Compatible With | Notes |
|-----------|-----------------|-------|
| astro@7.3.6 | Node >= 22.12.0 | Verified from the npm `engines` field. Node 18/20 dropped since Astro 6. |
| astro@7.3.6 | vite ^8.3.1 | Bundled dependency. Vite 8 is the bundler. |
| @tailwindcss/vite@4.3.3 | vite ^5.2 \|\| ^6 \|\| ^7 \|\| ^8 | Verified peer range, so compatible with Astro 7's Vite 8. |
| @astrojs/check@0.9.10 | typescript ^5 \|\| ^6 | **Excludes TypeScript 7.** Pin `typescript@~6.0`. |
| @astrojs/sitemap@3.7.4 | zod ^4.3.6, sitemap ^9 | Installs its own dependencies. Requires `site` in the config. |
| @astrojs/vercel@11.0.12 | astro ^7.0.0 | Only needed for SSR/functions on Vercel. Not for static output. |
| Astro 7 compiler | Strict HTML | Rust compiler is stricter: close all non-void tags and keep markup valid. `compressHTML` now defaults to `'jsx'` whitespace rules, so check spacing between inline elements (e.g. icon + label). |
| Astro 7 Markdown | Sätteri replaces remark/rehype | Irrelevant for v1, since content is in `.astro` and `.ts` files. Matters only if remark plugins are added later. |

## Sources

- npm registry (`npm view`), 2026-10-07 — astro 7.3.6 (engines, dependencies), tailwindcss/@tailwindcss/vite 4.3.3 (peer deps), typescript 7.0.2 and 6.0.3, @astrojs/check 0.9.10 (peer deps), @astrojs/sitemap 3.7.4, @astrojs/vercel 11.0.12, sharp 0.35.5, schema-dts 2.1.0, astro-seo 1.2.0, astro-icon 1.2.0, @fontsource-variable/inter 5.3.0, wrangler 4.148.0, lighthouse 13.5.0, @vercel/analytics 2.0.1, @vercel/speed-insights 2.0.0, next 16.4.0, react 19.3.0 — HIGH
- https://docs.astro.build/en/guides/internationalization/ — i18n config keys, static file structure, `getRelativeLocaleUrl` — HIGH
- https://docs.astro.build/en/guides/upgrade-to/v7/ — Astro 7: Vite 8, Rust compiler, `compressHTML` default, Sätteri — HIGH
- https://docs.astro.build/en/guides/upgrade-to/v6/ — Node 22.12 floor, stable Fonts API — HIGH
- https://docs.astro.build/en/guides/fonts/ — Fonts API config with latin + cyrillic subsets — HIGH
- https://docs.astro.build/en/guides/images/ — `<Picture>` formats incl. AVIF, `image.layout`, sharp default — HIGH
- https://docs.astro.build/en/guides/integrations-guide/sitemap/ — sitemap `i18n` option, hreflang alternates, `site` required — HIGH
- https://tailwindcss.com/docs/installation/framework-guides/astro — Tailwind 4.3 with `@tailwindcss/vite` — HIGH
- https://vercel.com/docs/plans/hobby — Hobby limited to non-commercial personal use (last updated 2026-09-14) — HIGH
- https://cdn.jsdelivr.net/npm/@fontsource-variable/inter@5.3.0/wght.css — confirmed `U+02BB-02BC` is in the latin subset (Uzbek o'/g') — HIGH
- https://www.cdnplanet.com/geo/uzbekistan-cdn/ and https://gcore.com/blog/excellent-cdn-performance-in-uzbekistan/ (via search) — Cloudflare PoP in Tashkent, ~88% CDN share in Uzbekistan — MEDIUM
- https://gs.statcounter.com/search-engine-host-market-share/mobile/uzbekistan (via search) — Google ~74.9%, Yandex ~21-23% on mobile, June 2026 — MEDIUM
- https://trends.builtwith.com/analytics/country/Uzbekistan (via search) — Yandex Metrika 24.46%, Google Analytics 19.39% among tracked sites — MEDIUM
- https://developers.cloudflare.com/workers/frameworks/framework-guides/astro/ (via search) — Workers static assets for Astro, recommended for new projects — MEDIUM
- Not verified: Cloudflare free-plan commercial-use terms (read ToS), Vercel edge latency in Central Asia, `astro-icon` and `prettier-plugin-astro` compatibility with Astro 7 — LOW

<!-- GSD:stack-end -->

<!-- GSD:conventions-start source:CONVENTIONS.md -->

## Conventions

Conventions not yet established. Will populate as patterns emerge during development.
<!-- GSD:conventions-end -->

<!-- GSD:architecture-start source:ARCHITECTURE.md -->

## Architecture

Architecture not yet mapped. Follow existing patterns found in the codebase.
<!-- GSD:architecture-end -->

<!-- GSD:skills-start source:skills/ -->

## Project Skills

No project skills found. Add skills to any of: `.claude/skills/`, `.agents/skills/`, `.cursor/skills/`, `.github/skills/`, or `.codex/skills/` with a `SKILL.md` index file.
<!-- GSD:skills-end -->

<!-- GSD:workflow-start source:GSD defaults -->

## GSD Workflow Enforcement

Before using Edit, Write, or other file-changing tools, start work through a GSD command so planning artifacts and execution context stay in sync.

Use these entry points:
- `/gsd-fast` for a trivial task inline, with no subagents and no PLAN.md
- `/gsd-quick` for small fixes, doc updates, and ad-hoc tasks
- `/gsd-debug` for investigation and bug fixing
- `/gsd-execute-phase` for planned phase work

Do not make direct repo edits outside a GSD workflow unless the user explicitly asks to bypass it.
<!-- GSD:workflow-end -->

<!-- GSD:profile-start -->

## Developer Profile

> Profile not yet configured. Run `/gsd-profile-user` to generate your developer profile.
> This section is managed by `generate-claude-profile` -- do not edit manually.
<!-- GSD:profile-end -->
