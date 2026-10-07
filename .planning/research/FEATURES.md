# Feature Research

**Domain:** B2B industrial supplier landing page (transformer repair / manufacture / sale), Uzbekistan + CIS
**Researched:** 2026-10-07
**Confidence:** MEDIUM. General B2B and industrial-site patterns are well documented. Uzbek transformer-supplier conventions come from thin search evidence plus domain reasoning. Confidence is flagged per section.

## Context Constraints (from PROJECT.md)

Single-page, bilingual (UZ + RU), static-friendly, no backend. Conversion goals are call, Telegram and workshop visit. No quote form, no e-commerce, no admin panel. Audience is mixed: factories, utilities/builders, farms and small business. Not yet provided: product series/ratings, warranty terms, client list.

## Evidence Highlights

- Telegram is the dominant messenger in Uzbekistan (about 70% prefer it; a vc.ru digest cites 76% reach). Urban and younger users skew higher, so rural farm owners may still prefer a phone call. Both CTAs are therefore needed. (MEDIUM, secondary sources: ca-barometer.org, vc.ru)
- Mobile is the main access device (about 82% prefer mobile internet). (MEDIUM)
- Search share in Uzbekistan: Google about 77%, Yandex about 18% (about 21% on mobile). Yandex Maps and 2GIS matter for local discovery, so listing there is worth doing alongside Google Maps. (MEDIUM, StatCounter via search)
- Competing local suppliers (for example UZTRANSFORMATOR on prom.uz, listings on goldenpages.uz) present the same core facts. These are: TMG series, 25 to 3150 kVA, 6/10/35 kV, a 2-year warranty and "certified". A bare "we sell transformers" page is therefore the baseline, not a differentiator. (MEDIUM)
- B2B best practice: trust signals sit next to CTAs, case studies and named clients are the strongest proof, and mobile speed plus HTTPS are baseline. (MEDIUM, general B2B sources)

## Feature Landscape

### Table Stakes (Users Expect These)

Missing any of these and a factory buyer or utility procurement person leaves, or assumes the business is unreliable.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| Hero with one-line value prop (repair, produce, sell transformers), "since 2010", Andijan | Visitor must understand the business in 5 seconds | LOW | Must state all three activities. Use plain wording in both languages, not translated slogans. |
| Sticky click-to-call button (`tel:`) | Phone is the primary B2B channel in the region, and mobile users want one tap | LOW | Visible on mobile at all scroll positions. Use the full international format `+998...`. |
| Telegram CTA (`t.me/...` deep link) | Dominant messenger in Uzbekistan. It replaces the contact form. | LOW | Optionally prefill text through `?text=` (for example "Salom, transformator haqida..."). Place it beside the call button, never alone. |
| Services section: repair, production, sale | Core of the offer. Buyers search by need ("remont transformatora"). | LOW | Three cards. State what repair covers (rewinding, oil change, diagnostics, testing). Confirm the actual repair scope with the client. |
| Product range overview by type: oil-filled power/distribution, dry-type, small/specialty (welding, isolation, step-down, custom winding) | Buyers need to see their category quickly | MEDIUM | Needs real data from the client (series, kVA range, voltage class). Without it the section looks generic. |
| Key spec ranges per product type (kVA range, voltage classes, cooling or oil type) | Engineers and procurement scan for "do you make my size?". Competitors list them. | MEDIUM | Blocked on the client supplying ratings. Show ranges, not a full catalog. |
| Trust block: certificates, licenses (scan or photo thumbnails with lightbox), "since 2010" | Industrial buyers fear counterfeit or unsafe equipment | LOW | Real scans, not stock badges. Confirm which certificates may be shown publicly. |
| Real photos: workshop, production process, finished units | Proves the business is real and physical. Stock images destroy trust in this niche. | LOW | Compress and lazy-load. Uses supplied photos. |
| Contact section: address, working hours, phone, Telegram, embedded map | Workshop visit is a stated CTA. Visitors need to find the place. | LOW | Google Map embed (coords 40.7568, 72.3380). Add "open in Yandex Maps / 2GIS" links, since Yandex has about 20% of mobile search. Lazy-load the iframe for speed. |
| Language switcher UZ / RU (persistent, remembered) | Two audiences. CIS partners read Russian, local buyers use both. | MEDIUM | Separate URLs per language (`/uz/`, `/ru/`) with `hreflang`. Do not rely on a JS-only toggle, which hurts SEO. Uzbek should use Latin script. |
| Mobile-first responsive layout, fast load | Most users are on mobile, often on mid-range devices and variable networks | MEDIUM | Image optimization, minimal JS, system or subset fonts with Cyrillic and Uzbek Latin glyphs (Oʻ, Gʻ). |
| HTTPS on custom domain | Browser warnings kill trust | LOW | Standard with static hosting. |
| SEO basics: titles, meta, `LocalBusiness` JSON-LD, per-language tags, sitemap | Buyers find suppliers through Google (about 77%) and Yandex | LOW-MEDIUM | Target both languages. Terms to cover: "ремонт трансформаторов Андижан", "transformator ta'mirlash". |
| Delivery section: delivery across Uzbekistan | Out-of-region buyers ask whether you deliver | LOW | Be specific about regions. Do not claim delivery terms the client has not confirmed. |
| Footer with full contact, legal name, hours | Legitimacy signal | LOW | Include company legal name and TIN (INN) if the client agrees. |

