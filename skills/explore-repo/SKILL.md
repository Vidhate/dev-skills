---
description: "Explore a new repository and generate an interactive two-tab .codebase/index.html orientation file"
allowed-tools: ["Agent"]
---

# Explore Repo

You are the coordinator. You do not write files or render anything. You run subagents in sequence, collect their outputs, and pass everything to a rendering agent.

## Step 1: Launch three Explore subagents in parallel

Fire all three simultaneously in a single response:

---

**Agent 1 — Entry Points & Core Files**
```
Search this codebase for:
(a) The main execution entry points — files named main, index, app, server, cmd, or similar top-level runners.
(b) The 4–6 most important files a new developer should read to understand core functionality — where the most meaningful business logic, algorithms, or key abstractions live.

Be thorough. Read the entry files themselves, not just their names. Trace what they import and invoke.

Return in this exact format:

ENTRY_POINTS:
- path: <file path>
  description: <one sentence on what this file does>
  key_lines:
    - line: <line number>
      reason: <why this line is significant>

CORE_FILES:
- path: <file path>
  why: <one sentence on what core concept this file illuminates>
  look_for: <one sentence on what specifically to look for inside it>
  key_lines:
    - line: <line number>
      reason: <why this line is significant>

NOTES:
<Write 3–5 sentences of dense prose describing the overall execution flow, architectural patterns observed, and anything surprising or important about how this codebase is structured. This will be used as source-of-truth context for diagram generation.>
```

---

**Agent 2 — Domain Models (deep)**
```
Search this codebase for the core data models, types, interfaces, schemas, and entities that define the domain. Look in files named types, models, schema, entities, interfaces, or similar. Find the 5–10 most important domain concepts. Read each definition fully.

Return in this exact format:

MODELS:
- name: <EntityName>
  file: <file path>
  description: <one sentence on what this entity represents>
  key_lines:
    - line: <line number>
      reason: <why this line is significant — e.g. "class definition", "critical field", "important method">
  fields:
    - name: <field name>
      type: <field type>
      note: <optional — one short phrase if the field needs explanation, else omit>
  relationships:
    - target: <OtherEntityName>
      label: <short verb phrase, e.g. "has many", "belongs to", "references">

NOTES:
<Write 3–5 sentences of dense prose describing the domain model as a whole — the core business concepts, how the entities relate to each other at a high level, and any patterns (e.g. event sourcing, CQRS, normalisation strategy) that are evident. This will be used as source-of-truth context for diagram generation.>
```

---

**Agent 3 — Tech Stack**
```
Read the dependency manifest(s) in this repo (package.json, requirements.txt, go.mod, Cargo.toml, pom.xml, build.gradle, Gemfile, or equivalent). Also look for any config files (Dockerfile, .env.example, CI config, deployment manifests).

Return in this exact format:

STACK:
  language: <name and version>
  framework: <primary framework>
  test_runner: <test tool>
  build_tool: <build tool>
  deploy_config: <dockerfile / k8s / serverless / none>

DEPENDENCIES:
- name: <package name>
  purpose: <one short phrase>

NOTES:
<Write 2–3 sentences on the overall technology choices — why this stack makes sense for the apparent use case, any notable architectural dependencies, and any tooling that would affect a new developer's setup.>
```

---

## Step 2: Launch the File Explorer subagent

After all three agents from Step 1 have returned, collect every unique file path mentioned across Agent 1 (`ENTRY_POINTS` paths, `CORE_FILES` paths) and Agent 2 (`MODELS` file paths). Then fire this agent:

---

**Agent 4 — File Explorer Data**

Pass the collected critical file paths into this prompt:

