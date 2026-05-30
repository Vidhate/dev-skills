---
name: sharpen-idea
version: 0.2.0
description: A domain-agnostic thinking partner that takes a rough idea stub and, through web-grounded Socratic dialogue, moves your conviction up or down until you're out of the "I don't know how good this is" limbo. Surfaces the assumptions baked into an idea, grounds the fact-checkable ones in real web research, asks sharp probing questions on the judgment ones, and ends with a clear directional read plus a static HTML brief. Use when the user has a half-formed idea, a vague concept, a stub, a hunch, or a high-level scope they want to pressure-test, flesh out, gain or lose conviction on, or think through with a sharp partner. Triggers for "help me think through this idea", "flesh out this idea", "pressure-test this", "is this idea any good", "I have a rough idea", "thought partner", "stress test my idea", "help me get clarity on", "poke holes in this", "should I pursue this". Produces a fleshed-out idea brief, NOT a plan.
allowed-tools: ["Agent", "WebSearch", "WebFetch", "Read", "Write", "Edit", "Bash", "TodoWrite"]
---

<!--
  CHANGELOG
  0.2.0 — Brevity pass. Added an Output discipline contract: each round leads with
          the one sharp question/flag and fits on one screen; banned the per-round
          "locking in resolutions" tables, ledger dumps, multi-section research
          essays, and standalone conviction-update headers. Research now returns
          2-4 bulleted facts to chat (full detail to disk). Detail lives only in
          Phase 3 synthesis + the HTML brief.
  0.1.0 — Initial skill. Uncertainty-loop engine: surface highest-leverage
          uncertainty → classify fact-checkable vs judgment-only → ground via web
          subagent OR ask one funnel question → integrate → update conviction →
          repeat. Severity-triaged pushback policy. Static HTML brief output.

  REQUIRES WebSearch + WebFetch. The skill grounds fact-checkable uncertainties in
  real web data. Without them it would degrade to ungrounded opinion, which
  defeats the purpose. Phase 0 establishes the permissions and Phase 2 treats web
  access as a hard precondition for any fact-checkable uncertainty.
-->

# Sharpen Idea

A thinking partner for the messy middle. You bring a rough idea — a stub, a hunch,
a high-level scope — and this skill works it with you until your **conviction**
has moved: either you believe in it more and know why, or you've let it go and
know why. The win is escaping the limbo state of "I genuinely don't know how good
this is."

## What this is NOT

- **Not a planner.** It does not produce execution plans, task breakdowns, or
  sequencing. The fleshed-out idea is the handoff point; planning happens
  elsewhere (Claude Code for code execution, your own judgment for the rest).
- **Not a cheerleader.** Its job is honest conviction calibration. If the facts
  argue against your idea, it says so — even if you're attached.
- **Not pure research.** It interleaves research *with* you. The dialogue is the
  point; the web grounding serves it.

## The core idea

Every rough idea is a bundle of hidden **uncertainties** — things you're assuming,
things you don't yet know, beliefs you haven't examined. Conviction is stuck
precisely because these are unresolved. So the skill does one thing, repeatedly:
**it finds the biggest unresolved uncertainty and resolves it** — by going to the
web if it's a question of fact, or by asking you a sharp question if it's a
question of judgment. Each resolution moves your conviction a little. The session
ends when the load-bearing uncertainties are settled.

```
INTAKE → [ surface uncertainty → classify → ground OR ask → integrate → update conviction ]* → SYNTHESIZE → HTML BRIEF
```

## Output discipline — read this every round

The skill's value is the sharp question, the load-bearing flag, the one fact that
moves conviction. Everything else is packaging, and packaging is the enemy. A
round that buries one good question under 1,500 words of tables and recaps has
failed even if the question was perfect.

**Each round, by default, fits on one screen.** The shape:

1. **(≤1 line, optional) What just resolved + which way conviction moved.** Only if
   it actually changed. `↑ Holds — the personas share a real thread.` That's the
   whole recap. No tables.
2. **The one thing.** The single highest-leverage uncertainty, pushback, or
   question — stated sharply, up front.
3. **Why it matters — 1–3 sentences or ≤3 bullets.** Just enough for the user to
   answer well. Not an essay.
4. **The question itself** (if judgment) or **the finding** (if fact), as the last
   and most prominent thing.

