# Red Derby Ventures Portfolio Site Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build and deploy a static single-page portfolio website for Red Derby Ventures LLC showcasing 4 work items (MyCity311, My Next Book, My Next Thrift, Consulting) on Railway.

**Architecture:** Single static HTML page with a separate CSS tokens file. No JavaScript framework, no build step. Railway serves the static files via an nginx Docker container. The design uses an editorial light theme (off-white background, red accent) with a 2×2 card grid.

**Tech Stack:** HTML5, CSS3 (custom properties / CSS variables), nginx (via Docker), Railway

---

## File Structure

```
Red-Derby-Ventures/
├── index.html              # Single page — nav, hero, 4-card grid
├── assets/
│   └── css/
│       └── tokens.css      # CSS custom properties (design tokens)
├── Dockerfile              # nginx static file server for Railway
├── nginx.conf              # nginx config
└── docs/
    └── superpowers/
        ├── specs/
        │   └── 2026-05-26-portfolio-site-design.md
        └── plans/
            └── 2026-05-26-portfolio-site.md
```

---

### Task 1: Project scaffold — tokens, Dockerfile, nginx

**Files:**
- Create: `assets/css/tokens.css`
- Create: `Dockerfile`
- Create: `nginx.conf`

- [ ] **Step 1: Create the CSS tokens file**

Create `assets/css/tokens.css`:

```css
:root {
  /* Primitive */
  --color-red-600: #D42B2B;
  --color-red-700: #B82424;
  --color-neutral-50: #FAFAF8;
  --color-neutral-100: #F4F4F0;
  --color-neutral-200: #E8E8E4;
  --color-neutral-300: #F0F0EC;
  --color-neutral-500: #999999;
  --color-neutral-600: #555555;
  --color-neutral-700: #444444;
  --color-neutral-900: #111111;
  --color-white: #ffffff;
  --color-blue-linkedin: #0A66C2;

  /* Semantic */
  --color-primary: var(--color-red-600);
  --color-primary-hover: var(--color-red-700);
  --color-bg: var(--color-neutral-50);
  --color-surface: var(--color-white);
  --color-border: var(--color-neutral-200);
  --color-border-inner: var(--color-neutral-300);
  --color-text: var(--color-neutral-900);
  --color-text-secondary: var(--color-neutral-600);
  --color-text-muted: var(--color-neutral-500);
  --color-tag-bg: var(--color-neutral-100);

  /* Component */
  --btn-primary-bg: var(--color-primary);
  --btn-primary-bg-hover: var(--color-primary-hover);
  --btn-primary-text: var(--color-white);
  --btn-secondary-border: var(--color-primary);
  --btn-secondary-text: var(--color-primary);
  --btn-secondary-bg-hover: var(--color-primary);
  --btn-secondary-text-hover: var(--color-white);
  --card-bg: var(--color-surface);
  --card-border: var(--color-border);
  --card-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
  --card-shadow-hover: 0 8px 28px rgba(0, 0, 0, 0.09);

  /* Typography */
  --font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
  --font-size-xs: 11px;
  --font-size-sm: 12px;
  --font-size-base: 14px;
  --font-size-md: 17px;
  --font-size-lg: 19px;
  --font-size-xl: 48px;
  --font-weight-regular: 400;
  --font-weight-medium: 500;
  --font-weight-semibold: 600;
  --font-weight-bold: 700;
  --font-weight-extrabold: 800;

  /* Spacing */
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --space-5: 18px;
  --space-6: 20px;
  --space-7: 24px;
  --space-8: 28px;
  --space-10: 32px;
  --space-12: 48px;
  --space-14: 56px;
  --space-18: 72px;
  --space-20: 80px;

  /* Radius */
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 10px;
  --radius-xl: 14px;
  --radius-full: 9999px;
}
```

- [ ] **Step 2: Create the Dockerfile**

Create `Dockerfile`:

```dockerfile
FROM nginx:alpine
COPY . /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

- [ ] **Step 3: Create nginx.conf**

Create `nginx.conf`:

```nginx
server {
    listen 80;
    server_name _;
    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    gzip on;
    gzip_types text/html text/css application/javascript;
}
```

- [ ] **Step 4: Verify Docker builds locally**

```bash
cd ~/Red-Derby-Ventures
docker build -t rdv-portfolio .
docker run -p 8080:80 rdv-portfolio
```

Expected: nginx starts, `http://localhost:8080` returns 404 (no index.html yet — that's fine at this stage).

Stop the container with `Ctrl+C`.

- [ ] **Step 5: Commit**

```bash
cd ~/Red-Derby-Ventures
git add assets/ Dockerfile nginx.conf
git commit -m "feat: project scaffold — tokens, Dockerfile, nginx config"
```

---

### Task 2: Nav and hero section

**Files:**
- Create: `index.html`

- [ ] **Step 1: Create index.html with nav and hero**

Create `index.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Red Derby Ventures</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="assets/css/tokens.css">
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      font-family: var(--font-family);
      background: var(--color-bg);
      color: var(--color-text);
      min-height: 100vh;
    }

    /* ── Nav ── */
    nav {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: var(--space-6) var(--space-12);
      border-bottom: 1px solid var(--color-border);
      background: var(--color-bg);
    }
    .nav-logo {
      display: flex;
      align-items: center;
      gap: var(--space-3);
      text-decoration: none;
    }
    .nav-dot {
      width: 22px;
      height: 22px;
      background: var(--color-primary);
      border-radius: var(--radius-full);
      flex-shrink: 0;
    }
    .nav-name {
      font-size: var(--font-size-base);
      font-weight: var(--font-weight-bold);
      letter-spacing: 0.08em;
      text-transform: uppercase;
      color: var(--color-text);
    }
    .nav-links {
      display: flex;
      gap: var(--space-10);
      list-style: none;
    }
    .nav-links a {
      font-size: 13px;
      font-weight: var(--font-weight-medium);
      color: var(--color-text-secondary);
      text-decoration: none;
    }
    .nav-links a:hover { color: var(--color-text); }

    /* ── Hero ── */
    .hero {
      padding: var(--space-18) var(--space-12) var(--space-14);
      max-width: 900px;
      margin: 0 auto;
    }
    .hero-eyebrow {
      font-size: var(--font-size-xs);
      font-weight: var(--font-weight-bold);
      letter-spacing: 0.12em;
      text-transform: uppercase;
      color: var(--color-primary);
      margin-bottom: var(--space-4);
    }
    .hero-title {
      font-size: var(--font-size-xl);
      font-weight: var(--font-weight-extrabold);
      line-height: 1.1;
      color: var(--color-text);
      margin-bottom: var(--space-6);
      letter-spacing: -0.02em;
    }
    .hero-subtitle {
      font-size: var(--font-size-md);
      color: var(--color-text-secondary);
      max-width: 520px;
      line-height: 1.6;
    }
  </style>
</head>
<body>

  <nav>
    <a class="nav-logo" href="/">
      <div class="nav-dot"></div>
      <span class="nav-name">Red Derby Ventures</span>
    </a>
    <ul class="nav-links">
      <li><a href="#work">Products</a></li>
      <li><a href="#consulting">Consulting</a></li>
      <li><a href="mailto:smithar106@gmail.com,ashleerthomas@gmail.com?subject=Hello — Red Derby Ventures">Contact</a></li>
    </ul>
  </nav>

  <section class="hero">
    <div class="hero-eyebrow">Red Derby Ventures LLC</div>
    <h1 class="hero-title">Building software<br>that matters.</h1>
    <p class="hero-subtitle">We create consumer apps and civic technology — and advise organizations on the challenges shaping our world.</p>
  </section>

</body>
</html>
```

- [ ] **Step 2: Verify in browser**

```bash
open index.html
```

