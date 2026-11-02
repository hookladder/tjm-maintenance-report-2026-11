<p class="eyebrow">Technical log</p>

# Optimizations

| # | Issue | Fix | Result |
|---|---|---|---|
| 1 | Brand fonts loaded from the staging domain | Regenerated Elementor's local Google Fonts and Hummingbird's copies | **0** requests to staging |
| 2 | Combined assets out of date | Rebuilt Hummingbird's optimized files | Home stylesheets 45 → **16** |
| 3 | Critical CSS reported "Optimized" but not applied | Above the fold · load asynchronously, then regenerate | Inlined on inner pages, **0** render-blocking |
| 4 | 1.8 MB of JavaScript on first load | Delay JavaScript with the Elementor stack excluded | TBT 380 – 590 ms → **0 ms** |
| 5 | 1-hour page-cache lifetime | 24-hour lifetime | More visits served from cache (0.34 s TTFB) |

## 1. Brand fonts on the staging domain

Sofia Sans, Sofia Sans Semi Condensed and Anton are served locally by Elementor. Their stylesheets were generated before launch and still pointed every `@font-face` at `toujoursmikes-dev.tempurl.host`, so each visit opened a connection to the staging server for its fonts. I confirmed it in the resource timeline: the three woff2 files above the fold came from the staging host.

**Fix** — Elementor's *Clear Files & Data* does not reset this cache; it is cleared when the font-display setting changes. I switched **Elementor → Settings → Performance → Google Fonts font-display** to another value and back to `swap`, which regenerated the three stylesheets on `toujoursmikes.ca`. Hummingbird keeps its own compressed copy of each file, so I cleared its optimized assets as well (see 2).

**Result** — every font now loads from `toujoursmikes.ca/wp-content/uploads/elementor/google-fonts/`. The site no longer depends on the staging server.

## 2. Combined assets

Hummingbird's *Combine* was enabled, but the files it served had been built before the last settings change: the home page still loaded **45 separate stylesheets**, all render-blocking. Clearing the optimized assets let Hummingbird rebuild them.

| Page | Stylesheet links |
|---|---|
| Home (FR and EN) | 45 → **16** |
| Menu — Pizzas | 47 → **18** |

## 3. Critical CSS

The dashboard reported critical CSS as *Optimized*, yet no page contained it, and the mode was *Load on user interaction*, which holds the full stylesheet back until the first tap or scroll. I switched to **Above the fold · Load asynchronously** and regenerated. Inner pages (blog, contact, about, FAQ) now inline their above-the-fold CSS and load the rest without blocking the first paint. The home page and menu archives are served from the combined files in 2.

## 4. Delay JavaScript

The home page shipped **1.8 MB of JavaScript**: the TikTok feed pulls in React and the WordPress component library (~400 KB), plus Google Tag Manager, accessiBe, CookieYes and analytics. I enabled **Delay JavaScript** and excluded everything the first paint depends on:

- **Plugins and theme:** Elementor, Elementor Pro, Happy Addons (+ Pro), JetEngine, JetPopup, JetSmartFilters, MLR Menu Core, Smush Pro, WPConsent, Polylang, Hello Elementor Child
- **Core files:** jQuery, jQuery Migrate, jQuery UI Core, `wp-hooks`, `wp-i18n`, imagesLoaded, hoverIntent

Everything else loads on the visitor's first interaction (or after 20 s). I verified FR and EN home, the blog, contact and a menu page as a logged-out mobile visitor with no interaction: hero headline, sliders, sticky header, mobile menu and menu items all render on load, with no console errors. Tag Manager, the consent banner and accessiBe start on the first scroll or tap.

**Result** — Total Blocking Time **0 ms** on every page tested; first-load JavaScript 1.80 MB → **331 KB**.

## 5. Page cache

The host's static cache serves a page in **0.34 s** versus ~1.2 s for a PHP render. Raising the lifetime from 1 to **24 hours** keeps the bilingual menu and location pages cached between visits. Content edits still purge the affected pages automatically through H+L Render Guard.
