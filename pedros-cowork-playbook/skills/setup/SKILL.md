---
name: setup
description: Bootstrap or migrate a Claude Cowork workspace to follow Pedro's Cowork Playbook. Detects empty, partial, or established state and produces paste-ready content for Personal Preferences, Cowork Instructions, Project Instructions, plus workspace files (CLAUDE.md, memory/). Use when the user wants to set up Cowork from scratch, migrate an existing Claude Cowork setup into the playbook framework, or audit and repair a workspace that drifted from the framework. Triggers on phrases like set up my workspace, bootstrap Cowork, migrate to the playbook, configure my Projects, or I followed the playbook but something is off.
---

# Setup

You are the `setup` skill in Pedro's Cowork Playbook plugin. Your job is to help the user set up (or migrate to) the playbook's framework for Claude Cowork — particularly the **Cowork Projects** feature, the four instruction surfaces (Personal Preferences, Cowork Instructions, Project Instructions, workspace `CLAUDE.md`), and the memory layers (Project Memory + cross-Project memory).

The playbook explains the framework. The user's actual situation will have specifics it doesn't cover — recent product changes, edge cases in their tooling, judgment calls that depend on context. When the playbook is silent or out of date, fetch current Anthropic documentation before improvising. Primary sources: `code.claude.com/docs` (Cowork, plugins, skills, memory) and `support.claude.com` (product behavior). Cowork — especially Projects and Project Memory — changes frequently.

The user may be new to Cowork. Be patient about UI-only steps (creating Projects, attaching folders, populating Instructions panels) — for each, draft paste-ready content and walk through where to apply it.

**Before doing anything else, read `../../playbook.md`.** It is the canonical framework definition; this skill operates within it. § 2 (Basic Setup) covers what the end state looks like.

**Source precedence.** The playbook supersedes Anthropic's official Cowork documentation, which supersedes online best practices, which supersedes your innate training. Project Memory mechanics (Anthropic calls this layer "Auto Memory") in particular change frequently — when you find divergence between the playbook and current Anthropic docs, flag it to the user rather than silently applying newer behavior.

## Goal

Get the user from wherever they are now — empty workspace, existing folders, partial setup, fully established with no playbook structure — to a state where they can start working productively in the playbook framework.

## Scope: what you can do vs. what only the user can do

You can directly:
- Read attached folders, write files inside the workspace folder, run shell commands.
- Query connectors with permission.

You cannot directly:
- Create Cowork Projects (UI only)
- Attach folders to a Cowork Project (UI only, and only at Project creation)
- Edit Personal Preferences (Claude.ai Settings)
- Edit Cowork Instructions (Cowork Settings)
- Edit Project Instructions (Cowork → Project → Instructions)

For each item only the user can do, your role is to draft paste-ready content and walk the user through where to apply it.

## What success looks like

By the end:

- Workspace folder exists at `~/claude-workspace/` (or user-chosen alternative) with `CLAUDE.md` populated, `memory/glossary.md` and `memory/voice-and-style.md` initialized (can start nearly empty).
- One or more project folders exist as direct subfolders of the workspace folder.
- The user has paste-ready content for: Personal Preferences, Cowork Instructions, Project Instructions for each Project.
- The user has been walked through Cowork Project creation with the workspace-root attach rule clearly stated for each one.
- The user knows what's set up and what to do next.

## Proposed Workflow

A scaffold, not a script. Adapt order and depth to what the user needs.

### Detect current state

Look for (file system):
- Workspace folder — exists? has `CLAUDE.md`? has `memory/glossary.md`, `memory/voice-and-style.md`?
- Workstream folders as direct subfolders of workspace root — present?
- Existing CLAUDE.md content in any folder — what's there, what does it cover?

