# HTML Design System — Strategic Brief

Self-contained design system for the Phase 4 HTML brief. Compressed from the warm-canvas editorial system (cream + coral + dark navy trinity, slab-serif display + humanist sans body). Use exactly the tokens and components below — don't introduce new colors, new fonts, or new component types.

---

## The trinity (non-negotiable)

| Surface | Color | Use |
|---|---|---|
| **Cream canvas** | `#faf9f5` | Default page floor — anchor every section here. Never use pure white. |
| **Coral primary** | `#cc785c` | Primary CTAs, full-bleed callout bands, eyebrow labels, accent dots. **Scarce on individual elements; generous on full-bleed cards.** |
| **Dark navy** | `#181715` | Verdict cards, footer, alternating dark sections, "Don't" cards. The cream-to-dark contrast is the page's pacing rhythm. |

Never paint accent moments in a fourth color. No purple, no green sections, no saturated blue.

---

## Token reference

```css
:root {
  --primary: #cc785c;
  --primary-active: #a9583e;
  --ink: #141413;
  --body: #3d3d3a;
  --body-strong: #252523;
  --muted: #6c6a64;
  --muted-soft: #8e8b82;
  --hairline: #e6dfd8;
  --hairline-soft: #ebe6df;
  --canvas: #faf9f5;
  --surface-soft: #f5f0e8;
  --surface-card: #efe9de;
  --surface-cream-strong: #e8e0d2;
  --surface-dark: #181715;
  --surface-dark-elevated: #252320;
  --on-dark: #faf9f5;
  --on-dark-soft: #a09d96;
  --accent-amber: #e8a55a;
  --success: #5db872;
  --warning: #d4a017;
  --error: #c64545;
  --serif: "Cormorant Garamond", "Tiempos Headline", Garamond, serif;
  --sans: "Inter", -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
  --mono: "JetBrains Mono", ui-monospace, monospace;
}
```

Load fonts via Google Fonts (`<link>` tag) — `Cormorant+Garamond:wght@400;500;600&family=Inter:wght@400;500;600&family=JetBrains+Mono:wght@400;500`. Cormorant Garamond is the closest open-source substitute for Anthropic's licensed Copernicus / Tiempos Headline.

---

## Type scale (display = serif weight 400 with negative tracking, never bold)

| Element | Family | Size / Line | Tracking | Where |
|---|---|---|---|---|
| h1 (hero) | serif 400 | 64px / 1.05 | -1.5px | Hero only |
| h2 (section) | serif 500 | 44px / 1.1 | -1px | Section openers |
| h3 (sub) | serif 500 | 28px / 1.2 | -0.3px | Verdict card title, sub-sections |
| h4 (card) | serif 500 | 22px / 1.3 | 0 | Card titles |
| eyebrow | sans 500 caps | 12px / 1.4 | 1.5px | Above each section h2 (coral) |
| lede | sans 400 | 20px / 1.5 | 0 | Intro paragraph below h2 |
| body | sans 400 | 16px / 1.55 | 0 | Default running text |
| caption | sans 500 | 13px / 1.4 | 0 | Card meta, KPI labels |
| code | mono 400 | 14px / 1.6 | 0 | Inline file paths |

---

## Spacing & shape

```css
/* Spacing tokens — base 4px */
xxs:4 · xs:8 · sm:12 · md:16 · lg:24 · xl:32 · xxl:48 · section:96

/* Radius */
sm:6 · md:8 (buttons/inputs) · lg:12 (cards) · xl:16 (hero) · pill:9999
```

**Section padding:** 96px top/bottom between bands. **Card padding:** 32px (feature cards), 48px (verdict + coral bands). **Max content width:** 1200px centered, 32px gutters.

---

## Component primitives

The brief uses **only** these. Don't invent new ones.

### `topnav`
Sticky 64px cream nav bar. Brand mark + wordmark left; nav-link pills right. Pills become coral-tinted card on `.active`.

