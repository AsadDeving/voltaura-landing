# Pitfalls Research

**Domain:** Bilingual (Uzbek + Russian) B2B industrial landing page, transformer repair/manufacture/sales, Andijan, Uzbekistan
**Researched:** 2026-10-07
**Confidence:** MEDIUM. Web research was limited to three searches. Most findings come from established web-platform behavior (hreflang, tel:/t.me links, LCP, iframe weight) and domain knowledge. Items tagged [VERIFY] depend on facts I could not confirm.

Project context: no backend, no form, and CTAs are call, Telegram and visit only. This makes the call, Telegram and map links the whole conversion funnel. Most critical pitfalls are about those three links and about trust content. They are not about code complexity.

---

## Critical Pitfalls

### Pitfall 1: Placeholder or unverified content ships to production

**What goes wrong:**
The page goes live with "+998 XX XXX XX XX", a dummy Telegram handle, stock-photo transformers, invented product ratings ("up to 10 000 kVA"), made-up warranty terms, or Lorem ipsum in one language. PROJECT.md says exact series/ratings, warranty terms and the client list are "not yet provided". Those are the gaps most likely to be filled with plausible-sounding inventions.

**Why it happens:**
The layout is built before the content is in hand, and "TODO later" never gets closed. Product specs sound generic, so a developer fills them from other manufacturers' sites.

**How to avoid:**
- Keep a `CONTENT-REQUIRED.md` checklist of every fact the client must supply: phone, Telegram username, address and landmark, working hours, product series and kVA/kV ranges, warranty, certificate scans, client/partner names, and permission to publish each.
- Write content as data in one place (e.g. `content/uz.json`, `content/ru.json`). A build-time check fails if any value is empty, contains `TODO`, `XXX`, `lorem`, or is missing in either language.
- Where facts are missing, omit the claim. Do not soften it into vague copy. "Transformers 10–2500 kVA" is only acceptable if the client confirmed it.

**Warning signs:**
Round-number claims nobody can source. One language has fewer sections than the other. The phone number is a different format in different places.

**Phase to address:**
Content gathering, before or at the start of the build phase. The build-time content lint belongs in the scaffolding phase. The final launch checklist is the last gate.

---

### Pitfall 2: Uzbek script and typography handled wrong (Latin vs Cyrillic, apostrophes, fonts)

