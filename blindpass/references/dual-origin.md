# Dual origin

OAuth discovery (`/.well-known`) lives only on `https://api.blindpass.app`.

PAT lives only on `https://api-pat.blindpass.app`. That host 404s OAuth metadata. A PAT sent to the OAuth host still gets Authenticate.

Laptop: edit `url` in `~/.cursor/plugins/local/blindpass/.cursor-plugin/mcp.json` (and `mcp.json`) to `http://127.0.0.1:18084/mcp` and `http://api-pat.blindpass.localhost:18084/mcp`. Do not use Cursor Configure for the host.

Clients that load every `mcpServers` entry: leave `BLINDPASS_PAT` empty so the PAT URL 401s closed. Team connectors must use per-user OAuth, not a shared PAT.
