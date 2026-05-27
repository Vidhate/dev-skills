# Verification Agent Protocol

After synthesis completes, spawn a **Verification Agent (V1)** that audits all deliverables for consistency, accuracy, and completeness. This step catches issues that individual agents and synthesis can miss.

## When to Run

- After synthesis is complete and all deliverable files are written
- Before presenting the final output to the user
- Uses one agent: **V1: Verification**

## Agent Task

The V1 agent reads ALL deliverable files (not raw files) and checks them against the rules below. It produces a `verification-report.md` in the project directory.

## Universal Checks

These apply to every skill in the startup plugin:

### 1. Claims Without Source
Every quantitative claim must have a data label: **[Data]**, **[Estimate]**, **[Assumption]**, or **[Opinion]**. Flag any number, percentage, or factual assertion without a label.

### 2. Internal Contradictions
Cross-check numbers and statements across deliverable files. Flag when:
- The same metric appears with different values in two files
- A claim in one file contradicts a claim in another
- Confidence ratings disagree (e.g., "High confidence" in one file, different data in another suggests Medium)

### 3. Confidence Rating Consistency
Verify that confidence ratings match the evidence:
- A claim with only one Tier 3 source cannot be rated **High**
- A claim with multiple Tier 1 sources should not be rated **Low**
- Every major section must have a confidence rating

### 4. Data Gaps Declared
Every deliverable must have a Data Gaps section. Flag:
- Files missing the Data Gaps section entirely
- Sections where data is clearly thin but no gap is declared
- Gaps mentioned in raw files that didn't make it into the synthesized deliverables

### 5. Flags Present
Every deliverable must end with Red Flags and Yellow Flags sections. Flag:
- Files missing these sections
- Files with "No flags identified" where the content clearly contains risks

### 6. Stale Data
Flag any data point older than 18 months that isn't marked as potentially outdated.

### 7. Duplicate Sources
Flag when the same source is used as "independent corroboration" in multiple places. Two claims both citing the same blog post don't have independent verification.

## Skill-Specific Checks: startup-competitors

In addition to the universal checks above, verify:

### Battle Card vs. Report Consistency
- Every competitor in the battle cards must appear in the `competitors-report.md` Key Players table
- Strengths/weaknesses in battle cards must not contradict the competitor profiles in the report
- Threat levels must be consistent between battle cards and the report

### Matrix vs. Profiles Alignment
- Every competitor in `competitive-matrix.md` must have a profile in `competitors-report.md`
- Feature ratings (Strong/Adequate/Weak/Missing) in the matrix must be supported by evidence in the report or battle cards
- Gap analysis items must connect to actual findings, not assumptions

### Pricing Landscape vs. Profiles Consistency
- Pricing data in `pricing-landscape.md` must match pricing mentioned in battle cards
- Value metrics must be consistent across the pricing landscape and competitor profiles
- Switching cost assessments must align between pricing landscape and battle cards

### Cross-Deliverable Coherence
- Strategic opportunities in the report must be supported by evidence from at least two deliverables (e.g., pricing gap + customer complaint)
- Strategic risks must be traceable to specific competitor data
- Moat assessment must reference specific competitors and evidence

### HTML Brief Checks (Phase 4)
After Phase 4 generates `index.html`, also verify:

1. **HTML deliverable exists** at `{project-name}/index.html` and is well-formed (contains `</html>`, `<style>`, and `<script>` blocks).
2. **Viewport meta tag present.** `<meta name="viewport" content="width=device-width, initial-scale=1.0">` must be in `<head>`. Without this the mobile breakpoints don't activate.
3. **Tab count matches battle cards.** Number of `.tab` elements in `index.html` equals the number of `battle-cards/*.md` files. Catches drift when synthesis adds a battle card but HTML wasn't refreshed.
4. **Citations point to real files.** Every `.source-cite` and `.source-cite-inline` block references a filename that exists in the project directory. Catches typos and stale citations after deliverable renames. Cite the file path verbatim (e.g., `executive-summary.md`, `battle-cards/norm-law.md`).
5. **No fabricated numbers.** Spot-check 5 quantitative claims in the HTML (KPIs, pull-stats, pricing band) — each must appear in a synthesis deliverable. Flag any HTML number that doesn't have a direct source in the markdown.
6. **Empty-section integrity.** If a section renders a `.data-gap` placeholder, confirm the corresponding markdown deliverable is genuinely missing or empty — don't gap-out sections with available source data.
7. **Verification section present and accurate.** The HTML's `#verification` section must reflect the *current* `verification-report.md` — not a stale version. Cross-check: the verdict badge color matches the actual issue counts; the Critical / Warnings / Info counts match the report; the checklist's checked items match the report's checklist.

### Verification self-roll-up

This verification report itself feeds back into the HTML brief's `#verification` section in Phase 4. That means:

- **Write `verification-report.md` first** with the standard sections (Summary, Critical, Warnings, Info, Verification Checklist). Phase 4 reads from this file directly.
- **Use consistent labels.** The HTML reads `## Summary` for the counts; `## Critical Issues`, `## Warnings`, `## Info` for the issue lists; `## Verification Checklist` for the checkbox list. If you rename these headings, the HTML rollup breaks.
- **Be honest in this file.** It will be visible in the HTML to whoever reads the brief. Don't soften critical issues to make the verdict badge green.

## Output: verification-report.md

```markdown
# Verification Report: {project-name}
*Generated: {date}*

## Summary
- **Critical issues:** {count}
- **Warnings:** {count}
- **Info:** {count}

## Critical Issues
Issues that could mislead decision-making. The process pauses here for user review.

### {Issue title}
- **File(s):** {affected files}
- **Section:** {section name}
- **Problem:** {description}
- **Suggested fix:** {how to resolve}

## Warnings
Issues that reduce quality but don't block decisions.

### {Issue title}
- **File(s):** {affected files}
- **Problem:** {description}
- **Suggested fix:** {how to resolve}

## Info
Minor improvements and observations.

- {observation}
- {observation}

## Verification Checklist
- [ ] All quantitative claims labeled
- [ ] No internal contradictions found
- [ ] Confidence ratings consistent with evidence
- [ ] Data gaps declared in all deliverables
- [ ] Red/Yellow flags present in all deliverables
- [ ] No stale data unmarked
- [ ] No duplicate-source false corroboration
- [ ] Battle cards consistent with report (skill-specific)
- [ ] Matrix aligned with profiles (skill-specific)
- [ ] Pricing landscape consistent with profiles (skill-specific)
- [ ] Opportunities backed by multi-source evidence (skill-specific)
- [ ] HTML brief exists and is well-formed (Phase 4)
- [ ] HTML tab count matches battle-cards/*.md count (Phase 4)
- [ ] HTML citations reference real files (Phase 4)
- [ ] No fabricated numbers in HTML (Phase 4)
- [ ] Empty-section placeholders match actual missing deliverables (Phase 4)
```

## Flow Control

The skill runs autonomously after intake — **never pause for user input here**, even on critical issues. Critical findings are surfaced in two places already: the `verification-report.md` file, and the `#verification` section of the Phase 4 HTML brief (which colors its verdict badge red when critical issues are present). The user reviews them after the run.

- **Regardless of issue counts:** print a one-line summary ("Verification: {N} critical, {N} warnings, {N} info — see `verification-report.md`") and continue to Phase 4.
- **Be honest in the report.** Don't soften critical issues to make the rollup look better — the file is visible to whoever reads the brief.
