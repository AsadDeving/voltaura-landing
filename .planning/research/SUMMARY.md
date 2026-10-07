# Project Research Summary

**Project:** Voltaura's Transformator landing page (Andijan, Uzbekistan)
**Domain:** Bilingual (Uzbek + Russian) mobile-first B2B industrial landing page (transformer repair, manufacture, sale)
**Researched:** 2026-10-07
**Confidence:** MEDIUM-HIGH (stack and architecture HIGH, features and pitfalls MEDIUM)

## Executive Summary

This is a single-page, static, bilingual brochure site. The whole conversion funnel is three plain links: call (`tel:`), Telegram (`t.me`) and visit (map). There is no backend, form or app state. Experts build this as a build-time pipeline: one page template, one dictionary per language and one language-neutral facts file, all rendered to plain HTML at `/uz/` and `/ru/` with near-zero client JS. The audience is mostly on mobile and slow networks, so page weight and LCP matter more than any feature.

The recommended approach is Astro 7 (static output) with Tailwind CSS 4, Astro's built-in i18n routing, hand-written typed dictionaries, build-time AVIF/WebP images and self-hosted Inter (latin + cyrillic). Inter is verified to cover the Uzbek oʻ/gʻ modifier letters. React is deliberately not used in v1; it stays available as an Astro island if a quote form is added later. Analytics is Yandex Metrika plus GA4 with click goals on the call and Telegram links. The product angle is "repair, production and sale under one roof", backed by real certificates and real workshop photos.

The main risks are about content and links, not code:
- **Placeholder or invented claims** (phone, kVA ranges, warranty, clients) are the biggest risk. The client has not yet supplied ratings, warranty or client names.
- **Uzbek typography and unreviewed translation** (wrong apostrophes, missing glyphs, machine translation).
- **Broken CTA links** (non-E.164 `tel:`, phone-based Telegram links).
- **Heavy images and map iframe.**
- **Google-only local SEO.**
- **Domain and DNS ownership.**

Mitigations are mostly structural: a content lint in the build, key-parity checks on dictionaries, a single facts file, an image pipeline and a launch checklist.

## Key Findings

### Recommended Stack

Static output with no host adapter, so the site deploys unchanged on Cloudflare, Vercel, Netlify or nginx. Detail in STACK.md.

**Core technologies:**
- Astro 7.3.x (Node >= 22.12): SSG, i18n routing, image pipeline, Fonts API. Zero JS by default gives the best mobile LCP.
- Tailwind CSS 4.3 via `@tailwindcss/vite`: CSS-first `@theme` for the navy/white palette. Do not use `@astrojs/tailwind` or a v3 config.
- TypeScript pinned to `~6.0`: `@astrojs/check` does not accept TS 7.
- Built-in i18n plus typed `uz.ts`/`ru.ts` dictionaries (about 20-line `t()`): no i18n library needed for 2 languages and about 100 strings.
- `@astrojs/sitemap` with the `i18n` option: emits hreflang alternates.
- Astro `<Picture>` plus sharp: build-time AVIF/WebP, no runtime image billing.
- Astro Fonts API with Fontsource Inter (latin + cyrillic): self-hosted, no Google Fonts request.
- Cloudflare free (Tashkent PoP) for hosting and DNS, or Vercel Pro ($20/mo). Vercel Hobby is non-commercial only and must not be used.
- Yandex Metrika plus GA4, loaded after first paint, with click goals on `tel:` and `t.me`.
- `schema-dts` (dev only) for typed `LocalBusiness` JSON-LD.

### Expected Features

Detail in FEATURES.md.