Expected: nav with red dot + wordmark on left, links on right; hero with red eyebrow, large headline, subtitle.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: nav and hero section"
```

---

### Task 3: Cards grid — shared styles

**Files:**
- Modify: `index.html` — add card grid CSS and section wrapper (before `</body>`)

- [ ] **Step 1: Add card grid styles inside the `<style>` block in index.html**

Add the following CSS inside the existing `<style>` tag, before `</style>`:

```css
    /* ── Cards Section ── */
    .cards-section {
      padding: 0 var(--space-12) var(--space-20);
      max-width: 1100px;
      margin: 0 auto;
    }
    .section-label {
      font-size: var(--font-size-xs);
      font-weight: var(--font-weight-bold);
      letter-spacing: 0.12em;
      text-transform: uppercase;
      color: var(--color-text-muted);
      margin-bottom: var(--space-7);
    }
    .grid {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: var(--space-7);
    }

    /* ── Card ── */
    .card {
      background: var(--card-bg);
      border: 1px solid var(--card-border);
      border-radius: var(--radius-xl);
      overflow: hidden;
      box-shadow: var(--card-shadow);
      transition: box-shadow 0.2s ease, transform 0.2s ease;
      display: flex;
      flex-direction: column;
    }
    .card:hover {
      box-shadow: var(--card-shadow-hover);
      transform: translateY(-2px);
    }
    .card-header {
      padding: var(--space-8) var(--space-8) var(--space-6);
      border-bottom: 1px solid var(--color-border-inner);
    }
    .card-icon {
      width: 44px;
      height: 44px;
      border-radius: var(--radius-lg);
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 20px;
      margin-bottom: var(--space-4);
    }
    .card-category {
      font-size: var(--font-size-xs);
      font-weight: var(--font-weight-bold);
      letter-spacing: 0.1em;
      text-transform: uppercase;
      color: var(--color-primary);
      margin-bottom: 6px;
    }
    .card-title {
      font-size: var(--font-size-lg);
      font-weight: var(--font-weight-extrabold);
      color: var(--color-text);
      letter-spacing: -0.01em;
    }
    .card-body {
      padding: var(--space-6) var(--space-8) var(--space-7);
      flex: 1;
    }
    .card-desc {
      font-size: var(--font-size-base);
      color: var(--color-text-secondary);
      line-height: 1.65;
      margin-bottom: var(--space-5);
    }
    .card-tags {
      display: flex;
      flex-wrap: wrap;
      gap: 6px;
    }
    .tag {
      font-size: var(--font-size-xs);
      font-weight: var(--font-weight-semibold);
      padding: var(--space-1) var(--space-3);
      border-radius: var(--radius-full);
      background: var(--color-tag-bg);
      color: var(--color-text-secondary);
      letter-spacing: 0.02em;
    }

    /* ── Card Footer ── */
    .card-footer {
      padding: var(--space-5) var(--space-8);
      border-top: 1px solid var(--color-border-inner);
      display: flex;
      align-items: center;
      gap: var(--space-3);
    }
    .btn-primary {
      font-size: var(--font-size-sm);
      font-weight: var(--font-weight-bold);
      color: var(--btn-primary-text);
      background: var(--btn-primary-bg);
      border: none;
      border-radius: var(--radius-md);
      padding: var(--space-2) var(--space-4);
      cursor: pointer;
      text-decoration: none;
      letter-spacing: 0.02em;
      white-space: nowrap;
      transition: background 0.15s ease;
    }
    .btn-primary:hover { background: var(--btn-primary-bg-hover); }
    .btn-secondary {
      font-size: var(--font-size-sm);
      font-weight: var(--font-weight-semibold);
      color: var(--btn-secondary-text);
      background: transparent;
      border: 1.5px solid var(--btn-secondary-border);
      border-radius: var(--radius-md);
      padding: 7px var(--space-4);
      cursor: pointer;
      text-decoration: none;
      letter-spacing: 0.02em;
      white-space: nowrap;
      transition: background 0.15s ease, color 0.15s ease;
    }
    .btn-secondary:hover {
      background: var(--btn-secondary-bg-hover);
      color: var(--btn-secondary-text-hover);
    }
    .coming-soon {
      font-size: var(--font-size-xs);
      color: var(--color-text-muted);
      font-weight: var(--font-weight-medium);
    }

    /* ── Consulting footer ── */
    .consulting-footer {
      padding: var(--space-5) var(--space-8);
      border-top: 1px solid var(--color-border-inner);
    }
    .consulting-cta { margin-bottom: var(--space-4); }
    .consulting-profiles {
      display: flex;
      align-items: center;
      gap: var(--space-4);
    }
    .profiles-label {
      font-size: var(--font-size-xs);
      color: var(--color-text-muted);
      font-weight: var(--font-weight-medium);
    }
    .profile-link {
      display: flex;
      align-items: center;
      gap: 6px;
      font-size: var(--font-size-sm);
      font-weight: var(--font-weight-semibold);
      color: var(--color-neutral-700);
      text-decoration: none;
    }
    .profile-link:hover { color: var(--color-text); }
    .li-icon {
      width: 20px;
      height: 20px;
      background: var(--color-blue-linkedin);
      border-radius: var(--radius-sm);
      display: flex;
      align-items: center;
      justify-content: center;
      color: var(--color-white);
      font-size: var(--font-size-xs);
      font-weight: var(--font-weight-extrabold);
      flex-shrink: 0;
    }
