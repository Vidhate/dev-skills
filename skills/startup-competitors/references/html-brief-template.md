# HTML Brief Template

How to generate the Phase 4 single-page interactive brief (`{project-name}/index.html`).

The HTML is a **presentation layer** over the synthesis deliverables. **Never re-research, re-quantify, or re-interpret.** Every figure, quote, and claim in the HTML must trace to an existing markdown file. If a deliverable wasn't written (e.g., synthesis skipped pricing), render the corresponding HTML section as a `.data-gap` placeholder.

---

## Required reading before generating

1. `references/html-design-system.md` — design tokens and component primitives
2. All synthesis deliverables in `{project-name}/`:
   - `executive-summary.md` (most-used source — pulls into Hero, KPIs, Roadmap, Gating Milestones)
   - `competitors-report.md` (Thesis, Market, Strategy, Risks)
   - `competitive-matrix.md` (Market section table)
   - `pricing-landscape.md` (Pricing section)
   - `battle-cards/*.md` (Threats tabs — one per file)
3. `intake.md` (footer metadata)
4. Raw file counts in `raw/` (footer metadata)

---

## Section structure (in order)

| # | Section | id | Background | Source(s) |
|---|---|---|---|---|
| 1 | Hero + verdict card | `#hero` | cream | `executive-summary.md` § Verdict |
| 2 | Thesis (KPIs + cards + featured quote) | `#thesis` | cream | `competitors-report.md` § Executive Summary + Key Findings |
| 3 | Market Landscape (concentration table + matrix) | `#market` | surface-soft | `competitors-report.md` § Market Concentration + `competitive-matrix.md` |
| 4 | Top Threats (tabs — one per battle card) | `#threats` | cream | `battle-cards/*.md` |
| 5 | Pricing (rates + cost ranges + coral pricing band) | `#pricing` | cream | `pricing-landscape.md` |
| 6 | Strategy (opportunities accordion + avoid cards) | `#strategy` | surface-soft | `competitors-report.md` § Strategic Opportunities + § Strategic Risks |
| 7 | Risks (red/yellow flags grid + monthly monitor coral cards) | `#risks` | cream | `competitors-report.md` § Red Flags + `executive-summary.md` § Top 3 Risk Flags |
| 8 | Roadmap (4-up year cards + gating milestones) | `#roadmap` | surface-soft | `executive-summary.md` § Recommended Sequencing + § Gating Milestones |
| 9 | Verification (audit summary + warnings) | `#verification` | cream | `verification-report.md` |
| 10 | Footer | — | surface-dark | `intake.md` + `raw/` count |

The sticky top nav lists sections 2–9 (skip Hero and Footer in nav). The Verification section gets a `Verification` nav link.

---

## Content mapping rules (block → source)

### Hero verdict card
- Eyebrow: "Deep Discovery · {Live or Knowledge-Based} · Generated {date}"
- h1: a one-line headline summarizing the thesis (10–14 words, picked from the executive summary)
- Lede: 2–3 sentences paraphrasing the verdict
- Verdict card (dark) below: pill "Verdict" + h3 (verdict headline) + 2 paragraphs from `executive-summary.md` § "The verdict in one paragraph"
- **No source cite on the Hero** (intentionally — clean first impression)

### KPI grid (4 cards)
- Pick **4 most striking quantitative findings** from `competitors-report.md` or `executive-summary.md`
- Format: large serif number + 13px label
- These are the page's elevator pitch in numbers — choose ones that anchor the thesis
- Cite at section footer: `↳ competitors-report.md § Executive Summary · executive-summary.md § Verdict`

### Thesis section
- One eyebrow → one h2 → lede
- KPI grid (above)
- 3-up card grid: pick 3 supporting findings (customer pain validated · talent pool · content whitespace are the pattern)
- One `.quote` block: featured verbatim from sentiment mining if a strong one exists
- Section footer cite: `↳ competitors-report.md § Key Findings · raw/review-mining.md` (if quote pulled from raw)

