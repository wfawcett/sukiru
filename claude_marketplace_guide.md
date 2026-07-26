# Claude Marketplace & Plugin Management Guide

This comprehensive guide is built specifically for your coding agent to understand, create, and manage a custom **Claude Marketplace**, and for you to manage subscriptions and automate installations using `extraKnownMarketplaces` and plugin configurations.

---

## Part 1: Guide for Your Coding Agent (Creating & Managing a Marketplace)

Your agent needs to scaffold, validate, and structure a custom plugin marketplace repository that Claude Code can consume.

### 1. Marketplace Repository Structure
A Claude Marketplace is fundamentally a standard Git repository (e.g., hosted on GitHub) containing a central registry file and individual plugin directories.

```text
my-claude-marketplace/
├── .claude-plugin/
│   └── marketplace.json       # Central registry manifest
├── plugins/
│   ├── my-custom-plugin/
│   │   ├── .claude-plugin.json  # Individual plugin manifest
│   │   ├── skills/              # Custom agent skills / prompts
│   │   ├── commands/            # Custom slash commands
│   │   └── hooks/               # Event hooks (Pre/Post tool use, etc.)
```

### 2. Creating the Central Manifest (`.claude-plugin/marketplace.json`)
The agent must generate a root `.claude-plugin/marketplace.json` file adhering to this schema:

```json
{
  "name": "my-org-marketplace",
  "owner": {
    "name": "Engineering Team",
    "email": "eng@example.com"
  },
  "metadata": {
    "description": "Internal tools, hooks, and extensions for our team",
    "version": "1.0.0"
  },
  "plugins": [
    {
      "name": "security-linter",
      "version": "1.0.0",
      "description": "Scans modified files for internal security compliance",
      "source": "./plugins/security-linter"
    }
  ]
}
```
*Note: The `name` field must always use `kebab-case`.*

### 3. Creating Individual Plugins (`plugins/<plugin-name>/.claude-plugin.json`)
Inside each plugin folder, the agent must define the plugin metadata manifest:

```json
{
  "name": "security-linter",
  "version": "1.0.0",
  "description": "Automated security rule checker",
  "entrypoint": "./index.js"
}
```

---

## Part 2: Guide for You (Managing Subscriptions & Auto-Installing Plugins)

As the human administrator or lead developer, you can automate how marketplaces and plugins get pulled into environments using configuration files.

### 1. Auto-Configuring Marketplaces (`extraKnownMarketplaces`)
To prevent team members or automated agents from having to manually run marketplace add commands, you can pre-register marketplaces globally or per project inside `.claude/settings.json`.

Create or modify `.claude/settings.json` in your project root:

```json
{
  "extraKnownMarketplaces": {
    "my-org": {
      "source": {
        "source": "github",
        "repo": "your-org/my-claude-marketplace"
      }
    }
  }
}
```

#### Supported Source Types:
* **GitHub:** `{"source": "github", "repo": "owner/repo"}`
* **Git URL:** Direct cloning endpoints (`https://gitlab.com/...`)
* **Local Directory:** Absolute or relative paths for testing.

Once a project folder with this configuration is trusted, Claude Code automatically syncs the marketplace.

### 2. Managing Subscriptions and Auto-Installing Plugins (`enablePlugins`)
To ensure specific plugins are forcefully loaded or subscribed to across environments (such as CI/CD pipelines or standardized developer laptops), you declare them under your configuration settings.

Update `.claude/settings.json` to include auto-enabled plugins tied to your marketplace name:

```json
{
  "extraKnownMarketplaces": {
    "my-org": {
      "source": {
        "source": "github",
        "repo": "your-org/my-claude-marketplace"
      }
    }
  },
  "enablePlugins": {
    "security-linter@my-org": true,
    "code-review@claude-plugins-official": true
  }
}
```

#### How the Syntax Works:
* Format follows: `plugin-name@marketplace-identifier`
* Setting a plugin to `true` under `enablePlugins` instructs Claude Code to fetch, resolve, and hook up the plugin automatically on session startup.

---

## Quick Reference CLI Commands for Verification

If you or your agent need to test the workflow interactively via the terminal/CLI:

1. **Manually Add Marketplace:**
   ```bash
   /plugin marketplace add your-org/my-claude-marketplace
   ```
2. **Install Plugin Directly:**
   ```bash
   /plugin install security-linter@my-org
   ```
3. **Reload Plugins Post-Configuration:**
   ```bash
   /reload-plugins
   ```
