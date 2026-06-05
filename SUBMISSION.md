# Directory Submission Readiness

Tracking against the
[Claude connector submission requirements](https://claude.com/docs/connectors/building/submission).
This extension is a **local connector / desktop extension (`.mcpb`)**, so it uses
the desktop extension submission form (not the remote MCP form).

## Done

- [x] `manifest.json` at version 0.3 (>= 0.2) with a `privacy_policies` array of HTTPS URLs.
- [x] README includes a **Privacy Policy** section covering data accessed, where it
      goes, retention, authorization, and 1Password's policy link.
- [x] Tool declarations with descriptions for all eight tools.
- [x] MIT `LICENSE`.

## Remaining before submission

- [ ] **Tool annotations** (`title`, plus `readOnlyHint` / `destructiveHint`). These
      are emitted by the 1Password `onepassword-mcp` binary at runtime, not by this
      manifest. Confirm the binary sets them; the read-only tools (`list_environments`,
      `list_variables`, `list_local_env_files`) should carry `readOnlyHint`, and
      `create_*` / `append_*` / `rename_*` are write operations.
- [ ] **Icon** — add a 512×512 PNG (`icon.png`) and reference it via the manifest
      `icon` field. Recommended for directory listings.
- [ ] **Publish the documentation URL** — the `documentation` field and README
      currently point at a placeholder (`mcp-claude-server`).
- [ ] **Package & validate** — `mcpb validate manifest.json` then `mcpb pack`, and
      verify one-click install in Claude Desktop on macOS.
- [ ] **Security review** — confirm compliance with Anthropic's security standards
      for local connectors.
- [ ] Decide repo visibility for submission (currently private).

## Notes

- The MCP server binary ships with the 1Password desktop app at
  `/Applications/1Password.app/Contents/MacOS/onepassword-mcp`; it is **not** bundled
  inside the `.mcpb`. Installation therefore depends on the 1Password desktop app
  being present with the **1Password Labs MCP Server** experiment enabled.
- macOS only (`compatibility.platforms: ["darwin"]`).
