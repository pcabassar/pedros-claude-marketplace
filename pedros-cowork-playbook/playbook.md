# Pedro's Cowork OS

Make better decisions, faster. Stop re-explaining yourself. Get hours back every week. This system turns Claude Cowork from a chat window into infrastructure — Claude as your domain expert, context that compounds across Projects, routine work running on its own.

A modular system in tiers. Each tier solves one problem and stands alone. Adopt the tier that fits your need; stop where the cost stops being worth it.

- **Tier 0 — Prerequisites.** Cowork installed, plugin installed, connectors enabled.
- **Tier I — Instructions Setup (foundation).** Smart, consistent sessions for every Cowork Project.
- **Tier II — Cross-Project Memory.** Context that carries across projects and time.
- **Tier III — Organization.** Files stay organized as you work.
- **Tier IV — Automation.** Recurring leverage on cadence.

## Who this is for

Operators — sales, ops, founders, advisors — who use Claude Cowork (or are about to) and want a stable operating layer underneath their work. If you've outgrown ad-hoc setups and want a system that compounds rather than drifts, this is for you.

## Companion artifacts

- **Plugin.** Bundles every template, prompt, and skill referenced below. Install: [github.com/pcabassar/pedros-claude-marketplace](https://github.com/pcabassar/pedros-claude-marketplace).
- **Overview.** One-page visual summary, designed for sharing.
- **Slide.** Single-image stack of the four tiers.

## How to use this with the plugin

The plugin includes two skills:

- **`setup`** — bootstraps a fresh workspace into the system, or migrates an existing setup into it.
- **`refresh-workspace`** — keeps cross-Project memory current. Diffs your workspace against recent activity and proposes promotions, demotions, and stale flags.

Invocation differs by environment:

- **Cowork:** invoke via natural language. *"Set up my workspace per the playbook." "Run the refresh-workspace skill."* Claude picks the right skill based on context.
- **Claude Code:** invoke via slash command. `/pedros-cowork-playbook:setup`, `/pedros-cowork-playbook:refresh-workspace`.

The same applies to Anthropic's `/consolidate-memory` skill (used for cleaning up a Cowork Project's auto-memory): slash command in Claude Code, natural language in Cowork.

## Terminology

- **project** (lowercase) — your workstream. A focus area that earns its own folder. Bigger than a task, smaller than your whole life. Could be ongoing (your finances, a client engagement) or time-bound (a launch, a fundraise).
- **Cowork Project** (capital P) — Cowork's UI organizational unit. Has folders attached, instructions, isolated memory. Each of your projects gets one.

## 1. Cowork in a page

The Cowork features this system builds on:

