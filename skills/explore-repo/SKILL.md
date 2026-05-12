---
description: "Explore a new repository and generate an interactive CODEBASE.html orientation file"
allowed-tools: ["Agent", "Read(**/DESIGN.md)", "Read(DESIGN.md)"]
---

# Explore Repo

You are about to explore the current repository using three parallel subagents, then hand all findings to a dedicated rendering subagent that writes the HTML. Your main thread only coordinates — it does not write or render anything.

## Step 0: Check for DESIGN.md

**You must attempt this step before launching any subagents.** Read `./DESIGN.md` from the root of the current repo. This is a mandatory read attempt — do not skip it.

- If the file exists and has content: store its full contents. You will pass them to the rendering agent in Step 2.
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
  relationships:
    - target: <OtherEntityName>
      label: <short verb phrase, e.g. "has many", "belongs to", "references">
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

## Step 2: Launch the rendering subagent

Once all three Explore agents have returned, fire a single general-purpose subagent and pass it the complete structured output from all three agents plus the DESIGN.md contents (or note that none was found). The rendering agent's sole job is to write `CODEBASE.html`.

Pass this exact prompt to the rendering agent, with the agent outputs substituted in:

---

```
You are a rendering agent. Your only job is to write a single self-contained `CODEBASE.html` file to the current working directory. Do not explain your work. Do not summarise what you wrote. Just write the file and stop.

You have been given the following data from a codebase exploration:

<agent1_output>
{PASTE AGENT 1 OUTPUT HERE}
</agent1_output>

<agent2_output>
{PASTE AGENT 2 OUTPUT HERE}
</agent2_output>

<agent3_output>
{PASTE AGENT 3 OUTPUT HERE}
</agent3_output>

<design_file>
{PASTE DESIGN.md CONTENTS HERE, OR WRITE "NOT FOUND" IF ABSENT}
</design_file>

---

## Design source

- If <design_file> has content: use it as your primary design guide (colors, typography, tone, layout). Adapt the structure below to match that design language.
- If <design_file> is "NOT FOUND": use the default design spec below.

## Default design spec

- Color palette: dark background `#0d1117`, card surfaces `#161b22`, borders `#30363d`, primary accent `#58a6ff`, secondary accent `#3fb950`, muted text `#8b949e`, body text `#e6edf3`, amber `#e3b341`.
- Typography: `-apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif`. File paths and code in monospace.
- Mermaid.js is loaded via CDN: `https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js`. All other behaviour is pure inline CSS and JS — no other external dependencies.

## Layout

**Header strip** — full width. Left: repo name (large) + one-sentence summary below it. Right: tech stack pills (language, framework, test runner, build tool, deploy config) as small rounded badge tags.

**Main body** — two-column layout:

- Left column (40%) — Entry Points panel with `#58a6ff` top border accent. For each entry point: monospace filename pill + description below it.

- Right column (60%) — Domain Model panel with `#3fb950` top border accent.
  - Live search/filter input at the top. Typing filters cards by name in real time (case-insensitive).
  - Each model is a clickable accordion card. Collapsed: model name (bold) + file path (muted monospace) + one-line description + expand chevron (▶).
  - Expanded: fields table (Field / Type / Note columns, subtle row striping) + relationships listed below as muted italic lines.
  - One card open at a time. Smooth max-height CSS transition, 200ms ease.

**Diagrams section** — full-width panel below the two columns, `#161b22` background, `#58a6ff` top border accent. Title: "Relationships & Dependencies". Use Mermaid.js to render the following diagrams side by side (or stacked if content warrants it):

1. **Domain model relationship diagram** — a `graph LR` (or `erDiagram` if relationships are well-defined) showing connections between the models from Agent 2. Use the `relationships` field on each model to draw edges with labels. Only include models and edges that have explicit relationship data — do not invent connections.

2. **Entry point flow diagram** — a `graph TD` showing the execution flow from entry points down to the key abstractions or services they touch, inferred from Agent 1's entry point descriptions and Agent 2's model data. Keep it to 8–12 nodes maximum — prune aggressively for clarity.

Initialise Mermaid with: `mermaid.initialize({ startOnLoad: true, theme: 'dark' })`.
Wrap each diagram in a `<div class="mermaid">` block. If there is insufficient relationship data to draw a meaningful diagram, omit that specific diagram rather than drawing an empty or trivial one.

**Where to Start section** — full-width strip, `#161b22` background, `#e3b341` top border accent. Title: "Where to Start Reading". Numbered list from CORE_FILES. For each file: amber number badge + monospace path badge (click to copy, show "Copied!" tooltip) + `why` sentence + `look_for` in smaller muted italic below.

**Footer** — muted centered text: `Generated by /explore-repo · {DATE}`.

## Content mapping

| Section | Source |
|---|---|
| Repo name | Infer from manifest `name` field or root folder name |
| One-sentence summary | Synthesize from Agent 1 + Agent 2 |
| Tech stack pills | Agent 3 STACK |
| Entry Points panel | Agent 1 ENTRY_POINTS |
| Domain Model panel | Agent 2 MODELS |
| Domain relationship diagram | Agent 2 MODELS relationships |
| Entry point flow diagram | Agent 1 ENTRY_POINTS + Agent 2 MODELS |
| Where to Start | Agent 1 CORE_FILES |
| Design overrides | design_file if present |
```

---

## Step 3: Completion message

Once the rendering agent confirms the file is written, respond with a single short line — nothing more:

```
✓ CODEBASE.html ready. Open it in a browser to explore the repo.
```

Do not summarise the design choices, list the sections, describe the color palette, enumerate the models, or explain what was generated. The HTML speaks for itself.
