---
description: "Explore a new repository and generate an interactive CODEBASE.html orientation file"
allowed-tools: ["Agent", "Write(CODEBASE.html)", "Read(**/DESIGN.md)", "Read(DESIGN.md)"]
---

# Explore Repo

You are about to explore the current repository using three parallel subagents. Each agent has a single focused question and must return data in a specific structured format. Their job is **data gathering only** — no formatting, no visualization. After all three return, you (the skill) own the rendering step entirely.

## Step 0: Check for DESIGN.md

**You must attempt this step before launching any subagents.** Read `./DESIGN.md` from the root of the current repo. This is a mandatory read attempt — do not skip it.

- If the file exists and has content: store its full contents. You will use them to override the default visual design in Step 2.
- If the file does not exist or is empty: note that no design file was found and proceed. This is the common case — not an error.

## Step 1: Launch three Explore subagents in parallel

Fire all three at the same time (in a single response, as parallel tool calls):

---

**Agent 1 — Entry Points & Core Files**
```
Search this codebase for:
(a) The main execution entry points — files named main, index, app, server, cmd, or similar top-level runners.
(b) The 4–6 most important files a new developer should read to understand how the core functionality works — not just where execution starts, but where the most meaningful business logic, core algorithms, or key abstractions live.

Return your findings in this exact format — no extra prose:

ENTRY_POINTS:
- path: <file path>
  description: <one sentence on what this file does>

CORE_FILES:
- path: <file path>
  why: <one sentence on what core concept or functionality this file illuminates>
  look_for: <one sentence on what specifically to look for inside it>
```

---

**Agent 2 — Domain Models (deep)**
```
Search this codebase for the core data models, types, interfaces, schemas, and entities that define the domain. Look in files named types, models, schema, entities, interfaces, or similar. Find the 5–10 most important domain concepts.

For each one, read the actual definition and extract its fields.

Return your findings in this exact format — no extra prose:

MODELS:
- name: <EntityName>
  file: <file path>
  description: <one sentence on what this entity represents>
  fields:
    - name: <field name>
      type: <field type>
      note: <optional — one short phrase if the field needs explanation, else omit>
  relationships: <optional — one sentence describing how this model relates to others, else omit>
```

---

**Agent 3 — Tech Stack**
```
Read the dependency manifest(s) in this repo (package.json, requirements.txt, go.mod, Cargo.toml, pom.xml, build.gradle, Gemfile, or equivalent).

Return your findings in this exact format — no extra prose:

STACK:
  language: <name and version>
  framework: <primary framework>
  test_runner: <test tool>
  build_tool: <build tool>
  deploy_config: <dockerfile / k8s / serverless / none>

DEPENDENCIES:
- name: <package name>
  purpose: <one short phrase>
```

---

## Step 2: Render CODEBASE.html

Once all four agents have returned, write a `CODEBASE.html` file at the root of the repo. You are fully responsible for the visual design and interactivity. All CSS and JS must be inline — no external dependencies.

### Design source

- **If DESIGN.md was found in Step 0** — use its contents as your primary design guide (colors, typography, tone, layout preferences). Adapt the structure below to match that design language.
- **If DESIGN.md was not found** — use the default design spec below.

### Default design spec

- **Color palette** — dark background `#0d1117`, card surfaces `#161b22`, borders `#30363d`, primary accent `#58a6ff`, secondary accent `#3fb950`, muted text `#8b949e`, body text `#e6edf3`, amber `#e3b341`.
- **Typography** — `-apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif`. File paths and code in `monospace`.
- **No external fonts, no CDN links, no images.** Pure HTML + inline CSS + inline JS only.

### Layout

**Header strip** — full width. Left: repo name (large) + one-sentence summary below it. Right: tech stack pills (language, framework, test runner, build tool, deploy config) rendered as small rounded badge tags. Each pill is `#161b22` background with `#30363d` border and `#e6edf3` text.

**Main body** — two-column layout below the header:

- **Left column (40%)** — Entry Points panel with `#58a6ff` top border accent.
  - For each entry point: monospace filename pill + description below it.

- **Right column (60%)** — Domain Model panel with `#3fb950` top border accent.
  - Each model is a **clickable card**. Collapsed state shows: model name (bold) + file path (muted monospace) + one-line description. An expand chevron (▶) on the right.
  - Clicking the card expands it in-place to reveal a fields table: columns are Field, Type, Note. Style the table with subtle row striping. If `relationships` is present, show it below the table as a muted italic line.
  - Only one card can be expanded at a time. Clicking an open card collapses it. Clicking a different card collapses the open one and expands the new one.
  - Smooth CSS transition on expand/collapse (max-height animation, 200ms ease).

**Where to Start section** — full-width strip below the two columns, `#161b22` background, `#e3b341` top border accent.
- Title: "Where to Start Reading"
- Numbered list from Agent 1's `CORE_FILES`. For each file:
  - Number badge (amber circle) + file path in a monospace clickable badge that copies the path to clipboard on click (show a brief "Copied!" tooltip) + the `why` sentence in body text + the `look_for` sentence in smaller muted italic text below.

**Interactivity requirements:**
- Domain model cards expand/collapse on click (accordion, one open at a time).
- File path badges in "Where to Start" copy path to clipboard on click with a tooltip confirmation.
- Add a small live search/filter input at the top of the Domain Model panel. Typing filters visible model cards by name in real time (case-insensitive). If no matches, show "No models match." Cards that don't match are hidden, not removed.
- All interactive behaviour must work with zero external JS libraries.

**Footer** — muted centered text: `Generated by /explore-repo · {DATE}`.

### Content mapping

| Section | Source |
|---|---|
| Repo name | Infer from root folder name or manifest `name` field |
| One-sentence summary | Synthesize from Agent 1 + Agent 2 findings |
| Tech stack pills | Agent 3 `STACK` fields |
| Entry Points panel | Agent 1 `ENTRY_POINTS` |
| Domain Model panel | Agent 2 `MODELS` — collapsed cards with expandable field tables |
| Where to Start | Agent 1 `CORE_FILES` |
| Design overrides | DESIGN.md if found in Step 0 |