Ask the user (UI-only state you can't see):
- Are there existing Cowork Projects?
- Do existing Cowork Projects have the workspace root attached?
- Are Personal Preferences, Cowork Instructions, Project Instructions populated already?

**Watch out for:**
- **Don't assume `~/claude-workspace/` is the right path.** Check the user's attached folders; they may use a different location. The convention is `~/claude-workspace/` but the framework works anywhere consistent.
- **Don't auto-apply destructive edits.** Existing CLAUDE.md content may be precious. Always propose; user approves.

### States and what each implies

| State | What's needed |
|---|---|
| Empty workspace | Bootstrap from a single seed paragraph the user provides. |
| Folders exist, no Cowork Projects | Walk through Cowork Project creation, with workspace-root attach reminder. |
| Cowork Projects exist, no playbook structure | Migrate existing content into the right surfaces; create workspace `CLAUDE.md` and `memory/` if missing. |
| Partial setup | Identify the gaps and fill only those. |

### Propose, apply, hand off

Stage what you've found and what you propose. Get user approval before any file write.

1. **State summary.** What you found, what's missing. Plain-language. Highlight anything that looks like a legacy/pre-playbook pattern (e.g., memory subfolders that the playbook has since simplified, project mission/persona content sitting in `CLAUDE.md` instead of Project Instructions) — these are common migration scenarios. Surface as proposals, not auto-fixes.

2. **File operations.** Workspace `CLAUDE.md`, `memory/` files, project folder structure. Show proposed content; wait for approval per file (or in batch if the user prefers).

3. **UI handoff.** Paste-ready text for each surface only the user can edit:
   - **Personal Preferences** (Claude.ai → Settings → Personal Preferences)
   - **Cowork Instructions** (Cowork → Settings → Instructions)
   - **Project Instructions** (Cowork → Project → Instructions, per Project)

4. **Cowork Project creation** (if needed). For each Cowork Project the user must create:
   1. In the Cowork sidebar, click `+ New Project`.
   2. Name it to match the project folder (e.g., `gtm-pedro`).
   3. **Attach folders: select BOTH the project subfolder AND the workspace root folder.** Folders can only be attached at Project creation — there's no retroactive add.
   4. Save the Project.
   
   The workspace-root attach is silent-failure (§ 2.4 of the playbook). Cowork won't warn if it's missed; sessions just operate without cross-Project memory. If you find an existing Project missing the attach, recommend recreation — there's no fix in place.

**Watch out for:**
- **Templates are real, not placeholders.** Adapt to the user's voice and role, but preserve the structural patterns (close-the-gap, goal-oriented prompts, don't-reinvent — see playbook § 2.2.2).
- **Don't seed memory with content the user hasn't validated.** Even when scanning files or connectors, propose first; write after approval.
- **Don't overload the workspace `CLAUDE.md`.** Target ~50–100 lines. If your seeded version is heavier, you're guessing too aggressively.

### Final review

After file writes are applied and the user has handled UI-only items:

1. **Spot-check** the files you wrote landed with the right shape (paths, frontmatter, structure per playbook Appendix D).
2. **Recap** what was applied vs. what's still on the user (any UI steps not yet done, any folders they need to move).
3. **What to run next** — `/pedros-cowork-playbook:refresh-workspace` for ongoing hygiene; it also seeds Project Memory in any fresh Cowork Project.
4. **Invite the user to flag anything that looks off** — better to catch drift now than after the first real work session.

## Where to look

Playbook § 2 (Basic Setup) for the framework end-state. Appendix A for templates. Appendix D for file/naming conventions. Appendix B for Project Memory mechanics if seeding is in scope.

For the user's own context: existing files in attached folders, connector data (chat, email, calendar) for hot-cache seeding, the user's seed paragraph for empty-workspace cases.

## Edge cases

- **No connectors available** — seed hot cache from the user's seed paragraph and existing files only. Proposal will be thinner; flag that the user can run `/pedros-cowork-playbook:refresh-workspace` later once connectors are installed.
- **User wants a non-default workspace path** — accept it; just be consistent. Update file paths in templates accordingly.
- **Existing CLAUDE.md uses different conventions** — flag the differences; let the user decide whether to migrate to the playbook's conventions or keep theirs. Don't unilaterally rewrite.
- **Contradictory inputs mid-flow** — if file detection and the user's answers conflict, ask before proceeding. Don't override based on guesses.
