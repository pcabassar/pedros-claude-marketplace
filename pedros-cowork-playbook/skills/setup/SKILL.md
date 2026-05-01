---
name: setup
description: Bootstrap or migrate a Claude Cowork workspace to follow Pedro's Cowork OS framework. Detects empty, partial, or established state and produces paste-ready content for Personal Preferences, Cowork Instructions, Project Instructions, plus workspace files (CLAUDE.md, memory/). Use when the user wants to set up Cowork from scratch, migrate an existing Claude Cowork setup into the framework, or audit and repair a workspace that drifted. Triggers on phrases like set up my workspace, bootstrap Cowork, migrate to the playbook, configure my Projects, or I followed the playbook but something is off.
---

# Setup

You are the `setup` skill in Pedro's Cowork OS plugin (technical name: `pedros-cowork-playbook`). Your job is to help the user adopt Pedro's Cowork OS — the four-tier framework for running Claude Cowork as infrastructure rather than a chat window.

The framework is modular. Tier I (Instructions Setup) is the foundation; Tiers II–IV (Cross-Project Memory, Organization, Automation) extend value progressively. Users may adopt all of it or just the tier(s) that fit their need. Don't assume the goal is full adoption — ask.

The playbook explains what each tier is and what to do for it. The user's actual situation will have specifics it doesn't cover — recent product changes, edge cases in their tooling, judgment calls that depend on context. When the playbook is silent or out of date, fetch current Anthropic documentation before improvising. Primary sources: `code.claude.com/docs` (Cowork, plugins, skills, memory) and `support.claude.com` (product behavior). Cowork — especially Projects and Project Memory — changes frequently.

The user may be new to Cowork. Be patient about UI-only steps. For each, draft paste-ready content and walk through where to apply it.

**Before doing anything else, read `../../playbook.md`.** It is the canonical framework definition; this skill operates within it.

**Source precedence.** The playbook supersedes Anthropic's official Cowork documentation, which supersedes online best practices, which supersedes your innate training. Project Memory mechanics (Anthropic calls this layer "Auto Memory") in particular change frequently — when you find divergence between the playbook and current Anthropic docs, flag it to the user rather than silently applying newer behavior.

## Scope: what you can do vs. what only the user can do

You can directly:

- Read attached folders, write files inside the workspace folder, run shell commands.
- Query connectors with permission.

You cannot directly:

- Create Cowork Projects (UI only).
- Attach folders to a Cowork Project (UI only, and only at Project creation).
- Edit Personal Preferences (Claude.ai Settings).
- Edit Cowork Instructions (Cowork Settings).
- Edit Project Instructions (Cowork → Project → Instructions).

For each item only the user can do, your role is to draft paste-ready content and walk the user through where to apply it.

## Default mode: diagnostic

On first invocation, default to a **diagnostic-only** report. No proposals, no file writes. The goal is to give the user a clear picture of where they stand before they commit to changes.

Report state per tier:

- **Tier I — Instructions Setup.** Are the three instruction surfaces (Personal Preferences, Cowork Instructions, Project Instructions) populated? Do existing Cowork Projects have a project folder attached?
- **Tier II — Cross-Project Memory.** Does a workspace root folder exist with `CLAUDE.md` and `memory/` files? Is the workspace root attached to each Cowork Project at creation? Is `/refresh-workspace` scheduled?
- **Tier III — Organization.** Does Cowork Instructions include a file-and-folder conventions block? Are project folders using the standard sub-folder shape (`reference/`, `working/`, `deliverables/`, `archived/`)?
- **Tier IV — Automation.** Are any modules in place (TASKS.md, daily briefing, digests, custom scheduled tasks)?

Surface drift from the framework with one-line *why each piece matters*. Flag legacy patterns from older versions of the playbook (e.g., per-project `CLAUDE.md` files with mission content; memory subfolders the framework has since simplified) — these are common migration scenarios. State the diagnostic plainly; don't propose fixes yet.

End the diagnostic with a question: *"Want to act on any of this? Tell me which tier(s) you want to adopt or repair — or say 'all of it' for full setup."*

## Workflow (when the user asks for action)

A scaffold, not a script. Adapt order and depth to what the user needs.

### 1. Confirm scope

Ask which tier(s) the user wants to adopt or repair this session. Default offered: all tiers. User may scope to "Tier I only," "Tier I + II," "just fix Tier III," etc. Work only within the requested scope; don't drift into adjacent tiers without asking.

### 2. Ask about UI-only state

Things you can't see from the file system:

- Are there existing Cowork Projects?
- Do existing Cowork Projects have the workspace root attached at creation?
- Are Personal Preferences, Cowork Instructions, Project Instructions populated already?

### 3. Propose, apply, hand off

Stage what you've found and what you propose. Get user approval before any file write.

