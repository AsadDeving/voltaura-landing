# Phase 1: Trilingual Core Page & Contact - Context

**Gathered:** 2026-10-07
**Status:** Ready for planning

<domain>
## Phase Boundary

A visitor can read the landing page in Uzbek Latin, Uzbek Cyrillic or Russian, understand what Voltaura does from the hero, and reach the business in one tap (call, Telegram, map). Includes the language routing, translation checks, one facts data file, hero, sticky mobile CTA bar, contact/address/map section and the image pipeline. Content sections (services, products, trust, FAQ), SEO/analytics and launch are later phases.

</domain>

<decisions>
## Implementation Decisions

### URL scheme and language codes
- **D-01:** Uzbek Latin is served at `/`, Uzbek Cyrillic at `/uz-cyrl/`, Russian at `/ru/`. No page lives at two URLs, so no duplicate-content canonical juggling for the root. — **Reversibility:** costly — changing URLs after launch needs redirects and re-indexing in Google/Yandex.
- **D-02:** `/` never redirects by browser language or IP (matches FOUND-05). Each page links to the other two variants with a plain link.
- **D-03:** Language codes declared on pages and in hreflang: `uz-Latn`, `uz-Cyrl`, `ru`, plus `x-default` pointing at `/`. — **Reversibility:** costly — hreflang values are indexed by search engines.

### Hero and visual style
- **D-04:** Hero uses a real photo background (transformer or workshop, supplied by the client) under a navy overlay, with the headline and call/Telegram buttons on top. Hero image is eager-loaded, high priority, modern format, correctly sized.
- **D-05:** Directly under the hero, a stats row with three facts: "Since 2010" (year computed from 2010, never hard-coded as an age), "Delivery across Uzbekistan", "Partners in 5 countries". Repair, production and sale are stated in the headline/subhead.
- **D-06:** Palette is navy and white with one accent color (electric blue or amber, chosen by contrast check against the logo) used only for the main call button and key highlights. Logo sits in the header and is inlined SVG.

### Claude's Discretion
- Language switcher design and placement (plain links, keeps the section anchor; mobile layout).
- Sticky mobile call/Telegram bar styling and desktop behavior.
- Map section layout beneath address, landmark and hours.
- Font choice, as long as it covers Latin Uzbek (oʻ, gʻ) and Cyrillic including Uzbek letters (қ, ғ, ҳ, ў); a glyph test is required before committing.
- Preview deploy as an early plan task.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Project
- `.planning/PROJECT.md` — scope, constraints, trilingual decision
- `.planning/REQUIREMENTS.md` — FOUND-01..06, CORE-01..07, PERF-02
- `.planning/ROADMAP.md` — Phase 1 goal and success criteria
- `.planning/STATE.md` — flagged risks (Cyrillic glyph gap, content gating)

### Research
- `.planning/research/SUMMARY.md` — overall findings and conflicts resolved
- `.planning/research/STACK.md` — Astro 7, Tailwind 4, fonts, images
- `.planning/research/ARCHITECTURE.md` — build pipeline, components, i18n structure, SEO mechanics
- `.planning/research/PITFALLS.md` — Uzbek typography, CTA correctness, map and image weight

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- None — greenfield project, no source code yet.

### Established Patterns
- None yet. Phase 1 establishes them: one page template, per-language dictionaries, one site facts file.

### Integration Points
- The site facts file (phone, Telegram, address, coordinates, hours, founding year) will feed CTAs, footer, contact section and later the Phase 3 structured data.

</code_context>

<specifics>
## Specific Ideas

- The stats row repeats delivery and partner facts that Phase 2 expands on; keep them as short summary facts here.
- Research suggested `prefixDefaultLocale: true`; the user chose Uzbek Latin at the root instead (D-01), so configure Astro with the default locale unprefixed.

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope. Language switcher and sticky-bar details were not discussed and are left to Claude's discretion above.

</deferred>

---

*Phase: 1-Trilingual Core Page & Contact*
*Context gathered: 2026-10-07*
