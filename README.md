# ai-skill-by-sahan

A personal Claude plugin marketplace — a home for the skills/plugins Sahan builds with Claude, version-controlled on GitHub instead of only living in Claude's account-level skill library.

## What's in here

```
sahan-skills/
├── .claude-plugin/
│   └── marketplace.json          # lists every plugin in this repo
└── plugins/
    └── youtube-fintuber-digest/  # summarizes his YouTube fintuber playlist
        ├── .claude-plugin/
        │   └── plugin.json
        └── skills/
            └── youtube-fintuber-digest/
                └── SKILL.md
```


## Adding it in Claude Code (CLI)

```bash
/plugin marketplace add <your-username>/sahan-skills
/plugin install youtube-fintuber-digest@sahan-skills
```

Or non-interactively:

```bash
claude plugin marketplace add <your-username>/sahan-skills
claude plugin install youtube-fintuber-digest@sahan-skills
```

## Using it in Cowork

Cowork installs plugins from a downloadable `.plugin` file (a zip of one plugin's folder) shown as a card in chat with an install button, rather than by adding a marketplace URL directly in the UI. To get a plugin from this repo into Cowork, ask Claude to package the plugin folder (e.g. `plugins/youtube-fintuber-digest`) as a `.plugin` file and send it to you — it'll show up as an installable card. If you update the skill content in this repo, re-run that packaging step to refresh the installed version.

## Adding another skill/plugin later

1. Create a new folder under `plugins/<plugin-name>/` with its own `.claude-plugin/plugin.json` and `skills/<skill-name>/SKILL.md`.
2. Add an entry for it to the `plugins` array in `.claude-plugin/marketplace.json`.
3. Commit and push. Bump the plugin's `version` field whenever you want existing installs to pick up the change (Claude only updates when the version changes).
