# Bergamota

Cursor plugin that ships the **Bergamota** custom mode: default agent conventions for surgical diffs, ctx history search before acting, explicit git consent, planning before large builds, and evidence-based verification.

Lighter than **poteto-mode**. Attach both when you want pstack rigor on top.

## What's inside

| Component | Path |
|-----------|------|
| Custom mode / skill | `skills/bergamota-mode/` |

Requires Cursor **custom modes** (`glass_custom_modes`) to appear in the mode picker. Without that gate it still loads as a skill (`/bergamota-mode`).

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
```

Or pick **Bergamota** from the custom modes tray.

## Plugin layout

```text
bergamota-mode/
├── .cursor-plugin/plugin.json
├── assets/logo.svg
├── skills/bergamota-mode/
│   └── SKILL.md
├── LICENSE
└── README.md
```

## License

MIT. See [LICENSE](./LICENSE).

## Author

Marcelo Barella.
