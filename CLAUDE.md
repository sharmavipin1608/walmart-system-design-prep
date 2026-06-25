# CLAUDE.md — Walmart System Design Interview Prep

## Project Goal
Generate 5 production-quality HTML system design documents for Walmart Global Tech senior/staff interview prep. Each file covers one highly-asked system design problem using a consistent 8-step framework and visual design system.

---

## Reference File
`walmart-inventory-system-design.html` is the **gold standard** for all output.
- Match its structure, CSS variables, component patterns, and tone exactly.
- When in doubt, open it and replicate.

---

## The 8-Step Framework
Every system design HTML file must follow these steps **in order**, each as a named section with a sidebar nav item:

| Step | ID | Title |
|------|----|-------|
| 01 | `features` | Feature Selection |
| 02 | `nfr` | Non-Functional Requirements |
| 03 | `estimation` | Back of Envelope Estimation |
| 04 | `entities` | Core Entities |
| 05 | `api` | API Design |
| 06 | `hld` | High Level Design |
| 07 | `datamodel` | Data Model |
| 08 | `tradeoffs` | Tradeoffs & Deep Dives |

### Step-by-step rules

**01 — Feature Selection**
- Present 5-6 candidate features, select 3 core ones
- Each feature gets a card with: feature number, name, description
- Include an explicit "Below the Line" block listing scoped-out features with a note on why to mention them
- Interview tip callout: keep interviewer engaged in selection process

**02 — Non-Functional Requirements**
- Map each NFR to a specific feature (not generic system-wide lists)
- Use the `nfr-grid` card layout: feature label, NFR title, requirement description
- Include "Below the Line" block for out-of-scope NFRs
- Interview tip callout: per-feature mapping is a staff-level signal

**03 — Back of Envelope Estimation**
- 4 cards: Assumptions, Derived Numbers, Peak Load, Conclusions
- Always calculate: Write RPS, Read RPS, Storage/day, Bandwidth
- Conclusions must justify component choices (e.g. "100K RPS → Redis cache needed")
- Interview tip callout: numbers justify architecture decisions

**04 — Core Entities**
- 4–6 entities in a 2-column grid of cards
- Each card: emoji icon, entity name (colored by role), description, key fields in mono font
- End with a callout explaining the key relationship between entities (use an analogy)
- Interview tip: define entities before API — anchors data model and API shapes

**05 — API Design**
- Group endpoints by feature with a mono subheader per feature
- Each endpoint: method badge (color-coded), path, description, request/response JSON with syntax highlighting
- Always include idempotency_key on mutation endpoints
- End with a warn callout about idempotency

**06 — High Level Design**
- Build INCREMENTALLY — one diagram per feature, then a merged full diagram
- Each feature subsection: mono subheader in feature color, arch-diagram, flow description card
- Feature 1: minimal components only
- Feature 2: label as "adds to Feature 1", show only new components
- Feature 3: label as "adds to Feature 1", show only new components
- End with "Merged Full Architecture" diagram showing all components
- Interview tip callout: incremental building shows reasoning

**07 — Data Model**
- One table per storage layer (e.g. Postgres, Kafka/ClickHouse, Redis)
- Table columns: Column, Type, Notes
- Mono subheader per table showing storage engine and role
- Focus only on fields that drive design decisions: shard keys, version columns, TTLs, indexes
- Interview tip: don't list every field

**08 — Tradeoffs & Deep Dives**
- 3 deep dives minimum, each triggered by a specific NFR
- Each deep dive structure:
  - NFR label in mono accent font
  - Problem question as bold heading
  - BAD solution card (red left border): naive approach + why it fails
  - GOOD solution card (yellow left border): better but still has tradeoffs
  - GREAT solution card (green left border): production-grade answer
- End with a Walmart-specific callout relevant to the problem domain
- Interview tip callout: NFR-driven deep dives with escalation pattern

---

## Design System

### Color Palette (CSS variables — copy exactly)
```css
--bg:        #0d0f14;
--surface:   #151820;
--surface2:  #1c2030;
--border:    #252a3a;
--accent:    #4f8ef7;   /* blue — primary, Feature 1 */
--accent2:   #7c5cfc;   /* purple — Feature 3, entities */
--green:     #3dd68c;   /* Feature 2, good solutions */
--yellow:    #f5c542;   /* Kafka, warn callouts */
--red:       #f76b6b;   /* bad solutions, below-the-line */
--muted:     #5a6480;
--text:      #e2e8f0;
--text-dim:  #8899bb;
--mono:      'JetBrains Mono', monospace;
--sans:      'Inter', sans-serif;
```

### Typography
- Import from Google Fonts: `JetBrains Mono` (400, 600) + `Inter` (300, 400, 500, 600, 700)
- Display/hero: Inter 700, 28px, letter-spacing -0.02em
- Section titles: Inter 600, 16px
- Body: Inter 400, 14px, line-height 1.6
- Code/mono labels: JetBrains Mono, 11–12px
- Section numbers: JetBrains Mono 11px in accent color

