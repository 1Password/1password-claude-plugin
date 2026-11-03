# Changelog

All notable changes to this plugin are documented here. A new entry is added when you push a release branch; see [Releasing](README.md#releasing).

## [0.2.2] - 2026-11-03

### Fixes
- The skill now uses the 1Password MCP server's renamed tools, `list_secrets` and `append_secrets`, and says "secrets" instead of "variables". (#20)
- Added a setup step for macOS 27 Golden Gate, where macOS blocks access to 1Password app data until you allow it in System Settings. (#23)

## [0.2.1] - 2026-10-27

### Features
- Renamed the plugin's display name from "1Password" to "1Password Developer Tools". The plugin ID stays `1password`, so existing installs keep working.
- The plugin now sets `OP_MCP_SOURCE=plugin` when it starts the 1Password MCP server.
