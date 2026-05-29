# HTML Brief Template

A single self-contained static HTML file that captures the clarity surfaced in the
session. The brief is **collateral** — the real output is the user's shifted
conviction — but it should be a durable, scannable record they can revisit.

Write it to `.sharpen/<slug>/brief.html`. No external assets except Google Fonts.

## Design system

Use the warm-canvas system from
`skills/startup-competitors/references/html-design-system.md` for visual
consistency across the user's skills. The non-negotiables:

- **Trinity only:** cream canvas `#faf9f5` (never pure white), coral `#cc785c`
  (scarce on elements, generous on full-bleed bands), dark navy `#181715`
  (verdict card, footer). No fourth color.
- **Type:** Cormorant Garamond (serif 400/500, negative tracking, never bold) for
  all display; Inter for body; JetBrains Mono for meta.
- **Rhythm:** alternate cream → cream-card → dark → cream → coral-band → dark
  footer. 96px between major sections. Max width 1200px.

Pull the full `:root` token block and component CSS from that design system file
into the `<style>` block. Don't invent new components or colors.

## Section structure (in order)

Map the Phase 3 synthesis onto these sections:

1. **Hero (cream)** — eyebrow `SHARPENED IDEA`, h1 = the sharpened idea in one
   line, lede = one-sentence framing. Below it, the **verdict card (dark navy)**:
   coral pill badge `CONVICTION`, the directional read (Leaning toward / Leaning
   away / Genuinely split), and two short paragraphs of honest reasoning. If split,
   state the single fact or decision that would tip it.

2. **The bet (cream)** — the sharpened idea statement expanded: what it is, who
   it's for, the core thing being bet on. A few short paragraphs.

3. **What moved conviction (`surface-soft` band)** — a `card-grid-2` or list of the
   resolutions that mattered. Each card: the uncertainty, how it resolved, and a
   coral/navy direction marker (↑ pushed toward / ↓ pushed away). This is the
   spine of the brief — give it weight.

4. **What the web said (cream)** — the web-grounded findings as cards or a table.
   Each finding: the question, the verdict, confidence (high/medium/low/
   inconclusive), and a `source-cite` line with the URLs. Mark inconclusive ones
   honestly — don't hide them.

5. **Parked flaws (cream, hairline cards)** — surmountable obstacles flagged but
   not resolved. Frame as "the user owns these going forward," not as dealbreakers.

6. **Open judgments (cream)** — the calls only the user can make that are still
   open. These are the genuine cliffhangers.

7. **Footer (dark)** — wordmark `sharpen-idea` left, meta line right
   (`{n} uncertainties worked · {n} web-grounded · {date}`).

## Honesty in the artifact

- Visually distinguish **web-grounded fact** (carries a source) from **the user's
  own assertion** from **the skill's inference**. A small caption tag per claim is
  enough (`web` / `your call` / `inference`).
- If conviction landed negative, the brief says so cleanly. A brief that talks the
  user back into a dead idea is a failure.
- Use the `data-gap` component for any section the session didn't reach, rather
  than padding it with filler.

## Keep it honest-sized

Don't manufacture sections to look thorough. A session that worked three
uncertainties produces a short brief — that's fine. Length should track the actual
clarity surfaced, not a template quota.
