---
name: startup-competitors
version: 1.1.0
description: Deep competitive intelligence for any market. Analyzes competitors' products, pricing, customer sentiment, GTM strategy, and growth signals using real web data. Produces battle cards, pricing landscape, feature matrix, and an interactive HTML brief. Use when the user wants to understand their competitive landscape, analyze competitors, compare products in a market, or research who they're competing against. Triggers for "who are my competitors", "competitive analysis", "competitor research", "battle cards", "pricing comparison", "competitor pricing", "market players", "competitive intelligence", "competitive landscape", "who else is in this space", "competitive moat", or any request to profile, compare, or map competitors in a category. Works standalone — no prior startup-design session needed.
allowed-tools: ["Agent", "WebSearch", "WebFetch", "Read", "Write", "Edit", "Bash", "TodoWrite"]
---

<!--
  CHANGELOG
  1.1.0 (2026-05-15) — Phase 4 HTML brief; section-level citations; strict web search
                      precondition; verification report rolls into HTML; mobile-friendly
                      breakpoints; design system + brief template added as references.
                      Packaged into dev-skills plugin.
  1.0.0 — Initial skill: intake, 3-wave research, synthesis, verification.

  INSTALL NOTES (dev-skills plugin):
  This skill REQUIRES WebSearch + WebFetch to produce high-quality output. The
  `allowed-tools` frontmatter declares the dependency, but the target project must
  also grant the permissions. If your project doesn't already, add to that
  project's `.claude/settings.json`:

    {
      "permissions": {
        "allow": ["WebSearch", "WebFetch"]
      }
    }

  The skill halts and prompts you if these tools aren't available — see Phase 2.
-->


# Startup Competitors

Deep competitive intelligence that goes beyond surface-level profiles. Produces actionable battle cards, pricing landscape analysis, and strategic vulnerability mapping using real web data.

## How It Works

```
INTAKE → RESEARCH (3 parallel waves) → SYNTHESIS → BATTLE CARDS → HTML BRIEF
```

The process is focused: understand the product, research competitors deeply across 3 dimensions, synthesize findings, and produce actionable output. Typical runtime: 15-25 minutes in Claude Code (parallel agents), 30-45 minutes in Claude.ai (sequential).

### Language

Default output language is **English**. If the user writes in another language or explicitly requests one, use that language for all outputs instead.

---

## Phase 0: Environment Bootstrap (MANDATORY — runs before intake)

The skill cannot run autonomously without specific permissions pre-granted in the project's `.claude/settings.local.json`. Phase 0 ensures they exist before any work begins. **Run this before reading any other reference file and before asking the user any intake questions.**

### Process

1. **Check for the settings file** at `<cwd>/.claude/settings.local.json`. If `<cwd>` is not a project root, check the nearest ancestor containing a `.claude/` directory; if none, treat as missing.

2. **Verify the required permissions are present.** The file must contain (under `permissions.allow`) at minimum every entry in the canonical block below. Missing entries → treat the file as incomplete.

   Canonical required allowlist:
   ```json
   {
     "permissions": {
       "allow": [
         "WebSearch",
         "WebFetch",
         "Read",
         "Write",
         "Edit",
         "Agent",
         "Bash(mkdir:*)",
         "Bash(ls:*)",
         "Bash(cat:*)",
         "Read(//Users/*/.claude/plugins/**)",
         "Read(//Users/*/.claude/skills/**)"
       ]
     }
   }
   ```

3. **If the file is missing or incomplete:**
   - Create `<cwd>/.claude/` if needed (`mkdir -p`).
   - Write `settings.local.json` with the canonical block above. If the file exists but is missing entries, merge — preserve any existing allow entries and add the missing ones.
   - **Halt the skill.** Print this exact message to the user and stop. Do not continue to Phase 1:

     > **Setup complete — please restart and re-invoke.**
     >
     > I've written the permissions this skill needs to `.claude/settings.local.json`. Claude Code only loads permissions at session start, so this current session can't pick them up.
     >
     > Please **restart Claude Code** (or open a new session) and re-invoke the skill. It will then run end-to-end without further interruption.
     >
     > Why this is needed: this skill spawns parallel research subagents that need WebSearch and WebFetch. Without pre-granted permissions, every subagent triggers its own approval prompt, which breaks autonomy and risks silent fallback to ungrounded output.

