# Changelog

All notable changes to this plugin are documented here. A new entry is added when you push a release branch; see [Releasing](README.md#releasing).

## [0.2.2] - 2026-11-03

### Features
-

### Fixes
-

### Security
-

<!--
PRs merged since the last release (delete this comment when done):
* Repo cleanup (#1)
* Add validate mounted env hook (#2)
* Add the agent skill and MCP configuration (#4)
* Update docs and shorten plugin description (#5)
* Remove hook to stop repeated macOS access prompts (#10)
* docs: update MCP setup to use Developer settings (#14)
* Update install steps (#15)
* Add release workflow and changelog (#12)
* Rename Environment "variables" to "secrets" (#13)
* Apply GitHub Actions security best practices (#17)
* Revert "Update variables to secrets (#13)" (#19)
* Apply GitHub Actions security best practices (#18)
* feat(mcp): tag MCP connections made through the plugin (#21)
* Rename plugin display name to 1Password Developer Tools (#22)
* Add macOS 27 MCP permission step to README and reference (#23)
* Rename Environment "variables" to "secrets" (#20)
-->

## [0.2.1] - 2026-10-27

### Features
- Renamed the plugin's display name from "1Password" to "1Password Developer Tools". The plugin ID stays `1password`, so existing installs keep working.
- The plugin now sets `OP_MCP_SOURCE=plugin` when it starts the 1Password MCP server.
