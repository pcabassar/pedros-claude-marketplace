# Pedro's Claude Cowork Playbook

> **Status note (2026-04-29):** Draft v4 restructured around Basic Setup. Sections 1–6 + Appendices A–D + References drafted.

## About this playbook

Claude Cowork is easy to start using. Getting its full potential takes a bit of thought.

The infrastructure is powerful — persistent file access, connector-driven context from your external tools, memory that carries across sessions, skills and plugins that compose. Used well, every session compounds: each one starts with more context than the last. Used carelessly, instructions drift across surfaces, memory bloats, and you end up re-explaining who people are every Monday.

This playbook is the technique. A six-channel partition that keeps Cowork coherent without re-explanation.

**Don't be put off by the length.** The bulk is explanation and templates, not steps. With the templates in Appendix A and the `/pedros-cowork-playbook:setup` skill, getting up and running takes 30–60 minutes — most of which is copy-paste-adapt rather than original writing.

> **A note on skill names.** Plugin skills in Claude Code and Cowork are namespaced by the plugin name. Throughout this playbook, the setup skill is referenced as `/pedros-cowork-playbook:setup` and the refresh skill as `/pedros-cowork-playbook:refresh-workspace`. That's what you'll type after installing the plugin.

## 1. About Claude Cowork

### 1.1 Summary

Claude Cowork is Anthropic's agentic desktop application — a workspace where you delegate knowledge work to Claude, with persistent file access, connector-driven context from your external tools, and memory that carries across sessions.

### 1.2 Key features

These are the Cowork features this playbook builds on.

| Feature | What it does |
|---|---|
| **Cowork Projects** | UI organizational unit. Each Project has a folder (or set of folders), instructions, and an isolated memory space. Foundation for everything else in this playbook. |
| **Project Memory** *(Anthropic sometimes calls this "Auto Memory")* | Per-Cowork-Project autonomous memory layer. Claude writes durable observations to typed markdown files (`user_*`, `feedback_*`, `project_*`, `reference_*`) indexed by `MEMORY.md`. Loaded into every session in that Cowork Project. |
| **Connectors** | Read perimeter to external tools — email, calendar, chat, drive, docs, project trackers. Each enabled connector lets Claude see (and sometimes write to) that tool. |
| **Skills** | Specialized capability bundles invoked on demand or by description match. A skill is a goal-oriented prompt that runs in-session when relevant. |
| **Plugins** | Bundles of skills, MCP servers, and reference content. Installable from a marketplace or a local source. |
| **Scheduled tasks** | Background automation that runs on a cadence — daily, weekly, custom. Produces reports, refreshes data, sends digests. |
| **Artifacts** | Persistent rendered HTML pages that re-fetch data from connectors when reopened. Useful for trackers, dashboards, recurring views. |

### 1.3 Current limitations

What Cowork doesn't do well today (as of April 2026):

- **No native cross-Project memory.** Project Memory is hard-isolated per Cowork Project. Cross-Project context requires the workspace-folder pattern this playbook describes.
- **Instruction channel ambiguity.** Four places where guidance and context can live (Personal Preferences, Cowork Instructions, Project Instructions, `CLAUDE.md` files) can each hold either kind of content. Without a partition rule, content drifts across surfaces and contradicts itself.
- **Cowork Instructions revert bug.** A known issue where Cowork Instructions can silently revert when a prior session writes to the underlying config. See § 5 for the workaround.
- **No native shared collaboration.** Cowork sessions are single-user. Sharing work products with a team requires external infrastructure (GitHub, hosted dashboards, cloud drive).
- **Operating cost.** Always-on connectors plus weekly scheduled tasks plus daily briefings burn message limits fast. We recommend the Max (20x) subscription, but it will also work with Pro.

This playbook addresses the first three directly. A future companion will cover working around the sharing limitation.

## 2. Basic Setup

The `/pedros-cowork-playbook:setup` skill walks you toward this folder structure:

```
~/claude-workspace/                # workspace folder (root)
├── CLAUDE.md                      # cross-Project memory hot cache
├── memory/                        # cross-Project memory full storage
│   ├── glossary.md                # terms, nicknames, codenames
│   └── voice-and-style.md         # how you write and speak
├── project-1/                     # one project — direct subfolder
│   ├── reference/
│   ├── working/
│   └── deliverables/
├── project-2/
└── ...
```

### 2.1 Folders and Projects

> **A note on terminology.** From here on, **project** (lowercase) refers to your workstream — a focus area that earns its own folder. Could be ongoing (your finances, your career) or time-bound (a client engagement, a launch). Bigger than a task, smaller than your whole life. **Cowork Project** (capital P) refers to Cowork's UI organizational feature.

Each item below is required.

#### 2.1.1 Workspace folder

Your workspace folder (e.g., `~/claude-workspace/`) serves as the root for all your projects and the place where cross-Project memory lives.

#### 2.1.2 Project subfolders

Every project lives in a subfolder of the workspace folder.

#### 2.1.3 Move existing folders

If your existing project folders live elsewhere on disk, move them under the workspace folder. *(Cross-Project memory only works when they're co-located.)*

#### 2.1.4 Cowork Projects

Each project has a matching Cowork Project. **You must:** create a Cowork Project for each, and connect it to **both** the project subfolder and the workspace folder.

> The workspace-folder attach is silent-failure: skip it and cross-Project memory doesn't load, but Cowork won't warn you. Sessions run; they just operate without context you assume is there. *Folders can only be attached at Cowork Project creation* — you can't add the workspace folder retroactively.

### 2.2 Instructions Setup

Four places hold instructions, complementary not duplicative (MECE). We recommend using the templates in Appendix A as starting points — they are real working examples, evolved through use.

#### 2.2.1 Personal Preferences

**Lives in:** Claude.ai → Settings → Personal Preferences.

**Holds:** how you want Claude to communicate with you. Tone, format, length, when to push back, when to ask. Applies to every Cowork session and every Cowork Project.

**What to put here:** communication style, format preferences, push-back rules, boundaries on questions.

**What NOT to put here:** project-specific rules, identity context, anything tied to a single project.

**Template:** see Appendix A.1.

#### 2.2.2 Cowork Instructions

**Lives in:** Cowork → Settings → Instructions.

**Holds:** cross-project operating rules and Claude's overall posture in your Cowork environment. Loads with every session, regardless of which Cowork Project you're in.

**What to put here:**
- Claude's overall posture across all your work
- The folder + Cowork Project setup pattern (so Claude knows where to find things)
- Workspace conventions that apply across all Cowork Projects
- Operating rules that don't fit Personal Preferences

**What NOT to put here:** project-specific persona (Project Instructions), detailed identity context (workspace `CLAUDE.md`).

**Patterns worth borrowing** from working examples (full set in Appendix A.2):

- **Close-the-gap pattern.** *"Operate as a subject-matter expert. When not one, close the gap immediately through research."* Prevents Claude from producing confident-but-shallow work when it's outside its expertise.
- **Goal-oriented prompt pattern.** *"When drafting prompts for other agents, be goal-oriented, not prescriptive. State the outcome; let the agent figure out tooling and sequencing."* Why every skill in this playbook is described by its goal rather than its procedure.
- **Don't-reinvent pattern.** *"Use Cowork's features aggressively — Skills, Plugins, scheduled tasks, connectors. Don't reinvent what a skill already handles."* Why this playbook uses Anthropic's `/consolidate-memory` directly rather than wrapping it in a custom skill.

**Note:** Cowork Instructions are subject to a known revert bug — see § 5.

**Template:** see Appendix A.2.

#### 2.2.3 Project Instructions

**Lives in:** Cowork → Project → Instructions.

**Holds:** the mission and persona for one specific Cowork Project. What's this Project for, who's Claude in it, what rules apply only here.

**What to put here:**
- Project mission (one paragraph)
- Project-specific persona ("operate as my GTM strategist")
- Project-specific operating rules
- Skills and tools to prefer or avoid in this Cowork Project

**What NOT to put here:** cross-project content, hot cache (people / terms / projects — those go in workspace `CLAUDE.md`).

**Template:** see Appendix A.3.

### 2.3 Cross-Project Memory

Optional, but required for cross-Project memory to run.

#### 2.3.1 Workspace CLAUDE.md

**Where to create and edit:** `~/claude-workspace/CLAUDE.md`.

**Holds:** cross-Project hot cache. Your personal context, your organization, your team, the people and projects and terms you reach for most often. Loads with every Cowork Project session that has the workspace folder attached.

**Format:** tables for compactness. Target ~50–100 lines.

**What to put here:**
- Your personal context, your organization, your direct team
- Top ~15 people beyond the team
- Active projects table (code → name → folder URL → status → description)
- Top ~15 terms and acronyms
- Pointers to `memory/glossary.md` and `memory/voice-and-style.md` for fuller cross-cutting language and voice

**What NOT to put here:** project-specific hot cache (Project Instructions); per-project rich detail (Project Memory handles that); an umbrella `# Memory` header (collides with Project Memory's `MEMORY.md` and confuses the reader).

**Template:** see Appendix A.4.

#### 2.3.2 Workspace memory/

**Where to create and edit:** `~/claude-workspace/memory/glossary.md` and `~/claude-workspace/memory/voice-and-style.md`.

**Holds:** cross-Project full storage — kept deliberately small. Two files only:

- `glossary.md` — terms, nicknames, codenames you use across projects
- `voice-and-style.md` — how you write and speak

**Why so small:** people and project indexes live in workspace `CLAUDE.md` (short descriptors); per-project rich detail lives in each Cowork Project's Project Memory. The `memory/` folder holds only what's genuinely cross-cutting *and* worth user-curating manually: shared language and shared voice.

**What NOT to put here:** per-person profiles (Project Memory + connectors handle this); per-project deep-dives; project-specific reference material (project folder's `reference/`).

#### 2.3.3 Workspace memory refresh task

Set up a recurring task to clean up your workspace `CLAUDE.md`, `memory/glossary.md`, and `memory/voice-and-style.md` weekly, using the `/pedros-cowork-playbook:refresh-workspace` skill as a base.

The skill diffs cross-Project memory against recent file activity, connector signals, and Project Memory updates — proposing promotions (rising frequency), demotions (declining or stale), and stale-entry flags. Always proposes; never auto-applies. The skill also seeds Project Memory in the current Cowork Project if it's empty — combining initial seeding and ongoing hygiene in one operation.

Run manually when you sense drift, or as a weekly scheduled task.

### 2.4 What Claude does automatically

- Loads all instructions (Personal Preferences, Cowork Instructions, workspace `CLAUDE.md`, Project Instructions) and Project Memory at the start of every session.
- Updates Project Memories periodically automatically. *(For mechanics, see Appendix B.)*

You don't need to do anything for these.

## 3. Optional Modules

These extend the playbook with capabilities that aren't required for the core system. Each lives in its own file; install only what you need.

### 3.1 Task system

A markdown-based task tracker (`TASKS.md`) at the workspace root with project-coded entries (e.g., `BO3`, `MC1`), status sections (Backlog / Up Next / In Progress / Waiting On / Done), and a Friday archive cadence to `DONE-LOG.md`.

Details: `modules/task-system.md`.

### 3.2 Daily briefing

A scheduled task that runs every weekday morning, pulls from Calendar, Email, and `TASKS.md`, and produces a prioritized briefing — meetings, action items, waiting-on items, and the one thing that matters most.

Details: `modules/daily-briefing.md`.

### 3.3 Newsletter digest

Scheduled tasks that summarize inbound streams — newsletters, specific Slack channels, RSS feeds. Keeps you current without drowning in inputs.

Details: `modules/newsletter-digest.md`.

## 4. vs. Anthropic's productivity plugin

Anthropic's [productivity plugin](https://github.com/anthropics/knowledge-work-plugins/tree/main/productivity) is the closest thing to a default operating system for Cowork. This playbook has a similar goal and addresses critical inherent issues:

1. **One working directory.** Assumes one `CLAUDE.md` and one `memory/`. If you have multiple projects, you choose between bloating one memory file or splitting context across files that can't see each other.

2. **No Project Memory awareness.** Predates Anthropic's per-Cowork-Project autonomous memory layer; doesn't integrate with it.

3. **Forces features you may not want.** A basic `TASKS.md` and a `dashboard.html` come bundled. No way to install just the memory pattern without the rest.

4. **No automation.** Hygiene is manual via `/update`. No scheduled refresh; the user has to remember.

5. **Hard to extend.** Conventions are baked in (CLAUDE.md format, `memory/` structure, bundled skills). Adding to or replacing what's there means working against the plugin rather than building on top.

What this playbook does:

1. **Cross-Project workspace pattern** (§ 2). Workspace folder holds shared context; each Cowork Project has its own folder and its own Project Memory. Memory layers stack rather than compete.

2. **Project Memory integrated as a third memory layer.** Per-Cowork-Project autonomous memory + user-curated cross-Project memory, each with its own niche.

3. **Modular optional features.** Tasks, daily briefing, newsletter digest — each in its own module. Install only what you need.

4. **Scheduled hygiene.** The Workspace Hygiene task runs weekly; same logic available manually via `/pedros-cowork-playbook:refresh-workspace`.

5. **Built to extend.** Goal-oriented skills, separate reference files, explicit conventions. Customize or add without fighting the plugin.

**Recommendation.** Use this playbook, not the productivity plugin — at least not in its current version (1.2.0, March 2026). 

## 5. Known Issue: Cowork Instructions Revert Bug

**Symptom:** Cowork Instructions in Settings → Cowork can silently revert to a stale version when a prior session writes to the underlying config file with an outdated in-memory snapshot.

**Tracked in:** GitHub issues [#40175](https://github.com/anthropics/claude-code/issues/40175) and [#40731](https://github.com/anthropics/claude-code/issues/40731).

**Workaround:** pin one long-running session that holds your Cowork Instructions in context and treat it as the source of truth. When Instructions revert, restore from that session.

Anthropic is aware. Watch the issues for a fix.

## 6. Security and threat model

Connectors give Claude a wide read perimeter. The same surface that makes the system useful also exposes it to whatever an adversary can plant in those streams. This section surfaces the concern; it doesn't claim authority.

*I'm not a security expert. For authoritative guidance, see the [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) and Anthropic's [responsible use guidance](https://docs.claude.com/en/docs/test-and-evaluate/strengthen-guardrails/reduce-prompt-injection-attacks). For Cowork-specific data handling, check Anthropic's terms before connecting regulated data.*

### 6.1 Threats (summary)

Three risks worth knowing about. Specifics evolve; treat the framing below as orientation, not assessment.

1. **Indirect prompt injection** through email, chat, calendar, or document content that Claude ingests. Highest-likelihood threat as of April 2026.

2. **Sensitive data exposure** — anything Claude reads goes through Anthropic's models. Verify alignment with your data-handling obligations before connecting regulated sources.

3. **Audit gap** — Cowork sessions may not appear in standard enterprise audit trails. Verify before adopting at scale.

### 6.2 Do-not-connect list

Folders to never attach to a Cowork Project:

- Files protected by attorney-client privilege
- Files protected by HIPAA, FERPA, GDPR, or similar regulations
- Cryptographic keys, passwords, secret tokens, API credentials
- Production database backups or dumps
- Anything an NDA forbids you from sharing with third-party AI services

Connectors to scope narrowly or skip entirely:

- Email accounts that receive sensitive third-party content (legal, medical, HR communications)
- Chat workspaces with vendor-confidential discussions
- Drives containing customer data subject to processing agreements

### 6.3 Practices that reduce risk

- **Per-action approval for external writes.** Never grant Claude blanket write permission to external connectors. Approve each send, post, schedule individually.
- **Audit scheduled tasks.** A daily briefing that pulls from email reads every email each morning. Verify nothing in your inbox is meant to stay confidential.
- **Don't take "read everywhere" literally** if you handle regulated data. Broad ≠ all.
- **Treat Project Memory's PII rules as advisory, not enforced.** The system-prompt rules ask Claude not to save sensitive PII; there's no server-side filter. The model's judgment is the only safeguard.

### 6.4 Open issues to watch

- Indirect prompt injection mitigations (Anthropic is actively working on this; assume it's not solved)
- Audit log coverage for Cowork
- Per-connector scope controls (UI may improve)

This playbook does not solve security — it surfaces the surface. Pair adoption with your organization's existing data-handling policies.

## 7. Appendix A: Templates

Per-channel templates and examples. Copy-paste-adapt.

**These are real working examples**, evolved through months of use — not theoretical placeholders. The patterns they encode (close-the-gap research, goal-oriented prompts, don't-reinvent leverage) are named in § 2.2.2 and inform the design of the skills throughout this playbook. Adapt rather than fill in blanks.

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

```
# Claude in Cowork

1. Operate as a subject-matter expert. When not one, close the gap immediately through research (web, documents, papers). No acting on anything but current best practices.
2. Write straight-line: direct, active voice, concise. No fillers, no hedging, no repetition.
3. Make every output tailored, researched, and grounded in current best practice.
4. When drafting prompts for other agents, be goal-oriented, not prescriptive. State the outcome; let the agent figure out tooling and sequencing.
5. Use Claude Cowork's features aggressively — Skills, Plugins, scheduled tasks, connectors. Don't reinvent what a skill already handles.
6. Never edit CLAUDE.md files without explicit confirmation from Pedro of the exact wording.
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

`/Users/pedro/claude-workspace/` is the workspace root. It holds:

- A `CLAUDE.md` file — cross-Project hot cache (people, projects, terms).
- A `memory/` folder — cross-Project full storage (`glossary.md`, `voice-and-style.md`).
- One subfolder per project (e.g., `gtm-pedro/`, `MIC-global-application/`, `life-of-meaning/`). Each project subfolder uses standard sub-folders (`reference/`, `working/`, `deliverables/`) as needed. Project mission and persona live in the matching Cowork Project's Project Instructions (UI), not in CLAUDE.md files inside the project folder.

### Standard Sub-Folders

Projects use these folder names consistently:

| Folder | Contents | Lifecycle |
|--------|----------|-----------|
| `reference/` | Stable context: positioning, principles, personas, guides. | Changes when strategy changes. |
| `working/` | Drafts, scratch, session handoffs, research notes. | Purged periodically. Read YAML frontmatter to decide relevance. |
| `deliverables/` | Finished outputs: published content, final docs, shipped assets. | Permanent unless superseded. |

Not every project needs all three. Use what fits.

---

## Me

Pedro J. Cabassa — Strategist and operator. 20+ years across insurance brokerage, healthcare tech, and venture building. Bilingual (English/Spanish), NYC-based, Guatemalan roots. Currently independent — building AI products, consulting, and interviewing selectively.

**Experience:** operations leadership (strategy → execution), early-stage venture building, AI-first product and tooling (last ~5 years), cross-functional operating between business and technical teams.

**Strengths:** systems thinking, translating between operator and engineer languages, building from zero, shipping AI-augmented workflows, pattern recognition across industries.

**Gaps I'm actively closing:** deeper hands-on with modern AI dev stacks (agents, RAG, evals, MCP); sales/GTM reps at scale.

## Further Context

When the inline context above isn't enough, pull from these files:

→ Voice and tone: `/Users/pedro/claude-workspace/memory/voice-and-style.md`
→ Glossary (terms, nicknames, codenames): `/Users/pedro/claude-workspace/memory/glossary.md`

## Task System

Tasks live in `/Users/pedro/claude-workspace/TASKS.md`. Pedro references tasks by code (e.g., `BO3`, `MC1`) — project code → name mapping is in `/Users/pedro/claude-workspace/memory/projects/index.md`.

Conventions:
- Active codes never recycle. Use the lowest number not currently in use for that project prefix.
- Sections: Backlog, Up Next, In Progress, Waiting On, Done.
- Waiting On tasks include `// waiting: [person]` suffix.
- Due dates use `// [date]` suffix when applicable.
- Completed tasks include `// done: [date]` suffix.
- Include one link per task when possible: `→ [link]` at end of line.
- Completed tasks archive to `/Users/pedro/claude-workspace/DONE-LOG.md` via the weekly task-cleanup scheduled task (Fridays).

## Emails

- Personal: pedrocabassa@gmail.com
- Wellthy: pedro.cabassa.c@wellthy.com
- Co-Created: pedro@co-created.com
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

[Your company or primary organization. Include: name, your role there, brief context — what the company does, headcount, key tools and systems, internal language. Skip or simplify if you're independent.]

## My Team

The people you work with directly — manager, direct reports, frequent collaborators.

| Who | Role |
|-----|------|
| **[Nickname]** | [Full name, role on team] |
| ... | ... |

## People

Top ~15 contacts beyond your immediate team. Short descriptors — Project Memory carries deeper context per Cowork Project. Full glossary: `memory/glossary.md`.

| Who | Role |
|-----|------|
| **[Nickname]** | [Full name, role, company, one-line context] |
| ... | ... |

## Projects

Active project code → name → folder → status → description. Short descriptors — each Cowork Project's Project Memory carries the rest.

| Code | Name | Folder | Status | Description |
|------|------|--------|--------|-------------|
| **MC** | MIC Global | [./MIC-global-application/](file:///Users/pedro/claude-workspace/MIC-global-application/) | Pitch phase | AI/strategic pitch and program management. |
| **GT** | GTM Pedro | [./gtm-pedro/](file:///Users/pedro/claude-workspace/gtm-pedro/) | Active | Personal brand, positioning, outbound pipeline. |
| **OA** | Ops Advisory | [./ops-advisory/](file:///Users/pedro/claude-workspace/ops-advisory/) | Active | Advisory on operations, org design, AI-enabled operating models. |
| **LF** | Life of Meaning | [./life-of-meaning/](file:///Users/pedro/claude-workspace/life-of-meaning/) | Active | Career, health, relationships, meaning. |

## Terms

Top ~15 cross-project terms. Full glossary: `memory/glossary.md`.

| Term | Meaning |
|------|---------|
| **[Term]** | [Definition] |
| ... | ... |
```

### A.5 Workspace memory/ structure

Two files. That's it.

```
memory/
├── glossary.md          # full decoder ring (terms, nicknames, codenames)
└── voice-and-style.md   # how you write and speak
```

People and project indexes live in workspace `CLAUDE.md` (short descriptors). Per-project rich detail lives in each Cowork Project's Project Memory. The `memory/` folder holds only what's genuinely cross-cutting *and* worth user-curating: shared language and shared voice.

## 8. Appendix B: Project Memory mechanics

*Anthropic's name for this layer is "Auto Memory"; this playbook calls it "Project Memory" because that's what it actually is.*

For the curious. Anthropic actively iterates on this layer; details below are accurate as of 2026-04-28.

### B.1 Storage

Per Cowork Project, hard isolated. One "space" UUID per Cowork Project. Path: `~/Library/Application Support/Claude/local-agent-mode-sessions/<session>/spaces/<space-uuid>/memory/`. Cross-Project leakage is impossible — verified by direct test.

### B.2 The four types

- `user_*` — durable user facts (role, preferences, knowledge)
- `feedback_*` — corrections + validations
- `project_*` — durable project facts (decisions, deadlines, motivations)
- `reference_*` — pointers to external systems

### B.3 File format

Frontmatter: `name`, `description`, `type`. Body for `feedback`/`project`: rule, then `**Why:**` line, then `**How to apply:**` line. Other types use freer-form prose.

### B.4 Index

`MEMORY.md` is a bare markdown index — one bullet per entry: `- [Title](file.md) — one-line hook`. Anthropic-stated cap: 200 lines / 25KB. Lines beyond are silently truncated at session start.

### B.5 Write triggers

Model-driven, not tool-call-driven. Claude uses standard Write/Edit tools. Triggers are governed by a system-prompt section that defines what to save and what to skip (sensitive PII, ephemeral state, code patterns, anything derivable from current files).

### B.6 Stale-warning runtime

Every read of a typed memory file is annotated with the file's age — *"This memory is N days old. Memories are point-in-time observations, not live state — verify against current code before asserting as fact."* Built into the runtime, not just system-prompt rules.

### B.7 Lifecycle

Cleanup via Anthropic's `/consolidate-memory` skill (manual). Three phases: take stock, consolidate, tidy index. No auto-trigger documented as of writing.

### B.8 Sources

Verbatim system-prompt fragments captured in `system-prompt-memory-references-2026-04-28.md`. Anthropic's docs at code.claude.com/docs/en/memory.

## 9. Appendix C: Worked examples

### C.1 gtm-pedro asks "what's the status of the pipeline?"

You're in your gtm-pedro Cowork Project. You ask Claude: "What's the status of the pipeline?"

Claude has loaded:

1. **Personal Preferences** — direct, numbered, push back where appropriate.
2. **Cowork Instructions** — operate as Pedro's chief of staff; cite sources; write inside the workspace only.
3. **Project Instructions** — operate as Pedro's Chief GTM Executive; lead with researched recommendations.
4. **Workspace `CLAUDE.md`** — top 15 people across all projects; project codes including `GT` for gtm-pedro.
5. **Project Memory** for the gtm-pedro Cowork Project — `MEMORY.md` with entries about Pedro's GTM delegation expectations and recent pipeline state.

The auto-loaded context is enough for Claude to know the question is scoped to gtm-pedro (Project Instructions named the project), recall recent pipeline conversations (Project Memory), and answer in Pedro's preferred format (Personal Preferences). If a pipeline contact like "Sam at Acme" became recurring, the next refresh-workspace pass would propose promoting them into the workspace `CLAUDE.md` people table.

### C.2 A new person "Sam" starts appearing

Sam shows up in Slack threads three times this week. Your Workspace Hygiene scheduled task runs Sunday and notices: "Sam mentioned 3 times across two channels; not in any memory or hot cache."

The task proposes:

1. Add Sam to `memory/people/sam-{lastname}.md` with what's known from chat (role, team, when first mentioned).
2. If Sam continues being mentioned next week, propose promotion to the workspace `CLAUDE.md` people table.

You approve or skip. If approved, the next Cowork session in any Project sees Sam in workspace `CLAUDE.md` the moment frequency justifies it.

The framework handles people moving in and out of relevance without you needing to manually maintain a contact list.

## 10. Appendix D: File and naming conventions

### D.1 Naming

- Lowercase kebab-case: `pedros-cowork-playbook.md`, not `Pedros_Cowork_Playbook.md`.
- Date stamps when relevant (drafts, snapshots): `playbook-2026-04-28.md`.
- Be specific enough to identify the file without opening it.

### D.2 YAML frontmatter

On working files (drafts, references, snapshots):

```yaml
---
title:
purpose:
audience:
status: current | draft | superseded
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
superseded_by:  # only if status is superseded
---
```

Treat files with `status: superseded` as archived; don't reference their content.

### D.3 Folder structure

```
~/claude-workspace/             # workspace root
├── CLAUDE.md                   # workspace hot cache (§ 2.3.1)
├── memory/                     # workspace full storage (§ 2.3.2)
├── TASKS.md                    # optional — see § 5.1
├── DONE-LOG.md                 # optional — see § 5.1
├── project-1/                  # project folder, must be direct subfolder
│   ├── reference/              # stable strategic context
│   ├── working/                # drafts, scratch
│   └── deliverables/           # finished outputs
└── project-2/
    └── ...
```

### D.4 memory/ contents

```
memory/
├── glossary.md          # all terms, nicknames, codenames
└── voice-and-style.md   # how you write and speak
```

Only these two files. Per-project content lives in Project Memory; people/project indexes live in workspace `CLAUDE.md`. See § 2.3.2.

## 11. References

### 11.1 Anthropic official sources

**Cowork product**
- [Claude Cowork — Anthropic product page](https://claude.com/product/cowork)
- [Get started with Claude Cowork](https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork)
- [Organize tasks with Projects in Cowork](https://support.claude.com/en/articles/14116274-organize-your-tasks-with-projects-in-claude-cowork)

**Memory**
- [Claude Code memory docs (CLAUDE.md, auto-memory)](https://code.claude.com/docs/en/memory)
- [Use Claude's chat search and memory](https://support.claude.com/en/articles/11817273-use-claude-s-chat-search-and-memory-to-build-on-previous-context)
- [Anthropic — memory for teams launch](https://www.anthropic.com/news/memory)

**Plugins and skills**
- [knowledge-work-plugins repo](https://github.com/anthropics/knowledge-work-plugins) — open source plugins; productivity plugin lives here.
- [productivity plugin folder](https://github.com/anthropics/knowledge-work-plugins/tree/main/productivity)
- [Memory tool API docs](https://docs.claude.com/en/docs/agents-and-tools/tool-use/memory-tool) — the underlying primitive Anthropic uses for Project Memory.

**Bug tracking**
- [Issue #40175 — Cowork Instructions revert](https://github.com/anthropics/claude-code/issues/40175)
- [Issue #40731 — Cowork Instructions revert (related)](https://github.com/anthropics/claude-code/issues/40731)
- [Issue #45076 — Project metadata silent disappearance](https://github.com/anthropics/claude-code/issues/45076)
- [Issue #29434 — sensitive content in memory cannot be redacted](https://github.com/anthropics/claude-code/issues/29434)

### 11.2 Community sources (flagged as community)

Project Memory mechanics (Anthropic's term: "Auto Memory") — Anthropic publishes high-level docs but the deep mechanics are reverse-engineered. Treat the items below as best-current-understanding, verify before depending on specifics.

- [claudefa.st — Auto Memory mechanics](https://claudefa.st/blog/guide/mechanics/auto-memory)
- [claudefa.st — Auto Dream consolidation](https://claudefa.st/blog/guide/mechanics/auto-dream)
- [Milvus blog — Claude Code memory and memsearch](https://milvus.io/blog/claude-code-memory-memsearch.md)
- [DEV — Claude Code's memory: 4 layers of complexity](https://dev.to/chen_zhang_bac430bc7f6b95/claude-codes-memory-4-layers-of-complexity-still-just-grep-and-a-200-line-cap-2kn9)

Independent Cowork guides (consulted during research; none treats the full instruction-channel set as a partitioned system):

- Ruben Hassid — uses Global Instructions as a dispatcher pointing to ABOUT ME / TEMPLATES / PROJECTS folders.
- Alex Banks — layers Project Instructions over Global + Context files.
- Jeff Su — treats channels as separate features.
- Karo Zieminski — focuses on plugin/skill/sub-agent tactics.

### 11.3 Internal source materials

- `working/system-prompt-memory-references-2026-04-28.md` — verbatim fragments of Claude's system prompt governing Project Memory; primary source for § 2.4 and Appendix B.
- `working/pedros-cowork-backlog-and-research-2026-04-28.md` — research notes, design decisions, open questions that fed v4.
- `working/early-feedback-on-playbook-v4-2026-04-28.md` — external review captured for triage.
