# Web-Grounding Subagent

When an uncertainty is fact-checkable, you don't guess and you don't ask the user
— you send a subagent to find out. This is what makes the skill's conviction reads
trustworthy instead of vibes.

## When to dispatch

Dispatch a subagent for any uncertainty whose answer exists in the world:
demand signals, prior art / "has someone built this," competitors, feasibility,
market size, pricing norms, regulations, technical gotchas others hit, benchmarks,
adoption data, "is claim X actually true."

Do NOT dispatch for judgment-only uncertainties — those are questions for the user.

## Dispatch contract

Spawn the subagent with the `Agent` tool. Give it exactly:

1. **The uncertainty as a sharp research question.** Not "research the market" —
   "Do small B2B teams currently pay for standalone X tools, and roughly what do
   they pay?"
2. **What a useful answer looks like** — the specific thing that would move
   conviction. Tell it the decision the answer feeds.
3. **The output contract** below.

### The subagent must:

- Run real `WebSearch` / `WebFetch`. If web tools are unavailable to it, it must
  return the sentinel `WEB_UNAVAILABLE` and nothing else — never fabricate.
- Gather from multiple independent sources; prefer primary/recent over blog
  rehash.
- Distinguish **what the sources say** from **its own inference**.
- Note disconfirming evidence, not just confirming. You want the honest picture.
- Write full findings to `.sharpen/<slug>/research/<uncertainty-slug>.md` with
  inline source URLs, so the work persists and the brief can cite it.
- Return a compact answer to you: the verdict, the confidence (high/medium/low or
  "inconclusive"), the 2-4 load-bearing facts, and the source URLs.

## Honesty rules (non-negotiable)

- **Cite or qualify.** Every factual claim that reaches the brief carries a source,
  or is explicitly marked as the user's own assertion or your inference.
- **Inconclusive is a valid answer.** If the web doesn't settle it, say
  "inconclusive" and explain what's missing. Do not manufacture confidence to give
  the user a clean story.
- **Disconfirming evidence counts double.** If the research undercuts the idea,
  that is a *successful* round — lost conviction with a reason is a win.
- **Never let absent tools become silent opinion.** `WEB_UNAVAILABLE` aborts the
  fact-check; surface it to the user rather than answering from training data.

## Integrating the result — keep it to a handful of bullets

The full findings live in the research file on disk. What reaches the **chat** is
short:

1. **2–4 bullets** of the load-bearing facts, each with its inline source link.
   Not a landscape essay, not a multi-section write-up, not a table of every tool
   you found. The single facts that move conviction — nothing else.
2. **One line** on which way it moves conviction and why.
3. Mark the uncertainty resolved (or "researched, still inconclusive" — itself a
   finding the user should sit with).
4. If it surfaced a *new* uncertainty, add it to the ledger silently.

If the user wants the full landscape, they'll ask — then point them at the
research file or expand. Default to crisp. See the Output discipline section in
SKILL.md.

## Parallelism

If two or three independent fact-checkable uncertainties are open at once, you may
dispatch their subagents in parallel and keep a judgment question going with the
user meanwhile — but never leave the user waiting on a blank screen. The dialogue
is the foreground; research runs behind it.
