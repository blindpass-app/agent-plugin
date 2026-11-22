# BlindPass Agent Plugin

MCP server + skills so an agent can sign in to sites, submit 2FA codes and run commands with secrets from your [BlindPass](https://blindpass.app) vault — without the model ever seeing a password. Every use is approved by you and logged.

## Install

**Claude Code / Claude desktop**

```
/plugin marketplace add https://github.com/blindpass-app/agent-plugin
/plugin install blindpass@blindpass
```

Then run `/mcp` and connect the `blindpass` server (OAuth). Optional: a PAT (`bp_pat_…` from BlindPass → Org) as `BLINDPASS_PAT` for the PAT server instead. Enable **one** server.

**Cursor**

Run `/add-plugin https://github.com/blindpass-app/agent-plugin` (or Add Marketplace with that URL), install **blindpass**, then Connect — or set `BLINDPASS_PAT`.

## What is in here

- `blindpass/` — the plugin: MCP servers (`api.blindpass.app` OAuth, `api-pat.blindpass.app` PAT), the `blindpass` skill and references. Details in [`blindpass/README.md`](blindpass/README.md).
- `.claude-plugin/` and `.cursor-plugin/` — marketplace manifests.

This repository is a published mirror; changes land in BlindPass first and are copied here with each plugin release.

MIT licensed.
