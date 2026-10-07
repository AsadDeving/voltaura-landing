# Roadmap: Voltaura's Transformator Landing Page

## Overview

A trilingual (Uzbek Latin, Uzbek Cyrillic, Russian) static landing page that turns visitors into calls, Telegram chats and workshop visits. The work runs in four steps. First, a core page where a visitor reads the site in their language, understands what Voltaura does and reaches it in one tap. Second, the content and trust sections that prove the business is real. Third, search visibility and click tracking so the owner can see what works. Fourth, verification on real devices and launch on the client's own domain with every fact signed off. Client-supplied facts (product ranges, certificates, hours, Telegram username, domain access) gate Phases 2 and 4, so content gathering runs in parallel from day one.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Trilingual Core Page & Contact** - Three language URLs, hero, one-tap call and Telegram, address and lazy map, single facts file
- [ ] **Phase 2: Content & Trust Sections** - Services, repair timeline, product range, real photos and certificates, delivery and partners, FAQ, Telegram help button
- [ ] **Phase 3: Search Visibility & Analytics** - Per-language SEO tags, local business structured data, sitemap and robots, tap tracking in Metrika and GA4
- [ ] **Phase 4: Verification & Launch** - Speed, accessibility and real-device checks, live on the client's domain over HTTPS, 404 and favicons, client sign-off

## Phase Details

### Phase 1: Trilingual Core Page & Contact
**Goal:** As a visitor, I want to read the page in my language and reach Voltaura in one tap, so that I can call or message them right away.
**Mode:** mvp
**Depends on**: Nothing (first phase)
**Requirements**: FOUND-01, FOUND-02, FOUND-03, FOUND-04, FOUND-05, FOUND-06, CORE-01, CORE-02, CORE-03, CORE-04, CORE-05, CORE-06, CORE-07, PERF-02
**Success Criteria** (what must be TRUE):
  1. Visitor can open the site in Uzbek Latin, Uzbek Cyrillic or Russian, each on its own static URL, and the root URL shows the Uzbek page with no automatic redirect by browser language or IP.
  2. Visitor can switch language from any section with a plain link that keeps their place on the page, and every page declares its language with reciprocal hreflang plus x-default.
  3. Uzbek (oʻ, gʻ) and Cyrillic text renders correctly in a self-hosted font, and the build fails if a translation key is missing or a TODO, lorem or empty placeholder is left in any language.
  4. Visitor sees a hero stating repair, production and sale of transformers, "since 2010" and Andijan, with the hero image in a modern format at the right size and loaded first. Visitor can call or open a Telegram chat with one tap, and on mobile a sticky call and Telegram bar is always visible.
  5. Visitor can see the address, landmark and working hours, load the embedded map only on tap, and open Google Maps, Yandex Maps or 2GIS directions with plain links. Changing the phone, Telegram, address or coordinates in the one data file updates every place they appear.
**Plans**: TBD
**UI hint**: yes

### Phase 2: Content & Trust Sections
**Goal:** As a buyer, I want to see what Voltaura repairs, makes and sells with real proof, so that I trust it enough to contact them.
**Mode:** mvp
**Depends on**: Phase 1
**Requirements**: CONT-01, CONT-02, CONT-03, CONT-04, CONT-05, CONT-06, CONT-07, CONT-08
**Success Criteria** (what must be TRUE):
  1. Visitor can read the services (transformer repair, production and sales) and see the repair process as a step-by-step timeline, in their language.
  2. Visitor can browse the product range by type (power/distribution, dry-type, small/specialty) and sees only ranges the client confirmed. No rating, warranty or client claim the client has not confirmed appears anywhere on the page.
  3. Visitor can see real workshop and product photos and certificate scans that are readable.
  4. Visitor can see that delivery covers all Uzbekistan and which partner countries the business works with (China, Russia, Kazakhstan, Tajikistan, Kyrgyzstan), and can read a short FAQ in their language.
  5. Visitor can tap a "not sure which transformer?" button that opens Telegram with a prefilled message in the page language.
**Plans**: TBD
**UI hint**: yes

### Phase 3: Search Visibility & Analytics
**Goal:** As the owner, I want buyers to find the page in each language and see which taps lead to contact, so that I can grow inbound calls.
**Mode:** mvp
**Depends on**: Phase 2
**Requirements**: SEO-01, SEO-02, SEO-03, SEO-05
**Success Criteria** (what must be TRUE):
  1. Each language page has its own title, description and social preview image, visible in the page source and when the link is shared.
  2. Each language page carries local business structured data (address, phone, coordinates, founding year 2010) that validates and matches what the visitor sees.
  3. Site publishes a sitemap with language alternates and a robots file that crawlers can fetch.
  4. Site owner can see call, Telegram and directions taps broken down by language in Yandex Metrika and GA4.
**Plans**: TBD

### Phase 4: Verification & Launch
**Goal:** As the owner, I want the site verified on real devices and live on my domain, so that I can send customers to it with confidence.
**Mode:** mvp
**Depends on**: Phase 3
**Requirements**: PERF-01, PERF-03, PERF-04, SEO-04, LAUNCH-01, LAUNCH-02, LAUNCH-03, LAUNCH-04
**Success Criteria** (what must be TRUE):
  1. On throttled slow 4G the page loads with largest content paint under 2.5 s, layout shift under 0.1 and under 30 KB of JavaScript, in all three languages.
  2. Page is usable at 320 px width in all three languages and passes a basic accessibility check.
  3. Call and Telegram links work when tapped on a real Android phone, a real iPhone and inside the Telegram in-app browser.
  4. Site is live on the client's own domain over HTTPS with one canonical host, hosted on Cloudflare free with the commercial-use terms check recorded, and the owner can verify it in Google Search Console and Yandex Webmaster.
  5. Visitor sees a translated 404 page and favicons, and the client has signed off on every fact shown (numbers, certificates, partners, map pin).
**Plans**: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Trilingual Core Page & Contact | 0/TBD | Not started | - |
| 2. Content & Trust Sections | 0/TBD | Not started | - |
| 3. Search Visibility & Analytics | 0/TBD | Not started | - |
| 4. Verification & Launch | 0/TBD | Not started | - |
