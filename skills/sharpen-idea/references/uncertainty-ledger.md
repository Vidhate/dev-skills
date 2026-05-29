# The Uncertainty Ledger

The ledger is the engine's fuel. An idea is stuck in limbo because the
uncertainties inside it are unresolved. Your job is to make those uncertainties
explicit, rank them, and burn them down one at a time.

## What counts as an uncertainty

Anything the idea silently depends on but hasn't established:

- **Assumptions** — "people will pay for this," "no one has built this," "this is
  technically possible," "I'll have time for it."
- **Open questions** — things the user themselves doesn't know yet.
- **Unexamined beliefs** — claims stated as fact that haven't been checked.
- **Undefined terms** — words doing heavy lifting without a clear meaning.

Surface them by interrogating the stub: for every clause, ask "what has to be true
for this to work?" Each answer is a candidate uncertainty.

## Two tags per uncertainty

Tag every uncertainty on two independent axes. The tags decide what happens to it.

### Axis 1 — How is it resolved?

- **Fact-checkable** — has an answer that exists in the world independent of the
  user. Demand, competitors, prior art, feasibility, market size, regulations,
  "has someone tried this," benchmarks, numbers. → **Goes to the web.**
- **Judgment-only** — the answer lives in the user's head, values, or intent. What
  they want, why it matters to them, risk appetite, what they'd trade off, how
  they'd scope it, taste. → **Goes to a question for the user.**

Some uncertainties are mixed ("is there demand, and do I care about that segment?")
— split them into a fact half and a judgment half.

### Axis 2 — How load-bearing is it?

- **Load-bearing** — if this resolves badly, the whole idea is fake. Tackle these
  first; they decide whether anything else is worth discussing.
- **Peripheral / surmountable** — a real issue, but the idea survives it; it's a
  detail or an obstacle you could route around. Park these; don't let them derail
  early thinking.

## How to work the ledger

1. **Rank by leverage.** Highest leverage = resolving it moves conviction most.
   Load-bearing + currently-believed-but-unchecked = top of the list.
2. **Burn down one per round.** Never batch. One uncertainty, one resolution, one
   conviction update.
3. **Track state.** Each uncertainty is `open`, `resolved`, or `parked`. TodoWrite
   is a good home for the live ledger.
4. **Stop when the load-bearing ones are resolved.** Peripheral uncertainties can
   stay open forever — they become "parked flaws" in the brief. You do not need a
   clean ledger to finish; you need the load-bearing rows settled.

## Anti-patterns

- **Don't surface only the comfortable uncertainties.** The valuable ones are
  usually the ones the user is avoiding. Go there.
- **Don't treat every assumption as load-bearing.** Most aren't. Over-challenging
  peripheral stuff is how you kill good ideas prematurely.
- **Don't research what's actually a judgment call**, or ask the user what the web
  can answer. Misclassifying wastes the user's attention and your tool calls.