### `hero` (cream)
120px top padding. Eyebrow → h1 → lede → meta dot-list → optional dark `verdict-card` below.

### `verdict-card` (dark inside hero)
Dark navy background, on-dark text, coral pill badge ("Verdict"), serif h3, two paragraphs in on-dark-soft. Padding 48px. Radius 12px.

### `kpi-grid` (4-up)
Cream cards with serif 38px numerator + 13px muted label. Used once per page near top.

### `card-grid-2` / `card-grid-3`
Cream `surface-card` background, 32px padding, radius 12px. Variants: `.dark` (navy + on-dark text), `.coral` (coral fill + white text — use for monitor / "Don't" cards).

### `tabs` + `tab-panel`
Underline-style tabs with coral active indicator. Tab row uses `flex-wrap` for >5 tabs. Panels fade in.

### `threat-card`
Border-1px hairline cream card, 40px padding. Header: title + sub + threat badge (red/amber/coral). Body: paragraph + 4-quadrant `threat-grid` (Strengths · Weaknesses · How to win · Churn signal).

### `details` (accordion)
Cream card with `+` / `−` coral toggle. Use for the Strategy section's opportunities — keeps page scannable.

### `quote`
Coral left-border, serif italic 22px, sans attribution underneath in muted. Use for the featured customer voice.

### Tables
Soft-cream header row, hairline-soft body rows. Caption typography for source cite below. Wrapped in 12px-radius `table-wrap`.

### `pull-stat` (3-up)
Big serif numbers (52px) over thin black top borders. Use for striking standalone facts.

### `coral-band` (full-bleed)
Coral fill, white text, 48-56px padding, radius 12px. Use once for the suggested-pricing reveal or major CTA.

### `roadmap` (4-up)
Cream cards with mono coral "Year 1 / 2 / 3 / 4–5" eyebrow + serif h4 + body. Used once near the end.

### `footer` (dark)
Dark navy, on-dark-soft text, 64px padding. Brand wordmark left, meta line right.

### `source-cite` (NEW — citation footer)
End-of-section attribution. Sans 11px, letter-spacing 0.5px, color `--muted-soft`, 16px top margin, 12px padding-top, 1px hairline-soft top border. Format: `↳ {filename} § {section}` with `·` between multiple sources.

### `source-cite-inline`
Same typography, no top border, tighter margin. Use below tables and quotes.

### `data-gap` (placeholder for empty sections)
Cream card with muted dashed border, italic body text. Format: `<div class="data-gap">Insufficient data — see Data Gaps in competitors-report.md</div>`

---

## Surface rhythm

The page alternates: cream → cream-card → dark-mockup → cream → coral-band → dark-footer. Don't repeat the same surface mode in two consecutive bands. The cream-to-dark contrast is the brand's pacing mechanism.

Background colors by section:
- Hero, Thesis, Threats, Pricing, Risks → cream (`--canvas`)
- Market, Strategy, Roadmap → `--surface-soft` (slightly darker cream band)
- Footer → `--surface-dark`

---

## Do's and don'ts

**Do**
- Anchor every page on the cream canvas. Pure white reads as "any other AI tool."
- Serif 400 for all display headlines. Negative letter-spacing is non-negotiable on h1/h2.
- Reserve coral for primary CTAs and full-bleed callout moments.
- Pair feature cards (cream) with product/verdict cards (navy) in alternating bands.
- Use 96px between major sections.

**Don't**
- Don't bold serif display — Cormorant at 700 reads bombastic.
- Don't paint coral on every accent element. Scarce wins.
- Don't introduce a fourth surface tone (no purple, no green).
- Don't use Inter for display headlines. The serif is the brand voice.

---

## Canonical `<style>` block

The template (`html-brief-template.md`) ships with the full CSS implementing all primitives above. Don't hand-edit it — if a brief needs a new pattern, propose adding it as a named component here first.