```

- [ ] **Step 2: Add the section wrapper HTML before `</body>`**

Add after the `</section>` (hero) closing tag and before `</body>`:

```html
  <section class="cards-section" id="work">
    <div class="section-label">Our Work</div>
    <div class="grid">
      <!-- cards go here — Task 4 -->
    </div>
  </section>
```

- [ ] **Step 3: Open in browser and verify**

```bash
open index.html
```

Expected: hero section visible, empty space below where the grid will go. No visual regressions on nav/hero.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: card grid shared styles and section wrapper"
```

---

### Task 4: Product cards (MyCity311, My Next Book, My Next Thrift)

**Files:**
- Modify: `index.html` — replace `<!-- cards go here -->` comment with the 3 product cards

- [ ] **Step 1: Replace the `<!-- cards go here — Task 4 -->` comment with the 3 product cards**

Replace `<!-- cards go here — Task 4 -->` with:

```html
      <!-- MyCity311 -->
      <article class="card">
        <div class="card-header">
          <div class="card-icon" style="background:#FFF0F0;">🏙️</div>
          <div class="card-category">Civic Tech · iOS App</div>
          <h2 class="card-title">MyCity311</h2>
        </div>
        <div class="card-body">
          <p class="card-desc">AI-powered 311 reporting for 50 US cities. Residents submit requests in seconds; cities get cleaner data and faster resolution.</p>
          <div class="card-tags">
            <span class="tag">iOS</span>
            <span class="tag">50 Cities</span>
            <span class="tag">AI</span>
            <span class="tag">Government</span>
          </div>
        </div>
        <div class="card-footer">
          <a class="btn-primary" href="https://home.mycity311.co" target="_blank" rel="noopener">Learn more</a>
          <span class="coming-soon">App coming soon</span>
        </div>
      </article>

      <!-- My Next Book -->
      <article class="card">
        <div class="card-header">
          <div class="card-icon" style="background:#FFF5E6;">📚</div>
          <div class="card-category">Consumer Tech · iOS App</div>
          <h2 class="card-title">My Next Book</h2>
        </div>
        <div class="card-body">
          <p class="card-desc">AI book discovery built around your reading identity. A quiz surfaces your literary archetype, then surfaces books you'll actually finish.</p>
          <div class="card-tags">
            <span class="tag">iOS</span>
            <span class="tag">AI</span>
            <span class="tag">Books</span>
            <span class="tag">Subscriptions</span>
          </div>
        </div>
        <div class="card-footer">
          <a class="btn-primary" href="https://mynextbook.me" target="_blank" rel="noopener">Learn more</a>
          <a class="btn-secondary" href="https://apps.apple.com/app/id6763831526" target="_blank" rel="noopener">Download the app</a>
        </div>
      </article>

      <!-- My Next Thrift -->
      <article class="card">
        <div class="card-header">
          <div class="card-icon" style="background:#E8F8F0;">👗</div>
          <div class="card-category">Consumer Tech · iOS App</div>
          <h2 class="card-title">My Next Thrift</h2>
        </div>
        <div class="card-body">
          <p class="card-desc">Thrift shopping reimagined as curation. 87,000+ items surfaced by your style DNA — secondhand finds that feel like they were pulled just for you.</p>
          <div class="card-tags">
            <span class="tag">iOS</span>
            <span class="tag">Fashion</span>
            <span class="tag">AI</span>
            <span class="tag">Secondhand</span>
          </div>
        </div>
        <div class="card-footer">
          <a class="btn-primary" href="https://mynextthrift.app" target="_blank" rel="noopener">Learn more</a>
          <span class="coming-soon">App coming soon</span>
        </div>
      </article>

      <!-- Consulting — Task 5 -->
```

