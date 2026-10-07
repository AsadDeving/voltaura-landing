# Architecture Research

**Domain:** Bilingual (Uzbek + Russian) static marketing landing page for a local B2B/B2C transformer business (Voltaura's Transformator, Andijan)
**Researched:** 2026-10-07
**Confidence:** HIGH for structure and SEO mechanics (Astro docs and Google hreflang docs verified). MEDIUM for the exact framework pick, which is a judgment call (see Alternatives).

## Standard Architecture

A bilingual landing page has no runtime backend. It is a **build-time pipeline**: one set of language-neutral section components, fed by one dictionary per language and one language-neutral data file, rendered once per locale into plain HTML under `/uz/` and `/ru/`. The only client-side behavior is progressive enhancement (mobile menu, language-preference memory). Every conversion path (call, Telegram, visit) is a plain link, so it works without JS.

### System Overview

```
┌───────────────────────────────────────────────────────────────────┐
│                     CONTENT LAYER (source of truth)                │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌───────────┐ │
│  │ i18n/uz.json │ │ i18n/ru.json │ │ site.config  │ │ products  │ │
│  │ (all copy)   │ │ (all copy)   │ │ phone, tg,   │ │ .ts (ids, │ │
│  │              │ │              │ │ geo, address │ │ images,   │ │
│  │              │ │              │ │ since, hours │ │ keys only)│ │
│  └──────┬───────┘ └──────┬───────┘ └──────┬───────┘ └─────┬─────┘ │
│         └────────┬───────┘                │               │       │
│            t(locale, key)                 │               │       │
├──────────────────┼────────────────────────┼───────────────┼───────┤
│                  ▼     PRESENTATION LAYER ▼               ▼       │
│  ┌───────────────────────────────────────────────────────────┐   │
│  │ Page template  src/pages/[lang]/index.astro (1 template)   │   │
│  │  <BaseLayout lang>  → <head> SEO block + header + footer   │   │
│  │   Hero · Services · Products · Delivery/Partners ·         │   │
│  │   Trust · Contact(+MapEmbed)   (+ sticky CTA bar mobile)   │   │
│  └───────────────────────────────────────────────────────────┘   │
│  ┌──────────────┐ ┌──────────────┐ ┌─────────────────────────┐   │
│  │ LangSwitcher │ │ CtaButtons   │ │ SeoHead (title, desc,   │   │
│  │ (plain <a>)  │ │ tel:/t.me    │ │ canonical, hreflang,    │   │
│  │              │ │              │ │ OG, JSON-LD)            │   │
│  └──────────────┘ └──────────────┘ └─────────────────────────┘   │
├───────────────────────────────────────────────────────────────────┤
│                       BUILD / ASSET PIPELINE                       │
│  ┌────────────┐ ┌─────────────────┐ ┌──────────┐ ┌────────────┐  │
│  │ Image opt. │ │ Font subsetting │ │ Sitemap  │ │ robots.txt │  │
│  │ AVIF/WebP  │ │ (Latin+Cyrillic)│ │ + hreflang│ │            │  │
│  └────────────┘ └─────────────────┘ └──────────┘ └────────────┘  │
├───────────────────────────────────────────────────────────────────┤
│                         DELIVERY LAYER                             │
│   dist/ (static HTML/CSS/img)  →  static host + CDN + TLS          │
│   custom domain (DNS A/CNAME) · / → /uz/ redirect · 404 page      │
└───────────────────────────────────────────────────────────────────┘
        External (links/embeds only, no server calls from our code):
        tel: · t.me/<handle> · Google Maps iframe · Google/Yandex crawlers
```

### Component Responsibilities

| Component | Responsibility | Typical Implementation |
|-----------|----------------|------------------------|
| Locale routing | Serve each language under its own crawlable URL (`/uz/`, `/ru/`); `/` routes to the default | Static site generator i18n routing with `prefixDefaultLocale: true`; one page template reused per locale |
| Translation dictionaries | Hold every user-visible string per language, identical key set | `src/i18n/uz.json`, `src/i18n/ru.json` + tiny typed `t(locale, key)` helper; a build check fails if key sets differ |
| Site config (language-neutral facts) | One home for phone, Telegram handle, address text, coordinates, founding year, hours, map URL | `src/config/site.ts`. Never duplicated into dictionaries or components |
| Product/service data | Language-neutral structure (ids, image refs, ordering); text resolved by key | `src/data/products.ts` holds `{ id, image, group }`; names/specs live in dictionaries under `products.<id>.*` |
| Section components | One component per page section; take `lang` (or the resolved strings) as props and render | `Hero.astro`, `Services.astro`, `Products.astro`, `DeliveryPartners.astro`, `Trust.astro`, `Contact.astro` |
| CTA components | Render call / Telegram / directions as plain links; one definition used in hero, sticky bar, contact, footer | `CtaButtons.astro` reading `site.ts`; `href="tel:+998..."`, `href="https://t.me/..."` |
| Language switcher | Link to the same page in the other locale; remember preference | Two `<a>` links with `hreflang` and `lang` attributes; optional `localStorage` write on click |
| SEO head | Per-locale `<title>`, description, canonical, hreflang cluster, OG tags, JSON-LD | One `SeoHead` component in `BaseLayout`, fed from dictionaries and `site.ts` |
| Structured data (JSON-LD) | `LocalBusiness` entity (name, tel, address, geo, `foundingDate`, `areaServed`, `sameAs`, `inLanguage`) | Built as a JS object in the component and serialized into `<script type="application/ld+json">`, so facts come from `site.ts` and cannot drift from visible text |
| Map embed | Show location without hurting load speed | Google Maps `<iframe loading="lazy">` (or click-to-load facade) beside a plain "Open in Google Maps" link |
| Asset pipeline | Convert supplied photos/logo/certificates into responsive, small, correctly sized files | Build-time image optimization (`srcset`, AVIF/WebP, explicit width/height); self-hosted subset fonts with `font-display: swap` |
| Sitemap and robots | Enumerate both locale URLs with alternates; allow crawling | Sitemap integration with i18n option (emits `xhtml:link rel="alternate"` per URL); static `robots.txt` pointing to sitemap |
| Deployment | Build, publish `dist/`, attach custom domain, force HTTPS, root redirect | Static host + CDN; CI on push (builds on merge to main) |

## Recommended Project Structure

```
voltaura-site/
├── astro.config.mjs            # site URL, i18n locales, sitemap integration
├── public/
│   ├── robots.txt
│   ├── favicon.*  / og-uz.jpg / og-ru.jpg   # untouched static files
│   └── fonts/                  # self-hosted subset woff2 (Latin + Cyrillic)
├── src/
│   ├── assets/                 # processed by build: logo, workshop photos, certificates
│   ├── config/
│   │   └── site.ts             # phone, telegram, address, geo, since, mapUrl, domain
│   ├── i18n/
│   │   ├── uz.json             # ALL copy, Uzbek (Latin script)
│   │   ├── ru.json             # ALL copy, Russian; same keys as uz.json
│   │   ├── index.ts            # t(), locale list, getAlternates()
│   │   └── check-keys.ts       # build/CI guard: key parity between dictionaries
│   ├── data/
│   │   └── products.ts         # ids, groups, image refs (no prose)
│   ├── layouts/
│   │   └── BaseLayout.astro    # <html lang>, SeoHead, Header, Footer, slot
│   ├── components/
│   │   ├── sections/           # Hero, Services, Products, DeliveryPartners, Trust, Contact
│   │   ├── ui/                 # CtaButtons, LangSwitcher, StickyCtaBar, MapEmbed
│   │   └── seo/                # SeoHead, LocalBusinessJsonLd
│   ├── pages/
│   │   ├── index.astro         # root: redirect/choose language (see Pattern 3)
│   │   ├── [lang]/index.astro  # THE single page template; getStaticPaths -> uz, ru
│   │   └── 404.astro
│   └── styles/                 # tokens (navy/white), global, utilities
└── .github/workflows/          # build + deploy
```

### Structure Rationale

- **One template, two dictionaries:** The Russian and Uzbek pages are the same layout by construction, so they cannot drift structurally. Only strings differ. This is the single biggest maintenance win for a two-language site.
- **`site.ts` separate from dictionaries:** Phone, handle, coordinates and address are facts, not translations. One edit updates the visible text, the `tel:` link, and JSON-LD together.
- **`products.ts` holds structure only:** Adding a product series means one data entry plus two dictionary keys. Prose never lives in data files, so translation stays in one place.
- **`assets/` vs `public/`:** Anything in `src/assets` is optimized at build time. `public/` is for files that must keep exact names and URLs (robots, favicon, OG images).
- **Folders scoped to this project's size:** One page, six sections. No CMS, no content collections, no state library. Those are overkill here (see Anti-Patterns).

## Architectural Patterns

### Pattern 1: Locale-prefixed static routes from a single template

**What:** Every locale gets its own URL prefix (`/uz/`, `/ru/`), generated at build time from one page file via `getStaticPaths`. Each locale prefix is real HTML, so crawlers and link previews see the right language with no JS.
**When to use:** Always for SEO-relevant bilingual sites. Prefer path prefixes over subdomains (one domain, one authority, simpler DNS and certs) and over cookie/JS language swapping (not crawlable).
**Trade-offs:** `/uz/` and `/ru/` are two URLs to maintain in sitemap and hreflang (handled automatically). Needs a decision for what `/` does (Pattern 3).

**Example:**
```typescript
// astro.config.mjs
import { defineConfig } from "astro/config";
import sitemap from "@astrojs/sitemap";

export default defineConfig({
  site: "https://example-voltaura.uz",           // real domain once known
  i18n: {
    locales: ["uz", "ru"],
    defaultLocale: "uz",
    routing: { prefixDefaultLocale: true, redirectToDefaultLocale: true },
  },
  integrations: [
    sitemap({ i18n: { defaultLocale: "uz", locales: { uz: "uz-UZ", ru: "ru-UZ" } } }),
  ],
});

// src/pages/[lang]/index.astro
export function getStaticPaths() {
  return [{ params: { lang: "uz" } }, { params: { lang: "ru" } }];
}
```

### Pattern 2: Flat key dictionaries with a build-time parity check

**What:** Two JSON files with identical keys, grouped by section (`hero.title`, `services.repair.body`, `products.dry.name`). A `t(locale, key)` helper reads them. A check script fails the build if either file is missing a key the other has, or contains an empty string.
**When to use:** Any small static i18n site. A full i18n library (i18next, etc.) is unnecessary because there is no pluralization, runtime switching, or interpolation beyond a couple of numbers.
**Trade-offs:** Hand-rolled but about 20 lines. Missing translations become build errors rather than silent fallbacks to the wrong language, which is the safer failure for a trust-driven page.

**Example:**
```typescript
// src/i18n/index.ts
import uz from "./uz.json";
import ru from "./ru.json";
const dict = { uz, ru } as const;
export type Locale = keyof typeof dict;
export const locales: Locale[] = ["uz", "ru"];
export const t = (lang: Locale, key: string): string =>
  key.split(".").reduce((o: any, k) => o?.[k], dict[lang]) ?? (() => { throw new Error(`Missing ${lang}:${key}`); })();
```

### Pattern 3: Explicit root (`/`) handling with a stable x-default

**What:** The root URL is not a third content variant. Either (a) host-level redirect `/` to `/uz/`, or (b) a tiny static page that links to both languages and uses a small inline script to forward returning visitors to their stored or browser-matched language. In the head of both language pages, the hreflang cluster is `uz`, `ru`, and `x-default` pointing at the default locale page.
**When to use:** Always decide this explicitly. Google requires hreflang annotations to be reciprocal and self-referencing, and says `x-default` is the fallback for users whose language does not match any listed version.
**Trade-offs:** Auto-redirect by `Accept-Language` is fragile (many Uzbek users run Russian-language phones but search in Uzbek, or vice versa) and can confuse crawlers. Prefer a deterministic default plus a visible switcher. Do not hard-redirect crawlers by IP or language.

**Example:**
```html
<!-- in SeoHead, rendered on /uz/ and /ru/ identically -->
<link rel="canonical" href="https://example-voltaura.uz/uz/" />
<link rel="alternate" hreflang="uz" href="https://example-voltaura.uz/uz/" />
<link rel="alternate" hreflang="ru" href="https://example-voltaura.uz/ru/" />
<link rel="alternate" hreflang="x-default" href="https://example-voltaura.uz/uz/" />
```

### Pattern 4: Contact links as plain URLs from one config

**What:** Every CTA is a native link built from `site.ts`: `tel:+998XXXXXXXXX` for call, `https://t.me/<handle>` for Telegram, a `https://maps.google.com/?q=40.7568,72.3380` link for directions. Repeat them in hero, a mobile sticky bar, contact section, and footer.
**When to use:** The success metric is calls, Telegram messages, and visits. Mobile visitors (the majority) need one-tap action with no JS and no form.
**Trade-offs:** No lead capture or server-side logging. Measure with analytics click events on those links (optional, a later phase) rather than a backend.

### Pattern 5: JSON-LD generated from the same data as the visible page

**What:** Build the `LocalBusiness` object in code from `site.ts` plus the locale's dictionary (name, description) and emit it as a per-locale `<script type="application/ld+json">`. Include `name`, `url`, `telephone`, `address` (`PostalAddress`, Andijan, UZ), `geo` (40.7568, 72.3380), `foundingDate` 2010, `areaServed`, `inLanguage`, `image`/`logo`, and `sameAs` (Telegram, Maps).
**When to use:** Always. Hand-pasted JSON-LD goes stale when the phone number or address changes.
**Trade-offs:** Slightly more code than a static snippet, but removes a whole class of data-consistency bugs. Schema fields must match visible page content to be eligible for rich results.

## Data Flow

### Build-time flow (the only "request flow" that matters)

```
 i18n/uz.json ─┐                       ┌─► dist/uz/index.html
 i18n/ru.json ─┼─► t(lang,key) ─┐      │
 config/site.ts ┤                ├─► [lang]/index.astro ─┤
 data/products ─┘  sections ─────┘   (getStaticPaths)    └─► dist/ru/index.html
                                            │
 src/assets/* ──► image optimizer ──────────┼─► dist/_astro/*.avif|webp
 astro.config ──► sitemap integration ──────┴─► dist/sitemap-*.xml (+ hreflang)
```

### Visitor flow (runtime, no server)

```
Search (Google ~75-77% / Yandex ~18-21% on mobile in UZ, per StatCounter)
   │
   ▼
Lands on /uz/ or /ru/  (hreflang steers each language to the right URL)
   │
   ├─ tap tel:       ─► phone dialer
   ├─ tap t.me link  ─► Telegram app
   ├─ tap directions ─► Google Maps app (or map iframe on page)
   └─ tap switcher   ─► same section in other locale (plain link, optional pref stored)
```

### State Management

There is essentially none. The page is stateless HTML. The only client state is the optional remembered language (`localStorage`) and the mobile menu open/closed flag, both handled with a few lines of vanilla JS. No framework runtime, store or hydration is needed.

### Key Data Flows

1. **Copy flow:** author edits `uz.json`/`ru.json` → build resolves keys → static HTML per locale. Single direction, no runtime lookup.
2. **Facts flow:** `site.ts` → CTA links, footer, contact section, JSON-LD (all from one source).
3. **SEO flow:** locale + route list → SeoHead hreflang/canonical + sitemap alternates. Both must agree with each other and with the actual published URLs.
4. **Asset flow:** originals in `src/assets` → build-time optimization → hashed, immutable-cached files in `dist/`.

## Build Order (dependencies between components)

```
1. Project skeleton + i18n routing + BaseLayout   (everything depends on this)
        │
2. Dictionaries + site.ts + t() + parity check    (all sections need strings and facts)
        │
3. Design tokens + core UI (CtaButtons, header, LangSwitcher, footer)
        │
4. Sections in conversion order: Hero → Contact(+Map) → Services → Products → Trust → Delivery/Partners
        │            (Hero + Contact first = a shippable "can they reach us" page)
5. Asset pipeline tuning (real photos, certificates, fonts, performance budget)
        │
6. SEO layer: SeoHead, hreflang, JSON-LD, sitemap, robots, OG images
        │
7. Deployment: CI build, host, custom domain, HTTPS, root redirect, 404
        │
8. Verification: Lighthouse mobile, hreflang validation, Rich Results test, real-device check, Search Console + Yandex Webmaster submit
```

**Ordering rationale for the roadmap:**
- **Routing and i18n infrastructure first.** Retrofitting `/uz` and `/ru` after building a single-language page means touching every component. Build the dual-locale skeleton while the page is nearly empty.
- **Facts and CTAs before sections.** The contact config feeds hero, sticky bar, contact section, and JSON-LD. Defining it once early avoids duplicated phone numbers.
- **Hero + Contact vertically first** delivers the core value (understand + one-tap contact) before the content-heavy sections. Products and Trust depend on content the client has not yet supplied (series/ratings, warranty), so they should come later and use placeholders without blocking.
- **SEO after content stabilizes but before launch.** JSON-LD and hreflang depend on final URLs, the final domain, and final copy, but they are cheap if the data model above is in place.
- **Deployment can start early as a thin slice** (deploy the skeleton to a preview URL in step 1-2) to surface domain/DNS issues early. DNS propagation is the slowest external dependency, so request the domain access from the client early.

**Phase-grouping suggestion:** (1) Foundation: skeleton, i18n, config, layout, CI preview deploy. (2) Core page: hero, contact, map, CTAs, switcher. (3) Content sections: services, products, delivery/partners, trust. (4) Assets and performance. (5) SEO and structured data. (6) Domain cutover and verification.

## Scaling Considerations

| Scale | Architecture Adjustments |
|-------|--------------------------|
| 0-10k visitors/month (expected) | Static files on a CDN. Nothing to scale. Cost is near zero |
| 10k-1M/month | Still static. Only watch image weight and third-party scripts (Maps iframe, analytics) |
| Needs leads, prices, or catalog growth | Add a form endpoint or Telegram bot webhook (serverless function), then consider content collections or a headless CMS. Out of scope for v1 |

### Scaling Priorities

1. **First bottleneck: page weight on mobile networks.** Fix with responsive AVIF/WebP, lazy-loaded below-the-fold images, subset fonts, lazy map.
2. **Second bottleneck: content maintainability.** If the product list grows or non-developers must edit, move dictionaries to a CMS or add content collections. Do not do this until needed.

## Anti-Patterns

### Anti-Pattern 1: Client-side language switching on a single URL

**What people do:** One page, both languages in the DOM or loaded via JS, toggled with a button and cookie.
**Why it's wrong:** Search engines see one language per URL, shared links open in the wrong language, no hreflang is possible, and it shows a flash of the wrong language.
**Do this instead:** Separate crawlable URLs per language (`/uz/`, `/ru/`) and a switcher that is a normal link.

### Anti-Pattern 2: Auto-redirect by browser language or IP

**What people do:** Redirect `/` (or every page) by `Accept-Language`/geo with no escape.
**Why it's wrong:** Mismatches real preference (many bilingual users), breaks crawling and shared links, and traps users.
**Do this instead:** Deterministic default, `x-default` hreflang, visible switcher, optionally a soft suggestion banner or stored preference only after an explicit user choice.

### Anti-Pattern 3: Hard-coding the Uzbek text and "translating later"

**What people do:** Write Uzbek strings inside components, then copy the file and edit it for Russian.
**Why it's wrong:** Two diverging templates, missing sections in one language, and double bugfix work.
**Do this instead:** Strings only in dictionaries from the first commit; components reference keys; parity check in CI.

### Anti-Pattern 4: Facts duplicated across components and JSON-LD

**What people do:** Phone number typed in hero, footer, contact, and a pasted JSON-LD block.
**Why it's wrong:** One missed edit leaves a wrong number on the page or in search results. NAP inconsistency also hurts local SEO.
**Do this instead:** `site.ts` is the only place; everything else imports it.

### Anti-Pattern 5: Heavy framework and third-party weight for a one-page site

**What people do:** SPA runtime, UI kit, i18n library, chat widgets, large map SDK, unoptimized original photos.
**Why it's wrong:** Slow first load on mobile data, which is the main audience, and it lowers conversions.
**Do this instead:** Zero-JS-by-default static HTML, lazy-loaded map iframe, optimized images, a hard Lighthouse mobile budget.

### Anti-Pattern 6: Translating but not localizing SEO metadata

**What people do:** Translate the body but leave title, meta description, OG tags, image alt text, and JSON-LD in one language.
**Why it's wrong:** Search snippets and link previews (Telegram previews are a key channel here) show the wrong language.
**Do this instead:** Every `SeoHead` field comes from the locale dictionary; separate OG images per language if they contain text.

## Integration Points

### External Services

| Service | Integration Pattern | Notes |
|---------|---------------------|-------|
| Phone dialer | `tel:+998...` link | Use full international format. Hide nothing behind JS |
| Telegram | `https://t.me/<handle>` link (optionally a prefilled `?text=` hint in each language) | Link previews use OG tags, so provide per-locale OG title, description, image |
| Google Maps | `<iframe loading="lazy">` embed from the share/embed dialog, plus a plain directions link | Embed adds third-party weight and cookies. Consider click-to-load facade. Coordinates 40.7568, 72.3380 from PROJECT.md |
| Google Search Console | Verify domain (DNS TXT), submit sitemap | Primary search engine in UZ (~75% of searches) |
| Yandex Webmaster | Verify site, submit sitemap | Yandex.uz is a meaningful secondary share (~18-21%), higher among Russian-speaking users |
| Analytics (optional) | Lightweight, cookieless script, or click events on CTAs | Needed to measure the stated success metric (calls and Telegram taps). Defer to a late phase |
| Hosting + DNS | Static host with CDN, custom domain, auto-TLS, HTTPS redirect | Client owns the domain. Get registrar access early; DNS is the slowest external step |

### Internal Boundaries

| Boundary | Communication | Notes |
|----------|---------------|-------|
| Dictionaries ↔ components | Build-time `t(lang,key)` calls | Components never import a dictionary directly except through `t()` |
| `site.ts` ↔ CtaButtons / Contact / JSON-LD | Direct import | Single source of truth for contact facts |
| `[lang]/index.astro` ↔ sections | Props (`lang`), no cross-section state | Sections stay independent and reorderable |
| SeoHead ↔ astro i18n config | Shared `locales` list | Hreflang, sitemap, and router must derive from the same list |
| Build ↔ host | Static `dist/` artifact | Host config only for HTTPS, root redirect, 404, cache headers |

## Alternatives Considered

| Decision | Recommended | Alternative | Why not (for this project) |
|----------|-------------|-------------|---------------------------|
| Framework | Astro (static output, built-in i18n routing, image optimization, official sitemap with i18n) | Next.js static export | Heavier runtime, more config, no benefit for a single static page |
| Framework | Astro | Plain HTML + two hand-copied folders | Works for a tiny page, but duplicates structure, so drift between languages is likely. Acceptable only if the client wants zero tooling |
| Framework | Astro | Vite + React + react-i18next | Client-rendered routing hurts SEO and mobile load time. The user knows React, but nothing here needs it. Astro can embed React islands later if needed |
| i18n | Own dictionaries + `t()` | i18next or similar libraries | Overkill: no runtime switching, plurals, or namespaces needed |
| Language URLs | Path prefix (`/uz/`, `/ru/`) | Subdomains | Splits authority, more DNS/cert work, no benefit for two languages |
| Content | JSON/TS files in repo | Headless CMS | Client edits are rare, an admin panel is out of scope. Revisit if content changes often |

## Open Questions and Flags for Planning

- **Uzbek script:** Assume Latin script (the current standard used in most business and web contexts in Uzbekistan). Confirm with the client. Cyrillic Uzbek would be a third variant and is out of scope. Use `lang="uz"` in HTML and `hreflang="uz"`. Verify the chosen font covers Uzbek Latin letters with modifiers (oʻ, gʻ) and Russian Cyrillic.
- **Default locale and `/`:** Recommended `uz` as default with `x-default` pointing to `/uz/`. Confirm with the client whether Russian should be the default, since some industrial and CIS-partner audiences skew Russian.
- **Domain:** Not yet known. `site` in config, canonical, hreflang, sitemap, and JSON-LD all depend on it. Use an environment variable so the preview URL and final domain both work.
- **Certificates and photos:** Content and format (PDF vs image) unknown. Plan an asset-processing step and a lightbox only if needed. Certificates as images can reuse the image pipeline.
- **Hosting choice:** Any static host works. Pick one where the client can retain ownership of the account and DNS. Confirm Uzbekistan edge performance is acceptable (test from the target region).
- **Cookie and consent:** Google Maps iframe and analytics may need a notice depending on client preference. Low priority for local audience but decide before adding analytics.

## Sources

- Astro i18n routing guide (locales, defaultLocale, prefixDefaultLocale, redirectToDefaultLocale, fallback, `getRelativeLocaleUrl`): https://docs.astro.build/en/guides/internationalization/ (HIGH, official docs)
- Astro sitemap integration, i18n option generates `xhtml:link rel="alternate"` entries per locale: https://docs.astro.build/en/guides/integrations-guide/sitemap/ (HIGH, official docs, confirmed via search results)
- Google Search Central, localized versions and hreflang (reciprocal links, self-reference, `x-default`, HTML/sitemap/HTTP header methods): https://developers.google.com/search/docs/specialty/international/localized-versions (HIGH, official docs)
- StatCounter, search engine market share in Uzbekistan, mobile (Google ~75%, Yandex.uz ~21%): https://gs.statcounter.com/search-engine-host-market-share/mobile/uzbekistan (MEDIUM, third-party analytics)
- Project context: C:/Users/user/Desktop/business/.planning/PROJECT.md
- Patterns for Pattern 2, 4, 5 and Anti-Patterns reflect established static-site practice (author synthesis, MEDIUM)

---
*Architecture research for: bilingual (uz/ru) static marketing landing page*
*Researched: 2026-10-07*
