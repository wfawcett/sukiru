# sukiru — Claude Marketplace

A minimal, well-structured example of a custom Claude Marketplace repository.
Use this as a reference when creating your own.

## Quick Start

```
/plugin marketplace add wfawcett/sukiru
/plugin install sukiru@sukiru
```

## Plugins

| Plugin | Skill | Description |
|---|---|---|
| `sukiru` | `intent` | Interview-based intent clarification. Turns vague ideas into clear, actionable intention statements before any spec or brainstorm work begins. |

### Trigger

```
/sukiru:intent
```

## Structure

This repository demonstrates the correct marketplace layout:

```
sukiru/
├── .claude-plugin/
│   └── marketplace.json       # Central registry (root-level name + owner + metadata + plugins[])
├── plugins/
│   └── sukiru/
│       ├── .claude-plugin.json # Plugin manifest (name, version, description, entrypoint)
│       └── skills/
│           └── intent/
│               └── SKILL.md    # Skill definition
├── README.md
└── marketplace.md
```

## Key Conventions

1. **Marketplace manifest** (`.claude-plugin/marketplace.json`)
   - `name` uses `kebab-case`
   - `owner` includes both `name` and `email`
   - `metadata` wraps `description` and `version`
   - `plugins[]` lists each plugin with `name`, `version`, `description`, `source`

2. **Plugin manifest** (`plugins/<name>/.claude-plugin.json`)
   - Single file, **not** a directory
   - Must include `name`, `version`, `description`, `entrypoint`
   - Optional: `author`, `keywords`, `skills`

3. **Source field**
   - Use `{"source": "github", "repo": "owner/repo"}` for GitHub-hosted marketplaces
   - Use a Git URL for other hosts
   - Use a local path for testing

4. **Skills**
   - Each skill gets its own `SKILL.md` with frontmatter (`name`, `description`)
   - Referenced from the plugin manifest's `skills[]` array