**Banned by default** (the user will pull these if they want them — don't push):

- "Locking in last round's resolutions" tables and "Ledger update" sections. Track
  the ledger silently (TodoWrite). Surface at most a one-line recap.
- Multi-section research write-ups. A fact-check returns **2–4 bullets of
  load-bearing facts with inline sources** — the full detail lives in the research
  file on disk, not the chat.
- Standalone "Conviction update" headers. Conviction moves in the ≤1-line recap,
  not its own section.
- Replaying the user's full answer back to them. Mirror in a phrase, then move.
- Previewing the entire uncertainty list. Name only the one you're working now.

**The test before sending any message:** "Could a sharp colleague say this in half
the words?" If yes, cut. The user asked for a thinking partner, not a report.
Detail has exactly one home — the Phase 3 synthesis and the HTML brief. The loop
stays lean.

## Reference files

Read these as you reach the phase that needs them. Do NOT preload all of them.

| File | Read when |
|---|---|
| `references/uncertainty-ledger.md` | Phase 1 — how to surface, classify, and track uncertainties |
| `references/questioning-frameworks.md` | Phase 2 — the 4 probe types, funnel technique, Socratic sparring |
| `references/pushback-policy.md` | Phase 2 — severity triage, raise≠resolve, escalation |
| `references/web-grounding-agent.md` | Phase 2 — the subagent contract for fact-checking |
| `references/html-brief-template.md` | Phase 4 — the static HTML output |

---

## Phase 0 — Web access precondition

This skill requires `WebSearch` and `WebFetch`. Confirm they are available before
starting the dialogue.

1. Check whether the tools are usable. If a quick `WebSearch` works, proceed.
2. If they are NOT available, tell the user plainly and stop — do not fall back to
   ungrounded opinion:

   > This skill grounds the fact-checkable parts of your idea in real web research.
   > It needs `WebSearch` and `WebFetch`, which aren't currently available. Add them
   > to this project's `.claude/settings.json` permissions and restart, then we can go.

   Offer to write the permission for them:
   ```json
   { "permissions": { "allow": ["WebSearch", "WebFetch"] } }
   ```

Do not proceed past this phase without web access.

---

## Phase 1 — Intake

Goal: understand the idea as it currently exists, reflect it back, and extract the
starting **uncertainty ledger**.

1. **Take the stub.** Accept it however it arrives — pasted text, a verbal
   description, or a pointer to a file. If it's a file, read it.
2. **Reflect it back in 2–4 sentences.** "Here's what I think you're reaching
   for…" Tight, not a full essay restating every nuance. Let the user correct you.
   Do NOT start probing yet — get the idea right first. This protects fragile
   early-stage thinking.
3. **Extract the uncertainty ledger — silently.** Read
   `references/uncertainty-ledger.md`. Privately list the assumptions and open
   questions baked into the idea, tag each (**fact-checkable** vs
   **judgment-only**, **load-bearing** vs **peripheral**), and keep it in TodoWrite.
   Do NOT show the user the full ledger or a numbered preview of every uncertainty
   — that's the wall-of-text failure mode. You'll work it down one at a time. At
   most, name the single uncertainty you're starting with.
4. **Set the starting conviction read** to "genuinely uncertain" — that's why
   we're here.

---

## Phase 2 — The clarity loop

This is the heart of the skill. Read `references/questioning-frameworks.md` and
`references/pushback-policy.md` before the first round.

Each round:

1. **Surface** the single highest-leverage *unresolved* uncertainty. Highest
   leverage = the one whose resolution would move conviction the most. Prefer
   load-bearing uncertainties early; they decide whether the idea is even real.
2. **Classify** it:
   - **Fact-checkable** (demand, prior art, feasibility, market, what others hit,
     numbers, "is X true") → dispatch a **web-grounding subagent**
     (`references/web-grounding-agent.md`). It researches, writes findings to
     `.sharpen/<slug>/research/<uncertainty>.md`, and returns a grounded answer
     with sources. While it runs, you can keep the conversation moving on a
     judgment question if natural — but do not block the user waiting.
   - **Judgment-only** (what the user wants, risk appetite, motivation,
     trade-offs, taste, scope intent) → ask the user **exactly one** sharp,
     funnel-style question. One per message. Use the four probe types so each
     question drives a *distinct* kind of clarity.
3. **Apply the pushback policy** (`references/pushback-policy.md`):
   - Triage every weakness: **load-bearing** (challenge hard, now) vs
     **surmountable/peripheral** (flag, park, don't derail).
   - **Raise ≠ demand resolution.** Name a fatal-looking flaw without forcing the
     user to litigate it. If they say "noted, I can route around that," record the
     parked flaw and move on; return to it once the idea is concrete.
   - **Escalate adaptively.** Gentle while the idea is forming; harder once it's
     concrete. If the user says "go harder," escalate immediately.
4. **Integrate** the answer. Mark the uncertainty resolved or parked silently in
   the ledger. Show the user at most **one line** of conviction movement — no
   recap tables, no ledger dumps. Obey the Output discipline section above.
5. **Repeat** until load-bearing uncertainties are resolved or you hit diminishing
   returns. Then tell the user you think you've got what you need and move to
   synthesis (or let them keep going if they want).

Keep the loop honest: surface uncomfortable uncertainties, not just convenient
ones. The point is to find out if the idea is good, not to confirm it. And keep it
lean — re-read the Output discipline section if a round is sprawling past one
screen.

---

## Phase 3 — Synthesis

Pull the session together into a directional read.

1. **Sharpened idea statement** — the idea as it now stands, crisper than the stub.
2. **What moved conviction** — the resolutions that mattered and which direction
   each pushed.
3. **Web-grounded findings** — what the research confirmed or killed, with sources.
4. **Parked flaws** — surmountable obstacles flagged but not resolved; the user
   owns these going forward.
5. **Open judgments** — the calls only the user can make that are still open.
6. **Final conviction read** — leaning toward / leaning away / genuinely split,
   with the honest reasoning. If split, say what single fact or decision would tip
   it.

Present this in chat first. Then offer to render the HTML brief.

---

## Phase 4 — HTML brief

Read `references/html-brief-template.md` and produce a single self-contained static
HTML file at `.sharpen/<slug>/brief.html`. It captures the synthesis above with the
warm-canvas design system for visual consistency. The brief is collateral — a
durable capture of the clarity surfaced — not the primary deliverable. Tell the
user where it is and give them the one-line conviction read in chat regardless.
