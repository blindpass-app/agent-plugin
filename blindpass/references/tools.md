# Tool map

| Tool | Result | Notes |
|------|--------|--------|
| `get_version` | `{ version }` | Requires PAT or MCP OAuth |
| `list_items` | id, title, username, urls, type, collection_id, favorite | Metadata only; filter by `query`, `url`, `collection` (id or `private`) |
| `fill_login` | `{ ok, status: "waiting_for_approval", grant_id, item_id }` | The person approves in the BlindPass browser extension, which fills the open tab; poll `fill_status` |
| `submit_totp` | `{ ok, status: "waiting_for_approval", grant_id, item_id }` | Same as `fill_login`; the extension fills the one-time-code field; code never returned |
| `inject_env` | `{ ok }` | CLI/local executor |
| `request_approval` | `{ pending, approval_id }` | Lookalike / policy prompt |
| `get_secret` | `{ item_id, type, fields, display, handling }` or `{ item_id, field, value, display, handling }`, or `{ ok: false, error: "use_cli", item_id, command, hint }` | Scope `mcp:secrets` ("Read secrets", the person ticks it at connect). Only items in the granted collections; `not_granted`, `stale_copy` (retry later). `display: "never"`: use, never show; forget after use. Every read is audited. `use_cli`: run `command` with your real command after `--` (the connection prefers or requires the CLI). `fallback_reason` (1–300 characters, only when you cannot run commands) returns the value on a "Prefer the CLI" connection, audited as `secret.read.fallback` with the reason; ignored on "Only through the CLI"; longer is `invalid_request` |
| `fill_status` | `{ grant_id, status }` | `waiting_for_approval`, `filled`, `declined` or `expired`; only the grant's person |
| `rename_item` | `{ ok, item }` (metadata) | Scope `mcp:organize`; title only, needs write access to the item |
| `update_item` | `{ ok, item }` (metadata) | Scope `mcp:organize`; `username`, `urls`, `favorite` only |
| `list_collections` | `{ collections: [id, name, item_count], private_item_count }` | No scope |
| `rename_collection` | `{ ok, collection }` | Scope `mcp:organize`; admin access to the collection |
| `delete_collection` | `{ ok }` or `collection_not_empty` | Scope `mcp:organize`; Owner/Admin; empty collections only |
| `find_duplicates` | `{ groups: [site, username, items] }` | No scope; passwords are not compared |
| `list_activity` | `{ events: [action, detail, actor_email, created_at] }` | Owner/Admin; newest first, `action` filter, `limit` ≤ 200 |
| `move_item` | `{ ok, item }` (metadata) | Scope `mcp:organize`; `collection_id` or `"private"` (owner only); needs write access to item and target |
| `create_collection` | `{ ok, collection, existing }` | Scope `mcp:organize`; Owner/Admin; reuses a same-named collection |
| `delete_item` | `{ ok, deleted }` (metadata) | Scope `mcp:delete`; permanent, needs write access to the item |

Forbidden: `get_password`, `export_item`. Only `get_secret` returns secrets, and only from what the person granted.

CLI companions (run by the person, or by you inside a shell): `blindpass run --item <id> --env NAME=field -- <command>` delivers a secret to one command; `blindpass unlock [--minutes N]` keeps one Keychain approval for N minutes (default 60, at most 720); `blindpass lock` ends it; `blindpass status` shows `locked` or `unlocked until …`.