```
You are gathering raw data for an IDE-style file explorer embedded in an HTML page.

The following files have been identified as critical in this codebase:
<critical_files>
{LIST ALL UNIQUE CRITICAL FILE PATHS FROM AGENTS 1 AND 2 HERE, ONE PER LINE}
</critical_files>

Your tasks:

TASK A — Full directory tree:
Walk the entire repository directory structure. Exclude: .git, node_modules, __pycache__, .venv, venv, dist, build, .next, coverage. Return the tree as indented text (2 spaces per level), directories first within each level, like this:

DIRECTORY_TREE:
src/
  api/
    routes.py
    handlers.py
  models/
    user.py
  main.py
README.md
package.json

TASK B — Critical file contents:
For each file in <critical_files>, read its full contents and return them verbatim — character for character, exactly as they appear on disk. Do not paraphrase, summarise, or regenerate any content. If a file exceeds 600 lines, include the first 600 lines and append "... [truncated at 600 lines]".

Return in this format for each file:

FILE_CONTENT:
path: <file path>
---
<exact file contents here>
---

TASK C — Annotations:
For each critical file, return the line numbers that are most significant (entry of a class, key function, important constant, etc.), using the key_lines data provided below.

ANNOTATIONS from exploration agents:
{PASTE ALL key_lines DATA FROM AGENTS 1 AND 2 HERE, GROUPED BY FILE PATH}
```

---

## Step 3: Launch the rendering subagent

Once all four agents have returned, fire a single general-purpose subagent. Pass it the complete outputs from all agents. The rendering agent reads DESIGN.md itself and writes `.codebase/index.html` — nothing else.

Use this exact prompt, substituting all agent outputs:

---

```
You are a rendering agent. Your only job is to write a single self-contained `.codebase/index.html` file. Create the `.codebase/` directory at the root of the current working directory if it does not already exist, then write the file. Do not explain, summarise, or describe what you wrote. Write the file and stop.

## Step 0: Read DESIGN.md

Before generating any HTML, attempt to read `./DESIGN.md` from the root of the current repo. Most repos will not have this file — that is the normal case, not an error.

- Found and has content → use it as your primary design guide (colors, typography, tone, layout) for both tabs. Adapt the structure below to match that design language.
- Not found or empty → use the default design spec below for both tabs.

## Exploration data

<agent1_output>{AGENT 1 FULL OUTPUT}</agent1_output>
<agent2_output>{AGENT 2 FULL OUTPUT}</agent2_output>
<agent3_output>{AGENT 3 FULL OUTPUT}</agent3_output>
<agent4_output>{AGENT 4 FULL OUTPUT}</agent4_output>

## Default design spec

- Color palette: dark background `#0d1117`, card surfaces `#161b22`, borders `#30363d`, primary accent `#58a6ff`, secondary accent `#3fb950`, muted text `#8b949e`, body text `#e6edf3`, amber `#e3b341`, highlight yellow `#f0e68c22` (semi-transparent, for line highlights).
- Typography: `-apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif`. All file paths, code, and file contents in `monospace`.
- Mermaid.js via CDN: `https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js`. Everything else: pure inline CSS + JS, zero other external dependencies.

---

## Global structure: two tabs

The entire page is controlled by a tab bar fixed at the top. Two tabs:

1. **Overview** — high-level codebase intelligence (existing layout, described below)
2. **Explorer** — read-only IDE-style file browser (new, described below)

Tab bar style: fixed position, full width, `#010409` background, tab buttons with active state highlighted using the primary accent underline. Switching tabs shows/hides the corresponding `<div>` — no page reload.

---

## Tab 1: Overview

### Header strip
Full width. Left: repo name (large) + one-sentence summary synthesised from Agent 1 and Agent 2 NOTES. Right: tech stack pills (language, framework, test runner, build tool, deploy config) as small rounded badge tags.

### Main body — two columns
- **Left column (40%)** — Entry Points panel, `#58a6ff` top border accent. For each entry point: monospace filename pill + description.
- **Right column (60%)** — Domain Model panel, `#3fb950` top border accent.
  - Live search/filter input at the top (case-insensitive, filters cards by name in real time).
  - Each model: clickable accordion card. Collapsed state: name (bold) + file path (muted monospace) + one-line description + chevron (▶). Expanded: fields table (Field / Type / Note, subtle row striping) + relationships as muted italic lines below the table.
  - One card open at a time. Smooth max-height CSS transition, 200ms ease.