4. **If the file is present and complete:** silently log "Phase 0: permissions verified" to PROGRESS.md (which doesn't exist yet — defer this line until PROGRESS.md is created in Phase 1) and proceed to Phase 1.

### Why Phase 0 cannot be skipped

- Permissions are read at session start by Claude Code; mid-session grants don't propagate to subagents spawned in parallel.
- The WebSearch probe in Phase 2 is a runtime check (does the tool actually work right now?) — Phase 0 is the *configuration* check that prevents Phase 2 from ever failing.
- See `SETUP.md` in the skill root for the user-facing version of this contract.

---

## Phase 1: Intake

Short and focused — 1-2 rounds of questions, not an extended interview. The goal is just enough context to run targeted research.

### Check for Prior startup-design Work

Before asking questions, check if a `startup-design` session has already been completed for this project. Look for these files in the working directory or subdirectories:

- `01-discovery/competitor-landscape.md` — competitor profiles and analysis
- `01-discovery/market-analysis.md` — market size, trends, regulatory
- `01-discovery/target-audience.md` — customer personas, pain points
- `00-intake/brief.md` — product description and context

If these files exist, read them and use the data as a head start:
- Extract the product description, target market, and known competitors from the brief
- Use the competitor list from `competitor-landscape.md` as the starting point for deeper analysis (startup-design profiles 5-8 competitors at surface level — this skill goes much deeper on each)
- Pull market size and trends from `market-analysis.md` to contextualize the competitive landscape
- Use customer pain points from `target-audience.md` to focus the sentiment mining on what matters most

Tell the user: "I found data from a previous startup-design session. I'll use it as a starting point and go deeper on the competitive analysis."

Skip the intake interview entirely if the startup-design files provide enough context. Go straight to research.

### What to Ask (if no prior data exists)

**Round 1 — The basics:**
- What's your product/idea? (one sentence is fine)
- What problem does it solve and for whom?
- What market/category are you in?
- Do you know any competitors already? (names, URLs)

**Round 2 — Sharpening (only if needed):**
- What geography/market are you targeting?
- What's your pricing model or range?
- What do you consider your key differentiator?

Don't over-interview. If the user gives a clear description upfront, skip straight to research. The competitive analysis itself will surface what matters.

### Output

Save to `{project-name}/intake.md` — a brief summary of the product, market, and known competitors. If built on startup-design data, note the source files used. The project name should be derived from the product/market (kebab-case, e.g., `ai-email-assistant`).

Create `{project-name}/PROGRESS.md` with: project name, skill name (`startup-competitors`), start date, language, research mode (Live / Knowledge-Based), and a phase checklist. The checklist should include: Phase 1 Intake, Phase 1.5 Depth Assessment, Phase 2 Waves 1–3, Post-Research Checkpoint, Phase 3 Synthesis, Phase 3.5 Verification, **Phase 4 HTML Brief**. Update it after each phase completes. If PROGRESS.md already exists from a previous session, resume from the last incomplete phase.

---

## Phase 1.5: Research Depth Assessment (autonomous)

After intake, assess market complexity and **auto-select** the research depth. Do not ask the user — the scoring rubric is deterministic, and asking breaks autonomy.

> **Reference:** Read `references/research-scaling.md` for the complexity scoring matrix, tier definitions, and wave configurations.

### Process

1. Score three factors from the intake: market breadth (1-3), known competitors (1-3), geographic scope (1-3).
2. Sum the scores (range 3-9) and map to a tier: Light (3-4), Standard (5-7), Deep (8-9).
3. **Check the original intake text for an explicit user override.** If the user typed "quick", "fast", or "light" during intake → force Light. If they typed "deep", "thorough", or "comprehensive" → force Deep. Otherwise use the scored tier.
4. Record the selected tier in PROGRESS.md along with the score breakdown and any override reason.
5. Tell the user *as a one-line statement* (not a question): "Running **{tier}** research (complexity score {X}/9, {N} agents per wave)." Then proceed immediately to Phase 2.

The selected tier determines the number of agents per wave and search rounds per agent in Phase 2. See `research-scaling.md` for exact wave configurations per tier.

---

## Phase 2: Research

Three parallel research waves, each attacking the competitive landscape from a different angle. Together they produce a 360-degree view.

### Environment Detection

Check if the `Agent` tool is available:

- **Agent tool available (Claude Code):** Spawn all agents within each wave in parallel. This is faster.
- **Agent tool NOT available (Claude.ai, web):** Execute research sequentially, following the same templates. Same depth, just slower.

### Web Search — MANDATORY precondition (hard fail, not prompt-and-wait)

**This skill requires WebSearch AND WebFetch. Without them the output quality collapses** — competitor funding, pricing, lateral moves, regulatory state, and 2025-2026 events are not in parametric knowledge with sufficient detail. **Silent fallback to parametric knowledge is forbidden.** Phase 0 should have already ensured the permissions exist; this is the runtime verification.

#### Parent-level precondition

Before spawning any subagent, run a single trivial `WebSearch` query (e.g., "site:ftc.gov press release"):

- **If it succeeds:** record "WebSearch precondition: ok" in PROGRESS.md and proceed.
- **If it fails for any reason (permission denied, error, no results due to disabled tool):** **abort the skill immediately.** Do not prompt the user to grant permission mid-run — that defeats autonomy. Print:

  > **Aborting: WebSearch is not available in this session.**
  >
  > Phase 0 wrote the required permissions to `.claude/settings.local.json`, but they are not active in the current session. Please restart Claude Code and re-invoke. If this keeps happening, check `SETUP.md` and confirm `WebSearch` and `WebFetch` are in the allow list.

  Do not continue. Do not switch to Knowledge-Based Mode silently.

#### Subagent-level precondition (every Wave 1/2/3 agent)

Subagents do **not** reliably inherit the parent's permission scope across all Claude Code modes. Every spawned subagent must self-check before doing any real work. Include this block verbatim at the top of every Phase 2 agent prompt:

> **Permission self-check (run this first, before any other action).**
>
> 1. Run a single trivial WebSearch: query `"site:example.com"`.
> 2. If the call errors with a permission denial or returns a tool-unavailable error: write the single token `PERMISSION_DENIED` to your output file (`{your-assigned-output-path}`) and exit immediately with no other content.
> 3. If the call succeeds (even with zero results): proceed with the rest of the task.
>
> **Anti-fabrication contract.** Use WebSearch heavily — at least {search-rounds} distinct queries before drawing any conclusion about a competitor. Every concrete claim (number, date, funding amount, employee count, named feature) must be followed by either (a) a citation URL or (b) one of these tags on the same line: `[Data]`, `[Estimate]`, `[Vendor claim]`, `[Data Gap]`. If WebSearch returns nothing usable for a claim, write `[Data Gap]` — do **not** fall back to training-data memory. Outputs lacking either a URL or a tag will be rejected by the verification step.

Substitute `{your-assigned-output-path}` and `{search-rounds}` per agent.

#### Post-spawn sentinel check

After all subagents in a wave finish, **before reading their content as research output**:

1. Read each subagent's output file.
2. If any contain the token `PERMISSION_DENIED`, **abort the entire skill run** with the same message as the parent-level precondition failure above. Do not proceed to synthesis with partial waves.

This is the load-bearing check that prevents silent parametric output.

#### Knowledge-Based Mode

The deliberate-fallback Knowledge-Based Mode (user says "I don't want web search, just use what you know") still exists but must be invoked **explicitly by the user at intake time**. The skill never enters it automatically. In that mode: mark all findings with `[Knowledge-Based — verify independently]`, reduce confidence ratings by one level, and add a prominent disclaimer to every deliverable's header.

> **Reference:** Read `references/research-principles.md` before starting any wave. It defines source quality tiers, cross-referencing rules, and how to handle data gaps.

### Wave 1: Competitor Profiles + Pricing Intelligence

> **Reference:** Read `references/research-wave-1-profiles-pricing.md` for agent templates.

Two agents (or two sequential blocks):

**A1: Competitor Deep-Dives** — Identify and profile 5-8 direct competitors plus 2-3 adjacent solutions (broader platforms, manual alternatives, tools from neighboring categories that compete for the same budget). For each: product, features, team size, funding, traction signals, strengths, weaknesses. Go beyond their marketing page — check reviews, job postings, and funding data.

**A2: Pricing Intelligence** — For each competitor: reverse-engineer the pricing model. Not just "it costs $49/mo" but: what's the value metric (per seat? per usage? flat?), how do tiers differentiate, what pricing psychology do they use (anchoring, decoy, charm pricing), what's the switching cost (technical, contractual, emotional). Build a tier-by-tier comparison.

### Wave 2: Customer Sentiment Mining

> **Reference:** Read `references/research-wave-2-sentiment-mining.md` for agent templates.

Two agents (or two sequential blocks):

**B1: Review Mining** — Mine G2, Capterra, TrustRadius, Product Hunt, and App Store reviews for each competitor. Extract patterns: what do people praise? What do they complain about? What features do they request? Organize by competitor and by pain theme. Include verbatim quotes.

**B2: Forum & Community Mining** — Mine Reddit, Indie Hackers, Hacker News, Quora, and niche communities. Find: complaints about existing tools, "what do you use for X?" threads, migration stories, workaround discussions. Build a **language map** — the exact words customers use to describe their problems and desires. Identify **churn signals** — why people leave each competitor.

### Wave 3: GTM & Strategic Signals

> **Reference:** Read `references/research-wave-3-gtm-signals.md` for agent templates.

Two agents (or two sequential blocks):

**C1: Go-to-Market Analysis** — For each competitor: primary acquisition channel, sales motion (self-serve vs. sales-led), content strategy (blog frequency, topics, quality), social presence, paid advertising signals, partnership plays. Build a **channel opportunity map** showing competitor saturation vs. opportunity per channel.

**C2: Strategic & Growth Signals** — Funding trajectory (rounds, investors, timing), hiring patterns (engineering-heavy = building, sales-heavy = scaling, support-heavy = struggling), content/SEO footprint (what keywords they rank for, where the gaps are), product roadmap signals from changelogs and public statements. Identify **content pillars** each competitor owns and which topics nobody covers well.

---

### Post-Research Checkpoint (autonomous artifact, not a user prompt)

After all three waves complete, before synthesis, **write** a one-page alignment summary to `{project-name}/checkpoint.md`. Do **not** stop to ask the user — autonomy is the goal, and the verification step (Phase 3.5) plus the final deliverables surface anything the user needs to weigh in on.

The checkpoint file should contain:
- Number of competitors profiled (direct + adjacent).
- Top 3-5 customer pain themes surfaced.
- Most notable strategic signals (funding, hiring, GTM patterns).
- A "Worth re-running?" assessment: if any wave returned suspiciously thin output or the competitor set looks off, note it here. The user can read this file after the run and decide whether to re-invoke with refined intake.

Mention the checkpoint file in a single short status line to the user ("Wrote checkpoint summary to `{project}/checkpoint.md`. Continuing to synthesis.") and proceed.

---

## Phase 3: Synthesis

> **Reference:** Read `references/research-synthesis.md` for synthesis protocol and battle card template.

After the checkpoint, synthesize raw findings into strategic deliverables. This step creates the real value — it's not reporting, it's pattern-matching across data sources.

### How to Synthesize

1. Read all raw files before writing anything
2. Connect findings across waves: pricing gaps + customer complaints + hiring signals = strategic opportunities
3. Identify contradictions between sources and explain which to trust
4. Rate confidence for each major claim (High / Medium / Low)
5. Surface strategic implications — not just facts, but what they mean
6. Aggregate all data gaps from raw files into a dedicated "Data Gaps & Research Limitations" section in the competitors-report — every analysis has blind spots, and being explicit about them prevents false confidence
7. Include adjacent solutions (broader platforms, manual alternatives, tools from neighboring categories) — customers don't just choose between direct competitors, they choose between "good enough" options from adjacent spaces

### Output Files

Every deliverable file must start with a standardized header: `# {Title}: {product}` followed by `*Skill: startup-competitors | Generated: {date}*`. Every deliverable must end with Red Flags, Yellow Flags, and Sources sections.

**`{project-name}/competitors-report.md`** — The main deliverable:
- Executive summary (5-sentence competitive landscape overview)
- Market concentration assessment (fragmented / consolidating / dominated)
- Key findings per research dimension
- Strategic opportunities (where to compete)
- Strategic risks (where to avoid)
- Competitive moat assessment (network effects, switching costs, data moat, brand, scale)
- Data gaps & research limitations (mandatory — aggregate from all raw files)
- Red flags and yellow flags

**`{project-name}/competitive-matrix.md`** — Feature comparison table:
- Features as rows, competitors as columns
- Rating: strong / adequate / weak / missing
- Highlight gaps where no competitor serves well
- Your product included (or placeholder if pre-launch)

**`{project-name}/pricing-landscape.md`** — Dedicated pricing analysis:
- Tier-by-tier comparison across all competitors
- Value metric analysis (what each charges for and why)
- Pricing psychology breakdown (anchoring, decoy, freemium strategies)
- Price positioning map (axes: price vs. feature depth)
- Pricing whitespace — where there's room to position
- Switching cost matrix (per competitor: technical, contractual, emotional)

**`{project-name}/battle-cards/{competitor-name}.md`** — One per competitor:
- One-page format: who they are, their strengths, their weaknesses
- How to win against them (specific talking points)
- When they win over you (be honest)
- Customer objections and responses
- Key vulnerability to exploit
- Churn signals (why their customers leave)

### Raw Data

Keep raw research files in `{project-name}/raw/` for reference:
- `competitor-profiles.md`
- `pricing-intelligence.md`
- `review-mining.md`
- `forum-mining.md`
- `gtm-analysis.md`
- `strategic-signals.md`

---

## Phase 3.5: Research Verification

After synthesis completes and all deliverable files are written, run a verification pass.

> **Reference:** Read `references/verification-agent.md` for the full verification protocol, universal checks, and skill-specific checks.

### Process

1. Spawn agent **V1: Verification** — it reads all deliverable files and checks for: unlabeled claims, internal contradictions, confidence rating consistency, missing data gaps, missing flags, stale data, and duplicate-source false corroboration
2. V1 also runs startup-competitors-specific checks: battle card vs. report consistency, matrix vs. profiles alignment, pricing landscape vs. profiles consistency, cross-deliverable coherence
3. V1 produces `{project-name}/verification-report.md`
4. **Do not pause on critical issues.** Autonomy is the design contract. The verification report itself surfaces critical findings, and it rolls up into the HTML brief's `#verification` section (Phase 4) where it's prominently visible. After writing the report, print a one-line summary ("Verification: {N} critical, {N} warnings, {N} info — see `verification-report.md`") and proceed to Phase 4. The user can act on critical issues after the run completes.

In Claude.ai or when Agent tool is unavailable, run the verification checks yourself in the main conversation following the same protocol.

---

## Phase 4: HTML Brief Generation

After verification passes, generate `{project-name}/index.html` — a single-page interactive brief that presents the synthesis deliverables with progressive disclosure (scroll → tabs → accordion). **This runs by default on every session.** Skip only if the user explicitly says "no HTML" during intake.

> **References:**
> - Read `references/html-design-system.md` for the design tokens and component primitives (cream/coral/dark trinity, type scale, surface rhythm rules).
> - Read `references/html-brief-template.md` for the section structure, content-mapping table, citation rules, and the canonical `<style>` and `<script>` blocks.

### Process

1. Read all synthesis deliverables (`executive-summary.md`, `competitors-report.md`, `competitive-matrix.md`, `pricing-landscape.md`, `battle-cards/*.md`, `verification-report.md`) plus `intake.md`.
2. Generate the brief following the section structure in `html-brief-template.md`:
   - Hero + verdict card → Thesis (KPIs + cards + quote) → Market (concentration table + matrix) → Top Threats (one tab per battle card) → Pricing → Strategy (accordion) → Risks → Roadmap → **Verification (audit summary + checklist)** → Footer.
3. **Apply citation rules:** every major section ends with a `.source-cite` footer (`↳ {filename} § {section heading}`); tables and quotes get inline `.source-cite-inline`; battle card tabs each cite their source file. Hero is the only un-cited section (intentional).
4. **Apply the empty-section rule:** if a deliverable is missing or empty, render the section structurally but replace its content with a `.data-gap` placeholder. Don't drop the section.
5. **Apply honesty rules:** never invent numbers, paraphrase quotes, or reorder threats for narrative flow. Every figure in the HTML must trace to a markdown source.
6. **Roll up the verification report** into the `#verification` section: verdict badge (pass/warn/fail based on issue counts), Critical/Warnings/Info pull-stats, warning cards, collapsed info accordion, and the verification checklist rendered as Unicode `✓` / `○`.
7. **Mobile-friendly is mandatory:**
   - Include `<meta name="viewport" content="width=device-width, initial-scale=1.0">` in `<head>` (without this the breakpoints don't activate).
   - The canonical CSS already ships tablet (900px) and phone (600px) breakpoints plus a `pointer:coarse` touch-target floor. Don't strip them.
   - Tabs scroll horizontally on phones (no wrap) so the tab row stays visible.
   - Tables get a subtle scroll-hint gradient on phones.
8. Write `{project-name}/index.html` — self-contained, embedded CSS + JS, only external dependency is Google Fonts.

### Output

`{project-name}/index.html` — single-page interactive brief that complements the markdown deliverables. **Markdown is the source of truth; the HTML is the presentation layer.** Renders on desktop, tablet, and phone with the same content fidelity.

---

## Honesty Protocol

> **Reference:** Read `references/honesty-protocol.md` for full protocol and anti-pattern details.

Competitive intelligence is only useful if it's honest. Core rules apply (label claims, quantify, declare gaps), plus competitive-intelligence-specific additions:

1. **No cheerleading.** If a competitor is objectively better at something, say so. Battle cards that ignore competitor strengths are useless in real sales conversations.
2. **Label claims.** Use **[Data]**, **[Estimate]**, **[Assumption]**, **[Opinion]** tags. Never present guesses as facts.
3. **Quantify.** "$12M ARR growing 40% YoY" not "they're growing fast."
4. **Date everything.** Flag data older than 12 months.
5. **Declare gaps.** "DATA GAP: Could not find reliable data on [X]" is always better than fabrication.
6. **Surface red flags.** If the competitive landscape looks brutal, say so directly.
7. **Challenge confirmation bias.** When research confirms what the founder already believes, probe deeper. Look for disconfirming evidence.

See `references/honesty-protocol.md` for the full anti-pattern table (6 entries) and detailed protocol.

---

## Reference Files

Read only what you need for the current phase.

| File | When to Read | ~Lines | Purpose |
|------|-------------|--------|---------|
| `honesty-protocol.md` | Start of session | ~72 | Full honesty protocol with anti-patterns |
| `research-principles.md` | Before starting Phase 2 | ~54 | Source quality, cross-referencing, data gaps |
| `research-wave-1-profiles-pricing.md` | When running Wave 1 | ~186 | Agent templates for profiles + pricing |
| `research-wave-2-sentiment-mining.md` | When running Wave 2 | ~189 | Agent templates for review + forum mining |
| `research-wave-3-gtm-signals.md` | When running Wave 3 | ~192 | Agent templates for GTM + strategic signals |
| `research-synthesis.md` | After all waves complete | ~231 | How to synthesize + battle card template |
| `research-scaling.md` | After intake, before Phase 2 | ~80 | Complexity scoring, tier definitions, wave configurations |
| `verification-agent.md` | After synthesis | ~95 | Verification protocol, universal + skill-specific checks (incl. HTML) |
| `html-design-system.md` | Before generating Phase 4 HTML | ~150 | Design tokens, type scale, component primitives for the brief |
| `html-brief-template.md` | When generating Phase 4 HTML | ~280 | Section structure, content-mapping table, citation rules, canonical CSS + JS |