| Feature | What it does |
|---|---|
| **Cowork Projects** | UI organizational unit. Each Project has folders attached, instructions, and an isolated memory space. Foundation for everything else. |
| **Project Memory** | Per-Cowork-Project autonomous memory layer. Claude writes durable observations to typed markdown files indexed by `MEMORY.md`. Loaded into every session in that Cowork Project. (Anthropic's term for the underlying mechanism is "Auto Memory.") |
| **Connectors** | Read perimeter to external tools — email, calendar, chat, drive, docs, project trackers. Each enabled connector lets Claude see (and sometimes write to) that tool. |
| **Skills** | Goal-oriented capability bundles invoked on demand or by description match. |
| **Plugins** | Bundles of skills, MCP servers, and reference content. Installable from a marketplace or local source. |
| **Scheduled tasks** | Background automation that runs on a cadence — daily, weekly, custom. |
| **Artifacts** | Persistent rendered HTML pages that re-fetch data from connectors when reopened. |

What Cowork doesn't do well today (May 2026):

- **No native cross-Project memory.** Project Memory is hard-isolated per Cowork Project. Cross-Project context needs the workspace-folder pattern this playbook describes.
- **Instruction channel ambiguity.** Four places where guidance can live (Personal Preferences, Cowork Instructions, Project Instructions, `CLAUDE.md` files) can each hold either kind of content. Without a partition rule, content drifts.
- **Cowork Instructions revert bug.** A known issue where Cowork Instructions can silently revert. Workaround in § 3 (Known issues).
- **No native shared collaboration.** Cowork sessions are single-user. Sharing work products with a team needs external infrastructure (GitHub, hosted dashboards, cloud drive).
- **Operating cost.** Always-on connectors plus weekly scheduled tasks plus daily briefings burn message limits. Max (20x) recommended; works on Pro.

This playbook addresses the first three directly. A future companion will cover the sharing limitation.

## 2. The Tiers

### Tier 0 — Prerequisites

Cowork installed. The Cowork OS plugin installed. Connectors enabled to taste. Max (20x) subscription recommended for Tier IV.

### Tier I — Instructions Setup (foundation)

*Smart, consistent sessions for every Cowork Project.*

**Components:**

- **Personal Preferences** — how Claude talks to you.
- **Cowork Instructions** — how Claude operates across all your work.
- **Project Instructions** — mission and persona for one project.
- **Project Memory** — per-Project, automatic, no setup.

**What you do:**

1. One folder per project (anywhere on disk). *Cowork has full read/write/delete on attached folders — pick deliberately.*
2. One Cowork Project per project, attached to that folder.
3. Fill three instruction surfaces:
   - **Personal Preferences** (Claude.ai → Settings) — how you want Claude to communicate with you. Tone, format, push-back rules. Loads into every session, every Cowork Project. Template: Appendix A.1.
   - **Cowork Instructions** (Cowork → Settings) — Claude's operating posture across all your work. Loads into every session. Template: Appendix A.2.
   - **Project Instructions** (Cowork → Project → Instructions) — mission and persona for one Cowork Project. Loads only in that Project's sessions. Template: Appendix A.3.
4. Project Memory writes itself as you work — nothing to set up.

The four channels are MECE: each holds a different kind of content; nothing overlaps. Once they're filled, Claude auto-loads all of them at the start of every session — including the workspace `CLAUDE.md` from Tier II if it exists. You don't manage loading.

**Three patterns worth keeping in your Cowork Instructions** (full template in A.2):

- **Close-the-gap.** *"Operate as a subject-matter expert. When not one, close the gap immediately through research."* Prevents confident-but-shallow work.
- **Goal-oriented prompts.** *"State the outcome; let the agent figure out tooling and sequencing."* Why every skill in this plugin is described by its goal, not its procedure.
- **Don't-reinvent.** *"Use Cowork's features aggressively. Don't reinvent what a skill already handles."* Why this playbook uses Anthropic's `/consolidate-memory` directly rather than wrapping it.

### Tier II — Cross-Project Memory

*Context that carries across projects and time.*

**Components:**

- **Workspace folder** — parent folder attached to every Cowork Project.
- **Workspace `CLAUDE.md`** — people, projects, glossary pointers, voice. Shared everywhere.
- **Memory files** — `memory/glossary.md` (terms, nicknames, codenames) and `memory/voice-and-style.md` (how you write and speak).
- **`/refresh-workspace`** — keeps memory current as your work shifts.

**What you do:**

1. Create a workspace root folder (e.g., `~/claude-workspace/`). Move your project folders under it. *Cross-Project memory only works when project folders are co-located.*
2. Attach the workspace root to **each** Cowork Project at creation, alongside the project folder. ⚠ *Silent fail: Cowork won't warn if you skip this. Folders can only be attached at Cowork Project creation — no retroactive add. If you find a Cowork Project missing the attach, recreate it.*
3. Fill the workspace memory files:
   - **`CLAUDE.md`** at the workspace root — hot cache for cross-project context (people, projects, terms, voice pointers). Target ~50–100 lines. Template: Appendix A.4.
   - **`memory/glossary.md`** — terms, nicknames, codenames you use across projects.
   - **`memory/voice-and-style.md`** — how you write and speak.
4. Schedule `/refresh-workspace` weekly. The skill diffs your cross-Project memory against recent activity (file changes, connector signals) and proposes promotions, demotions, and stale flags. Always proposes; never auto-applies.

**Why so small.** People and project indexes live in workspace `CLAUDE.md` (short descriptors). Per-project rich detail lives in each project's folder. The workspace `memory/` holds only what's genuinely cross-cutting AND worth manually curating: shared language, shared voice.

**What NOT to put here.** Anything specific to one project goes in that project's folder, not workspace `memory/`. Use `reference/` for stable content (positioning, personas, guides), `working/` for drafts, `deliverables/` for finished outputs, and `archived/` (optional) for stale items worth keeping out of sight.

### Tier III — Organization

*Files stay organized as you work.*

**Component:**

- **File-and-folder conventions** — names files, sorts folders, archives the stale.

**What you do:**

Add the file-and-folder conventions block (Appendix A.2 § File Conventions) to your Cowork Instructions. It tells Claude:

- How to name files: lowercase kebab-case, dated where useful, descriptive enough to identify without opening (e.g., `gtm-plan-2026-04-14.md`).
- Where to put them per project: `reference/` (stable), `working/` (drafts), `deliverables/` (finished), `archived/` (optional, for stale items worth keeping).
- To use YAML frontmatter on working files (title, purpose, status, dates).
- To suggest archiving when folders get crowded.

Adapt the suggested folder shape to your work — these are conventions, not rules. Add subfolders within `reference/` (e.g., `reference/people/`, `reference/products/`) where it helps.

### Tier IV — Automation

*Recurring leverage. Pick what fits.*

Modules — install only what you need. Each runs as a scheduled task or a manual skill invocation:

- **Task system.** `TASKS.md` at workspace root with project-coded entries (e.g., `BO3`, `MC1`), status sections (Backlog / Up Next / In Progress / Waiting On / Done), Friday archive cadence to `DONE-LOG.md`.
- **Daily briefing.** Scheduled task pulling from Calendar, Email, and `TASKS.md` — produces a prioritized morning briefing covering meetings, action items, and the one thing that matters most.
- **Inbound digests.** Scheduled tasks summarizing newsletters, Slack channels, RSS feeds. Keeps you current without drowning in inputs.
- **Custom scheduled tasks.** Pattern for adding your own — anything recurring you'd rather not do by hand.

## 3. Known issues

Three Cowork issues with workarounds:

- **Cowork Instructions revert bug.** Cowork Instructions can silently revert to a stale version when a prior session writes to the underlying config with an outdated in-memory snapshot. Workaround: pin one long-running session that holds your Cowork Instructions in context as the source of truth. Restore from that session when revert happens. Anthropic is aware. Tracked in [issue #40175](https://github.com/anthropics/claude-code/issues/40175) and [#40731](https://github.com/anthropics/claude-code/issues/40731).
- **Silent-fail folder attach.** When creating a Cowork Project, you must attach BOTH the project folder AND the workspace root. Cowork won't warn if you skip the workspace root — sessions just operate without cross-Project memory. Folders can only be attached at Project creation. Fix: recreate the Cowork Project.
- **Operating cost.** Connectors + scheduled tasks + daily briefings burn message limits fast. Max (20x) recommended; works on Pro.

## 4. Security and threat model

Connectors give Claude a wide read perimeter, and attached folders give Claude write and delete access. The same surfaces that make the system useful expose it to whatever an adversary can plant in those streams, and to mistakes you make about what you've handed over. This section surfaces the concerns; it doesn't claim authority.

*I'm not a security expert. For authoritative guidance, see the [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) and Anthropic's [responsible use guidance](https://docs.claude.com/en/docs/test-and-evaluate/strengthen-guardrails/reduce-prompt-injection-attacks). For Cowork-specific data handling, check Anthropic's terms before connecting regulated data.*

### 4.1 What Cowork can do with attached folders

Cowork has full read, write, and delete access to any folder you attach to a Cowork Project — including all subfolders, recursively. Be deliberate:

- Use the workspace + project structure described in § 2. It scopes Cowork's reach to the folders you intend.
- Don't attach broad parent folders like `~/Documents/`. You'd give Cowork access to everything beneath — finances, downloads, anything you've forgotten about.
- If you must attach a sensitive folder for a specific use, scope tightly. Create a dedicated subfolder, attach only that, never the parent.

### 4.2 Threats

1. **Indirect prompt injection** through email, chat, calendar, or document content Claude ingests. Highest-likelihood threat as of May 2026.
2. **Sensitive data exposure.** Anything Claude reads goes through Anthropic's models. Verify alignment with your data-handling obligations before connecting regulated sources.
3. **Audit gap.** Cowork sessions may not appear in standard enterprise audit trails. Verify before adopting at scale.

### 4.3 Do-not-connect

Folders to never attach to a Cowork Project:

- Files protected by attorney-client privilege
- Files protected by HIPAA, FERPA, GDPR, or similar regulations
- Cryptographic keys, passwords, secret tokens, API credentials
- Production database backups or dumps
- Anything an NDA forbids you from sharing with third-party AI services

Connectors to scope narrowly or skip entirely:

- Email accounts that receive sensitive third-party content (legal, medical, HR)
- Chat workspaces with vendor-confidential discussions
- Drives containing customer data subject to processing agreements

### 4.4 Practices that reduce risk

- **Per-action approval for external writes.** Never grant Claude blanket write permission to external connectors. Approve each send, post, schedule individually.
- **Audit scheduled tasks.** A daily briefing that pulls from email reads every email each morning. Verify nothing in your inbox is meant to stay confidential.
- **Don't take "read everywhere" literally** if you handle regulated data. Broad ≠ all.
- **Treat Project Memory's PII rules as advisory.** The system-prompt rules ask Claude not to save sensitive PII; there's no server-side filter. The model's judgment is the only safeguard.

### 4.5 Open issues to watch

- Indirect prompt injection mitigations — Anthropic is actively working on this; assume it's not solved.
- Audit log coverage for Cowork sessions.
- Per-connector scope controls (UI may improve).

This playbook does not solve security — it surfaces the surface. Pair adoption with your organization's data-handling policies.

## 5. vs. Anthropic's productivity plugin

Anthropic's [productivity plugin](https://github.com/anthropics/knowledge-work-plugins/tree/main/productivity) is the closest thing to a default operating layer for Cowork. This system has a similar goal but addresses critical inherent issues:

1. **One working directory.** The productivity plugin assumes one `CLAUDE.md` and one `memory/`. Multiple projects either bloat one memory file or split context across files that can't see each other.
2. **No Project Memory awareness.** Predates Anthropic's per-Cowork-Project autonomous memory layer; doesn't integrate with it.
3. **Forces features you may not want.** A `TASKS.md` and a `dashboard.html` come bundled. No way to install just the memory pattern without the rest.
4. **No automation.** Hygiene is manual via `/update`. No scheduled refresh.
5. **Hard to extend.** Conventions are baked in. Adding to or replacing what's there means working against the plugin rather than building on top.

This playbook:

1. **Cross-Project workspace pattern** (§ 2). Workspace folder holds shared context; each Cowork Project has its own folder and its own Project Memory. Memory layers stack rather than compete.
2. **Project Memory integrated as a third memory layer.**
3. **Modular optional features** (Tier IV). Install only what you need.
4. **Scheduled hygiene.** `/refresh-workspace` runs weekly; same logic available manually.
5. **Built to extend.** Goal-oriented skills, separate reference files, explicit conventions.

**Recommendation.** Use this system, not the productivity plugin — at least not in its current version (1.2.0, March 2026).

## Appendix A — Templates

Per-channel templates and examples. Copy-paste-adapt.

These are real working examples, evolved through months of use — not theoretical placeholders. Adapt rather than fill in blanks.

### A.1 Personal Preferences

```
## Tone & Voice

1. Be direct and concise. Don't over-explain, I'll ask if I need more.

2. When something I said is wrong or could be sharper, say so plainly. Don't hedge.

## Format Conventions

3. Use numbered lists (1, 1.1, 2) when presenting alternatives — not bullets.

4. Name sections and files as a professional editor would — concise, declarative noun phrases. Example: 'Strategy' not 'The Why'.

5. Name files using lowercase kebab-case: project-descriptor-YYYY-MM-DD.ext. No spaces. Always include enough context to identify the file without opening it.

## Collaboration & Process

6. For non-trivial work, discuss and align before acting. Don't jump into creating, changing, or building things until we've agreed on the approach.

7. Before executing on a task, show a brief plan and wait for my go-ahead.

8. Treat my questions as things to answer, not as requests for action or solutions.

## Critical Engagement

9. Engage critically with me: what's strong, what's weak, what am I missing.

10. Always offer a recommendation with short reasoning.

## Research & Execution Quality

11. When a task requires best practices, specialized knowledge, or technical patterns you're not an expert in — research first.

12. Use subagents in parallel when available to improve speed, breadth, and accuracy of the task at hand.
```

### A.2 Cowork Instructions

Two parts: operating posture and file conventions.

```
# Claude in Cowork

1. Operate as a subject-matter expert. When not one, close the gap immediately through research (web, documents, papers). No acting on anything but current best practices.
2. Write straight-line: direct, active voice, concise. No fillers, no hedging, no repetition.
3. Make every output tailored, researched, and grounded in current best practice.
4. When drafting prompts for other agents, be goal-oriented, not prescriptive. State the outcome; let the agent figure out tooling and sequencing.
5. Use Claude Cowork's features aggressively — Skills, Plugins, scheduled tasks, connectors. Don't reinvent what a skill already handles.
6. Never edit CLAUDE.md files without explicit confirmation from me of the exact wording.
7. At the end of substantive sessions, suggest updates to context files (CLAUDE.md, memory/, reference/) if anything changed.

---

## File Conventions

### YAML Frontmatter

Every file Claude creates or edits must include this block at the top:

---
title:
purpose:
audience:
status: current | draft | superseded
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
superseded_by:  # only if status is superseded
---

Ignore files with `status: superseded` — treat them as archived, do not reference their content.

### Folder Structure

`/Users/[you]/claude-workspace/` is the workspace root. It holds:

- `CLAUDE.md` — cross-Project hot cache (people, projects, terms).
- `memory/` — cross-Project full storage (`glossary.md`, `voice-and-style.md`).
- One subfolder per project (e.g., `gtm-pedro/`, `MIC-global-application/`, `life-of-meaning/`). Each project subfolder uses standard sub-folders as needed.

| Folder | Contents | Lifecycle |
|--------|----------|-----------|
| `reference/` | Stable context: positioning, principles, personas, guides. | Changes when strategy changes. |
| `working/` | Drafts, scratch, session handoffs, research notes. | Purged periodically. Read YAML frontmatter to decide relevance. |
| `deliverables/` | Finished outputs: published content, final docs, shipped assets. | Permanent unless superseded. |
| `archived/` | Stale items worth keeping out of sight. | Optional. |

When a folder gets crowded, suggest moving stale items to `archived/` before purging. Project mission and persona live in the matching Cowork Project's Project Instructions (UI), not in `CLAUDE.md` files inside the project folder (those stay empty).

### Naming

Lowercase kebab-case. Date stamps where relevant: `playbook-2026-05-01.md`. Be specific enough to identify the file without opening it.

---

## Me

[Your name and one paragraph: role, focus, key context.]

## Further Context

When the inline context above isn't enough, pull from these files:

→ Voice and tone: `~/claude-workspace/memory/voice-and-style.md`
→ Glossary (terms, nicknames, codenames): `~/claude-workspace/memory/glossary.md`

## Emails

[Your email addresses, if relevant.]
```

### A.3 Project Instructions (gtm-pedro example)

```
**Mission.** Get Pedro visible to the right people, working on high-impact projects, and generating income from his AI + operations expertise. This project owns Pedro's personal brand, positioning, outbound pipeline, and all marketing/sales execution.

Success = Pedro is working on high-impact projects, making the equivalent of $400/hour, and/or with long-term upside.

**Posture.** You are Pedro's Chief GTM Executive. Think like the best-in-class go-to-market operator of the current era — strategy, positioning, outbound, content, research, and execution. Your writing is impeccable. Your research is thorough, modern, and validated.

Own GTM end-to-end the way a top-tier fractional CMO or senior consultant would. Anticipate what needs doing across strategy, positioning, research, content, outreach, and measurement. Set priorities, make the calls, run the plays, and bring Pedro decisions and finished work — not questions about what to do next or how to do it.

Ground your work in the modern GTM canon: April Dunford (positioning), Andy Raskin (strategic narrative), Justin Welsh (solo-operator / LinkedIn playbook), Bob Moesta and Jobs-to-be-Done (buyer motivation). Go beyond these to match current best practice.
```

### A.4 Workspace CLAUDE.md

```markdown
---
title: My Workspace
purpose: Hot cache for cross-Project context — me, organization, team, top people, projects, terms
status: current
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
---

# Workspace

## Me

[Your name and one paragraph: role, focus, key context.]

→ Voice and tone: `memory/voice-and-style.md`

## My Organization

[Your company or primary organization. Include: name, your role there, brief context. Skip or simplify if you're independent.]

## My Team

The people you work with directly — manager, direct reports, frequent collaborators.

| Who | Role |
|-----|------|
| **[Nickname]** | [Full name, role on team] |

## People

Top ~15 contacts beyond your immediate team. Short descriptors. Full glossary: `memory/glossary.md`.

| Who | Role |
|-----|------|
| **[Nickname]** | [Full name, role, company, one-line context] |

## Projects

Active project code → name → folder → status → description. Short descriptors.

| Code | Name | Folder | Status | Description |
|------|------|--------|--------|-------------|
| **GT** | GTM Pedro | [./gtm-pedro/](file:///Users/[you]/claude-workspace/gtm-pedro/) | Active | Personal brand, positioning, outbound pipeline. |

## Terms

Top ~15 cross-project terms. Full glossary: `memory/glossary.md`.

| Term | Meaning |
|------|---------|
| **ICP** | Ideal Customer Profile. |
```

### A.5 memory/ structure

Two files. That's it.

```
memory/
├── glossary.md          # full decoder ring (terms, nicknames, codenames)
└── voice-and-style.md   # how you write and speak
```

People and project indexes live in workspace `CLAUDE.md` (short descriptors). Per-project rich detail lives in each project's folder. The workspace `memory/` holds only what's genuinely cross-cutting AND worth manually curating.

## Appendix B — Project Memory mechanics

For the curious. Anthropic actively iterates on this layer; details below are accurate as of 2026-05-01.

### Storage

Per Cowork Project, hard isolated. One "space" UUID per Cowork Project. Path: `~/Library/Application Support/Claude/local-agent-mode-sessions/<session>/spaces/<space-uuid>/memory/`. Cross-Project leakage is impossible — verified by direct test.

### The four types

- `user_*` — durable user facts (role, preferences, knowledge)
- `feedback_*` — corrections + validations
- `project_*` — durable project facts (decisions, deadlines, motivations)
- `reference_*` — pointers to external systems

### File format

Frontmatter: `name`, `description`, `type`. Body for `feedback`/`project`: rule, then `**Why:**` line, then `**How to apply:**` line. Other types use freer-form prose.

### Index

`MEMORY.md` is a bare markdown index — one bullet per entry: `- [Title](file.md) — one-line hook`. Anthropic-stated cap: 200 lines / 25KB. Lines beyond are silently truncated at session start.

### Write triggers

Model-driven, not tool-call-driven. Claude uses standard Write/Edit tools. Triggers are governed by a system-prompt section that defines what to save and what to skip (sensitive PII, ephemeral state, code patterns, anything derivable from current files).

### Stale-warning runtime

Every read of a typed memory file is annotated with the file's age — *"This memory is N days old. Memories are point-in-time observations, not live state — verify against current code before asserting as fact."*

### Lifecycle

Cleanup via Anthropic's `/consolidate-memory` skill (manual). Three phases: take stock, consolidate, tidy index. No auto-trigger.

## References

### Anthropic official

- [Claude Cowork — product page](https://claude.com/product/cowork)
- [Get started with Cowork](https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork)
- [Organize tasks with Projects in Cowork](https://support.claude.com/en/articles/14116274-organize-your-tasks-with-projects-in-claude-cowork)
- [Memory docs (CLAUDE.md, auto-memory)](https://code.claude.com/docs/en/memory)
- [knowledge-work-plugins repo](https://github.com/anthropics/knowledge-work-plugins)
- [Memory tool API docs](https://docs.claude.com/en/docs/agents-and-tools/tool-use/memory-tool)

### Bug tracking

- [#40175 — Cowork Instructions revert](https://github.com/anthropics/claude-code/issues/40175)
- [#40731 — Cowork Instructions revert (related)](https://github.com/anthropics/claude-code/issues/40731)
- [#45076 — Project metadata silent disappearance](https://github.com/anthropics/claude-code/issues/45076)
- [#29434 — sensitive content in memory cannot be redacted](https://github.com/anthropics/claude-code/issues/29434)

### Community

Project Memory mechanics — Anthropic publishes high-level docs but the deep mechanics are reverse-engineered. Treat the items below as best-current-understanding.

- [claudefa.st — Auto Memory mechanics](https://claudefa.st/blog/guide/mechanics/auto-memory)
- [claudefa.st — Auto Dream consolidation](https://claudefa.st/blog/guide/mechanics/auto-dream)
- [Milvus blog — Claude Code memory and memsearch](https://milvus.io/blog/claude-code-memory-memsearch.md)

Independent Cowork guides consulted during research. None treats the full instruction-channel set as a partitioned system:

- Ruben Hassid — uses Global Instructions as a dispatcher pointing to ABOUT ME / TEMPLATES / PROJECTS folders.
- Alex Banks — layers Project Instructions over Global + Context files.
- Jeff Su — treats channels as separate features.
- Karo Zieminski — focuses on plugin/skill/sub-agent tactics.