### Layout
- Fixed left sidebar: 220px wide, sticky, full height
- Main content: flex-1, max-width 900px, padding 32px 40px
- Hero: full width, gradient background, border-bottom

### Key Component Patterns

**Section header:**
```html
<div class="section-header">
  <span class="section-num">01</span>
  <span class="section-title">Feature Selection</span>
</div>
```

**Feature cards (3-column grid):**
```html
<div class="feature-grid">
  <div class="feature-card f1">...</div>  <!-- blue top border -->
  <div class="feature-card f2">...</div>  <!-- green top border -->
  <div class="feature-card f3">...</div>  <!-- purple top border -->
</div>
```

**Callout variants:**
```html
<div class="callout">...</div>           <!-- blue — interview tip -->
<div class="callout warn">...</div>      <!-- yellow — warning -->
<div class="callout good">...</div>      <!-- green — positive signal -->
```

**Architecture boxes:**
```html
<div class="arch-box box-blue">...</div>
<div class="arch-box box-green">...</div>
<div class="arch-box box-purple">...</div>
<div class="arch-box box-yellow">...</div>
<div class="arch-box box-red">...</div>
<div class="arch-box box-gray">...</div>
```

**API method badges:**
```html
<span class="method method-get">GET</span>
<span class="method method-post">POST</span>
<span class="method method-put">PUT</span>
<span class="method method-del">DELETE</span>
```

**Bad/Good/Great deep dive cards** (inline styles, not classes):
- BAD: `border-left: 3px solid var(--red)`, red mono label prefix `❌ BAD —`
- GOOD: `border-left: 3px solid var(--yellow)`, yellow mono label prefix `⚠ GOOD —`
- GREAT: `border-left: 3px solid var(--green)`, green mono label prefix `✅ GREAT —`

**Below the Line block** (inline styles):
```html
<div style="background: var(--surface); border: 1px solid var(--border); border-radius: 8px; padding: 14px 16px;">
  <div style="font-family: var(--mono); font-size: 10px; color: var(--red); ...">⬇ Below the Line (Out of Scope)</div>
  ...
</div>
```

### Sidebar Navigation JS
```javascript
const sections = ['features','nfr','estimation','entities','api','hld','datamodel','tradeoffs'];
const navItems = document.querySelectorAll('.nav-item');
window.addEventListener('scroll', () => {
  let current = '';
  sections.forEach(id => {
    const el = document.getElementById(id);
    if (el && el.getBoundingClientRect().top < 120) current = id;
  });
  navItems.forEach((item, i) => {
    item.classList.toggle('active', sections[i] === current);
  });
}, { passive: true });
```

---

## File Naming Convention
```
walmart-{kebab-case-problem-name}-system-design.html
```
Examples:
- `walmart-inventory-system-design.html` ✅ (already done)
- `walmart-checkout-payment-system-design.html`
- `walmart-product-search-system-design.html`
- `walmart-fraud-detection-system-design.html`
- `walmart-notification-system-design.html`

---

## Research Instructions for Each Problem
Before generating each HTML file, web-search for:
1. Walmart-specific context for that problem domain (e.g. "Walmart payment system scale", "Walmart search architecture")
2. Recent interview reports mentioning that problem at Walmart Global Tech
3. Any known Walmart engineering blog posts on the topic

Use findings to add Walmart-specific callouts in the deep dives section — this differentiates the answer from generic system design.

---

## GitHub Pages Setup Instructions

After all 5 HTML files + index.html are generated:

### 1. Initialize repo
```bash
git init
git add .
git commit -m "Initial commit: Walmart system design interview prep"
```

### 2. Create GitHub repo and push
```bash
gh repo create walmart-system-design-prep --public
git remote add origin https://github.com/{your-username}/walmart-system-design-prep.git
git branch -M main
git push -u origin main
```

### 3. Enable GitHub Pages
Option A (via CLI):
```bash
gh api repos/{your-username}/walmart-system-design-prep/pages \
  --method POST \
  --field source='{"branch":"main","path":"/"}'
```

Option B (via UI):
- Go to repo → Settings → Pages
- Source: Deploy from branch → `main` → `/ (root)`
- Save

### 4. Access your site
```
https://{your-username}.github.io/walmart-system-design-prep/
```

### File structure expected by GitHub Pages
```
/
├── index.html                                    ← landing page (hero cards)
├── walmart-inventory-system-design.html
├── walmart-checkout-payment-system-design.html
├── walmart-product-search-system-design.html
├── walmart-fraud-detection-system-design.html
├── walmart-notification-system-design.html
└── CLAUDE.md                                     ← this file
```

---

## Tone & Interview Tips Style
- Every section starts with an `Interview tip` callout
- Tips are action-oriented: "Don't just list X — do Y instead"
- Walmart-specific context always goes in a `callout good` at the end of deep dives
- Language is direct, staff-level: no hedging, confident architecture decisions with stated tradeoffs
