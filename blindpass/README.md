# BlindPass Agent Plugin

Install this package in an Agent Plugins 1.0 client. Hosted MCP is backend infrastructure this plugin points at — not a standalone MCP product.

**Enable one server:**

- **OAuth:** `blindpass` → `https://api.blindpass.app/mcp` (no headers). Connect as yourself.
- **PAT:** `blindpass-pat` → `https://api.blindpass.app` is the wrong host. Use `https://api-pat.blindpass.app/mcp` with `Authorization: Bearer ${BLINDPASS_PAT}`.

Do not put a shared PAT on a team-wide connector. First-time Connect may sign up in the authorize tab (stay on that tab for the email code).

## Install

Published in the Maitalky catalog, <https://gitlab.com/maitalky/plugins>.

**Claude Code** (OAuth server only; run `/mcp` → `blindpass` → Authenticate afterwards):

```
/plugin marketplace add https://gitlab.com/maitalky/plugins.git
/plugin install blindpass@maitalky
```

For a PAT in Claude Code, add the PAT host yourself instead of the plugin's server:

```
claude mcp add --transport http blindpass-pat https://api-pat.blindpass.app/mcp --header 'Authorization: Bearer ${BLINDPASS_PAT}'
```

**Cursor:** Import Marketplace with `https://gitlab.com/maitalky/plugins`, install `blindpass`, then Connect or set `BLINDPASS_PAT`.

Releases: bump `version` in `plugin.json`, `.claude-plugin/plugin.json` and `.cursor-plugin/plugin.json`, then `just plugin-publish --push` opens the catalog merge request.

Privacy: <https://blindpass.app/legal/privacy>

## Laptop (`just be`)

Cursor’s **Local** install is a copy of this directory at `~/.cursor/plugins/local/blindpass`. It does not follow git. Change MCP hosts by editing **files**, not the Configure UI:

- `~/.cursor/plugins/local/blindpass/.cursor-plugin/mcp.json` (what Cursor loads)
- `~/.cursor/plugins/local/blindpass/mcp.json` (keep in sync)

Laptop URLs: `http://127.0.0.1:18084/mcp` (OAuth) and `http://api-pat.blindpass.localhost:18084/mcp` (PAT, so `Host` is `MCP_PAT_HOST`). Then Reload. `cp -R agent-plugin ~/.cursor/plugins/local/blindpass` overwrites those edits with prod URLs from git.

**Claude Code** in this repo reads the root `.mcp.json`: `blindpass-dev` → `https://127.0.0.1:18443/mcp` (OAuth) and `blindpass-dev-pat` → `http://api-pat.blindpass.localhost:18084/mcp` with `Authorization: Bearer ${BLINDPASS_DEV_PAT:-}` (export a laptop PAT as `BLINDPASS_DEV_PAT`). The Claude desktop app refuses `http` sign-in URLs, so the OAuth server goes through the backend's optional HTTPS listener, whose discovery and 401 challenge answer with `https` URLs:

1. `just dev-tls` (needs `mkcert`; trusts its CA once) writes `.dev-tls/cert.pem` and `key.pem` (gitignored).
2. In `.env`: `LOCAL_TLS_CERT` and `LOCAL_TLS_KEY` pointing at them (`LOCAL_TLS_PORT` defaults to `18443`, `LOCAL_TLS_PUBLIC_URL` to `https://127.0.0.1:<port>`). Set both files or neither: a half or unreadable setup stops `just be`.
3. Claude Code does not read the macOS keychain, so it needs `NODE_EXTRA_CA_CERTS="$(mkcert -CAROOT)/rootCA.pem"`. CLI: export it in `~/.zshenv` and restart `claude`. Desktop app started from the Dock: `launchctl setenv NODE_EXTRA_CA_CERTS "$(mkcert -CAROOT)/rootCA.pem"` (lost on reboot), then quit the app completely and reopen it.
4. `/mcp` → `blindpass-dev` → Authenticate. The `claude` CLI also accepts the loopback `http://127.0.0.1:18084/mcp`, so a terminal sign-in works without the certificate.

No stdio MCP.
