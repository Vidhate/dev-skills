# Setup — startup-competitors

This skill runs autonomously after intake **only if permissions are pre-granted** for the session it runs in. Without setup, the skill will pause on permission prompts mid-run (WebSearch, WebFetch, Read on plugin files, Agent spawning), and parallel subagents do *not* inherit grants made to other subagents — so approving once doesn't fix it for the rest.

## One-time setup

When you first invoke this skill in a project, **Phase 0** of the skill will:

1. Check for `.claude/settings.local.json` in the project root.
2. If the file is missing required permissions, **write the file and abort with a clear message asking you to restart Claude Code**.
3. On your next invocation, the permissions are loaded at session start and the skill runs end-to-end without interruption.

This is by design: Claude Code reads `settings.local.json` at session start, not on every tool call. There is no way for a skill to grant itself permissions inside the same session — the one-time restart is unavoidable.

## What the skill writes

If you'd rather create the file manually before first invocation, here's the canonical content. Drop this into `<project-root>/.claude/settings.local.json`:

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

The two `Read(...)` entries handle reading this skill's own `references/*.md` files from the plugin install path. Adjust the glob if your plugins live elsewhere.

## Per-platform notes

- **Claude Code (terminal)** — this is the platform the autonomy work targets. The `.claude/settings.local.json` mechanism applies here.
- **Claude.ai (web)** — no settings file; permissions are not an issue but parallel `Agent` execution isn't available, so the skill runs sequentially (slower, same depth).

## What happens if you skip setup

The skill will:

1. Fail Phase 0's precondition check and abort early with the message above (best case).
2. If you bypass Phase 0 somehow: subagents may fail a WebSearch probe and write a `PERMISSION_DENIED` sentinel to their output. The parent will detect this and abort the run rather than letting the subagent fall back to parametric knowledge silently.

In neither case will the skill silently produce ungrounded output — that was the explicit design goal of the autonomy refactor.

## Future permissions (heads-up)

A planned follow-up (link-validation subagent — see `FOLLOWUPS.md` #6) will additionally require:

- `Bash(python3:*)` — for the parallel link-validation script
- `Bash(curl:*)` — fallback HEAD probes

These are *not* in the canonical block above yet. They'll be added when #6 ships.