- [ ] **Step 2: Open in browser and verify all 3 cards render correctly**

```bash
open index.html
```

Expected: 3 cards in a 2×2 grid (with the 4th spot empty). MyCity311 and My Next Thrift show "App coming soon". My Next Book shows both buttons. Hover lifts each card slightly.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: product cards — MyCity311, My Next Book, My Next Thrift"
```

---

### Task 5: Consulting card

**Files:**
- Modify: `index.html` — replace `<!-- Consulting — Task 5 -->` with the consulting card

- [ ] **Step 1: Replace `<!-- Consulting — Task 5 -->` with the consulting card**

```html
      <!-- Consulting -->
      <article class="card" id="consulting">
        <div class="card-header">
          <div class="card-icon" style="background:#FFF0F0;">🌍</div>
          <div class="card-category">Advisory Services</div>
          <h2 class="card-title">Consulting</h2>
        </div>
        <div class="card-body">
          <p class="card-desc">Strategic advisory at the intersection of technology, policy, and cities. We work with organizations navigating complex challenges in three focus areas.</p>
          <div class="card-tags">
            <span class="tag">Climate Policy</span>
            <span class="tag">International Relations</span>
            <span class="tag">Urban Solutions</span>
          </div>
        </div>
        <div class="consulting-footer">
          <div class="consulting-cta">
            <a class="btn-primary" href="mailto:smithar106@gmail.com,ashleerthomas@gmail.com?subject=Consulting Inquiry — Red Derby Ventures">Get in touch</a>
          </div>
          <div class="consulting-profiles">
            <span class="profiles-label">Founders</span>
            <a class="profile-link" href="https://www.linkedin.com/in/arthursmith11/" target="_blank" rel="noopener">
              <div class="li-icon">in</div>
              Arthur Smith
            </a>
            <a class="profile-link" href="https://www.linkedin.com/in/ashthomas1/" target="_blank" rel="noopener">
              <div class="li-icon">in</div>
              Ashlee Thomas
            </a>
          </div>
        </div>
      </article>
```

- [ ] **Step 2: Verify full 2×2 grid in browser**

```bash
open index.html
```

Expected: all 4 cards visible in a 2×2 grid. Consulting card shows "Get in touch" button + two LinkedIn profile links with blue `in` icons.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: consulting card with mailto CTA and LinkedIn profiles"
```

---

### Task 6: Footer

**Files:**
- Modify: `index.html` — add footer HTML and CSS

- [ ] **Step 1: Add footer CSS inside the `<style>` block**

Add before `</style>`:

```css
    /* ── Footer ── */
    footer {
      border-top: 1px solid var(--color-border);
      padding: var(--space-8) var(--space-12);
      display: flex;
      align-items: center;
      justify-content: space-between;
    }
    .footer-left {
      display: flex;
      align-items: center;
      gap: var(--space-3);
    }
    .footer-dot {
      width: 14px;
      height: 14px;
      background: var(--color-primary);
      border-radius: var(--radius-full);
      flex-shrink: 0;
    }
    .footer-name {
      font-size: var(--font-size-xs);
      font-weight: var(--font-weight-bold);
      letter-spacing: 0.08em;
      text-transform: uppercase;
      color: var(--color-text-muted);
    }
    .footer-right {
      font-size: var(--font-size-xs);
      color: var(--color-text-muted);
    }
```

