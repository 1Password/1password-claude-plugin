# 1Password Plugin for Claude Code

> **Status: in development.** Not installable for real use yet.

The official [1Password](https://1password.com) plugin for
[Claude Code](https://code.claude.com). When complete it will ship a hook that validates locally
mounted `.env` files before Bash commands run, an agent skill with the complete Developer
Environment workflow, and MCP configuration for the 1Password desktop app server.

Secret values stay in 1Password — the agent sees variable names and mount paths, not secret
contents.

This is the Claude Code counterpart to the
[1Password Plugin for Cursor](https://github.com/1Password/cursor-plugin).

## Local `.env` file validation

Validates locally mounted `.env` files from
[1Password Environments](https://developer.1password.com/docs/environments) before Bash commands
execute. When a required environment file is missing, disabled, or misconfigured, the hook blocks the
command and explains how to fix it. Reading a `.env` with the Read tool is unaffected — only Bash is
gated.

Requires [sqlite3](https://www.sqlite.org/) on your `PATH` (pre-installed on macOS; install via your
package manager on Linux).

The hook **fails open**: if 1Password is not installed, the database is unavailable, or `sqlite3` is
missing, it reports no decision and your commands run as normal. It never auto-approves a command —
a passing check leaves your permission settings untouched.

**Validation modes.** By default, every 1Password mount belonging to the current project is checked.
To narrow that, add `.1password/environments.toml` at your project root:

```toml
mount_paths = [".env", "billing.env"]   # validate only these
mount_paths = []                        # disable validation for this project
```

**Platform support.** macOS and Linux, including WSL. On Windows, the hook skips immediately —
1Password Environments has no local `.env` mounts there.

**Debugging.** Run `claude --debug` to see hook matches and exit codes. Outside debug mode the hook
logs to `/tmp/1password-claude-code-hooks.log`. To run it by hand:

```bash
DEBUG=1 echo '{"hook_event_name":"PreToolUse","tool_name":"Bash","cwd":"/path/to/project","tool_input":{"command":"echo test"}}' \
  | ./scripts/validate-mounted-env-files.sh
```

## TODO

Add the agent skill and MCP configuration, then document requirements and installation.

## License and Support

- [Privacy Policy](https://1password.com/legal/privacy)
- [Support](https://support.1password.com)
- [MIT LICENSE](LICENSE)