**Must have (table stakes):**
- Hero with value prop (repair, production, sale), "since 2010" and Andijan.
- Sticky mobile CTA bar (call plus Telegram) with CTAs repeated per section.
- Services section, and product range by type with spec ranges (real client data only).
- Trust block: real certificates and real workshop and product photos.
- Contact section: address with landmark, hours, lazy map, Google/Yandex/2GIS links.
- Delivery across Uzbekistan and CIS partner countries.
- UZ/RU switcher on separate URLs with hreflang.
- Mobile performance, HTTPS, custom domain, SEO basics with `LocalBusiness` JSON-LD.
- Repair process timeline, short FAQ, CTA click tracking.

**Should have (competitive):**
- "Repair + production + sale under one roof" framing.
- Warranty and testing statement, client and partner logos, case snippets (blocked on client content).
- Audience entry points, "Not sure which transformer?" Telegram-prefill help block, custom-winding callout.
- PDF catalog per language, short workshop video, WhatsApp/Viber link.

**Defer (v2+):** quote form or Telegram bot, English version, per-series catalog pages, blog, CMS.
**Never build:** cart or public prices, chatbot, calculator, autoplay video, popups, fake trust elements.

### Architecture Approach

A build-time pipeline. The content layer (dictionaries, `site.ts` facts, `products.ts` structure) feeds one `[lang]/index.astro` template made of independent section components. It renders to static HTML per locale and is served from a CDN. Detail in ARCHITECTURE.md.

**Major components:**
1. Locale routing plus single page template: crawlable `/uz/` and `/ru/` via `getStaticPaths`.
2. Dictionaries with `t()` and a CI key-parity check: missing translations fail the build.
3. `site.ts` facts file: phone, Telegram, address, geo, hours and founding year in one place, feeding CTAs, footer, contact and JSON-LD.
4. Section and CTA components: Hero, Services, Products, Trust, DeliveryPartners, Contact, StickyCtaBar, LangSwitcher, MapEmbed.
5. SeoHead plus JSON-LD generator: per-locale title, meta, canonical, hreflang, OG and `LocalBusiness`, from the same data as the visible page.
6. Asset pipeline and deployment: images, font subsetting, sitemap, robots, CI deploy, root redirect, 404.

**Conflict between research files, resolved here:** STACK.md recommends `prefixDefaultLocale: false` (Uzbek at `/`, Russian at `/ru/`). ARCHITECTURE.md and FEATURES.md assume `/uz/` and `/ru/` with x-default pointing at `/uz/`. Both are valid. **Recommendation: `prefixDefaultLocale: true`.** It is symmetrical and makes the root an explicit decision. It needs a static `/` that links or redirects to `/uz/`, with no Accept-Language or IP redirects. Confirm the default language with the client.

### Critical Pitfalls

The full list of 22 is in PITFALLS.md. Top five:
1. **Placeholder or invented content ships.** Keep a `CONTENT-REQUIRED.md` checklist. Add a build lint that fails on empty, TODO, XXX or lorem values and on missing keys. Omit any claim the client has not confirmed.
2. **Uzbek typography and translation quality.** Latin script only for v1. Normalize oʻ/gʻ to U+02BB with a lint. Glyph-test the font before committing. Get native review of both languages. Test real strings at 320 px.
3. **Language as a JS toggle or auto-redirect.** Use separate static URLs, a plain `<a>` switcher that keeps the section anchor, reciprocal hreflang with x-default, and no IP or Accept-Language redirects.
4. **Broken CTAs.** `tel:+998XXXXXXXXX` (E.164) with separate display text. `https://t.me/<username>`, never phone-based, with optional prefilled text. Sticky bar. Test on real devices including the Telegram in-app browser.
5. **Weight and third-party cost.** Build-time AVIF/WebP, strip EXIF. Hero eager with `fetchpriority="high"`. Lazy or click-to-load map plus plain Google/Yandex/2GIS links. Defer analytics. Target LCP < 2.5 s on throttled mobile.

Also important:
- Local SEO for Google and Yandex with one NAP record. Start Google Business Profile, Yandex Business and 2GIS early (days to weeks).
- Register the domain in the client's name.

## Implications for Roadmap

Content gathering runs in parallel from day one, because it is the schedule bottleneck.

