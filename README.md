# Bergamota

Cursor plugin that ships the **Bergamota** custom mode and the skills it depends on: ctx history search, planning, plan execution, debugger, anti-slop, unslop, and the **ctx** MCP.

Lighter than **poteto-mode**. Attach pstack when you want that rigor on top. Superpowers **brainstorming** and **using-git-worktrees** stay in the Superpowers plugin (not copied here).

## What's inside

| Component | Path |
|-----------|------|
| Custom mode | `skills/bergamota-mode/` |
| ctx history search | `skills/ctx-agent-history-search/` |
| Planning | `skills/planning/` |
| Plan execution | `skills/subagent-build-plan/` |
| Debugger | `skills/debugger/` |
| Anti-slop | `skills/anti-slop/` |
| Unslop | `skills/unslop/` |
| ctx MCP | `mcp.json` (`ctx mcp serve`) |

Requires Cursor **custom modes** (`glass_custom_modes`) to appear in the mode picker. Without that gate it still loads as a skill (`/bergamota-mode`).

**ctx MCP** needs the `ctx` CLI on PATH (`curl -fsSL https://ctx.rs/install | sh`). First-party Cursor MCPs (`cursor-app-control`, browser) are not bundled.

## Install

### Marketplace

Search **Bergamota** in Cursor **Customize** after the listing is approved, or open the GitHub repository from the Cursor Marketplace.

### Local (user-level)

```bash
git clone https://github.com/Marcelo-Barella/bergamota-mode.git
mkdir -p ~/.cursor/plugins/local
rm -rf ~/.cursor/plugins/local/bergamota-mode
rsync -a --delete \
  --exclude '.git/' \
  --exclude '.cursor/' \
  "$(pwd)/bergamota-mode/" \
  ~/.cursor/plugins/local/bergamota-mode/
```

Then **Developer: Reload Window**. Confirm **bergamota-mode** under Settings → Plugins.

## Usage

```text
/bergamota-mode
/planning
/subagent-build-plan
/debugger
/anti-slop
/unslop
```

Or pick **Bergamota** from the custom modes tray.

## Plugin layout

```text
bergamota-mode/
├── .cursor-plugin/plugin.json
├── mcp.json
├── assets/logo.svg
├── skills/
│   ├── bergamota-mode/
│   ├── ctx-agent-history-search/
│   ├── planning/
│   ├── subagent-build-plan/
│   ├── debugger/
│   ├── anti-slop/
│   └── unslop/
├── LICENSE
├── NOTICE
└── README.md
```

## License

MIT. See [LICENSE](./LICENSE) and [NOTICE](./NOTICE).

## Author

Marcelo Barella.
