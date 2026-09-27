# Lovable MCP

The official [Model Context Protocol](https://modelcontextprotocol.io/) server for [Lovable](https://lovable.dev), an AI-powered full-stack app builder.

Connect Claude, Cursor, Codex, and other MCP-compatible clients to Lovable so they can create, edit, deploy, and manage Lovable projects through natural language.


## Endpoint

```
https://mcp.lovable.dev
```

Transport: [Streamable HTTP](https://modelcontextprotocol.io/specification/2025-06-18/basic/transports#streamable-http).

## Authentication

**OAuth 2.1 — no keys or client IDs to copy.** On first connection your client:

1. **Discovers** the authorization server from the RFC 9728 metadata at `https://mcp.lovable.dev/.well-known/oauth-protected-resource`, which points at `https://lovable.dev/oauth`.
2. **Registers itself** with [Dynamic Client Registration](https://www.rfc-editor.org/rfc/rfc7591) (RFC 7591) at `https://lovable.dev/oauth/register` — the `registration_endpoint` advertised in `https://lovable.dev/oauth/.well-known/oauth-authorization-server`. Clients that publish a [Client ID Metadata Document](https://datatracker.ietf.org/doc/draft-ietf-oauth-client-id-metadata-document/) (Claude, Claude Code, VS Code) use its URL as their `client_id` and skip this step.
3. **Signs you in**: a browser window opens so you can log in to Lovable and approve the requested scopes (`projects:read`, `projects:write`, `workspaces:read`, `workspaces:write`, `offline`). The client exchanges the authorization code (PKCE `S256`) for a token and sends it as `Authorization: Bearer <token>`.

Registration is open, with one rule: every `redirect_uris` entry must be a loopback address (`localhost`, `127.0.0.1` or `[::1]` — http or https, any port and path) or an allowlisted HTTPS host. Anything else is rejected with `invalid_redirect_uri`. Registration is rate-limited to 5 requests per minute per IP. A custom client whose OAuth callback lives on a public domain — a hosted agent or a server-side integration — needs its callback host or its Client ID Metadata Document domain allowlisted: [contact support](https://lovable.dev/support).

## Install

Every client below needs only the server URL and registers itself on first use. Cursor is the one exception: its OAuth callback (`https://www.cursor.com/agents/mcp/oauth/callback`) is neither loopback nor allowlisted, so it uses Lovable's pre-registered public client ID (`6d465f583e1e4ce5801b1616f735670c`) instead.

### Claude Code

```sh
claude mcp add --transport http lovable https://mcp.lovable.dev
```

### Claude Desktop

Add to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "lovable": {
      "type": "http",
      "url": "https://mcp.lovable.dev"
    }
  }
}
```

### ChatGPT

Add the URL directly — no client ID needed:

```
https://mcp.lovable.dev
```

### Cursor

Cursor's OAuth callback is neither loopback nor allowlisted (see [Authentication](#authentication)), so it needs Lovable's pre-registered client ID. Add to `~/.cursor/mcp.json` (or `.cursor/mcp.json` in a project):

```json
{
  "mcpServers": {
    "lovable": {
      "type": "http",
      "url": "https://mcp.lovable.dev",
      "auth": {
        "CLIENT_ID": "6d465f583e1e4ce5801b1616f735670c"
      }
    }
  }
}
```

### Windsurf

Add to `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "lovable": {
      "serverUrl": "https://mcp.lovable.dev"
    }
  }
}
```

### VS Code

```json
{
  "servers": {
    "lovable": {
      "type": "http",
      "url": "https://mcp.lovable.dev"
    }
  }
}
```

### Codex CLI

```sh
codex mcp add lovable --url https://mcp.lovable.dev
codex mcp login lovable
```

### Other clients

Any MCP client that supports OAuth with a loopback callback (OpenCode, Claude Desktop, …) works with `type: http` + `url` alone, as do hosted agents whose callback host is already allowlisted (Devin, Grok, Antigravity). If your client's registration is rejected with `invalid_redirect_uri`, see [Authentication](#authentication).

## Tools

The server exposes tools across these areas:

- **Projects & workspaces** — list, create, deploy, remix, inspect; manage visibility
- **Agent interaction** — send messages to a project's AI agent and retrieve responses
- **Code inspection** — diffs, file tree, file contents, edit history
- **Knowledge** — read and write project / workspace AI instructions
- **Cloud database** — enable a Postgres database, check status, run SQL, get connection info
- **Connectors & MCP servers** — list, add, and remove external integrations (Linear, Notion, Slack, custom MCP, …)
- **Templates & libraries** — browse template and library projects
- **Analytics** — historical and real-time project traffic
- **File uploads** — generate upload URLs for attaching images

> **Heads up:** `create_project` and `send_message` consume Lovable build credits. `deploy_project` publishes a public URL. `query_database` runs SQL directly against your project's database.

See the [Lovable MCP documentation](https://docs.lovable.dev/integrations/lovable-mcp-server#lovable-mcp-server-research-preview) for the full tool reference.

## Common workflow

Build and deploy a new app:

```
1. list_workspaces()                       → workspace_id
2. create_project(workspace_id, ...)       → project_id   (uses credits)
3. send_message(project_id, "Add ...")     → message_id   (uses credits)
4. get_diff(project_id, message_id)        → review changes
5. deploy_project(project_id)              → live URL
```

Most tools need a `workspace_id` — call `list_workspaces` first. `read_file` needs a git ref; get `latest_commit_sha` from `get_project`.

## Troubleshooting

| Symptom | Fix |
|---|---|
| `401 Unauthorized` / expired token | Re-run your client's login flow to refresh the OAuth token |
| `invalid_redirect_uri` during registration | The client's OAuth callback isn't loopback or allowlisted — see [Authentication](#authentication). In Cursor, set `auth.CLIENT_ID` as shown under [Cursor](#cursor); for a hosted or custom client, [contact support](https://lovable.dev/support) |
| `429 Too Many Requests` from `/oauth/register` | Registration is limited to 5 per minute per IP — wait a minute and retry |
| OAuth window loops or never completes | Sign in at lovable.dev first, then retry. In Cursor, confirm `auth.CLIENT_ID` is set |
| "Transport not supported" | The client must support Streamable HTTP |
| Tool fails on a missing `workspace_id` | Call `list_workspaces` first and pass the returned ID |

## Registry

This repository hosts the [`server.json`](./server.json) entry for the [MCP registry](https://registry.modelcontextprotocol.io/).

## License

[Apache License 2.0](./LICENSE)