### Differentiators (Competitive Advantage)

Align with the Core Value: instantly understood, trusted, one-tap contact. Local competitors mostly present a bare listing. Anything that proves capability or lowers perceived risk stands out.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| "Repair + production + sale under one roof" framing | Most competitors only sell. Repair plus manufacture means one supplier for the whole transformer lifecycle. | LOW | Messaging and layout only. This is the strongest unique angle. |
| Repair process timeline (receive, diagnose, rewind or refurbish, test, return) | Shows competence and makes a risky service feel predictable | LOW-MEDIUM | 4 to 6 icon steps. Add turnaround times only if confirmed. |
| Testing and quality control section (factory tests, protocol issued, standards followed) | Utilities and factories need test protocols. It signals engineering seriousness. | LOW | Content depends on what the client actually does. Do not invent claims. |
| Warranty statement (term, what it covers) | Competitors advertise 2 years. A clear warranty lowers risk. | LOW | Blocked on client input. If none is provided, omit rather than invent. |
| Workshop photo or short video gallery, optionally a 30 to 60 s walkthrough | Real production visuals are a strong trust signal for visit-based sales | LOW-MEDIUM | Self-host a compressed clip or link to YouTube/Telegram. Use a poster image and no autoplay, for bandwidth. |
| Client and partner logos or country flags (UZ, CN, RU, KZ, TJ, KG) | Social proof and CIS credibility. Named clients are the strongest proof. | LOW-MEDIUM | Needs client permission. Fall back to "partners from" countries if no logos. Blocked on client list. |
| Project / case snippets (for example "replaced 630 kVA unit for X farm in Y region") | Case studies are the highest-value B2B proof. It shows similar customers were served. | MEDIUM | 3 short cards. Requires real content. Skip if not available. |
| Audience entry points ("For factories", "For builders and utilities", "For farms and small business") | Mixed audience with equal weight. Segmenting helps each see themselves. | LOW | Anchor links or tabs, not separate pages. |
| "Not sure which transformer you need?" help block with Telegram prefill and a short selection hint (load kVA guidance) | Replaces the quote form and helps non-experts such as farmers | LOW | A Telegram deep link with prefilled message plus a short checklist (load, voltage, phase). No calculator needed. |
| Custom winding / non-standard order callout | Few suppliers advertise custom work. It attracts specialist leads. | LOW | A one-block highlight within specialty products. |
| Downloadable PDF catalog or price list | Procurement departments forward documents internally | LOW-MEDIUM | Static file per language. Needs client-provided data. A good v1.x item. |
| Click-tracking on CTAs (call, Telegram, map) | The success metric is inbound contact, and it needs to be measurable | LOW-MEDIUM | Privacy-light analytics (for example Plausible, Umami) or GA4/Yandex Metrica events. Yandex Metrica is common locally. |
| WhatsApp or Viber secondary contact | Some buyers, especially in Russia and Kazakhstan, still use them | LOW | Optional. Adds a link only and has no cost. Keep Telegram and phone primary. |
| FAQ section (repair time, warranty, delivery, how to choose, what documents) | Answers pre-sales questions without a call, and adds long-tail SEO | LOW | 6 to 8 questions in both languages. Use FAQ structured data. |

### Anti-Features (Commonly Requested, Often Problematic)