### Market Landscape
- Eyebrow → h2 → lede
- Market concentration table (3-row format from `competitors-report.md`) — inline cite below: `↳ competitors-report.md § Market Concentration`
- `pull-stat` 3-up: 3 striking standalone numbers
- 2-up card grid: "Where genuinely differentiated" cards
- Capability matrix from `competitive-matrix.md` — inline cite: `↳ competitive-matrix.md`
- Section footer cite: `↳ competitors-report.md § Market Concentration · competitive-matrix.md`

### Top Threats (tabs)
- One tab per `battle-cards/*.md` file. **Render all of them** (`flex-wrap` handles >5).
- Tab label: `{N}. {competitor name}` (numbered by threat order from `executive-summary.md` § Top 5 Threats)
- Each `tab-panel` contains a `threat-card` with:
  - Title + sub (one-liner with key facts)
  - Threat badge (red/amber based on threat level in battle card)
  - Intro paragraph
  - 4-quadrant `threat-grid`: Strengths · Weaknesses · How to Win · Churn Signal (4 bullets each, pulled directly from battle card)
- **Per-tab cite at panel bottom:** `↳ battle-cards/{filename}.md`

### Pricing
- Eyebrow → h2 → lede
- One or more pricing tables from `pricing-landscape.md` — each with inline cite below: `↳ pricing-landscape.md § {Table Heading}`
- Coral `coral-band` with suggested launch pricing — inline cite below: `↳ pricing-landscape.md § Suggested Launch Pricing`
- Section footer cite: `↳ pricing-landscape.md`

### Strategy
- Eyebrow → h2 → lede
- `details` accordion: one entry per strategic opportunity from `competitors-report.md` § Strategic Opportunities
- 3-up `.card.dark` grid: "Three things to avoid" from § Strategic Risks
- Section footer cite: `↳ competitors-report.md § Strategic Opportunities · § Strategic Risks`

### Risks
- Eyebrow → h2 → lede
- 2-up card grid: 6–8 red flag cards from `competitors-report.md` § Red Flags (one per card)
- 3-up `.card.coral` grid: "Top 3 risk flags to monitor monthly" from `executive-summary.md`
- Section footer cite: `↳ competitors-report.md § Red Flags · executive-summary.md § Risk Flags to Monitor`

### Roadmap
- Eyebrow → h2 → lede
- 4-up `roadmap` cards (Year 1 / 2 / 3 / 4–5) from `executive-summary.md` § Recommended Sequencing
- 3-up card grid: Gating milestones from `executive-summary.md` § Gating Milestones
- Section footer cite: `↳ executive-summary.md § Recommended Sequencing · § Gating Milestones`

### Verification
Summarize the audit. The brief is only as honest as its own checking — surface this in the HTML so readers can see what the verification pass caught.

- Eyebrow → h2 → lede explaining what verification covers
- **Verdict badge** at top: green ("Verification passed") / amber ("Passed with N warnings") / red ("N critical issues — see below") based on `verification-report.md` § Summary counts
- **3-up `pull-stat`** showing Critical / Warnings / Info counts as big serif numbers
- If there are Critical issues: a `.card.coral` (or `.card` with red accent) per issue showing title + affected files + suggested fix, pulled from `verification-report.md` § Critical Issues
- If there are Warnings: a 2-up card grid with up to 6 warnings (title + one-line description); if more than 6, add a note "and N more — see verification-report.md"
- Info items: collapsed into a single `details` accordion ("N info items") so they don't clutter the section
- **Verification checklist** at the bottom: render the checkbox list from `verification-report.md` § Verification Checklist, with `✓` for checked items and `○` for unchecked (use Unicode glyphs, color `--success` for `✓` and `--muted-soft` for `○`)
- Section footer cite: `↳ verification-report.md`
- **Don't fabricate.** If `verification-report.md` doesn't exist (verification was skipped), render the section with a `.data-gap` placeholder. If the verification passed cleanly with no warnings, show the green verdict + the pull-stat (with 0 / 0 / N) + the checklist — skip the warning/critical card grids entirely.