### Diagrams section
Full-width panel, `#161b22` background, `#58a6ff` top border. Title: "Relationships & Dependencies".

Use the Agent 1 and Agent 2 NOTES (verbose prose) as your primary source of truth for what relationships and flows actually exist — not just the structured fields. Use the structured `relationships` and `ENTRY_POINTS` data to draw edges precisely.

Render two Mermaid diagrams side by side:

1. **Domain model relationships** — `graph LR` or `erDiagram`. Draw edges using `relationships` data (target + label). Only draw edges that are explicitly stated. Do not invent connections.

2. **Execution flow** — `graph TD`. Trace from entry points through the key services, handlers, and models they touch. Use Agent 1 NOTES and Agent 2 NOTES to understand the real flow. Keep to 8–12 nodes — prune for clarity.

Initialise Mermaid: `mermaid.initialize({ startOnLoad: true, theme: 'dark' })`. Each diagram in a `<div class="mermaid">` block. If a diagram would be trivial or empty, omit it.

### Where to Start section
Full-width strip, `#161b22` background, `#e3b341` top border. Title: "Where to Start Reading". Numbered list from `CORE_FILES`. Each item: amber number badge + monospace path badge (click-to-copy with "Copied!" tooltip) + `why` sentence + `look_for` in smaller muted italic below.

### Footer
Muted centered text: `Generated by /explore-repo · {DATE}`.

---

## Tab 2: Explorer

A read-only IDE-style split-pane interface. This tab occupies the full viewport height below the tab bar.

### Layout
- **Left pane (25%, fixed height, scrollable)** — file tree
- **Right pane (75%, fixed height, scrollable)** — file content viewer
- A thin `#30363d` vertical divider between them.

### Left pane — file tree

Render the full directory tree from Agent 4 `DIRECTORY_TREE`. Rules:
- Directories are collapsible. All directories start expanded.
- Each file is a clickable row. Clicking loads that file's content into the right pane.
- **Critical files** (any file listed in Agent 4 `FILE_CONTENT`) are visually distinguished: a small colored dot before the filename. Color the dot by category:
  - Entry point files → `#58a6ff` (blue)
  - Domain model files → `#3fb950` (green)
  - Core files → `#e3b341` (amber)
  - A file may have multiple categories — show the highest-priority dot (blue > green > amber).
- Non-critical files appear in the tree normally but without a dot.
- Directory rows have a `▶`/`▼` toggle. Clicking collapses/expands children.
- Style: `#0d1117` background, `#e6edf3` text, subtle hover highlight `#161b22`, active/selected file highlighted with a left border in the accent color.

### Right pane — file content viewer

Default state (nothing selected): show a centered message — "Select a file from the tree to view its contents."

When a critical file is selected:
- Display the file path as a header above the content area.
- Render the file contents **exactly as provided in Agent 4 `FILE_CONTENT`** — do not alter, reformat, or regenerate any content character.
- Show line numbers in a fixed-width gutter on the left, styled in muted text.
- **Highlighted lines**: any line number listed in the annotations for this file gets a full-width background highlight in `#f0e68c22` (semi-transparent yellow). On hover over a highlighted line, show a tooltip with the `reason` text from the annotation.
- Wrap the content in a `<pre>` block with `white-space: pre` and `overflow-x: auto`.

When a non-critical file is selected:
- Show the file path as a header.
- Show a styled message in the content area: "Content not pre-loaded. Open this file in your editor to view it." with a click-to-copy path button.

### Explorer interactivity rules
- All JS inline, no libraries except Mermaid (which is only used in Tab 1).
- File tree state (which directories are open) persists while the tab is active.
- The currently selected file row stays highlighted in the tree even after scrolling.
- Line number gutter and content scroll together (use a flex container, not separate scroll regions).
```

---

## Step 4: Completion message

Once the rendering agent confirms the file is written, respond with exactly:

```
✓ .codebase/index.html ready — Overview tab for high-level intelligence, Explorer tab for annotated file browsing.
```

Nothing else. Do not describe, summarise, or list what was generated.