### Phase 1: Foundation and i18n skeleton
**Rationale:** Retrofitting locale routing later touches every component. Deploy a preview URL right away to surface host and DNS issues.
**Delivers:** Astro 7 and Tailwind 4 project; `/uz/` and `/ru/` routing and root `/` handling; BaseLayout, dictionaries with `t()`, key-parity check and content lint, `site.ts`; design tokens (navy/white, contrast checked); font with verified glyph sheet; image pipeline and performance budget; CI preview deploy.
**Addresses:** language switcher infrastructure, mobile-first foundation, HTTPS.
**Avoids:** pitfalls 1, 2, 3, 6, 12, 13, 15.

### Phase 2: Core conversion page (Hero, CTAs, Contact, Map)
**Rationale:** Hero plus Contact is a shippable "understand us and reach us in one tap" page.
**Delivers:** Hero, CtaButtons, StickyCtaBar, LangSwitcher (keeps anchors); Contact section with address, landmark, hours, lazy or facade map, Google/Yandex/2GIS links; Footer; CTA click-goal hooks (`data-goal`).
**Avoids:** pitfalls 5, 7, 14, 16.

### Phase 3: Content sections and translation
**Rationale:** Depends on client-supplied content. Use placeholders that the lint blocks from shipping.
**Delivers:** Services, repair process timeline, Products by type with spec ranges; Trust block (certificates, workshop photos), Delivery/Partners, FAQ; native-reviewed UZ and RU copy with a glossary; optional if content arrives: warranty, testing, client logos.
**Avoids:** pitfalls 1, 4, 10, 17.

### Phase 4: SEO, structured data, listings and analytics
**Rationale:** Needs final URLs, domain and copy, but is cheap given the data model. Start business listings earlier in parallel.
**Delivers:** SeoHead with per-locale title, meta, canonical, hreflang and x-default; per-language OG images, `LocalBusiness` JSON-LD, FAQ schema, sitemap, robots; Search Console and Yandex Webmaster; Yandex Metrika and GA4 with click goals; Google Business Profile, Yandex Business and 2GIS.
**Avoids:** pitfalls 8, 11, 19, 20.

### Phase 5: Performance, accessibility and device verification
**Rationale:** Measure against the budget with real content, because photos change page weight.
**Delivers:** Lighthouse mobile on slow 4G (LCP < 2.5 s, CLS < 0.1, JS < 30 KB); accessibility pass and 320 px checks in both languages; real low-end Android and Telegram in-app tests; EXIF check.
**Avoids:** pitfalls 6, 14, 16.

### Phase 6: Domain cutover and launch
**Rationale:** DNS is the slowest external dependency. Ask the ownership question at project start, but cut over last.
**Delivers:** Custom domain in the client's name, with http/https and www/apex canonicalization; bilingual 404 and favicon set; hosting decision recorded; full "Looks Done But Isn't" checklist; OG share test in both languages; client sign-off on every fact.
**Avoids:** pitfalls 9, 19, 20, 21.

### Phase Ordering Rationale
- Dependencies drive the order: routing and i18n, then facts and CTAs, then sections, then SEO (needs final URLs), then verification, then cutover.
- Content gathering must start immediately and in parallel (certificates, ratings, warranty, hours, Telegram username, domain access, partner permissions). Phases 3 and 4 are blocked on it.
- Tracking hooks go in with the CTAs in Phase 2, because they are awkward to retrofit.
- Scope stays at call, Telegram and visit. A form, chatbot and CMS are out of v1 (pitfall 18, data-law exposure).

### Research Flags
Needs research (`/gsd-plan-phase --research-phase <N>`):
- **Phase 4:** Yandex, 2GIS and Google Business Profile verification flow in Uzbekistan; real search phrasing in both languages (Andijon/Андижан).
- **Phase 6:** `.uz` vs `.com` registration, registrar DNS flexibility, Cloudflare free-plan commercial terms, host latency from Uzbekistan, any existing domain contract.
- **Phase 1 (small spike):** Inter glyph test for U+02BB/U+02BC; `astro-icon` and `prettier-plugin-astro` compatibility with Astro 7, or just use inline SVG.

