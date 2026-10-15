# 1Password Plugin for Claude Code

A [1Password](https://1password.com) plugin for [Claude Code](https://code.claude.com), built and maintained by 1Password. It ships two pieces that work together: an **agent skill** with the complete Developer Environment workflow, and **MCP configuration** for the 1Password desktop app server. Secret values stay in 1Password — the agent sees variable names and mount paths, not secret contents.

Install the **plugin** rather than hand-configuring an MCP entry on its own. The bundled `1password-environments` skill is the authoritative agent workflow; the MCP server's built-in documentation resources cover tool basics only and omit the import-and-mount steps.

For more on 1Password's developer tools, see the [1Password Developer Documentation](https://developer.1password.com).

## Requirements

- [1Password](https://1password.com) subscription
- [1Password desktop app](https://1password.com/downloads) on **macOS or Linux**
- [Claude Code](https://code.claude.com)

Additional requirements by feature:

- **MCP** — the 1Password Labs **MCP Server** experiment enabled in the desktop app (`onepassword://settings/labs`). If the setting is missing, your account may not have the `ai-local-mcp-server` feature flag. The plugin's `.mcp.json` launches the `1password-mcp` command from your `PATH`, as described in the [1Password MCP server documentation](https://www.1password.dev/environments/mcp-server).

> **Platform support:** MCP and local `.env` mounts are supported on **macOS and Linux**. 1Password Environments has no local `.env` mounts on Windows.

## Installation and Setup

### Step 1: Set up your Environments

Before using this plugin, configure your secrets in 1Password:

1. [Create one or more Environments](https://developer.1password.com/docs/environments) in 1Password to store your project secrets.
2. [Configure locally mounted `.env` files](https://developer.1password.com/docs/environments/local-env-file) for them.

### Step 2: Install the plugin

Installing the plugin registers the `1password-environments` agent skill and the MCP server configuration together.

**From this repository.** This repo is itself a plugin marketplace, so add it directly by its GitHub `owner/repo` name — no separate catalog needed:

```
claude /plugin marketplace add 1Password/1password-claude-plugin
claude /plugin install 1password@1password
```

Run `/plugin` afterwards to confirm the plugin is installed and the MCP server is connected.

**For local development**, point Claude Code at a checkout instead:

```bash
claude --plugin-dir /path/to/1password-claude-plugin
```

### Step 3: Enable MCP in 1Password (required for Environment management)

Enable the **MCP Server** experiment in the 1Password desktop app: open **Settings → Labs** (or use `onepassword://settings/labs`) and turn on **MCP Server**. The plugin's `.mcp.json` connects Claude Code to that server after this step.

The plugin registers the MCP command documented by 1Password:

```json
{
  "mcpServers": {
    "1password": {
      "command": "1password-mcp"
    }
  }
}
```

Install the 1Password desktop app on macOS or Linux so `1password-mcp` is available on your `PATH`. For platform-specific install paths and troubleshooting, see the [1Password MCP server documentation](https://www.1password.dev/environments/mcp-server).

## Features

### MCP and agent skill

The plugin connects Claude Code to the local 1Password MCP server and bundles the **`1password-environments`** skill (`skills/1password-environments/SKILL.md`). Claude reads that skill before calling MCP tools — it defines the complete workflow for importing a plain `.env` file, appending variables, and mounting at the source path. The MCP server's built-in docs cover tool basics but omit those import-and-mount steps.

See `skills/1password-environments/reference.md` for setup, mount problems, and troubleshooting.

#### Example prompts

- "List my 1Password Environments"
- "Mount my staging Environment as `.env` in this repo"
- "What variables are in my production Environment?"
- "Create a new Environment called `my-app-dev`"
- "Create an Environment from my project `.env` file"
- "Import `.env` into 1Password and mount it here"
- "Add a placeholder for my OpenAI API key"

#### MCP tools

Tools are namespaced `mcp__plugin_1password_1password__<tool>`.

| Tool | Description |
|------|-------------|
| `authenticate` | Authenticate with the 1Password desktop app; returns `accountId` |
| `list_environments` | List Developer Environments for an account |
| `create_environment` | Create a new Developer Environment |
| `rename_environment` | Rename an existing Developer Environment |
| `list_variables` | List variable names in an Environment (no values) |
| `append_variables` | Add or update Environment variables |
| `create_local_env_file` | Mount an Environment as a local `.env` file |
| `list_local_env_files` | List existing local `.env` mounts for an Environment |

Confirm the MCP server is connected with `/plugin` after installing the plugin and enabling the Labs experiment in 1Password.

## Plugin Structure

```
1password-claude-plugin/
├── .claude-plugin/
│   ├── plugin.json                    # Plugin manifest
│   └── marketplace.json               # Marketplace catalog (for distribution)
├── skills/
│   └── 1password-environments/
│       ├── SKILL.md                   # Agent skill for MCP workflows
│       └── reference.md               # Setup, mount problems, troubleshooting
├── .mcp.json                          # MCP server configuration
├── LICENSE
└── README.md
```

## Releasing

Each release gets its own PR. Don't change the version number in feature PRs.

1. Create a release branch from the latest `main`, named after the new version, and push it:

   ```bash
   git checkout main
   git pull
   git checkout -b release/v0.3.0
   git push -u origin release/v0.3.0
   ```

   The **Prepare release** workflow commits a version bump in [`.claude-plugin/plugin.json`](.claude-plugin/plugin.json) and an empty `0.3.0` section in [`CHANGELOG.md`](CHANGELOG.md) to the branch, then opens a `Release v0.3.0` PR.

2. Pull the workflow's commit:

   ```bash
   git pull
   ```

3. Open `CHANGELOG.md` and write what changed under **Features**, **Fixes**, or **Security**. Delete any heading you don't use. The new section includes a comment listing the PRs merged since the last release, to help you remember what went in. Delete the comment when you're done.

4. Commit and push:

   ```bash
   git commit -am "Update changelog for v0.3.0"
   git push
   ```

5. Merge the PR. Merging it is the release.

Claude Code only offers users an update when the `version` string changes, so changes merged to `main` reach existing users when the release PR is merged.

## Resources

- [1Password Environments](https://developer.1password.com/docs/environments) — documentation for 1Password's environment and secrets management
- [1Password Local `.env` Files](https://developer.1password.com/docs/environments/local-env-file) — how local `.env` file mounting works
- [1Password MCP server documentation](https://www.1password.dev/environments/mcp-server) — MCP setup and troubleshooting
- [Claude Code Plugins](https://code.claude.com/docs/en/plugins) — how to create and distribute Claude Code plugins

## License

[MIT](./LICENSE) — Copyright (c) 2026 1Password