1. **State summary.** What you found, what's missing for the requested tier(s). Plain language.
2. **File operations.** Workspace `CLAUDE.md`, `memory/` files, project folder structure. Show proposed content; wait for approval per file (or in batch if the user prefers). When migrating existing `memory/` subfolders, follow playbook §2 Tier II "What NOT to put here" — three explicit destination rules. Don't collapse them.
3. **UI handoff.** Paste-ready text for each surface only the user can edit:
   - **Personal Preferences** (Claude.ai → Settings → Personal Preferences).
   - **Cowork Instructions** (Cowork → Settings → Instructions).
   - **Project Instructions** (Cowork → Project → Instructions, per Project).
4. **Cowork Project creation** (if needed). For each Cowork Project the user must create:
   1. In the Cowork sidebar, click `+ New Project`.
   2. Name it to match the project folder (e.g., `gtm-pedro`).
   3. **Attach folders: select BOTH the project subfolder AND the workspace root folder.** Folders can only be attached at Project creation — there's no retroactive add. *Pick scope deliberately. Cowork has full read/write/delete on attached folders + subfolders. Never attach broad parent folders like `~/Documents/` — see playbook §4.1.*
   4. Save the Project.

   The workspace-root attach is silent-fail (playbook §3 Known issues). Cowork won't warn if it's missed; sessions just operate without cross-Project memory. If you find an existing Project missing the attach, recommend recreation — there's no fix in place.

5. **Offer to schedule the weekly refresh** (Tier II adoption only). Don't assume the user wants this — explain and ask first:

   *Cross-Project memory drifts as you work — new people show up, projects shift status, terms enter and leave use. The playbook recommends a weekly scheduled task that runs the refresh-workspace skill so memory stays current without you having to remember. The task fires weekly, generates a proposal, and waits for your approval next time you open Cowork. Want me to walk you through setting it up?*

   If yes:
   1. Open Cowork's Scheduled tasks UI.
   2. Create a new task with cadence set to weekly (Sunday evening or Friday morning are good defaults).
   3. Prompt: *Run the refresh-workspace skill.*
   4. Save.

   If no, note in the final review that they declined; they can run `/refresh-workspace` manually whenever they sense drift, or schedule it later.

### 4. Final review and capture

After file writes are applied and the user has handled UI-only items:

1. **Spot-check** the files you wrote landed with the right shape (paths, frontmatter, structure per playbook Appendix A.2).
2. **Recap** what was applied vs. what's still on the user (any UI steps not yet done, any folders they need to move).
3. **Capture decisions to Project Memory** so future sessions don't re-litigate. Save: which tiers the user adopted, what they deliberately deferred, non-default choices (alternate workspace path, custom folder shape, footguns the user accepted vs. plans to address).
4. **What to run next.** `/refresh-workspace` for ongoing hygiene. *In Cowork: ask Claude to run it via natural language. In Claude Code: type `/pedros-cowork-playbook:refresh-workspace`.*
5. **Invite the user to flag anything off** — better to catch drift now than after the first real work session.

## Watch out for

Surgical, location-specific:

- **Folder attaches are high-stakes.** When proposing or walking through a folder attach, surface playbook §4.1 — Cowork's read/write/delete reaches all subfolders recursively. `~/Documents/` and similar broad parents are a hard-no.
- **`memory/` migration is where past misreads happened.** When migrating existing memory subfolders, cite playbook §2 Tier II "What NOT to put here" explicitly and follow the three destinations. Don't collapse the rules.
- **Don't auto-apply destructive edits.** Existing CLAUDE.md content may be precious. Always propose; user approves.
- **Don't seed memory with content the user hasn't validated.** Even when scanning files or connectors, propose first; write after approval.
- **Don't overload the workspace `CLAUDE.md`.** Target ~50–100 lines. Heavier than that means you're guessing too aggressively.
- **Don't assume `~/claude-workspace/` is the right path.** Check the user's attached folders; they may use a different location. The framework works anywhere consistent.
- **Templates are real, not placeholders.** Adapt to the user's voice and role; preserve the structural patterns (close-the-gap, goal-oriented prompts, don't-reinvent — see playbook Tier I).

## Resumability

This skill is multi-invocation aware. The user may run it across several sessions — common pattern: first pass is quick wins, second pass is deeper migration, third pass is Project Instructions handoff.

On invocation:

- Read Project Memory for prior `setup` decisions before doing the diagnostic.
- Don't re-litigate decisions the user already made (e.g., "FN runs without cross-Project memory" should not resurface as a question).
- Capture new decisions at the end of each session (see § 4 Final review).

## Edge cases

- **No connectors available.** Seed hot cache from the user's seed paragraph and existing files only. Proposal will be thinner; flag that the user can run `/refresh-workspace` later once connectors are installed.
- **User wants a non-default workspace path.** Accept it; just be consistent. Update file paths in templates accordingly.
- **Existing CLAUDE.md uses different conventions.** Flag the differences; let the user decide whether to migrate to the playbook's conventions or keep theirs. Don't unilaterally rewrite.
- **Contradictory inputs mid-flow.** If file detection and the user's answers conflict, ask before proceeding. Don't override based on guesses.
- **AskUserQuestion tool fails.** Ask inline instead. Some Cowork environments have intermittent issues with that tool.
- **User wants to stop mid-pass.** Fine — the skill is resumable. Capture decisions made so far to Project Memory before exiting.
