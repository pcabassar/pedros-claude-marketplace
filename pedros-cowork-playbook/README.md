# Pedro's Cowork Playbook

A framework and plugin for getting [Claude Cowork](https://claude.com/product/cowork) to operate coherently across sessions and Cowork Projects. Partitions Cowork's instruction surfaces and memory layers into a six-channel system that stays consistent without re-explanation.

Built on Cowork's native primitives — Cowork Projects, Project Memory (Anthropic's "Auto Memory"), Connectors, Skills. Targets users who've outgrown ad-hoc setups and want a stable operating system underneath their work.

## Install

This plugin is distributed via [pedros-claude-marketplace](https://github.com/pcabassar/pedros-claude-marketplace).

```
/plugin marketplace add pcabassar/pedros-claude-marketplace
/plugin install pedros-cowork-playbook@pedros-claude-marketplace
```

After install, run `/reload-plugins` to activate.

## Quick start

Run the `/pedros-cowork-playbook:setup` skill. It detects your current state — empty workspace, existing folders, partially configured Projects — and guides you from there.

## What's inside

| File / folder | What it is |
|---|---|
| [`playbook.md`](./playbook.md) | The framework explanation. Read this first. |
| [`skills/setup/`](./skills/setup/) | Bootstrap or migrate a workspace into the framework. |
| [`skills/refresh-workspace/`](./skills/refresh-workspace/) | Keep cross-Project memory current; seed Project Memory in fresh Cowork Projects. |

Plugin skills are namespaced — invoke as `/pedros-cowork-playbook:setup` and `/pedros-cowork-playbook:refresh-workspace`.

## When to use what

| If you want to... | Read | Run |
|---|---|---|
| Understand the framework | `playbook.md` | — |
| Bootstrap a new workspace | `playbook.md` § 2 | `/pedros-cowork-playbook:setup` |
| Migrate an existing setup | `playbook.md` § 2 | `/pedros-cowork-playbook:setup` |
| Keep your workspace current | `playbook.md` § 2.3.3 | `/pedros-cowork-playbook:refresh-workspace` |
| Seed Project Memory in a new Cowork Project | — | `/pedros-cowork-playbook:refresh-workspace` (handles this too) |
| Clean up a Cowork Project's Project Memory | — | Anthropic's `/consolidate-memory` |

## Roadmap

Planned additions tracked in [`playbook.md` § 3](./playbook.md):

- Optional `task-system` module (TASKS.md + Friday archive)
- Optional `daily-briefing` module (scheduled task)
- Optional `newsletter-digest` module (scheduled inbound digests)

## Status

Draft v4. Source of truth: this repo. Issues and PRs welcome.

## Author

Pedro J. Cabassa — [github.com/pcabassar](https://github.com/pcabassar)

## License

MIT — see [LICENSE](../LICENSE) at the marketplace root.