**What goes wrong:**
Three separate failures:
1. Cyrillic Uzbek is expected by some older or industrial visitors, but the page is Latin-only with no acknowledgement. Or both scripts are added and the work doubles.
2. The Uzbek letters oʻ and gʻ (e.g. "Oʻzbekiston", "gʻisht", "tamirlash") are typed with the wrong character. They should be the letter plus U+02BB (modifier letter turned comma). Writers usually type ASCII `'` (U+0027) or U+2018 instead, because the Windows Uzbek Latin keyboard does not offer U+02BB easily. The result is inconsistent glyphs, wrong line-breaking (a `'` can split a word), and weak search matching. Source: [Wikipedia, Modifier letter turned comma](https://en.wikipedia.org/wiki/Modifier_letter_turned_comma).
3. A chosen font lacks the glyphs. The Latin subset of a webfont may omit U+02BB (check it), and if Cyrillic Uzbek is ever added, ў қ ғ ҳ are outside the basic `cyrillic` subset in Google Fonts and need `cyrillic-ext`. The browser then silently falls back to a system font for a few letters.

**Why it happens:**
Developers test with English and Russian only. Copy arrives from the client in Telegram or Word with mixed apostrophes.

**How to avoid:**
- Decision for v1: Uzbek in Latin script only (official script, what younger and business users read), Russian in Cyrillic. Record it in Key Decisions. Defer Cyrillic Uzbek unless the client asks.
- Normalise all Uzbek copy to U+02BB for oʻ/gʻ via a build or lint step (regex for `[oOgG]['’‘`]`). Use U+02BC for the tutuq belgisi (e.g. "ma'no" is `maʼno`).
- Pick one font family with verified Latin + Cyrillic coverage and test a glyph sheet: `Oʻzbekiston Gʻisht Shahar ʼ`, and Russian `Трансформаторы ёЁ`. If self-hosting, subset to Latin + Cyrillic and include U+02BB/U+02BC explicitly.
- Never use `font-variant: small-caps` or `text-transform: uppercase` blindly on Uzbek. Uppercase of oʻ should stay "Oʻ".

**Warning signs:**
Oʻ looks like a stray quote or sits at a different height than neighbouring text. Words wrap at the apostrophe. Fallback serif letters appear in the middle of Uzbek words.

**Phase to address:**
Design/typography foundation phase (font choice and glyph test). Content phase (normalisation).

---

### Pitfall 3: Language switcher is a JS toggle on a single URL

**What goes wrong:**
Both languages live at `/` and swap text with JavaScript or a cookie. Google and Yandex index only one language (often the default). The shared link opened in Telegram shows the wrong language, and `<html lang>` stays wrong, so screen readers and Chrome's translation prompt misbehave. Or the opposite: auto-redirect from `Accept-Language` sends Googlebot and Russian-speaking Uzbek users to the wrong version.

**Why it happens:**
It is the simplest client-side implementation, and a "landing page" feels like one URL.

**How to avoid:**
- Separate static URLs: `/uz/` and `/ru/`. Each page has its own `<title>`, meta description, OG tags and `<html lang="uz">` / `lang="ru"`.
- Add `hreflang="uz"`, `hreflang="ru"` and `hreflang="x-default"` links, reciprocal and absolute.
- Choose the root `/` behaviour deliberately: either a small language-choice page, or a static page for the default language (decide which with the client; Uzbek-first is typical given the audience). Do not do server redirects based on IP or Accept-Language. At most, offer a dismissible suggestion banner.
- The switcher should be plain `<a href>` links labelled in text ("Oʻzbekcha | Русский"). Do not use country flags. Remember the choice only as a convenience, never to override a URL that was requested.
- Each language needs its own canonical URL. Neither points to the other.

**Warning signs:**
Only one language shows up in `site:` search. View-source shows a single `<html lang>`. Sharing `/ru/` in Telegram previews Uzbek text.

**Phase to address:**
Architecture/scaffolding phase (routing and i18n structure). SEO phase (hreflang validation).

---

### Pitfall 4: Machine-translated or developer-translated copy, and text-length breakage

**What goes wrong:**
Uzbek is produced by Google Translate or by the developer and reads as unnatural to buyers. Russian is a direct copy of the Uzbek logic. Technical terms are inconsistent (transformator / transformer, "ta'mirlash" / "remont", kVA / кВА). Layouts built with Russian or English placeholder text break when the real Uzbek and Russian strings, which are often noticeably longer than English, overflow buttons, nav and cards.

**Why it happens:**
There is no budget or time for a native reviewer. Fixed-width buttons are designed against short English labels.

**How to avoid:**
- Have the client or a native speaker in the business review both languages before launch. Industrial buyers notice poor terminology immediately, and it undermines trust since 2010.
- Make a short glossary (kVA, kV, oil-filled / dry-type, winding, repair) in both languages and apply it consistently. Use "kVA/kV" the same way in both languages, or follow the client's convention.
- Design with real strings. Test buttons, nav, hero headline and product cards at 320 px width in both languages. Allow wrapping (`min-height` and padding instead of fixed `height`), and avoid `text-overflow: ellipsis` on key CTAs.

**Warning signs:**
Hero headline wraps to 5+ lines on a 360 px phone. CTA text is clipped. Client says "nobody talks like that".

**Phase to address:**
Content phase (translation + review), UI build phase (real-string testing).

---

### Pitfall 5: Click-to-call and Telegram CTAs break in real use

**What goes wrong:**
- `tel:` links with spaces, parentheses or a missing country code (`tel:+998 74 123-45-67` or `tel:0741234567`) fail on some devices or dial wrongly from abroad (CIS partners). Desktop users get a dead link or an unwanted app prompt.
- Telegram link points to `t.me/+998...` (phone-based). That only works if the owner's privacy settings allow finding by number, and it often shows "user not found". Using `tg://` deep links alone breaks in browsers and in-app webviews (Instagram, Facebook).
- The CTA is visible only in the hero. After scrolling the user must scroll back to act, so the "one tap" promise in Core Value is broken.
- The business Telegram is a personal account that is unmonitored, or the first-message text is empty so the user does not know what to write.

**Why it happens:**
The number is pasted from how people display it, not in E.164 format. Nobody tests on real devices.

**How to avoid:**
- Use `tel:+998XXXXXXXXX` (E.164, digits only, no spaces) as href, and display a formatted number as text. Check the exact format with the client (Andijan landlines use area code 74 in international form; mobile uses an operator prefix) [VERIFY with client number].
- Use a Telegram username link: `https://t.me/<username>`. Optionally add `?text=` with a prefilled message in the page language (URL-encode Cyrillic). Confirm the account is a business account or a username that someone monitors, with a stated response time. Ask the client to set a public username if they only have a phone number.
- Keep a sticky bottom bar on mobile (Call / Telegram) in both languages. Keep it tall enough (44–48 px tap targets) and make sure it does not cover the footer, the cookie notice or the map.
- On desktop, make `tel:` still useful: show the number as selectable text, and add a copy affordance.
- Track clicks (see Pitfall 11). Test each link on a real Android phone and on iOS Safari, and in the Telegram in-app browser.

**Warning signs:**
Link text and href differ in digits. Test click on desktop does nothing visible. Telegram link says "If you have Telegram, you can contact..." without opening the chat.

**Phase to address:**
Build phase (CTA components), verification phase (real-device test with client's actual number).

---

### Pitfall 6: Image weight and LCP on slow mobile networks

**What goes wrong:**
Real workshop and product photos come straight from a phone or camera at 3–10 MB each. The page loads 20–40 MB on a mobile connection and low-end Android. Hero image becomes the LCP and takes many seconds. Or the opposite mistake: the hero is `loading="lazy"`, which delays LCP. Photos also keep EXIF data, including GPS coordinates of the workshop or a home.

**Why it happens:**
"Real photos" is a project requirement, and nobody sets an image pipeline. Uploading the originals is the fastest path.

**How to avoid:**
- Image pipeline at build time: resize to the largest display size, output AVIF and WebP with JPEG fallback, `srcset`/`sizes`, explicit `width`/`height` (avoid layout shift), strip EXIF. Use the framework's image optimisation or `sharp`.
- Hero image: `fetchpriority="high"`, not lazy, preloaded if necessary, target under ~100–150 KB at mobile width. Everything below the fold: `loading="lazy"`, `decoding="async"`.
- Set a page weight budget up front (e.g. under ~500 KB initial transfer on mobile, excluding lazy images) and measure on Lighthouse mobile with throttled 4G plus a mid-range Android profile.
- Do not auto-play background video. If a workshop video is wanted, use a poster image and load on tap.
- Keep the JS budget small. A static site generator with near-zero client JS is the best fit. Avoid carousels and animation libraries for a few photos.

**Warning signs:**
Any image file over 300 KB in the repo. Lighthouse LCP over 2.5 s on throttled mobile. Layout jumps as images load.

**Phase to address:**
Scaffolding phase (image pipeline and budget), performance verification phase.

---

### Pitfall 7: Google Maps embed hurts performance and is the wrong only map

**What goes wrong:**
- A standard Google Maps `<iframe>` is heavy (hundreds of KB of JS, third-party cookies, many requests) and blocks the main thread. It drags down mobile performance scores and data cost, even when far down the page.
- The embed is the only way to reach the location. Many Uzbek users navigate with Yandex Maps, Yandex Go or 2GIS. Google Maps coverage of a workshop in an industrial area may place the pin wrong. The supplied coordinates (40.7568, 72.3380) have not been verified on the ground.
- The address is only in the map. There are no street-level directions, landmark (moʻljal in Uzbek, ориентир in Russian), or opening hours.
- Embed API keys exposed or restricted wrongly if the Maps Embed API (keyed) is used.

**How to avoid:**
- Do not load the iframe on page load. Use a click-to-load facade: a static map image (or styled block) with a "Show map" button that injects the iframe, or `loading="lazy"` as a minimum. Always add `title` to the iframe.
- Provide plain route links beside the map: Google Maps (`https://www.google.com/maps/search/?api=1&query=40.7568,72.3380`), Yandex Maps and optionally 2GIS, each opening the native app on mobile. These cost nothing in page weight.
- Verify the pin location with the client on a phone, standing orders of precision: the pin must be on the workshop gate. Write the address in text in both languages, with a landmark, plus working hours and "call before visiting" if appropriate.
- Use the no-key iframe embed from the Maps share dialog, or a keyed Embed API with an HTTP-referrer restriction.

**Warning signs:**
Lighthouse flags third-party map code as a main-thread cost. The map shows only a city centre. Visitors call to ask "where exactly are you?".

**Phase to address:**
Contact section build phase. Final verification includes pin check with the client.

---

### Pitfall 8: Local SEO set up for Google only, with inconsistent business data

**What goes wrong:**
- Only Google structured data and meta tags are done. Yandex (still used in Uzbekistan, especially by Russian-speaking users) and 2GIS are ignored. No Yandex Webmaster verification, no Yandex Business listing [VERIFY current regional share], no 2GIS listing.
- Google Business Profile is not claimed or verified. Verification can be slow or awkward in Uzbekistan (postcard or video options vary) [VERIFY]. The profile is created at launch, so the page ships without the local-pack presence it needs.
- NAP (name, address, phone) differs between the site, structured data, Google profile and the Telegram channel. For example "Voltaura's Transformator" vs "Voltaura Transformator", the phone with and without +998, address in different spellings.
- JSON-LD `LocalBusiness` uses the wrong type or missing fields. Phone is not E.164, no `geo`, no `openingHours`, `inLanguage` is missing, and there is a single schema for both languages.
- Only generic keywords. Nobody checks how buyers actually search: in Russian "ремонт трансформаторов Андижан", "купить трансформатор Узбекистан", in Uzbek "transformator taʼmirlash Andijon", "transformator sotib olish". Note the city spelling: Andijon (Uzbek) vs Андижан (Russian) vs Andijan (English).
- Apostrophe in the business name ("Voltaura's") causes issues in URLs, titles and structured data if not escaped or normalised.

**How to avoid:**
- Write one canonical NAP record (in a content file) that feeds the page, JSON-LD, meta tags and the profile listings.
- Use `LocalBusiness` (or a more specific type such as `Store`/`ProfessionalService` if suitable) in JSON-LD per language page, with `telephone` in E.164, `address`, `geo`, `openingHoursSpecification`, `url`, `logo`, `image`, `sameAs` (Telegram, Maps, profiles), `areaServed: Uzbekistan`. Validate with Google's Rich Results Test.
- Register and verify with Google Search Console and Yandex Webmaster. Submit a sitemap with hreflang alternates. Add `robots.txt`.
- Start Google Business Profile verification, Yandex Business and 2GIS listing before the site launches. Their lead time is days to weeks. Put these as launch-checklist items for the client.
- Use localized, unique `<title>` and meta description per language, in the actual search phrasing. Spell the city naturally in each language (Andijon / Андижан).
- Add Open Graph and `og:locale` (`uz_UZ`, `ru_RU`) and a 1200x630 image for Telegram link previews, which is where this link will most often be shared. Telegram caches previews, so get this right before first share.

**Warning signs:**
Rich Results Test shows warnings. Profile address differs from site. The business is not in Yandex Maps search.

**Phase to address:**
SEO/launch phase, with business-profile registration kicked off earlier in parallel.

---

### Pitfall 9: Domain, DNS and hosting go wrong at the end

**What goes wrong:**
- Domain is registered in the developer's name or an agency account, or the client cannot log in. If a `.uz` domain is used, the registrar process, documents and timing differ from global TLDs [VERIFY]. Renewal lapses silently after a year and the site disappears.
- DNS misconfiguration: no `www` to apex redirect (two indexable versions), missing HTTPS or an expired certificate, wrong A/CNAME records, long TTL when making last-minute changes, and email (MX) records broken if the client uses domain email.
- Hosting is chosen without regard to latency and reachability from Uzbekistan. Some global CDNs have no nearby presence [VERIFY]. A slow first-byte to a distant region compounds the mobile problem.
- Personal data: Uzbekistan's data law (ZRU-547) has localization requirements, recently eased in 2026 for some categories but still strict for others (see [Legal500](https://www.legal500.com/developments/thought-leadership/personal-data-compliance-in-uzbekistan/), [Kun.uz](https://kun.uz/en/news/2026/03/27/uzbekistan-amends-personal-data-law-to-facilitate-global-payment-systems)). With no form and no backend, this risk is low. It rises if analytics, a form or a CRM is added later, or if a Telegram bot collects numbers. Do not add a form "quickly" without revisiting this. This is not legal advice.

**How to avoid:**
- Domain registered in the client's name, with the client as registrant and admin contact. Store credentials with the client. Turn on auto-renew and add calendar reminders.
- Pre-launch DNS checklist: HTTPS with auto-renewing cert, HTTP to HTTPS and `www` to apex (or reverse) 301 redirects, one canonical host in sitemap/canonicals/hreflang, lower TTL a day before cutover.
- Test load time from a mobile connection in Uzbekistan (or ask the client to test) on the chosen host. Prefer static hosting with CDN edge caching and compress with Brotli.
- Keep v1 data-free: no forms, no cookies beyond analytics. Document this in Key Decisions and flag any future form for a legal and hosting review.

**Warning signs:**
Both `www` and non-`www` respond with 200. Certificate warning on one of them. The client cannot say where the domain is registered.

**Phase to address:**
Deployment phase. Domain ownership question should be answered at project start.

---

### Pitfall 10: Trust signals that are weak, unverifiable or risky

**What goes wrong:**
- "Since 2010", "certified", "CIS partners" are asserted in text with no evidence. B2B industrial buyers expect proof: certificate scans, license numbers, photos of the workshop, test bench, named references.
- Certificates are shown as tiny unreadable thumbnails, or published with personal or sensitive details (national ID, bank details, signatures, stamps) that were not meant to be public. Or they are expired or from the wrong scope.
- Stock photos of other factories or wind turbines replace real work. Buyers in a small market recognise them.
- Partner logos or names from China, Russia, Kazakhstan, Tajikistan, Kyrgyzstan are shown without the partners' consent.
- Product range pages say nothing concrete (no power ranges, voltages, cooling type, standards), so a technical buyer cannot qualify the supplier and calls with basic questions, or leaves.
- Claims such as "best", "highest quality", "guaranteed" without terms.

**How to avoid:**
- Show verifiable specifics: number of years (computed from 2010 in a build step, not hard-coded "15+ years" that goes stale), certificate names and numbers, issue/expiry dates, click-to-enlarge scans, real workshop and test-bench photos with captions, and a named delivery area.
- Review each certificate for sensitive data before publishing. Get written (Telegram message is fine) consent for partner names or logos.
- Add a compact spec table per product family only for values the client confirmed (rated power in kVA, voltage class in kV, cooling type, oil-filled vs dry-type). Add "Don't see your rating? Custom winding: call" for special cases.
- State repair process in simple steps (diagnose, quote, repair, test, return), turnaround and warranty only if the client confirms.
- Show the physical address, working hours and a real person's name or role near the CTAs. Local buyers value that someone answers.

**Warning signs:**
The trust section has icons and slogans but no document, photo or number. Client says "we'll send certificates later" the week of launch.

**Phase to address:**
Content phase (collect), UI build phase (trust section), launch checklist (review permissions).

---

### Pitfall 11: Conversions are not measured, so the success metric cannot be evaluated

**What goes wrong:**
The success metric is "inbound calls, Telegram messages and workshop visits", but nothing is measured. Without a form, there are no submissions to count. Analytics is installed with default pageviews only. Or heavy GA scripts and a consent banner are added and hurt performance. Or tracking uses `onclick` on the link that breaks in some browsers.

**How to avoid:**
- Pick one lightweight, privacy-friendly analytics (or Yandex Metrica, which fits the Russian-speaking audience and offers session replay and a map click report, plus Google Analytics if the client wants it) and define events: `click_call`, `click_telegram`, `click_directions`, `lang_switch`. Fire them via event listener on `tel:`, `t.me` and map links with `navigator.sendBeacon`, and do not delay navigation.
- Record the language and the placement (hero, sticky bar, contact section) as event parameters. This tells the client which page version and CTA works.
- For offline conversion, add a simple routine: ask callers "how did you find us?", and use a distinct tracking number only if the client wants one. Do not add a second number that confuses NAP consistency.
- Analytics script loaded async, after the main content, within the JS budget. Review local data rules before adding any cookie-based tracking (see Pitfall 9).

**Warning signs:**
Client asks "is the site working?" after a month and nobody can answer. Events fire twice, or none on iOS.

**Phase to address:**
Analytics/launch phase.

---

## Moderate Pitfalls

### Pitfall 12: Fonts block rendering or hit third-party hosts
**What goes wrong:** Several font weights from Google Fonts add requests and delay text. Latin + Cyrillic subsets together can weigh 100+ KB per weight. Flash of invisible text on slow networks.
**Prevention:** Self-host WOFF2 subsets (Latin + Cyrillic, with U+02BB/U+02BC), at most 2 weights (or one variable font), `font-display: swap`, preload the main weight, metric-matched system fallback.

### Pitfall 13: Language switch loses context and breaks navigation
**What goes wrong:** Switching language sends the user to the top of the other page, or to the default home. Anchors (`#contact`) differ between languages and break.
**Prevention:** Use the same anchor IDs in both languages. Language switcher links to the same section (`/ru/#contact`). Keep it visible in the header at all breakpoints, including mobile.

### Pitfall 14: Mobile-first only on a flagship phone
**What goes wrong:** Looks fine on the developer's phone, but breaks on small (320–360 px) low-end Android, in Telegram's in-app browser, or in landscape. Hover-only interactions. Sticky bar covering content.
**Prevention:** Test on real low-end Android and in Telegram in-app browser, 320 px width, with browser zoom 200%. No hover-dependent content. Add `padding-bottom` to the body equal to the sticky bar height.

### Pitfall 15: Navy-and-white theme fails contrast and the logo is mishandled
**What goes wrong:** Light-blue text on white, grey captions at low contrast, or the supplied logo (probably a raster file) scaled up and blurry, with a white background box on a navy header. Favicon and OG image missing.
**Prevention:** WCAG AA contrast (4.5:1) on body text and buttons. Ask for an SVG or high-resolution logo. Make a transparent version and favicon set (32 px, 180 px Apple touch icon, 512 px). Make an OG image per language.

### Pitfall 16: Accessibility and `lang` details forgotten
**What goes wrong:** Map iframe without `title`, links with only icons and no accessible name ("Telegram" icon only), missing focus styles, mixed-language fragments (Russian brand name inside Uzbek text) without `lang` attributes.
**Prevention:** Label icon buttons in the page language, `lang` on inline foreign fragments, visible focus ring, run axe or Lighthouse accessibility.

### Pitfall 17: Dates, phone formats and units mixed between languages
**What goes wrong:** Russian uses "кВА", "кВ"; Uzbek-Latin copy uses "kVA", "kV". Decimal comma vs point, spaces in numbers (`1 000`), phone formats differ per page, working-hours format 24h vs 12h.
**Prevention:** One formatting helper and a short style guide. Use non-breaking spaces between numbers and units.

### Pitfall 18: Overreach in scope: adding a quote form, chatbot or CMS "because it is easy"
**What goes wrong:** A form without a backend or spam protection, a Telegram bot token exposed in client JS, or a third-party form service in an unknown jurisdiction. Contradicts Out of Scope in PROJECT.md and adds the data-law question from Pitfall 9.
**Prevention:** Hold to the call/Telegram/visit funnel for v1. If a form is demanded later, make it a separate phase with server-side Telegram bot delivery, spam protection and a legal check.

---

## Minor Pitfalls

### Pitfall 19: Missing 404, favicon, robots, sitemap
**What goes wrong:** Default host 404 page, no favicon (browser tab shows a generic icon), no `robots.txt`/`sitemap.xml` with hreflang.
**Prevention:** Include in the launch checklist. Add a bilingual 404 with the contact links.

### Pitfall 20: Telegram link preview shows wrong title or image
**What goes wrong:** Telegram caches previews. The first share (usually the owner posting in a group) shows an empty or default card, and it stays that way.
**Prevention:** Finish OG tags per language before anyone shares the URL. Re-scrape through Telegram's `@WebpageBot` if wrong.

### Pitfall 21: Copyright or licence problems with assets
**What goes wrong:** Icons, fonts or photos used without a licence (for example a stock transformer image, or a font with no web licence).
**Prevention:** Track licence per asset (a short `ASSETS.md`). Prefer open-licence fonts (OFL) and client-owned photos.

### Pitfall 22: Counting "visits" is not possible
**What goes wrong:** "Workshop visits" is a success metric but there is no way to attribute them.
**Prevention:** Include "Say you found us online" in contact copy and ask on reception, or measure directions clicks as a proxy. Be honest that visits are only a proxy metric.

---

## Technical Debt Patterns

| Shortcut | Immediate Benefit | Long-term Cost | When Acceptable |
|----------|-------------------|----------------|-----------------|
| Single URL with JS language toggle | Quick to build | One language indexed, wrong shared previews, a11y issues | Never |
| Hard-code strings in components | Fast first draft | Missing translations, hard to review with a native speaker | Only while prototyping, never at launch |
| Unoptimised original photos in repo | No pipeline needed | Huge page, slow LCP, EXIF leakage, large repo | Never past first prototype |
| Google Maps iframe above the fold, eager | Looks complete | Main-thread cost, data cost | Never; lazy or facade only |
| Placeholder phone/Telegram until the client replies | Unblocks layout | Dead links ship to production | Only with a build check that fails the release |
| Copy hard-coded "15 years" | Simple | Stale next year | Never; compute from 2010 |
| Google Fonts via CDN link | One line | Extra DNS/TLS round trip, render delay | Acceptable for a prototype only |
| Skipping Yandex/2GIS registration | Saves an afternoon | Misses a large part of Russian-speaking and navigation traffic | Not recommended; at minimum Yandex Webmaster + Yandex Maps listing [VERIFY share] |

## Integration Gotchas

| Integration | Common Mistake | Correct Approach |
|-------------|----------------|------------------|
| Telegram | Phone-based `t.me/+998...` link, or only `tg://` | `https://t.me/<username>` with optional URL-encoded `?text=` in page language |
| tel: links | Spaces, brackets, no `+998` | `tel:+998XXXXXXXXX`, formatted display text separately |
| Google Maps | Eager iframe, only map offered | Click-to-load facade plus plain Google/Yandex/2GIS route links |
| Google Business Profile | Created at launch, NAP differs from site | Start verification early; reuse one NAP record |
| Yandex | Not verified in Webmaster, no Business listing | Verify, submit sitemap, list the business, add Metrica events |
| Search Console | Only one language property tested | One domain property, sitemap with hreflang alternates, check International targeting issues |
| Analytics | Default pageviews only, heavy script | Lightweight tool, click events on call/Telegram/directions, async load |
| Domain registrar | Registered by developer | Registrant is the client; auto-renew on |
| CDN/host | Chosen without checking latency from Uzbekistan | Test from a local mobile network before committing |
| OG/Telegram preview | Tags added after the first share | Complete before first share; refresh via @WebpageBot |

## Performance Traps

| Trap | Symptoms | Prevention | When It Breaks |
|------|----------|------------|----------------|
| Multi-MB hero/gallery photos | Slow LCP, blank hero on 3G/4G | AVIF/WebP, srcset, hero under ~150 KB, lazy below fold | Immediately on mobile |
| Map iframe at load | Poor TBT/INP, extra requests | Facade / lazy load | Immediately on low-end Android |
| Many font weights/subsets | Late text, FOIT | Self-host, 1 variable font or 2 weights, swap, preload | Slow mobile connections |
| Animation/carousel libraries | JS bundle bloat | CSS only, near-zero JS | Low-end devices |
| Third-party widgets (chat, GA, pixels) | Main-thread blocking | Minimal, deferred, one analytics tool | Any |
| No caching headers | Repeat visitors re-download assets | Long-cache hashed assets, short-cache HTML | Repeat visits |
| Layout shift from images without dimensions | CLS high, mis-taps on CTA | width/height or aspect-ratio on every image, reserved space for map | Immediately |

Scale note: traffic will be tiny (a regional B2B business). There are no server scale concerns on a static site. The "scale" that matters is slow networks and weak devices, not user count.

## Security Mistakes

| Mistake | Risk | Prevention |
|---------|------|------------|
| Exposing a Telegram bot token or form-service key in client JS (if a form is added later) | Bot hijack, spam | Keep forms out of v1; if added, deliver server-side |
| EXIF GPS in photos | Reveals home address or exact locations | Strip metadata in the image pipeline |
| Publishing certificate scans with personal or banking data | Privacy/fraud risk | Review and redact before publishing |
| Unrestricted Maps API key | Quota theft and billing | Use the keyless embed, or restrict by HTTP referrer |
| Missing HTTPS or security headers on host | Browser warnings, tampering | Force HTTPS, HSTS, basic headers (CSP, `X-Content-Type-Options`, `Referrer-Policy`) |
| Domain account without 2FA, registered to the developer | Domain hijack or lock-out | Client-owned account, 2FA, auto-renew |
| `target="_blank"` external links without `rel="noopener"` | Tabnabbing (minor on modern browsers) | `rel="noopener noreferrer"` on external links |

## UX Pitfalls

| Pitfall | User Impact | Better Approach |
|---------|-------------|-----------------|
| CTA only in hero | User scrolls and cannot act without scrolling back | Sticky mobile bar plus CTAs in every section end |
| Wall of generic text about "quality" | Technical buyer cannot qualify supplier | Spec tables, real photos, named certificates, delivery area |
| Language switch hidden in a burger menu | Russian speakers see Uzbek and leave | Visible switcher in header at all sizes, text labels |
| Three audiences (industry, utilities/builders, farms/small business) mixed in one wall | Nobody sees their case | Short "for whom" cards or product-by-use section; one primary action in all of them |
| Map as the only address | Cannot find the gate | Text address, landmark, hours, route links |
| Tiny tap targets (links in footer) | Mis-taps on mobile | 44 px minimum targets with spacing |
| Popups, cookie banners, chat widgets covering the sticky bar | CTA blocked | Avoid popups; minimal or no banner if no cookie tracking |
| "Call us" with no hours | Visitors call when closed and give up | Show hours and Telegram as the out-of-hours option |

## "Looks Done But Isn't" Checklist

- [ ] **Uzbek page:** Often missing U+02BB normalisation and real native review. Verify with a regex scan and a native reader.
- [ ] **Russian page:** Often a thin copy of Uzbek structure. Verify with a Russian speaker that terminology is natural.
- [ ] **hreflang:** Often missing reciprocal links or `x-default`. Verify in view-source and a validator tool.
- [ ] **Phone link:** Often not E.164. Verify by tapping on Android and iOS, and from a number abroad.
- [ ] **Telegram link:** Often phone-based or wrong username. Verify it opens the right chat in the in-app browser.
- [ ] **Map:** Often wrong pin or eager iframe. Verify pin on site with client; check Network tab for no map request before interaction.
- [ ] **Images:** Often original sizes, EXIF present. Verify with file-size listing and `exiftool`.
- [ ] **Structured data:** Often phone/hours/geo missing. Verify in Rich Results Test, both languages.
- [ ] **Business listings:** Often not started. Verify Google Business Profile, Yandex and 2GIS statuses.
- [ ] **Placeholders:** Often hide in alt text, meta description, JSON-LD and the OG image. Verify with a grep for TODO/XXX/lorem/example.com.
- [ ] **Certificates:** Often unreadable or expired. Verify they can be enlarged and are cleared for publication.
- [ ] **Domain/DNS:** Often one host variant not redirected. Verify `http`, `https`, `www`, apex all end at one canonical URL.
- [ ] **Analytics:** Often installed but no events. Verify each CTA click appears in the report.
- [ ] **OG preview:** Often default card. Verify by sharing the URL to a private Telegram chat in both languages.
- [ ] **Mobile:** Often tested only in desktop devtools. Verify on a real low-end Android on mobile data.

## Recovery Strategies

| Pitfall | Recovery Cost | Recovery Steps |
|---------|---------------|----------------|
| Single-URL language toggle shipped | HIGH | Re-architect to `/uz/` and `/ru/`, add 301 redirects, hreflang, resubmit sitemaps; indexing lags weeks |
| Wrong apostrophes in Uzbek copy | LOW | Run regex normalisation; check fonts; re-deploy |
| Placeholder contacts live | LOW but costly in lost leads | Hotfix content, add the lint check, review all meta/JSON-LD |
| Heavy images/map | MEDIUM | Add image pipeline, re-export, convert map to facade; re-test Lighthouse |
| Telegram preview cached wrong | LOW | Re-scrape via `@WebpageBot`; change OG image URL if stale |
| Domain registered to developer | MEDIUM | Transfer or change registrant contact with the registrar; plan before any dispute |
| Business listings never claimed / duplicated | MEDIUM | Claim, merge duplicates, align NAP; verification delays |
| No analytics for first months | HIGH (data lost forever) | Install now; use phone-call survey as a stopgap |
| Poor translation discovered post-launch | MEDIUM | Native rewrite, update content files, redeploy; no structural change if content is separated |

## Pitfall-to-Phase Mapping

Assumed phase names (adjust to the roadmap): 1 Foundation/scaffolding (i18n routing, design system, image pipeline), 2 Content and page sections (content collection, translation, trust, products), 3 Contact, map and CTAs, 4 SEO, listings and analytics, 5 Deploy, domain and launch verification.

| Pitfall | Prevention Phase | Verification |
|---------|------------------|--------------|
| 1 Placeholder content | 1 (content lint), 2, 5 | Build fails on TODO/XXX/empty strings; final grep; client sign-off on every fact |
| 2 Uzbek script/typography | 1 (font, glyph test), 2 (normalisation) | Glyph sheet screenshot; regex scan finds zero `'` inside oʻ/gʻ |
| 3 Language URL structure | 1 | `/uz/` and `/ru/` return distinct HTML with correct `lang`, hreflang validates |
| 4 Translation quality, text length | 2 | Native review sign-off; 320 px screenshots in both languages |
| 5 CTA link correctness | 3 | Real-device taps on Android/iOS/Telegram in-app browser |
| 6 Image weight/LCP | 1 (pipeline), 5 (measure) | Lighthouse mobile throttled: LCP under 2.5 s; initial transfer within budget |
| 7 Map embed | 3 | No map network requests before interaction; pin verified on site; route links work |
| 8 Local SEO / listings | 4 (start listings in phase 2 or earlier) | Rich Results Test, Search Console, Yandex Webmaster verified; profiles claimed |
| 9 Domain/DNS/hosting | 5 (ownership question at project start) | Single canonical host, valid HTTPS, client holds registrar login |
| 10 Trust signals | 2 | Each claim has evidence (document, photo, number); permissions recorded |
| 11 Measurement | 4 | Test clicks appear as events in analytics |
| 12 Fonts | 1 | Network panel: at most 2 font files, self-hosted |
| 13 Switch context | 1 | Switcher keeps section anchor |
| 14 Mobile testing | 5 | Real-device checklist completed |
| 15 Contrast/logo | 1 | Contrast check, SVG/high-res logo, favicon set |
| 16 Accessibility | 3, 5 | axe/Lighthouse a11y score, keyboard pass |
| 17 Format consistency | 2 | Style guide applied; spot-check |
| 18 Scope creep (form/bot) | All; revisit at roadmap | Out-of-scope list unchanged unless decided |
| 19–22 Minor | 4, 5 | Launch checklist |

Research flags for phase planning:
- Phase 4 (SEO/listings): needs a short targeted check of current Yandex, 2GIS and Google Business Profile verification flow in Uzbekistan, and of keyword phrasing in both languages.
- Phase 5 (hosting/domain): verify `.uz` vs `.com` registration process, host latency from Uzbekistan, and whether the client has an existing domain contract.
- Phase 1 (fonts): glyph-coverage check of the chosen font for U+02BB/U+02BC is required before committing.
- Other phases: standard patterns, unlikely to need research.

## Sources

- [Wikipedia: Modifier letter turned comma (U+02BB), Uzbek Latin keyboard and apostrophe substitution](https://en.wikipedia.org/wiki/Modifier_letter_turned_comma) (MEDIUM)
- [Legal500: Personal data compliance in Uzbekistan (Law ZRU-547)](https://www.legal500.com/developments/thought-leadership/personal-data-compliance-in-uzbekistan/) (MEDIUM)
- [Kun.uz: Uzbekistan amends personal data law, March 2026](https://kun.uz/en/news/2026/03/27/uzbekistan-amends-personal-data-law-to-facilitate-global-payment-systems) (MEDIUM)
- [RankTracker: Local SEO guide for Uzbekistan](https://www.ranktracker.com/blog/a-complete-guide-for-doing-local-seo-in-uzbekistan/) (LOW; vendor blog, used only for the list of relevant map and directory platforms)
- Google Search Central documentation on hreflang and LocalBusiness structured data (established guidance, not re-fetched this session) (MEDIUM)
- Web platform knowledge: `tel:` and `t.me` link behavior, Core Web Vitals (LCP, CLS), lazy-loading and iframe cost (established, not re-fetched) (MEDIUM)
- Items tagged [VERIFY] (Google Business Profile verification options in Uzbekistan, Yandex and 2GIS shares, `.uz` registration process, CDN latency from Uzbekistan) are unconfirmed (LOW)

---
*Pitfalls research for: bilingual (Uzbek + Russian) B2B industrial landing page, Andijan, Uzbekistan*
*Researched: 2026-10-07*
