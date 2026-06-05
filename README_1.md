# Lovable MCP Server

The official [Model Context Protocol](https://modelcontextprotocol.io/) server for [Lovable](https://lovable.dev) — an AI-powered full-stack app builder.

Connect Claude, Cursor, VS Code, ChatGPT, and other MCP clients to Lovable so they can create, iterate on, deploy, and manage Lovable projects through natural language.

## Endpoint

```
https://mcp.lovable.dev
```

Transport: [Streamable HTTP](https://modelcontextprotocol.io/specification/2025-06-18/basic/transports#streamable-http)

## Authentication

**OAuth 2.1.** Clients discover the authorization server via RFC 9728 metadata at `https://mcp.lovable.dev/.well-known/oauth-protected-resource` and obtain a bearer token automatically. The token is passed as `Authorization: Bearer <token>`.

## Supported clients

| Client | Connect method |
|---|---|
| Claude (Desktop / claude.ai) | Settings → Connectors → Add custom connector |
| Claude Code | `claude mcp add --transport http lovable https://mcp.lovable.dev` |
| ChatGPT | Add URL directly — no client ID needed |
| Cursor | Config file — requires client ID (see below) |
| VS Code | MCP settings |

## Quick start

### Claude Code

```bash
claude mcp add --transport http lovable https://mcp.lovable.dev
```

### Claude Desktop

Add to `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

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

### Cursor / Windsurf / other clients

Clients other than Claude, Claude Code, and ChatGPT must include Lovable's public OAuth client ID:

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
      "url": "https://mcp.lovable.dev"
    }
  }
}
```

## Requirements

- A Lovable account on the **Pro or Business plan**
- One of the supported clients listed above

## Tools

### Projects and workspaces

| Tool | Description |
|---|---|
| `get_me` | Get the authenticated user's profile and workspaces |
| `list_workspaces` | List all workspaces the user belongs to |
| `get_workspace` | Get details for a specific workspace |
| `list_projects` | Search and list projects in a workspace |
| `get_project` | Get project details: editor URL, preview URL, screenshot |
| `create_project` | Create a new project, optionally with an initial build prompt |
| `deploy_project` | Publish a project and get the live URL |
| `remix_project` | Copy an existing project into a workspace |
| `set_project_visibility` | Set who can access the project in the editor |
| `set_folder_visibility` | Set a folder to personal or workspace visibility |
| `list_template_projects` | List available project templates |
| `list_library_projects` | List design system library projects in a workspace |

### Agent interaction

| Tool | Description |
|---|---|
| `send_message` | Send a chat message to a project's AI agent and wait for the response |
| `get_message` | Poll for message completion status after `send_message` with `wait=false` |

### Code inspection

| Tool | Description |
|---|---|
| `get_diff` | Get the unified diff from a message or commit SHA |
| `list_files` | List all files in a project at a specific git ref |
| `read_file` | Read the contents of a single file at a specific git ref |
| `list_edits` | List the edit history of a project with pagination |

### Knowledge

| Tool | Description |
|---|---|
| `get_workspace_knowledge` | Get the workspace's custom AI instructions |
| `set_workspace_knowledge` | Set the workspace's custom AI instructions |
| `get_project_knowledge` | Get a project's custom AI instructions |
| `set_project_knowledge` | Set a project's custom AI instructions |

### Cloud database

| Tool | Description |
|---|---|
| `get_database_status` | Check whether Lovable Cloud database is enabled for a project |
| `enable_database` | Enable Lovable Cloud on a project (takes 30–60 seconds) |
| `query_database` | Execute a SQL query on a Lovable Cloud project |
| `get_database_connection_info` | Get the connection string and API URL for a Lovable Cloud project |

### Connected MCP servers

| Tool | Description |
|---|---|
| `list_mcp_servers` | List all MCP servers connected to a workspace |
| `list_mcp_catalog` | Browse featured MCP server templates available to add |
| `list_connectors` | List available connector integrations |
| `list_connections` | List authenticated connector accounts for a workspace |
| `add_mcp_server` | Add an MCP server (catalog or custom) to a workspace |
| `remove_mcp_server` | Remove an MCP server from a workspace |

### Analytics

| Tool | Description |
|---|---|
| `get_project_analytics` | Get historical analytics for a published project |
| `get_project_analytics_trend` | Get real-time visitor count and 5-minute trend |

### File uploads

| Tool | Description |
|---|---|
| `get_file_upload_url` | Get a presigned URL to upload a file for use as a message attachment |

## Common workflows

### Build and deploy a new app

```
1. list_workspaces()           → get workspace_id
2. create_project(             → get project_id and preview_url
     workspace_id,
     description: "My app",
     initial_message: "Build a..."
   )
3. send_message(project_id, "Add X feature")
4. get_diff(project_id, message_id)   → review changes
5. deploy_project(project_id)         → get live URL
```

### Inspect an existing project

```
1. get_project(project_id)            → get latest_commit_sha
2. list_files(project_id, ref)        → explore structure
3. read_file(project_id, ref, path)   → read any file
4. list_edits(project_id)             → review history
```

## Full documentation

See the [Lovable MCP server documentation](https://docs.lovable.dev/integrations/lovable-mcp-server) for the complete tool reference, OAuth flow details, troubleshooting, and a copy-paste skill file for agent clients.

## Registry

This repository hosts the [`server.json`](./server.json) entry for the [official MCP registry](https://registry.modelcontextprotocol.io/).

## License

[Apache License 2.0](./LICENSE)