### Footer
- Brand block left: project name + skill name + generated date + reference to `{project-name}/`
- Meta right: raw file count + battle card count + estimated word count
- No source cite (it's the cite by definition)

---

## Citation rules (binding)

1. **Every major section ends with a `.source-cite` footer line.** Hero is the only exception.
2. **Format:** `↳ {filename} § {section heading verbatim}` — use the markdown heading text exactly. The `§` glyph is literal.
3. **Multiple sources:** separate with ` · ` (middle dot with spaces). Example: `↳ competitors-report.md § Red Flags · executive-summary.md § Risk Flags to Monitor`
4. **Truncation:** if a section synthesizes 3+ files, cite up to 3, then append ` · + raw/` to signal additional raw-file sources.
5. **Tables and pull-quotes get inline `.source-cite-inline`** — same typography, no top border, tighter margin.
6. **Battle card tabs:** each panel's last element is `↳ battle-cards/{filename}.md`.
7. **Never cite a file that doesn't exist** in the project directory. If a deliverable was skipped, render that section as `.data-gap` placeholder and omit the cite entirely.
8. **Don't paraphrase section headings** in cites. The whole point is that a reader can grep the markdown file for that exact heading.
9. **Inline cites are mandatory** for: capability matrix, every pricing table, the suggested-pricing coral band, featured pull-quotes.

---

## Empty-section rule

If a synthesis deliverable doesn't exist or is empty:

- Render the section structurally (eyebrow, h2, lede) as normal
- Replace the content block with a single `.data-gap` placeholder:

```html
<div class="data-gap">
  Insufficient data — see Data Gaps in competitors-report.md
</div>
```

- Omit the section's source cite (no fabricated cite)
- Do NOT drop the section entirely — preserves the brief's structural completeness and makes the gap visible

---

## Honesty rules (binding)

1. **Never invent numbers.** Every figure in HTML must appear in a synthesis deliverable. If a KPI feels weak, pick a different one — don't manufacture.
2. **Don't paraphrase quotes.** Verbatim only, with attribution.
3. **Don't summarize battle cards.** Pull bullets directly. The HTML's job is presentation, not editing.
4. **Threat tabs reflect threat order from `executive-summary.md`** — don't reorder for narrative flow.
5. **If two deliverables disagree on a number, use the more recent / higher-confidence one and add a parenthetical inline cite to both.**

---

## CSS — canonical `<style>` block

Embed the following at the top of every generated HTML file. This block implements all primitives in `html-design-system.md`. Do not hand-edit; if a primitive needs to evolve, update `html-design-system.md` first.

```css
:root {
  --primary:#cc785c; --primary-active:#a9583e; --ink:#141413;
  --body:#3d3d3a; --body-strong:#252523; --muted:#6c6a64; --muted-soft:#8e8b82;
  --hairline:#e6dfd8; --hairline-soft:#ebe6df;
  --canvas:#faf9f5; --surface-soft:#f5f0e8; --surface-card:#efe9de; --surface-cream-strong:#e8e0d2;
  --surface-dark:#181715; --surface-dark-elevated:#252320;
  --on-dark:#faf9f5; --on-dark-soft:#a09d96;
  --accent-amber:#e8a55a; --success:#5db872; --warning:#d4a017; --error:#c64545;
  --serif:"Cormorant Garamond","Tiempos Headline",Garamond,serif;
  --sans:"Inter",-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif;
  --mono:"JetBrains Mono",ui-monospace,monospace;
}
*{box-sizing:border-box;margin:0;padding:0}
html{scroll-behavior:smooth}
body{font-family:var(--sans);background:var(--canvas);color:var(--body);font-size:16px;line-height:1.55;-webkit-font-smoothing:antialiased}

/* Top nav */
.topnav{position:sticky;top:0;z-index:100;height:64px;background:var(--canvas);border-bottom:1px solid var(--hairline-soft);display:flex;align-items:center;padding:0 32px}
.topnav-inner{max-width:1200px;margin:0 auto;width:100%;display:flex;align-items:center;justify-content:space-between}
.brand{display:flex;align-items:center;gap:10px;font-family:var(--sans);font-weight:600;font-size:15px;color:var(--ink);letter-spacing:-0.2px}
.spike{width:14px;height:14px;display:inline-block;background:var(--ink);clip-path:polygon(50% 0,55% 45%,100% 50%,55% 55%,50% 100%,45% 55%,0 50%,45% 45%)}
.navlinks{display:flex;gap:4px}
.navlinks a{font-size:13px;font-weight:500;color:var(--muted);text-decoration:none;padding:8px 14px;border-radius:6px;transition:all 0.15s;cursor:pointer}
.navlinks a:hover,.navlinks a.active{color:var(--ink);background:var(--surface-card)}

/* Container + sections */
.container{max-width:1200px;margin:0 auto;padding:0 32px}
section{padding:96px 0;border-bottom:1px solid var(--hairline-soft)}
section:last-child{border-bottom:none}

/* Type */
h1,h2,h3,h4{font-family:var(--serif);font-weight:500;color:var(--ink);letter-spacing:-0.5px}
h1{font-size:64px;line-height:1.05;letter-spacing:-1.5px}
h2{font-size:44px;line-height:1.1;letter-spacing:-1px;margin-bottom:24px}
h3{font-size:28px;line-height:1.2;letter-spacing:-0.3px;margin-bottom:16px}
h4{font-size:22px;line-height:1.3;margin-bottom:12px;font-weight:500}
.eyebrow{font-family:var(--sans);font-size:12px;font-weight:500;letter-spacing:1.5px;text-transform:uppercase;color:var(--primary);margin-bottom:16px}
.lede{font-family:var(--sans);font-size:20px;line-height:1.5;color:var(--body-strong);margin-bottom:32px}
p{margin-bottom:16px;color:var(--body)}
p strong{color:var(--body-strong);font-weight:600}

/* Hero */
.hero{padding:120px 0 80px;background:var(--canvas)}
.hero h1{margin-bottom:24px;max-width:1000px}
.hero .lede{max-width:720px;font-size:22px}
.hero-meta{display:flex;gap:16px;margin-top:40px;flex-wrap:wrap;font-size:13px;color:var(--muted)}
.hero-meta span{display:flex;align-items:center;gap:6px}
.hero-meta .dot{width:6px;height:6px;border-radius:50%;background:var(--primary);display:inline-block}

/* Verdict card */
.verdict-card{background:var(--surface-dark);color:var(--on-dark);padding:48px;border-radius:12px;margin-top:48px}
.verdict-card h3{color:var(--on-dark)}
.verdict-card p{color:var(--on-dark-soft);font-size:17px;line-height:1.6}
.verdict-card .pill{display:inline-block;background:var(--primary);color:#fff;font-size:11px;font-weight:500;letter-spacing:1.5px;padding:5px 12px;border-radius:999px;margin-bottom:20px;text-transform:uppercase}

/* KPI grid */
.kpi-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:16px;margin:48px 0}
.kpi{background:var(--surface-card);padding:28px 24px;border-radius:12px}
.kpi-value{font-family:var(--serif);font-size:38px;line-height:1.05;color:var(--ink);margin-bottom:8px;letter-spacing:-0.5px}
.kpi-label{font-size:13px;color:var(--muted);line-height:1.4}

/* Card grids */
.card-grid-2{display:grid;grid-template-columns:1fr 1fr;gap:24px}
.card-grid-3{display:grid;grid-template-columns:repeat(3,1fr);gap:24px}
.card{background:var(--surface-card);border-radius:12px;padding:32px}
.card h4{margin-bottom:10px}
.card p{font-size:15px}
.card.dark{background:var(--surface-dark);color:var(--on-dark)}
.card.dark h4{color:var(--on-dark)}
.card.dark p{color:var(--on-dark-soft)}
.card.coral{background:var(--primary);color:#fff}
.card.coral h4{color:#fff}
.card.coral p{color:rgba(255,255,255,0.85)}

/* Tabs */
.tabs{display:flex;flex-wrap:wrap;gap:4px;border-bottom:1px solid var(--hairline);margin-bottom:32px}
.tab{padding:12px 18px;font-size:14px;font-weight:500;color:var(--muted);background:transparent;border:none;border-bottom:2px solid transparent;cursor:pointer;font-family:var(--sans);transition:all 0.15s;margin-bottom:-1px}
.tab:hover{color:var(--ink)}
.tab.active{color:var(--ink);border-bottom-color:var(--primary)}
.tab-panel{display:none}
.tab-panel.active{display:block;animation:fadeIn 0.2s ease}
@keyframes fadeIn{from{opacity:0;transform:translateY(4px)}to{opacity:1;transform:none}}

/* Threat card */
.threat-card{background:var(--canvas);border:1px solid var(--hairline);border-radius:12px;padding:40px}
.threat-header{display:flex;align-items:flex-start;justify-content:space-between;margin-bottom:24px;gap:24px;flex-wrap:wrap}
.threat-title h3{margin-bottom:4px}
.threat-title .sub{font-size:14px;color:var(--muted)}
.badge{display:inline-block;padding:4px 12px;border-radius:999px;font-size:12px;font-weight:500;letter-spacing:1px;text-transform:uppercase}
.badge.high{background:var(--error);color:#fff}
.badge.med{background:var(--accent-amber);color:var(--ink)}
.badge.low{background:var(--surface-cream-strong);color:var(--ink)}
.badge.coral{background:var(--primary);color:#fff}
.threat-grid{display:grid;grid-template-columns:1fr 1fr;gap:32px;margin-top:24px}
.threat-col h5{font-family:var(--sans);font-size:12px;font-weight:600;letter-spacing:1.5px;text-transform:uppercase;color:var(--primary);margin-bottom:12px}
.threat-col ul{list-style:none}
.threat-col li{font-size:14px;line-height:1.55;padding:8px 0 8px 18px;position:relative;color:var(--body);border-bottom:1px solid var(--hairline-soft)}
.threat-col li:last-child{border-bottom:none}
.threat-col li::before{content:"";position:absolute;left:0;top:16px;width:5px;height:5px;background:var(--primary);border-radius:50%}

/* Accordion */
details{background:var(--canvas);border:1px solid var(--hairline);border-radius:12px;margin-bottom:12px;overflow:hidden}
details summary{padding:20px 24px;cursor:pointer;font-weight:500;color:var(--ink);font-size:16px;list-style:none;display:flex;align-items:center;justify-content:space-between;transition:background 0.15s}
details summary::-webkit-details-marker{display:none}
details summary:hover{background:var(--surface-soft)}
details summary::after{content:"+";font-size:22px;color:var(--primary);font-weight:300}
details[open] summary::after{content:"−"}
details .details-body{padding:0 24px 24px;border-top:1px solid var(--hairline-soft);padding-top:20px}
details .details-body p{font-size:15px}
details .details-body ul{padding-left:18px}
details .details-body li{font-size:15px;padding:4px 0;color:var(--body)}

/* Quote */
.quote{border-left:3px solid var(--primary);padding:16px 0 16px 24px;margin:24px 0;font-family:var(--serif);font-size:22px;line-height:1.4;color:var(--body-strong);font-style:italic}
.quote .attribution{display:block;font-family:var(--sans);font-size:13px;color:var(--muted);font-style:normal;margin-top:12px;letter-spacing:0.5px}

/* Tables */
.table-wrap{overflow-x:auto;margin:24px 0;border-radius:12px;border:1px solid var(--hairline)}
table{width:100%;border-collapse:collapse;font-size:14px;background:var(--canvas)}
th{text-align:left;padding:14px 16px;font-weight:600;font-size:12px;letter-spacing:0.5px;text-transform:uppercase;color:var(--muted);border-bottom:1px solid var(--hairline);background:var(--surface-soft)}
td{padding:14px 16px;border-bottom:1px solid var(--hairline-soft);color:var(--body)}
tr:last-child td{border-bottom:none}
td strong{color:var(--ink)}

/* Pull stat */
.pull-stat{display:grid;grid-template-columns:1fr 1fr 1fr;gap:24px;margin:32px 0}
.pull-stat .item{padding:24px 0;border-top:2px solid var(--ink)}
.pull-stat .num{font-family:var(--serif);font-size:52px;line-height:1;color:var(--ink);letter-spacing:-1.5px;margin-bottom:8px}
.pull-stat .lbl{font-size:13px;color:var(--muted);line-height:1.45}

/* Coral band */
.coral-band{background:var(--primary);color:#fff;border-radius:12px;padding:56px 48px;margin-top:48px}
.coral-band h3{color:#fff;font-size:32px;margin-bottom:12px}
.coral-band p{color:rgba(255,255,255,0.9);font-size:17px;max-width:700px}

/* Roadmap */
.roadmap{display:grid;grid-template-columns:repeat(4,1fr);gap:16px;margin-top:32px}
.road-step{background:var(--canvas);border:1px solid var(--hairline);border-radius:12px;padding:28px}
.road-year{font-family:var(--mono);font-size:12px;color:var(--primary);letter-spacing:1px;text-transform:uppercase;margin-bottom:12px}
.road-step h4{font-size:18px;margin-bottom:10px}
.road-step p{font-size:13px;color:var(--body)}

/* Section intro */
.section-intro{margin-bottom:48px;max-width:760px}

/* Source citations */
.source-cite{font-family:var(--sans);font-size:11px;font-weight:500;letter-spacing:0.5px;color:var(--muted-soft);margin-top:32px;padding-top:12px;border-top:1px solid var(--hairline-soft)}
.source-cite-inline{font-family:var(--sans);font-size:11px;font-weight:500;letter-spacing:0.5px;color:var(--muted-soft);margin-top:8px}
.source-cite code,.source-cite-inline code{font-family:var(--mono);font-size:11px;color:var(--muted)}

/* Data gap placeholder */
.data-gap{background:var(--surface-soft);border:1px dashed var(--hairline);border-radius:12px;padding:32px;color:var(--muted);font-style:italic;font-size:15px;text-align:center}

/* Verification section */
.verdict-badge{display:inline-flex;align-items:center;gap:8px;padding:8px 16px;border-radius:999px;font-size:13px;font-weight:600;letter-spacing:0.5px;margin-bottom:24px}
.verdict-badge.pass{background:rgba(93,184,114,0.15);color:var(--success);border:1px solid rgba(93,184,114,0.3)}
.verdict-badge.warn{background:rgba(232,165,90,0.15);color:#a06f1f;border:1px solid rgba(232,165,90,0.3)}
.verdict-badge.fail{background:rgba(198,69,69,0.12);color:var(--error);border:1px solid rgba(198,69,69,0.3)}
.verdict-badge .dot{width:8px;height:8px;border-radius:50%;background:currentColor}
.checklist{list-style:none;padding:0;margin:24px 0;display:grid;grid-template-columns:1fr 1fr;gap:8px 24px}
.checklist li{padding:6px 0;font-size:14px;color:var(--body);display:flex;align-items:flex-start;gap:10px}
.checklist li .mark{font-family:var(--mono);font-size:14px;line-height:1.4;flex-shrink:0;width:14px}
.checklist li.ok .mark{color:var(--success)}
.checklist li.miss .mark{color:var(--muted-soft)}

/* Footer */
footer{background:var(--surface-dark);color:var(--on-dark-soft);padding:64px 32px;font-size:14px}
footer .container{display:flex;justify-content:space-between;align-items:center;flex-wrap:wrap;gap:24px}
footer .brand-foot{color:var(--on-dark);font-weight:600}

/* Responsive — tablet */
@media(max-width:900px){
  h1{font-size:42px;letter-spacing:-1px}
  h2{font-size:32px;letter-spacing:-0.5px}
  h3{font-size:24px}
  .container{padding:0 20px}
  .hero{padding:80px 0 56px}
  .hero .lede{font-size:18px}
  .kpi-grid{grid-template-columns:repeat(2,1fr)}
  .card-grid-2,.card-grid-3{grid-template-columns:1fr}
  .threat-grid{grid-template-columns:1fr;gap:24px}
  .pull-stat{grid-template-columns:1fr;gap:16px}
  .roadmap{grid-template-columns:1fr 1fr}
  .checklist{grid-template-columns:1fr}
  section{padding:64px 0}
  .navlinks{display:none}
  .topnav{padding:0 20px}
  .verdict-card,.coral-band{padding:32px 24px}
  .threat-card{padding:28px 20px}
  .card{padding:24px}
}

/* Responsive — phone */
@media(max-width:600px){
  h1{font-size:34px;line-height:1.1;letter-spacing:-0.5px}
  h2{font-size:26px;letter-spacing:-0.3px;margin-bottom:16px}
  h3{font-size:22px}
  h4{font-size:18px}
  .lede{font-size:16px}
  .hero{padding:56px 0 40px}
  .hero h1{margin-bottom:16px}
  .hero .lede{font-size:17px}
  .hero-meta{font-size:12px;gap:10px}
  .kpi-grid{grid-template-columns:1fr;gap:12px;margin:32px 0}
  .kpi{padding:20px}
  .kpi-value{font-size:32px}
  .roadmap{grid-template-columns:1fr}
  .pull-stat .num{font-size:42px}
  .verdict-card{padding:28px 20px;margin-top:32px}
  .verdict-card p{font-size:15px}
  .coral-band{padding:32px 20px}
  .coral-band h3{font-size:24px}
  .section-intro{margin-bottom:32px}
  /* Tabs: horizontal scroll instead of wrap on tiny screens */
  .tabs{flex-wrap:nowrap;overflow-x:auto;-webkit-overflow-scrolling:touch;scrollbar-width:none}
  .tabs::-webkit-scrollbar{display:none}
  .tab{flex-shrink:0;padding:10px 14px;font-size:13px}
  /* Table affordance: show scroll hint */
  .table-wrap{position:relative}
  .table-wrap::after{content:"";position:absolute;top:0;right:0;width:24px;height:100%;background:linear-gradient(to right,transparent,rgba(0,0,0,0.04));pointer-events:none;border-radius:0 12px 12px 0}
  table{font-size:13px}
  th,td{padding:10px 12px}
  /* Footer wraps gracefully */
  footer{padding:48px 20px}
  footer .container{flex-direction:column;align-items:flex-start;gap:12px}
  /* Larger touch targets */
  details summary{padding:18px 20px;min-height:48px}
}

/* Touch-target floor for interactive elements */
@media(pointer:coarse){
  .tab,.navlinks a,details summary{min-height:44px}
}
```

---

## Canonical `<script>` block

Tab switching + smooth nav + active-section highlighting. Embed at end of `<body>`:

```javascript
document.querySelectorAll('.tab').forEach(btn => {
  btn.addEventListener('click', () => {
    const target = btn.dataset.tab;
    document.querySelectorAll('.tab').forEach(b => b.classList.remove('active'));
    document.querySelectorAll('.tab-panel').forEach(p => p.classList.remove('active'));
    btn.classList.add('active');
    document.querySelector(`[data-panel="${target}"]`).classList.add('active');
  });
});
document.querySelectorAll('.navlinks a').forEach(a => {
  a.addEventListener('click', e => {
    e.preventDefault();
    const t = document.getElementById(a.dataset.target);
    if (t) t.scrollIntoView({behavior:'smooth',block:'start'});
  });
});
const sections = document.querySelectorAll('section[id]');
const navLinks = document.querySelectorAll('.navlinks a');
window.addEventListener('scroll', () => {
  let current = '';
  sections.forEach(s => {
    if (window.scrollY >= s.offsetTop - 100) current = s.id;
  });
  navLinks.forEach(a => a.classList.toggle('active', a.dataset.target === current));
});
```

---

## Generation procedure

1. Read all synthesis deliverables (including `verification-report.md` if present).
2. Identify which sections have source data vs. which need `.data-gap` placeholder.
3. Build the HTML page from the section structure table above. **Required `<head>` contents:**
   - `<meta charset="UTF-8">`
   - `<meta name="viewport" content="width=device-width, initial-scale=1.0">` (mandatory — without this the mobile breakpoints don't activate)
   - `<title>` (project name + " — Strategic Brief")
   - Google Fonts `<link>` for Cormorant Garamond + Inter + JetBrains Mono
4. Apply citation rules (every section footer, plus inline cites on tables/quotes/key cards). Hero stays uncited.
5. Embed the canonical `<style>` and `<script>` blocks.
6. Output to `{project-name}/index.html`.
7. Verify after generation: every `.source-cite` references a file that exists; tab count matches `battle-cards/*.md` count; viewport meta tag present.

The brief should be self-contained — no external dependencies except Google Fonts. The file should open and render correctly with a local double-click on desktop **and on mobile** (test at 375px and 768px breakpoints visually).
