# 1Password Environments — reference

Supplement to [SKILL.md](SKILL.md). Use for setup and mount problems.

## Plugin components

This plugin ships:

| Component | Path | Purpose |
|-----------|------|---------|
| MCP config | `.mcp.json` | Connects Claude Code to the 1Password desktop MCP server via `1password-mcp` (macOS/Linux) |
| Agent skill | `skills/1password-environments/SKILL.md` | Import, mount, and Environment management workflow |

Install the plugin with `/plugin install` — do not add the MCP server manually in
user settings without the bundled skill.

## Official documentation

Fetch hosted docs when you need product context, security boundaries, or
edge cases beyond this plugin's workflow. Index:
[llms.txt](https://www.1password.dev/llms.txt)

| Topic | Doc |
|-------|-----|
| Local `.env` mounts (FIFO, git conflicts, dotenv compatibility) | [local-env-file.md](https://www.1password.dev/environments/local-env-file.md) |
| MCP server (setup, auth prompts, security model) | [mcp-server.md](https://www.1password.dev/environments/mcp-server.md) |

## Setup

**Requirements**

- **macOS or Linux** with the [1Password desktop app](https://1password.com/downloads) installed
- **1Password Claude Code plugin installed** so this skill and MCP config load together
- 1Password Labs **MCP Server** experiment enabled in the desktop app (`onepassword://settings/labs`)
- Access to a 1Password account with Developer Environments enabled
- `1password-mcp` on your `PATH` (registered by the desktop app when MCP is enabled)

On **Windows**, MCP and local mounts are unavailable.
WSL reports as Linux and works normally.

MCP server setup and platform details: [mcp-server.md](https://www.1password.dev/environments/mcp-server.md)

**When things fail**

- Authentication or environment access fails — the 1Password desktop app may need approval, unlocking, or account access
- MCP server unavailable — enable the **1Password Labs MCP Server** experiment via `onepassword://settings/labs`. If the Labs setting is missing, the account may lack the `ai-local-mcp-server` feature flag or an admin may have disabled the local MCP server — see [mcp-server.md](https://www.1password.dev/environments/mcp-server.md)
- `create_local_env_file` fails — confirm the user is on macOS or Linux
- A mount is missing or disabled — see **Mount problems** below

## Mount problems

If `list_local_env_files` shows a mount that is missing or disabled:

1. Check whether 1Password already has a destination for the same path (`list_local_env_files`, or the 1Password app Destinations tab)
2. Fix the mount in 1Password (enable the destination, or remove it until migration finishes)

The import itself does not need shell — Read the `.env` directly to get its keys
and values, then use MCP tools per [SKILL.md](SKILL.md).
