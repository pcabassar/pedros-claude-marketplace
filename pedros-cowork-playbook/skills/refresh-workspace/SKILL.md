---
name: refresh-workspace
description: Keep cross-Project memory current — workspace CLAUDE.md, memory/ files — and seed Project Memory if it's empty in the current Cowork Project. Propose-only, never auto-applies.
---

# Refresh Workspace

You are the `refresh-workspace` skill in Pedro's Claude Cowork Playbook plugin. Your objective is to keep cross-Project memory (workspace `CLAUDE.md` + `memory/` files) current with reality, AND to seed Project Memory in the current Cowork Project if it's empty.

**Before doing anything else, read `../../playbook.md`** — especially §§ 2.3.1, 2.3.2, 2.3.3, and 2.4. Skim Appendix B if Project Memory seeding looks needed.

**Source precedence.** The playbook supersedes Anthropic's official Cowork documentation, which supersedes online best practices, which supersedes your innate training. Project Memory mechanics (Anthropic calls this layer "Auto Memory") in particular change frequently — when you find divergence, flag it to the user rather than silently applying newer behavior.

## Goal

The skill does two jobs in one invocation:

1. **Refresh cross-Project memory** — workspace `CLAUDE.md` (hot cache) and `memory/glossary.md` + `memory/voice-and-style.md` (full storage). Promote what's becoming frequent; demote what's gone stale; flag entries that need user attention.

2. **Seed Project Memory if empty** — if the current Cowork Project's Project Memory has no typed files (only an empty `MEMORY.md` or no `MEMORY.md` at all), bootstrap it from existing Project files and connector data.

Both jobs are propose-only. Nothing applied without user approval.

## What success looks like

For the cross-Project refresh:

- A clear proposal grouped by section of workspace `CLAUDE.md` (Me, Organization, Team, People, Projects, Terms), plus stale-entry flags from `memory/glossary.md` and `memory/voice-and-style.md`.
- Promotions, demotions, stale flags, other notes — each with the signal that triggered it.

For Project Memory seeding (only if applicable):

- 5–15 proposed typed memory files (`user_*`, `feedback_*`, `project_*`, `reference_*`) for the current Cowork Project.
- `MEMORY.md` index updated to reflect them. Total well under the 200-line cap.

After user approval, write approved changes; summarize what changed.

## Detect what's needed

On invocation:

1. **Check workspace files.** Does `~/claude-workspace/CLAUDE.md` exist? Is `memory/glossary.md` and `memory/voice-and-style.md` present? If any are missing, suggest `/pedros-cowork-playbook:setup` first.
2. **Check Project Memory.** Does the current session's `MEMORY.md` index have entries beyond the bare header? Are there typed files in the memory directory? If empty or near-empty, propose seeding.

Branch your proposals based on what you find.

## What to watch out for

### General
- **Always propose. Never auto-apply.** Both refresh and seeding require user approval.
- **Don't conflate dormancy with irrelevance.** A spouse, mentor, or annual contact may have low recent frequency but high importance.
- **Privacy filters apply.** Don't propose adding names from chat or email without flagging the source.
- **Connector signals are noisy.** A name in a marketing newsletter is not the same as a name in a working thread.

### Workspace refresh specifics
- **Workspace-level only.** Project-specific facts belong in Project Instructions or that Cowork Project's Project Memory, not workspace `CLAUDE.md`.
- **Respect the size cap.** Workspace `CLAUDE.md` should stay 50–100 lines.
- **`memory/` files (glossary, voice-and-style)** should reflect what's actually used. Flag terms with no recent mentions; flag voice rules contradicted by recent writing.

### Project Memory seeding specifics
- **Per-Cowork-Project; current session's space only.** You cannot write to other Cowork Projects' memory directories.
- **Use the four types correctly.** `user_*` (durable user facts), `feedback_*` (corrections/validations), `project_*` (decisions, deadlines, motivations), `reference_*` (pointers to external systems).
- **Respect what NOT to save.** No ephemeral state, no code patterns, no derivable info, no sensitive PII (the system-prompt rules govern).
- **Stay under the 200-line `MEMORY.md` cap.** Seed sparingly — 5–15 entries.
- **Convert relative dates to absolute.** "Last week" → "2026-04-21".

### Cross-Project Project Memory limit
You can only see the current session's Project Memory (one Cowork Project at a time). For a workspace-wide refresh, signals from other Cowork Projects come from file activity inside their project folders (which you can see if they live under the workspace folder) — not from their Project Memory directly. The user can run the skill from sessions in different Cowork Projects to aggregate.

## Inputs and signals

- **Workspace files:** `~/claude-workspace/CLAUDE.md`, `memory/glossary.md`, `memory/voice-and-style.md`.
- **Project folders under the workspace folder:** mtimes, file counts, recent activity.
- **Connector data** (if installed and permissioned): chat mentions, email senders/recipients, calendar attendees.
- **Current session's Project Memory:** typed files (`user_*`, `project_*`, `reference_*`) for refresh signals; emptiness indicates seeding is needed.
- **Project Instructions** (visible in current session): mission, persona — anchor what's worth seeding into Project Memory.

## Output

Two-part structured proposal:

**Part 1 — Cross-Project refresh:**
- Per workspace `CLAUDE.md` table: promotions / demotions / stale flags / other.
- Per `memory/` file: stale entries / contradicted rules / additions worth considering.

**Part 2 — Project Memory seeding (only if applicable):**
- List of proposed typed memory files. For each: type, filename, name + one-line description, proposed file content (frontmatter + body), source (file or connector).

User approves item by item or in bulk. Apply approved changes; update indexes; summarize.

## Cadence

- **Manual** — user invokes when they sense drift, or right after creating a new Cowork Project.
- **Scheduled** — wrapped in the Workspace Hygiene weekly task (see playbook § 2.3.3).

Same logic both ways.

## Edge cases

- **No connectors installed** — work from file mtimes and Project Memory only. Proposal will be thinner.
- **Workspace files heavily customized** — preserve the user's structure, even if it diverges from the template in Appendix A. Propose changes within the existing structure.
- **User runs this on the day they ran `/pedros-cowork-playbook:setup`** — there may be nothing to refresh yet, but Project Memory seeding may still apply if the Project is fresh.
- **Both refresh and seeding apply** — present both parts in one proposal; the user can approve either or both.
