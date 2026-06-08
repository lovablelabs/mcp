# Lovable MCP

The official [Model Context Protocol](https://modelcontextprotocol.io/) server for [Lovable](https://lovable.dev), an AI-powered full-stack app builder.

Connect Claude, Cursor, Codex, and other MCP-compatible clients to Lovable so they can create, edit, deploy, and manage Lovable projects through natural language.


## Endpoint

```
https://mcp.lovable.dev
```

Transport: [Streamable HTTP](https://modelcontextprotocol.io/specification/2025-06-18/basic/transports#streamable-http).

## Authentication

**OAuth 2.1.** MCP clients discover the authorization server via RFC 9728 metadata at `https://mcp.lovable.dev/.well-known/oauth-protected-resource` and obtain a bearer token automatically. The token is passed as `Authorization: Bearer <token>`.

## Install

Claude, Claude Code, and ChatGPT need only the URL. Any other client must also pass Lovable's public OAuth client ID (`6d465f583e1e4ce5801b1616f735670c`), shown below.

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

### Cursor / Windsurf

Add to your client's MCP config (`~/.cursor/mcp.json`, or Windsurf's `mcp_config.json`):

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

### VS Code

```json
{
  "servers": {
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

### Codex CLI

```sh
codex mcp add lovable --url https://mcp.lovable.dev
codex mcp login lovable
```

### Other clients

Use the same `type: http` + `url` + `auth.CLIENT_ID` shape as the Cursor example above.

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
| OAuth window loops or never completes | Confirm the client passes the `auth.CLIENT_ID` shown above |
| "Transport not supported" | The client must support Streamable HTTP |
| Tool fails on a missing `workspace_id` | Call `list_workspaces` first and pass the returned ID |

## Registry

This repository hosts the [`server.json`](./server.json) entry for the [MCP registry](https://registry.modelcontextprotocol.io/).

## License

[Apache License 2.0](./LICENSE)
