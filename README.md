# Pedro's Claude Marketplace

Plugins for [Claude Cowork](https://claude.com/product/cowork) and [Claude Code](https://claude.com/product/claude-code), built by [Pedro J. Cabassa](https://github.com/pcabassar).

## Plugins

| Plugin | What it does |
|---|---|
| [pedros-cowork-playbook](./pedros-cowork-playbook) | A framework and skills for getting Cowork to operate coherently across sessions and Projects. Partitions instruction surfaces and memory layers so context stays sharp without re-explanation. |

## Install

Add this marketplace, then install the plugins you want.

### Claude Code

```bash
# Add the marketplace
claude plugin marketplace add pcabassar/pedros-claude-marketplace

# Install a plugin
claude plugin install pedros-cowork-playbook@pedros-claude-marketplace
```

Or from inside a Claude Code session:

```
/plugin marketplace add pcabassar/pedros-claude-marketplace
/plugin install pedros-cowork-playbook@pedros-claude-marketplace
```

### Claude Cowork

Cowork uses the same plugin system as Claude Code. Add the marketplace using the `/plugin marketplace add` command from a Cowork session, then install plugins from this marketplace.

## What's a marketplace?

A marketplace is a catalog of plugins. Adding the marketplace registers the catalog with Claude — it doesn't install anything. You then install individual plugins from the catalog. See [Anthropic's plugin marketplace docs](https://code.claude.com/docs/en/plugin-marketplaces) for details.

## License

MIT — see [LICENSE](./LICENSE).

## Contributing

Issues and PRs welcome. Each plugin lives in its own subfolder with its own README.
