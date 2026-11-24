# Tool map

| Tool | Result | Notes |
|------|--------|--------|
| `get_version` | `{ version }` | Requires PAT or MCP OAuth |
| `list_items` | id, title, username, urls, type, collection_ids, favorite | Metadata only; `collection_ids` lists every collection of this team the item is in (empty = private); filter by `query`, `url`, `collection` (id: items in it; `private`: items in none) |
| `fill_login` | `{ ok, status: "waiting_for_approval", grant_id, item_id }` | The person approves in the BlindPass browser extension, which fills the open tab; poll `fill_status` |
| `submit_totp` | `{ ok, status: "waiting_for_approval", grant_id, item_id }` | Same as `fill_login`; the extension fills the one-time-code field; code never returned |
| `inject_env` | `{ ok }` | CLI/local executor |
| `request_approval` | `{ pending, approval_id }` | Lookalike / policy prompt |
| `get_secret` | `{ item_id, type, fields, display, handling }` or `{ item_id, field, value, display, handling }`, or `{ ok: false, error: "use_cli", item_id, command, hint }` | Every OAuth connection may call it, with no box at connect (scope `mcp:secrets` is implied; never on a PAT). Only items in the collections the person chose when connecting this app; others (and private items) are `not_granted`. The value comes from a copy the person's browser encrypts for this app when they allow the read; if the item changes after the Allow, the next read asks again. `display: "never"`: use, never show; forget after use. Every read is audited. No value without the person's Allow. `use_cli`: run `command` with your real command after `--` (the person uses the CLI, or the connection requires it). On "CLI first, otherwise ask me", a `fallback_reason` (1–300 characters, only when you cannot run commands), or a call when the person has no CLI in use, answers `{ ok: false, status: "waiting_for_approval", request_id, item_id, hint }`: the person allows or declines the read in the BlindPass extension or web app; call again with the same `item_id`, `field` and `request_id` (pending: `waiting_for_approval` again; allowed: the value once, audited as `secret.read.fallback` with the reason; then `used`; `declined`; `expired` after 5 minutes; another item, field or connection: `invalid_request`). For one hour after an Allow, new calls for the same item and `field` return the value directly, each audited the same way. An unknown `field` in an allowed copy is `unknown_field`. Never resend a new reason after a decline. Ignored on "Only through the CLI"; longer is `invalid_request` |
| `fill_status` | `{ grant_id, status }` | `waiting_for_approval`, `filled`, `declined` or `expired`; only the grant's person |
| `rename_item` | `{ ok, item }` (metadata) | Scope `mcp:rename` (every OAuth connection has it) or `mcp:organize`; title only, needs write access to the item |
| `update_item` | `{ ok, item }` (metadata) | Scope `mcp:organize`; `username`, `urls`, `favorite` only |
| `list_collections` | `{ collections: [id, name, item_count], private_item_count }` | No scope; an item in several collections counts in each; `private_item_count` = items in none |
| `rename_collection` | `{ ok, collection }` | Scope `mcp:organize`; admin access to the collection |
| `delete_collection` | `{ ok }` or `collection_not_empty` | Scope `mcp:organize`; Owner/Admin; empty collections only |
| `find_duplicates` | `{ groups: [site, username, items] }` | No scope; passwords are not compared |
| `list_activity` | `{ events: [action, detail, actor_email, created_at] }` | Owner/Admin; newest first, `action` filter, `limit` ≤ 200 |
| `move_item` | `{ ok, item }` (metadata) | Scope `mcp:organize`; leaves the item in exactly `collection_id`, out of its others, or `"private"` (owner only); needs write access to the item, the target and every collection it leaves |
| `add_item_to_collection` | `{ ok, item }` (metadata) | Scope `mcp:organize`; `item_id`, `collection_id`; keeps its other collections; needs write access to the item and the collection; already there changes nothing |
| `remove_item_from_collection` | `{ ok, item }` (metadata) | Scope `mcp:organize`; `item_id`, `collection_id`; needs write access to the item and the collection; its last collection only by the item's owner (`forbidden` otherwise), which makes it private |
| `create_collection` | `{ ok, collection, existing }` | Scope `mcp:organize`; Owner/Admin; reuses a same-named collection |
| `delete_item` | `{ ok, deleted }` (metadata) | Scope `mcp:delete`; permanent, needs write access to the item |

Forbidden: `get_password`, `export_item`. Only `get_secret` returns secrets, and only from what the person granted.

CLI companions (run by the person, or by you inside a shell): `blindpass run --item <id> --env NAME=field -- <command>` delivers a secret to one command; `blindpass unlock [--minutes N]` keeps one Keychain approval for N minutes (default 60, at most 720); `blindpass lock` ends it; `blindpass status` shows `locked` or `unlocked until …`.