| Feature | Why Requested | Why Problematic | Alternative |
|---------|---------------|-----------------|-------------|
| Quote or contact form with backend | Standard on B2B sites | Needs a server, spam protection and someone to monitor it. PROJECT.md already excludes it. Local buyers prefer a call or messenger. | Telegram deep link with prefilled message, plus click-to-call. Revisit in v1.x if leads are lost. |
| Online ordering, cart, public prices | Looks modern | Transformers are specified and negotiated. Prices vary by rating and metal cost. Stale prices cause disputes. | "Call for a quote" or "Message us on Telegram". |
| Full searchable product catalog with SKUs | Large manufacturers have one | Needs a CMS and constant upkeep, and a small business cannot keep it current. | Type-based overview with spec ranges, plus a PDF catalog. |
| Live chat widget / chatbot | Seen as modern | Needs staffing, adds JS weight and duplicates Telegram. | Telegram button. |
| English version | Looks international | Not requested. Doubles content and QA. Partners are in the CIS and read Russian. | Defer. Revisit if China or other export demand appears. |
| User accounts, login, admin panel | "Manage content easily" | Backend, security and maintenance cost for a mostly static page. | Edit content in code or Markdown and redeploy. |
| Blog / news section | SEO tip | Stale blog with no posts looks worse than none. No content owner. | A concise FAQ and well-written service sections. |
| Autoplay hero video or heavy animations | Visual impact | Hurts mobile load time and data costs. | Optimized hero photo, optional click-to-play video below the fold. |
| Auto-redirect by browser language | Convenience | Breaks sharing and SEO, and misdetects Uzbek users with Russian browsers. | Visible language switcher, remembered choice, `hreflang`. |
| Pop-ups, cookie-heavy trackers, newsletter signup | Lead capture habit | Irritating on mobile and out of scope. | Single unobtrusive CTA bar. |
| Fake trust elements (stock badges, invented clients or numbers, generic "ISO certified" icons) | Fast credibility | Industrial buyers can verify, and a false claim damages a 2010-founded reputation. | Only real certificates, real photos and confirmed claims. |
| Technical calculator (kVA selection, losses) | Seen as expert tooling | High build effort, with liability if the result is wrong. | A selection checklist and Telegram consultation. |
| Social media feed embeds | Shows activity | Slow, third-party scripts, inconsistent content. | Simple icon links to Telegram/Instagram if active. |

## Feature Dependencies

```
Real client content (ratings, certificates, photos, warranty, client list)
    ├──required by──> Product range with spec ranges
    ├──required by──> Trust block (certificates)
    ├──required by──> Warranty statement
    ├──required by──> Client/partner logos and case snippets
    └──required by──> PDF catalog

Bilingual content (UZ + RU translations)
    └──required by──> Every text section, FAQ, SEO titles, PDF catalog

Language switcher with separate URLs
    └──required by──> hreflang SEO and per-language LocalBusiness metadata

Contact CTAs (call + Telegram + map)
    ├──required by──> Sticky mobile CTA bar
    ├──required by──> "Not sure which transformer?" help block
    └──enhanced by──> Click-tracking analytics

Services section ──enhances──> Repair process timeline
Trust block ──enhances──> Workshop gallery / video
Delivery + partners section ──enhances──> Partner logos

Quote form with backend ──conflicts──> Static-friendly hosting (PROJECT.md constraint)
Autoplay video ──conflicts──> Mobile-first fast loading
```

### Dependency Notes

- **Product range requires client data:** PROJECT.md lists "exact product series/ratings" as not yet provided. Build the layout with placeholders and flag this as an early content-gathering task.
- **Everything textual requires translation:** UZ and RU copy should be written natively, not machine-translated. Translation is the likely schedule bottleneck, so content is drafted before or in parallel with build.
- **Language switcher requires URL strategy:** decide `/uz/` and `/ru/` routing before building any section, because it shapes the whole project structure.
- **Click-tracking enhances CTAs:** instrumenting events is cheap if done while building the buttons and awkward to retrofit.
- **Quote form conflicts with static hosting:** deliberately deferred. The Telegram prefill link covers the gap.

## MVP Definition

### Launch With (v1)

- [ ] Hero with value prop, "since 2010", Andijan, call + Telegram CTAs
- [ ] Sticky mobile CTA bar (call, Telegram)
- [ ] Services: repair, production, sale (with "all under one roof" framing)
- [ ] Product range by type with spec ranges (using real client data)
- [ ] Trust block: certificates, real workshop and product photos
- [ ] Delivery across Uzbekistan and CIS partner countries
- [ ] Contact: address, hours, map embed, Yandex/2GIS links
- [ ] UZ/RU switcher with separate URLs and `hreflang`
- [ ] Mobile-first performance, HTTPS, custom domain
- [ ] SEO: titles, meta, `LocalBusiness` JSON-LD, sitemap
- [ ] Repair process timeline (cheap and high-trust)
- [ ] Short FAQ (cheap, SEO and pre-sales value)
- [ ] CTA click-tracking (the success metric is contact volume)

### Add After Validation (v1.x)

- [ ] Client/partner logos and project snippets, once the client supplies names and permission
- [ ] Warranty and testing/protocol section, once terms are confirmed
- [ ] PDF catalog per language
- [ ] Short workshop walkthrough video
- [ ] Yandex Business / 2GIS / Google Business profile alignment with the page
- [ ] Simple contact form via a third-party endpoint, if call/Telegram misses leads

