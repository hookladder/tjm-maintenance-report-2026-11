<p class="eyebrow">Technical log</p>

# Hummingbird Pro notes

Hummingbird Pro is the right optimization layer for this site: WPMU DEV, our host, builds it, and it drives the host's static cache directly. With the configuration below, the site performs well. During this cycle I documented a few limitations of the tool itself, together with the practice that keeps each one from affecting the site.

## Current configuration

| Module | Setting |
|---|---|
| Page caching | Static server · 24-hour lifetime · homepage preload |
| Combine & compress | CSS and JavaScript on · fonts off |
| Critical CSS | Above the fold · load asynchronously |
| Delay JavaScript | On · 20 s timeout · Elementor stack and core jQuery excluded · inline scripts delayed |
| Text compression | Brotli (HTML, CSS, JS) |

## Tool limitations and how we handle them

| Limitation | How we handle it |
|---|---|
| **Dashboard status can differ from the front end.** Critical CSS showed "Optimized" while no page contained it. | After any asset change, I verify the live HTML as a logged-out visitor: stylesheet count, critical CSS block, delayed scripts. |
| **Optimized copies outlive their source.** Hummingbird kept serving old copies of the font stylesheets after Elementor regenerated them. | When a plugin regenerates its CSS, I clear Hummingbird's optimized assets once, then purge the page cache. |
| **Critical CSS is generated per page, on demand.** The home page and taxonomy archives (menu categories) are not covered. | Those templates load the combined stylesheets (16 – 18 files) and still reach 91 – 100 on PageSpeed. |
| **"Load on user interaction" defers all CSS until the first tap or scroll.** It looks best in lab tests but can leave pages unstyled on first paint. | The site uses **Load asynchronously**, so every visitor and crawler gets a fully styled first paint. |
| **"Exclude inline JavaScript" breaks delayed dependencies.** Inline snippets attached to delayed files (`moment`, `wp-date`, `wp-data`) run before their file and log errors. | Inline scripts stay delayed together with their files. Analytics and the consent banner start on the first interaction. |
| **Delay JavaScript has no Elementor awareness.** Delaying Elementor's core scripts stops sliders and entrance animations from initialising. | The whole Elementor stack is on the exclusion list. After adding a plugin with a frontend script, I check the home hero as a logged-out visitor. |
| **Elementor's Clear Files & Data does not reset local Google Fonts.** | Changing the font-display setting regenerates them; I use that after any domain change. |
| **Cache lifetime is capped at 24 hours.** | Purges are event-driven through H+L Render Guard, so 24 hours is enough for this content. |
