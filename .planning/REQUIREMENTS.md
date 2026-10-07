# Requirements: Voltaura's Transformator Landing Page

**Defined:** 2026-10-07
**Core Value:** A visitor instantly understands what Voltaura does (repair, produce, sell transformers), trusts it, and can contact the business in one tap.

## v1 Requirements

### Foundation & Languages

- [ ] **FOUND-01**: Visitor can open the site in Uzbek Latin, Uzbek Cyrillic or Russian, each on its own static URL
- [ ] **FOUND-02**: Visitor can switch language from any section with a plain link that keeps their place on the page
- [ ] **FOUND-03**: Every page declares its language and has reciprocal hreflang plus x-default
- [ ] **FOUND-04**: Build fails if a translation key is missing or a placeholder (TODO, lorem, empty) is left in any language
- [ ] **FOUND-05**: Visitor sees the Uzbek-language page when opening the root URL, with no automatic redirect by browser language or IP
- [ ] **FOUND-06**: All Uzbek and Russian text renders correctly, including oʻ and gʻ and Cyrillic letters, in a self-hosted font

### Core Page & Contact

- [ ] **CORE-01**: Visitor sees a hero stating repair, production and sale of transformers, "since 2010" and Andijan
- [ ] **CORE-02**: Visitor can call the business with one tap (`tel:` link in international format)
- [ ] **CORE-03**: Visitor can open a Telegram chat with one tap (`t.me` link)
- [ ] **CORE-04**: Visitor on mobile always sees a sticky call and Telegram bar
- [ ] **CORE-05**: Visitor can see the address, landmark and working hours
- [ ] **CORE-06**: Visitor can load an embedded map only on tap, and can open Google Maps, Yandex Maps or 2GIS directions with plain links
- [ ] **CORE-07**: Phone, Telegram, address and coordinates come from one data file used everywhere on the page

### Content Sections

- [ ] **CONT-01**: Visitor can read the services: transformer repair, production and sales
- [ ] **CONT-02**: Visitor can browse the product range by type: power/distribution, dry-type, small/specialty, with ranges only the client confirmed
- [ ] **CONT-03**: Visitor can see the repair process as a step-by-step timeline
- [ ] **CONT-04**: Visitor can see real workshop and product photos and readable certificate scans
- [ ] **CONT-05**: Visitor can see that delivery covers all Uzbekistan and which partner countries the business works with (China, Russia, Kazakhstan, Tajikistan, Kyrgyzstan)
- [ ] **CONT-06**: Visitor can read a short FAQ in their language
- [ ] **CONT-07**: Visitor can tap a "not sure which transformer?" button that opens Telegram with a prefilled message in the page language
- [ ] **CONT-08**: No unconfirmed claim (ratings, warranty, clients) appears on the page

### SEO & Analytics

- [ ] **SEO-01**: Each language has its own title, description and social preview image
- [ ] **SEO-02**: Each language page has local business structured data with address, phone, coordinates and founding year 2010
- [ ] **SEO-03**: Site publishes a sitemap with language alternates and a robots file
- [ ] **SEO-04**: Site owner can verify the site in Google Search Console and Yandex Webmaster
- [ ] **SEO-05**: Site owner can see call, Telegram and directions taps per language in Yandex Metrika and GA4

### Performance & Quality

- [ ] **PERF-01**: Page loads fast on slow 4G (largest content paint under 2.5 s, layout shift under 0.1, JavaScript under 30 KB)
- [ ] **PERF-02**: Images are served in modern formats at the right size, with the hero loaded first
- [ ] **PERF-03**: Page is usable at 320 px width in all three languages and passes a basic accessibility check
- [ ] **PERF-04**: Call and Telegram links verified on real Android, iPhone and the Telegram in-app browser

### Launch

- [ ] **LAUNCH-01**: Site is live on the client's own domain over HTTPS, with one canonical host
- [ ] **LAUNCH-02**: Site is hosted on Cloudflare free, with a recorded check of its commercial-use terms
- [ ] **LAUNCH-03**: Visitor sees a translated 404 page and the site has favicons
- [ ] **LAUNCH-04**: Client signs off on every fact shown (numbers, certificates, partners, map pin)

## v2 Requirements

### Growth

- **GROW-01**: PDF catalog per language
- **GROW-02**: Client and partner logos, case snippets, warranty and testing statement (once the client supplies them)
- **GROW-03**: Short workshop video
- **GROW-04**: WhatsApp or Viber as secondary contact
- **GROW-05**: English version
- **GROW-06**: Quote form or Telegram bot (needs data-law review)

## Out of Scope

| Feature | Reason |
|---------|--------|
| Online ordering, cart, public prices | Landing page drives inquiries, not e-commerce |
| Quote form with backend | Telegram covers inquiries; avoids personal-data handling |
| Chatbot / live chat | Extra weight and upkeep; Telegram link is enough |
| Price calculator | Needs data the client has not supplied |
| Admin panel / CMS / accounts | Static content is enough for v1 |
| Blog | No content plan; defer |
| Autoplay video, popups | Hurt mobile performance and trust |
| Automatic language redirect | Hurts SEO and surprises users |
| Fake trust badges or invented clients | Damages credibility |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| (filled by roadmapper) | | Pending |

**Coverage:**
- v1 requirements: 34 total

---
*Requirements defined: 2026-10-07*
*Last updated: 2026-10-07 after initial definition*
