<p class="eyebrow">Toujours Mikes × Hook + Ladder · Web Team</p>

# Web Maintenance Report — November 2026

<div class="band">
  <div><b>Property</b><span>toujoursmikes.ca (FR · EN)</span></div>
  <div><b>Window</b><span>Oct 2026 – Nov 1, 2026</span></div>
  <div><b>Stack</b><span>WordPress · Elementor Pro · Polylang · JetEngine · Happy Addons</span></div>
  <div><b>Hosting</b><span>WPMU DEV · Static Server Cache · Hummingbird Pro</span></div>
</div>

## Overview

This cycle focused on front-end performance and site health after launch. I moved the brand fonts off the staging domain, rebuilt Hummingbird's combined assets, switched critical CSS to asynchronous loading, enabled Delay JavaScript with the Elementor stack excluded and extended the page-cache lifetime. The home page now scores **98% on GTmetrix (grade A)** and **91 mobile / 100 desktop on PageSpeed**, with **zero blocking time** on every page tested. The security scan is clean.

<div class="cards">
  <div class="card good">
    <h5>GTmetrix · Home</h5>
    <div class="big">98% <small>A · 98% structure</small></div>
    <span class="delta up">▲ Performance 70% → 98%</span>
    <p>LCP 0.8 s · TBT 0 ms · CLS 0 · 1.06 MB</p>
  </div>
  <div class="card good">
    <h5>PageSpeed · Home</h5>
    <div class="big">91 <small>mobile · 100 desktop</small></div>
    <span class="delta up">▲ Mobile 54 → 91 · Desktop 94 → 100</span>
    <p>Mobile LCP 10.4 s → 3.3 s</p>
  </div>
  <div class="card good">
    <h5>Total Blocking Time</h5>
    <div class="big">0 ms</div>
    <span class="delta up">▲ from 380 – 590 ms (mobile)</span>
    <p>All pages tested, mobile and desktop</p>
  </div>
  <div class="card good">
    <h5>Wordfence scan</h5>
    <div class="big">0 <small>issues</small></div>
    <span class="delta up">23,345 files · 19,272 URLs</span>
    <p>No malware, no vulnerable components</p>
  </div>
</div>

### At a glance

| Area | Before | After |
|---|---|---|
| Home page weight (GTmetrix) | 2.63 MB · 137 requests | **1.06 MB · 53 requests** |
| JavaScript on first load | 1.80 MB | **331 KB** |
| Stylesheet requests, home | 45, all render-blocking | **16** |
| Brand fonts | Served from the staging domain | **Served from toujoursmikes.ca** |
| Critical CSS | "Optimized" but not applied | **Inlined, rest loads asynchronously** |
| Page-cache lifetime | 1 hour | **24 hours** |
| Security scan | — | **0 issues** |

> PageSpeed lab scores are simulated (slow 4G, mid-range Android) and vary a few points between runs. Real-user Core Web Vitals are a 28-day rolling average; the home page already passes them on mobile and will reflect these improvements progressively.
