# Red Derby Ventures — Portfolio Site Design Spec

**Date:** 2026-05-26  
**Repo:** smithar106/Red-Derby-Ventures  
**Deploy:** Railway  

---

## Overview

A single-page portfolio website for Red Derby Ventures LLC showcasing 4 work items: 3 products and a consulting practice. The site is public-facing — potential customers, city partners, consulting clients, and investors may land here.

---

## Design System

**Direction:** Editorial Light  
**Background:** `#FAFAF8` (off-white)  
**Primary accent:** `#D42B2B` (red)  
**Text:** `#111` (near-black)  
**Secondary text:** `#555`  
**Muted text:** `#999`, `#bbb`  
**Card background:** `#ffffff`  
**Card border:** `#E8E8E4`  
**Font:** Inter (system fallback: -apple-system, BlinkMacSystemFont, sans-serif)  

### Token Architecture (three-layer)

```css
/* Primitive */
--color-red-600: #D42B2B;
--color-neutral-50: #FAFAF8;
--color-neutral-900: #111111;

/* Semantic */
--color-primary: var(--color-red-600);
--color-bg: var(--color-neutral-50);
--color-text: var(--color-neutral-900);

/* Component */
--btn-primary-bg: var(--color-primary);
--card-border: #E8E8E4;
```

---

## Page Structure

Single HTML page. No routing, no JavaScript framework. Static HTML + CSS deployed via Railway.

```
nav
hero
cards-section (2×2 grid)
footer (optional — can be added later)
```

---

## Components

### Nav
- Left: red dot + "RED DERBY VENTURES" wordmark in uppercase
- Right: nav links — Products, Consulting, About, Contact
- Border-bottom separates from page body
- Background: `#FAFAF8`

### Hero
- Eyebrow: "Red Derby Ventures LLC" in red uppercase
- Headline: "Building software that matters." (2 lines, 48px, 800 weight)
- Subhead: "We create consumer apps and civic technology — and advise organizations on the challenges shaping our world."
- Max-width 900px, centered

### Cards Grid
- 2×2 grid, 24px gap
- Max-width 1100px
- Section label: "OUR WORK" in small uppercase gray

### Card Anatomy (product cards)
```
card-header
  icon (44×44px rounded, colored bg)
  category label (red, uppercase, 11px)
  title (19px, 800 weight)
card-body
  description (14px, #555)
  tags (pill chips, #F4F4F0 bg)
card-footer
  btn-primary "Learn more" → marketing site
  btn-secondary "Download the app" → App Store (or "App coming soon" text if not live)
```

### Card Anatomy (consulting card)
```
card-header
  icon (🌍, red-tinted bg)
  category: "Advisory Services"
  title: "Consulting"
card-body
  description
  tags: Climate Policy · International Relations · Urban Solutions
consulting-footer
  btn-primary "Get in touch" → mailto both founders
  founder LinkedIn profiles (Arthur + Ashlee)
```

---

## Cards Content

| Card | Category | Marketing Site | App Store |
|------|----------|---------------|-----------|
| MyCity311 | Civic Tech · iOS App | https://home.mycity311.co | Not live — show "App coming soon" |
| My Next Book | Consumer Tech · iOS App | https://mynextbook.me | https://apps.apple.com/app/id6763831526 |
| My Next Thrift | Consumer Tech · iOS App | https://mynextthrift.app | Not live — show "App coming soon" |
| Consulting | Advisory Services | — | — |

### Consulting CTAs
- **Email:** `mailto:smithar106@gmail.com,ashleerthomas@gmail.com?subject=Consulting Inquiry — Red Derby Ventures`
- **Arthur LinkedIn:** https://www.linkedin.com/in/arthursmith11/
- **Ashlee LinkedIn:** https://www.linkedin.com/in/ashthomas1/

---

## Button States

| Button | Default | Hover |
|--------|---------|-------|
| btn-primary | `#D42B2B` bg, white text | slight darken `#B82424` |
| btn-secondary | transparent, `#D42B2B` border+text | `#D42B2B` bg, white text |

---

## Interactions
- Cards: `translateY(-2px)` + elevated shadow on hover
- Buttons: color transitions on hover
- All external links: `target="_blank"`

---

## Tech Stack

| Layer | Choice | Reason |
|-------|--------|--------|
| HTML | Static HTML + CSS | No framework needed for a single marketing page |
| Styling | Vanilla CSS with CSS variables | Lightweight, design token compatible |
| Deploy | Railway | Consistent with rest of RDV stack |
| Server | Caddy or static file server via Railway | Simple static serving |

### Railway Config
- **Builder:** Nixpacks (detects static HTML) or Dockerfile with nginx/caddy
- **Start command:** serve static files from root
- Single `index.html` at project root

---

## File Structure

```
Red-Derby-Ventures/
├── index.html          # Main page
├── assets/
│   └── css/
│       └── tokens.css  # CSS custom properties
├── railway.json        # Railway config
└── docs/
    └── superpowers/
        └── specs/
            └── 2026-05-26-portfolio-site-design.md
```

---

## Future Considerations (out of scope for v1)
- Individual product detail pages
- About page with team bios
- Contact form (vs. mailto)
- App Store buttons for MyCity311 + My Next Thrift once live
