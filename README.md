# 1Password Plugin for Claude Code

> **Status: in development.** Not yet published to the Claude Code plugin marketplace.

The official [1Password](https://1password.com) plugin for [Claude Code](https://code.claude.com). It ships a **PreToolUse hook** that validates locally mounted `.env` files before Bash commands run. Secret values stay in 1Password — the agent sees variable names and mount paths, not secret contents.

For more on 1Password's developer tools, see the [1Password Developer Documentation](https://developer.1password.com).

## Requirements

- [1Password](https://1password.com) subscription
- [1Password desktop app](https://1password.com/downloads) on **macOS or Linux**
- [Claude Code](https://code.claude.com)
- [sqlite3](https://www.sqlite.org/) installed and available in your `PATH` (pre-installed on macOS; install via your package manager on Linux)

> **Platform support:** Local `.env` mounts and mount validation are supported on **macOS and Linux**, including WSL. On **Windows**, the hook exits immediately with no decision so Bash is not blocked; 1Password Environments has no local `.env` mounts on Windows.

## Installation and Setup

### Step 1: Set up your Environments

Before using this plugin, configure your secrets in 1Password:

1. [Create one or more Environments](https://developer.1password.com/docs/environments) in 1Password to store your project secrets.
2. [Configure locally mounted `.env` files](https://developer.1password.com/docs/environments/local-env-file) for them.

### Step 2: Install the plugin

When published, install from the Claude Code plugin marketplace. This registers the validation hook from `hooks/hooks.json`.

**From a marketplace** (once available):

```
/plugin marketplace add <marketplace-url>
/plugin install 1password@1password
```

**For local development**, point Claude Code at this repository:

```bash
claude --plugin-dir /path/to/1password-claude-plugin
```

See [Discover and install plugins](https://code.claude.com/docs/en/discover-plugins) and [Plugin marketplaces](https://code.claude.com/docs/en/plugin-marketplaces) for the full installation flow.

## Features

### Hooks

#### Local `.env` File Validation (`PreToolUse`)

Validates locally mounted `.env` files from [1Password Environments](https://developer.1password.com/docs/environments) before any Bash command runs. When required environment files are missing, disabled, or misconfigured, the hook blocks the command and surfaces actionable error messages so Claude can guide you to a fix. Reading a `.env` with the Read tool is unaffected — only Bash is gated.

This hook was originally developed in the [1Password Agent Hooks](https://github.com/1Password/agent-hooks) repository.

**How it works:**

Every time Claude Code attempts to run a Bash command, the hook:

1. **Discovers** your configured [local `.env` files](https://developer.1password.com/docs/environments/local-env-file) by querying the 1Password database.
2. **Validates** that each file exists as a valid FIFO (named pipe) and is enabled in 1Password.
3. **Passes** with no decision if all environment files are properly configured — your normal Bash permission settings apply unchanged.
4. **Blocks** the command and provides clear error messages when files are missing or disabled.

The hook uses a **"fail open"** approach: if 1Password is not installed, the database is unavailable, or `sqlite3` is missing, the hook reports no decision and execution proceeds. It never auto-approves a command — returning `permissionDecision: "allow"` would skip the permission prompt, so a passing check emits nothing (exit 0, empty stdout) and your normal permission settings apply.

##### Validation Modes

The hook supports two validation modes depending on whether a TOML configuration file is present.

**Default Mode**

When no `.1password/environments.toml` file exists in your project (or when the file exists but doesn't contain a `mount_paths` field), the hook automatically:

1. Detects your operating system (macOS or Linux).
2. Queries the 1Password database for all configured mount entries.
3. Filters to only the local `.env` files relevant to the current workspace.
4. Validates that each discovered file is enabled and exists as a valid FIFO.

**Configured Mode**

When a `.1password/environments.toml` file exists at your project root **and** contains a `mount_paths` field, only the specified files are validated:

```toml
# Validate only these specific files
mount_paths = [".env", "billing.env", "database.env"]
```

This gives you precise control over which files the hook checks. Configuration examples:

| Configuration                           | Behavior                                                                |
| --------------------------------------- | ----------------------------------------------------------------------- |
| `mount_paths = [".env"]`                | Only `.env` is validated                                                |
| `mount_paths = [".env", "billing.env"]` | Both files are validated                                                |
| `mount_paths = []`                      | Validation is disabled — all commands allowed                           |
| *(no TOML file)*                        | Default mode — all 1Password-mounted files in the project are validated |

Mount paths can be relative to the project root or absolute. Multi-line arrays are supported:

```toml
mount_paths = [
    ".env",
    "billing.env",
    "database.env",
]
```

For each file, the hook checks:

- **Exists** — the file is present on disk.
- **Is FIFO** — the file is a named pipe (how 1Password mounts secrets).
- **Is enabled** — the mount is turned on in the 1Password app.

##### Debugging

**Claude Code debug output**

Run `claude --debug` to see hook matches and exit codes in the session output.

**Manual testing with debug mode**

Run the hook directly with `DEBUG=1` to see detailed output on stderr:

```bash
echo '{"hook_event_name":"PreToolUse","tool_name":"Bash","cwd":"/path/to/your/project","tool_input":{"command":"echo test"}}' \
  | DEBUG=1 ./scripts/validate-mounted-env-files.sh
```

**Log file**

When not running in debug mode, the hook writes logs to `/tmp/1password-claude-code-hooks.log`. Log entries include timestamps and details about 1Password queries, validation results, and permission decisions.

## Plugin Structure

```
1password-claude-plugin/
├── .claude-plugin/
│   ├── plugin.json                    # Plugin manifest
│   └── marketplace.json               # Marketplace catalog (for distribution)
├── hooks/
│   └── hooks.json                     # PreToolUse mount validation
├── scripts/
│   ├── lib/
│   │   └── telemetry.sh               # Opt-in telemetry helpers for the validation hook
│   └── validate-mounted-env-files.sh  # Bash hook (macOS / Linux)
├── LICENSE
└── README.md
```

## Telemetry

The validation hook emits **opt-in** telemetry so 1Password can understand plugin adoption and the prevalence of common failure modes (missing files, disabled mounts). Two event types are emitted:

- `agent_hook_execution` — fired once per hook invocation; carries the hook name, plugin version, client (`claude-code`), bucketed duration, decision (`allow`/`deny`), reason for deny, validation mode (`default`/`configured`), and a count of mounts checked.
- `agent_hook_install` — fired once per `(hook_name, plugin_version)` on the first hook run after installation or upgrade; `install_method` is `plugin_marketplace`.

**Opt-in only.** Events are written only when the file `~/.config/1Password/telemetry-enabled` exists. The 1Password desktop app creates and removes this file based on your in-app telemetry preference (Settings → Manage Account → Data Usage). If the app has never run, or all accounts have opted out, no events are written.

**No PII.** Events contain hook name and version, client, decision, bucketed duration, mode, mount count, and a deny reason. No paths, file contents, environment names, or workspace paths are recorded.

**Fail-open.** Telemetry runs in a detached background subshell after the hook has returned its decision to Claude Code. Any failure (missing helpers, disk full, permission denied) is silently swallowed — telemetry can never affect a hook decision.

**Where events are written.** Events are appended as JSON lines to `~/.config/1Password/data/hook-events/events.jsonl`. The 1Password desktop app periodically ingests this file and forwards events to 1Password's telemetry pipeline. Telemetry only fires on macOS and Linux; the Windows early exit does not emit events.

**To disable.** Open the 1Password desktop app → Settings → Manage Account → Data Usage and turn off product telemetry.

## Resources

- [1Password Agent Hooks](https://github.com/1Password/agent-hooks) — the original hooks repository this plugin is based on
- [1Password Environments](https://developer.1password.com/docs/environments) — documentation for 1Password's environment and secrets management
- [1Password Local `.env` Files](https://developer.1password.com/docs/environments/local-env-file) — how local `.env` file mounting works
- [Claude Code Hooks](https://code.claude.com/docs/en/hooks) — how Claude Code hooks work
- [Claude Code Plugins](https://code.claude.com/docs/en/plugins) — how to create and distribute Claude Code plugins

## License

[MIT](./LICENSE) — Copyright (c) 2026 1Password
