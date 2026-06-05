# 1Password Developer Environments — Claude Desktop Extension
# THIS IS A DRAFT/WIP
A Claude [desktop extension](https://claude.com/docs/connectors/building/mcpb) (MCPB)
that connects Claude to the 1Password desktop app's local MCP server for
[1Password Developer Environments](https://www.1password.dev/).

The extension lets Claude help with secure project environment setup — listing
Developer Environments, inspecting variable names, adding variables, and creating
local `.env` mounts. Secret values stay controlled by the 1Password desktop app
and are never shared with Claude.

> **Platform:** macOS only. The MCP server binary ships with the 1Password
> desktop app and runs entirely on your machine.

This is the Claude port of the
[1Password Kiro plugin](https://github.com/1Password/1password-kiro-plugin).

## Documentation

> ⚠️ **Placeholder URL** — points to a not-yet-published page while
> development continues:

https://www.1password.dev/environments/mcp-claude-server

## Prerequisites

- macOS with the [1Password desktop](https://1password.com/downloads) app installed.
- The **1Password Labs MCP Server** experiment enabled in the desktop app.
  Open the Labs settings with this link: `onepassword://settings/labs`
- A 1Password account with Developer Environments enabled.

The local MCP server is expected at:

```text
/Applications/1Password.app/Contents/MacOS/onepassword-mcp
```

## Install

This is a local MCP server packaged as an MCP Bundle (`.mcpb`). To build and
install:

```bash
npm install -g @anthropic-ai/mcpb   # one-time
mcpb validate manifest.json         # check the manifest
mcpb pack                           # produces 1password-developer-environments.mcpb
```

Then in Claude Desktop, open **Settings → Extensions → Install Extension…** and
select the generated `.mcpb` file. Follow the prompts to install.

## What's Included

| File                            | Purpose                                                                                              |
| ------------------------------- | ---------------------------------------------------------------------------------------------------- |
| [`manifest.json`](manifest.json) | MCPB manifest: server config pointing at the bundled `onepassword-mcp` binary, tool declarations, and prompts. |
| [`USAGE.md`](USAGE.md)          | Detailed reference for the exposed tools, common flows, error handling, and safety rules.            |
| [`LICENSE`](LICENSE)            | MIT license.                                                                                         |

## Example Prompts

Once the extension is installed and enabled in Claude, you can ask for tasks like:

- "List my 1Password Environments"
- "Create a local .env mount here"
- "Show me the variable names in my project environment"
- "Add a placeholder variable for my OpenAI API key"
- "Create a new Environment called my-project"

The extension also ships slash-command prompts: `mount-env`, `list-environments`,
and `inspect-variables`.

The 1Password desktop app may prompt for approval when Claude connects to the
MCP server or accesses an Environment.

## Privacy Policy

This extension runs the 1Password desktop app's local MCP server on your machine.
It does not collect, store, or transmit any data on its own.

- **What data is accessed:** Developer Environment names, variable names, and —
  only when you explicitly mount an Environment — the creation of local `.env`
  files. Secret *values* are handled by the 1Password desktop app and are never
  returned to or stored by Claude.
- **Where data goes:** All access is mediated locally by the 1Password desktop
  app. This extension adds no network transport, telemetry, or third-party
  sharing of its own. Your interactions with Claude are governed by
  [Anthropic's privacy policy](https://www.anthropic.com/legal/privacy).
- **Data retention:** This extension persists nothing. Local `.env` mounts and
  Environment data are managed by 1Password.
- **Authorization:** The 1Password desktop app prompts for your approval before
  the MCP server is reachable and may prompt again for sensitive operations.

1Password's handling of your data is governed by the
[1Password Privacy Policy](https://1password.com/legal/privacy).

## License and Support

This extension integrates with [1Password](https://1password.com).

- [Privacy Policy](https://1password.com/legal/privacy)
- [Support](https://support.1password.com)
- [MIT LICENSE](LICENSE)