- [ ] **Step 2: Add footer HTML before `</body>`**

Add after the closing `</section>` of the cards section:

```html
  <footer>
    <div class="footer-left">
      <div class="footer-dot"></div>
      <span class="footer-name">Red Derby Ventures LLC</span>
    </div>
    <span class="footer-right">&copy; 2026 Red Derby Ventures LLC. All rights reserved.</span>
  </footer>
```

- [ ] **Step 3: Verify in browser**

```bash
open index.html
```

Expected: thin footer with red dot + name on left, copyright on right. Clean and minimal.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: footer"
```

---

### Task 7: Responsive styles (mobile)

**Files:**
- Modify: `index.html` — add media query CSS

- [ ] **Step 1: Add responsive CSS inside the `<style>` block**

Add before `</style>`:

```css
    /* ── Responsive ── */
    @media (max-width: 768px) {
      nav {
        padding: var(--space-4) var(--space-6);
        flex-wrap: wrap;
        gap: var(--space-3);
      }
      .nav-links { gap: var(--space-6); }

      .hero {
        padding: var(--space-12) var(--space-6) var(--space-10);
      }
      .hero-title { font-size: 36px; }

      .cards-section { padding: 0 var(--space-6) var(--space-12); }
      .grid { grid-template-columns: 1fr; }

      footer {
        padding: var(--space-6);
        flex-direction: column;
        gap: var(--space-3);
        text-align: center;
      }
    }
```

- [ ] **Step 2: Verify responsive layout**

Open `index.html` in browser. Use DevTools (F12 → toggle device toolbar) and set width to 375px (iPhone).

Expected: single-column card stack, smaller hero title, nav wraps cleanly, footer stacks vertically.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: responsive mobile styles"
```

---

### Task 8: Deploy to Railway

- [ ] **Step 1: Push to GitHub**

```bash
cd ~/Red-Derby-Ventures
gh auth switch --user smithar106
git remote add origin https://github.com/smithar106/Red-Derby-Ventures.git
git push -u origin main
```

Expected: code pushed to `smithar106/Red-Derby-Ventures` on GitHub.

- [ ] **Step 2: Connect Railway to the GitHub repo**

In the Railway dashboard (the project is already set up at `bed5cd7e-4d10-49c1-a6ee-553154ad25d2`):
1. Go to the service settings
2. Set **Source** → GitHub → `smithar106/Red-Derby-Ventures`
3. Set **Branch** → `main`
4. Railway will auto-detect the `Dockerfile` and build it

- [ ] **Step 3: Verify the Railway deploy**

Once the build completes, open the Railway-provided URL.

Expected: full portfolio page loads — nav, hero, 2×2 card grid, footer. All links work. MyCity311 and My Next Thrift show "App coming soon".

- [ ] **Step 4: (Optional) Set a custom domain**

In Railway service settings → Networking → add a custom domain if desired (e.g., `redderbyventures.com`).

- [ ] **Step 5: Final commit**

```bash
git commit --allow-empty -m "chore: deployed to Railway"
```

---

## Self-Review

**Spec coverage check:**

| Spec requirement | Task |
|-----------------|------|
| Editorial light design system | Task 1 (tokens.css) |
| Nav with logo + links | Task 2 |
| Hero with eyebrow, headline, subtitle | Task 2 |
| 2×2 card grid | Task 3 |
| MyCity311 card — Learn more + coming soon | Task 4 |
| My Next Book card — Learn more + Download | Task 4 |
| My Next Thrift card — Learn more + coming soon | Task 4 |
| Consulting card — Advisory Services label | Task 5 |
| Consulting mailto CTA | Task 5 |
| LinkedIn profiles for both founders | Task 5 |
| Footer | Task 6 |
| Mobile responsive | Task 7 |
| Railway deploy via Dockerfile | Task 8 |

**All spec requirements covered. No placeholders. Types/classnames consistent across all tasks.**