Standard patterns (skip research): Phase 2 (plain links, sticky bar, map facade), Phase 3 (content work; the risk is client content), Phase 5 (standard Lighthouse and device testing).

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | Versions verified against npm and official docs. Hosting and analytics are MEDIUM (regional data from third-party sources, Cloudflare commercial terms unverified). |
| Features | MEDIUM | General B2B patterns are solid. Uzbek supplier conventions rest on thin evidence (marketplace listings only). "Competitors only sell" is an inference. |
| Architecture | HIGH | Astro and Google hreflang docs verified. The one judgment call is the `/` and prefix strategy. |
| Pitfalls | MEDIUM | Mostly established web-platform behavior, from only 3 searches. Several [VERIFY] items remain. |

**Overall confidence:** MEDIUM-HIGH

### Gaps to Address
- **Client content not supplied** (series and kVA/kV ranges, warranty, certificates, client list, hours, phone, Telegram username). Create `CONTENT-REQUIRED.md` at project start, omit unconfirmed claims, let the lint block placeholders.
- **Default language and root behavior.** Confirm with the client (Uzbek-first assumed). Decide `prefixDefaultLocale` (recommended `true`).
- **Uzbek script.** Latin assumed; confirm with the client. Cyrillic Uzbek is out of scope.
- **Hosting.** Cloudflare free (recommended) vs Vercel Pro. Static output keeps both open. Check Cloudflare ToS and test latency from Uzbekistan.
- **Domain.** Not yet known or confirmed as client-owned. Canonicals, hreflang, sitemap and JSON-LD depend on it, so use an env variable for `site`.
- **Map pin.** Coordinates 40.7568, 72.3380 are unverified on the ground. Confirm with the client on a phone.
- **Native reviewer.** None identified. Translation quality is the biggest trust risk without one.
- **Legal and consent.** Low risk with no form or backend. Confirm whether analytics needs a notice. Revisit if a form is ever added.

## Sources

### Primary (HIGH)
- npm registry (`npm view`, 2026-10-07): astro, tailwindcss, @tailwindcss/vite, typescript, @astrojs/check, @astrojs/sitemap, sharp, schema-dts, @fontsource-variable/inter and others
- Astro docs: i18n, upgrade guides, fonts, images, sitemap integration
- Tailwind CSS Astro install guide
- Vercel Hobby plan terms (non-commercial only)
- Fontsource Inter CSS (U+02BB-02BC in latin subset)
- Google Search Central: localized versions and hreflang

### Secondary (MEDIUM)
- StatCounter Uzbekistan: Google about 75-77%, Yandex about 18-23% on mobile
- BuiltWith Uzbekistan: Yandex Metrika 24.5% vs Google Analytics 19.4%
- CDN Planet and Gcore: Cloudflare Tashkent PoP, about 88% CDN share
- Central Asia Barometer and vc.ru: Telegram reach about 70-76%, mobile preference about 82%
- prom.uz and goldenpages.uz listings: competitor baseline (TMG, 25-3150 kVA, 2-year warranty)
- B2B trust-signal articles; Wikipedia on U+02BB
- Legal500 and Kun.uz on the Uzbek personal data law (ZRU-547) and 2026 amendments
- Cloudflare Workers Astro guide

### Tertiary (LOW, needs validation)
- Cloudflare free-plan commercial-use terms
- Vercel edge latency in Central Asia
- `astro-icon` and `prettier-plugin-astro` compatibility with Astro 7
- Google Business Profile verification, `.uz` registration process, current Yandex and 2GIS shares ([VERIFY] items in PITFALLS.md)

---
*Research completed: 2026-10-07*
*Ready for roadmap: yes*