### Future Consideration (v2+)

- [ ] English version, only if export demand appears
- [ ] Expanded catalog pages per product series, if the product list grows
- [ ] Blog or news with a committed content owner

## Feature Prioritization Matrix

| Feature | User Value | Implementation Cost | Priority |
|---------|------------|---------------------|----------|
| Hero + call/Telegram CTAs | HIGH | LOW | P1 |
| Sticky mobile CTA bar | HIGH | LOW | P1 |
| Services section | HIGH | LOW | P1 |
| Product range with spec ranges | HIGH | MEDIUM | P1 |
| Trust block (certificates, real photos) | HIGH | LOW | P1 |
| Contact + map | HIGH | LOW | P1 |
| UZ/RU switcher with separate URLs | HIGH | MEDIUM | P1 |
| Mobile performance | HIGH | MEDIUM | P1 |
| SEO + LocalBusiness schema | HIGH | LOW | P1 |
| Delivery and partners | MEDIUM | LOW | P1 |
| Repair process timeline | MEDIUM | LOW | P1 |
| FAQ | MEDIUM | LOW | P1 |
| CTA click-tracking | MEDIUM | LOW | P1 |
| Audience entry points | MEDIUM | LOW | P2 |
| "Not sure which transformer?" help block | MEDIUM | LOW | P2 |
| Client logos / case snippets | HIGH | MEDIUM | P2 (blocked on content) |
| Warranty / testing section | HIGH | LOW | P2 (blocked on content) |
| Workshop video | MEDIUM | MEDIUM | P2 |
| PDF catalog | MEDIUM | MEDIUM | P2 |
| WhatsApp/Viber link | LOW | LOW | P3 |
| Quote form with backend | MEDIUM | HIGH | P3 (excluded) |

**Priority key:**
- P1: Must have for launch
- P2: Should have, add when possible
- P3: Nice to have, future consideration

## Competitor Feature Analysis

Evidence is limited to marketplace and directory listings (prom.uz, goldenpages.uz). It was not possible to review a representative sample of local transformer-supplier websites, so treat this as directional.

| Feature | Local marketplace listings (prom.uz, goldenpages.uz) | Large international manufacturers | Our Approach |
|---------|------------------------------------------------------|-----------------------------------|--------------|
| Product specs | TMG series, kVA range, voltage, warranty | Full catalog with PDFs and CAD | Type overview with spec ranges, PDF later |
| Repair service | Rarely foregrounded | Service division pages | Foreground repair as equal pillar |
| Trust | "Certified", 2-year warranty text | ISO badges, client logos, case studies | Real certificates, workshop photos, since 2010 |
| Contact | Phone number, sometimes Telegram | RFQ forms, live chat, distributor locator | Call, Telegram, visit with map |
| Languages | Mostly Russian | English plus local | UZ + RU, separate URLs |

## Sources

- [Telegram and messaging in Uzbekistan, Kyrgyzstan, Kazakhstan (Central Asia Barometer)](https://ca-barometer.org/en/publications/which-messaging-apps-are-popular-in-uzbekistan-kyrgyzstan-and-kazakhstan) (MEDIUM)
- [Digital in Uzbekistan: Telegram reach, YouTube, e-commerce (vc.ru)](https://vc.ru/marketing/2322864-digital-marketing-v-uzbekistane) (MEDIUM)
- [Search engine market share, Uzbekistan (StatCounter)](https://gs.statcounter.com/search-engine-host-market-share/all/uzbekistan) (MEDIUM)
- [UZTRANSFORMATOR listing (prom.uz)](https://www.prom.uz/company/uztransformator/) (MEDIUM, competitor reference)
- [Transformer repair/sale listings (goldenpages.uz)](https://www.goldenpages.uz/rubrics/?Id=106965) (LOW-MEDIUM, directory)
- [B2B website for manufacturing company (irpr.agency)](https://irpr.agency/b2b-website-for-manufacturing-company) (LOW, agency marketing content)
- [The Role of Trust Signals in B2B Sales Funnels (b2brocket.ai)](https://b2brocket.ai/blog-posts/the-role-of-trust-signals-in-b2b-sales-funnels) (LOW-MEDIUM)
- [Website Trust Signals Checklist for Service Businesses (groverwebdesign.com)](https://groverwebdesign.com/marketing/website-trust-signals-checklist/) (LOW-MEDIUM)
- Domain reasoning on industrial buyer behavior and the client's constraints in PROJECT.md (MEDIUM). The claim "most competitors only sell" is an inference from marketplace listings and should be validated with the client.

---
*Feature research for: B2B industrial transformer supplier landing page (Uzbekistan/CIS)*
*Researched: 2026-10-07*
