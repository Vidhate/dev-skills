# Research Scaling Protocol

Dynamic scaling adjusts research depth based on market complexity and user preference. Evaluated after intake, before research begins.

## Complexity Score

Assess three factors from the intake data:

| Factor | Low (1) | Medium (2) | High (3) |
|--------|---------|------------|----------|
| **Market breadth** | Ultra-niche, few players, well-defined segment | Defined market, moderate competition | Broad market, many segments, diverse players |
| **Known competitors** | 0-2 identified | 3-5 identified | 6+ identified |
| **Geographic scope** | Single country | Regional (e.g., Europe, North America) | Global or multi-region |

**Complexity score** = sum of the three factors (range: 3-9)

## Research Depth Tiers

| Tier | Score Range | Manual Trigger | Description |
|------|-----------|----------------|-------------|
| **Light** | 3-4 | User says "light", "quick", or "fast research" | Quick scan, fewer agents, 2-3 search rounds |
| **Standard** | 5-7 | Default (no override needed) | Current behavior, balanced depth |
| **Deep** | 8-9 | User says "deep", "thorough", or "deep research" | More agents, 5-6 search rounds, extra coverage |

**Manual override always wins.** If the user requests "light" on a score-9 market, use Light. If they request "deep" on a score-3 market, use Deep.

## User Communication (autonomous — no question, just a status line)

The skill auto-selects the tier and does not stop to ask. After scoring, print a single status line:

```
Running {Light/Standard/Deep} research — complexity score {X}/9 ({N} agents per wave, {R} search rounds each).
```

If the original intake message contained an override keyword (`quick`, `fast`, `light`, `deep`, `thorough`, `comprehensive`), honor it and note the override in the status line:

```
Running Deep research — user override (intake said "thorough"). Score was {X}/9.
```

No prompt, no waiting for user input — proceed directly to Phase 2.

## Wave Configuration: startup-competitors

### Light (3-4 score or user override)

**Wave 1: Competitor Profiles + Pricing** (1 agent)
- A1: Competitor Profiles & Pricing (merge A1+A2 into one agent, cover profiles and pricing together)

**Wave 2: Customer Sentiment** (1 agent)
- B1: Review & Community Mining (merge B1+B2 into one agent, cover reviews and forums together)

**Wave 3: GTM & Strategic Signals** (1 agent)
- C1: GTM & Growth Signals (merge C1+C2 into one agent, cover GTM and strategic signals together)

**Total: 3 agents** (vs. 6 Standard), 2-3 search rounds per agent

### Standard (5-7 score, default)

No changes to current wave structure:
- Wave 1: 2 agents (A1, A2)
- Wave 2: 2 agents (B1, B2)
- Wave 3: 2 agents (C1, C2)

**Total: 6 agents**, 3-4 search rounds per agent

### Deep (8-9 score or user override)

**Wave 1: Competitor Profiles + Pricing** (3 agents)
- A1: Competitor Deep-Dives (unchanged)
- A2: Pricing Intelligence (unchanged)
- A3: Adjacent Competitor Profiles (NEW: profile 3-5 adjacent/emerging competitors not in the direct set, including recent launches and stealth startups)

**Wave 2: Customer Sentiment** (3 agents)
- B1: Review Mining (unchanged)
- B2: Forum & Community Mining (unchanged)
- B3: Social Media Sentiment (NEW: mine Twitter/X, LinkedIn, and YouTube for competitor mentions, sentiment patterns, and influencer opinions)

**Wave 3: GTM & Strategic Signals** (3 agents)
- C1: Go-to-Market Analysis (unchanged)
- C2: Strategic & Growth Signals (unchanged)
- C3: Tech Stack & Product Analysis (NEW: analyze competitors' technology choices, API ecosystems, integration depth, and technical moats)

**Total: 9 agents**, 5-6 search rounds per agent

## PROGRESS.md

Record the selected tier in PROGRESS.md:

```markdown
- **Research Depth:** {Light/Standard/Deep} (score: {X}/9, {override: user request / auto})
```
