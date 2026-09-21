# Lovable plugin for Claude

Build, run and grow real apps from Claude. This plugin bundles the official [Lovable MCP server](https://mcp.lovable.dev) with 13 skills and three commands, for Claude, Claude Code and Cowork.

Claude does the thinking in the conversation. Lovable builds the app, hosts it, gives it a database and a URL. The skills in this plugin connect the two so an idea, a spreadsheet or a Claude Design ends up as something that exists and keeps running, and so the apps a person already has can be read, fixed and reported on without leaving Claude.

## Install

**Claude and Cowork.** Open the Customize menu, browse plugins, choose Lovable. Or install from [claude.com/plugins](https://claude.com/plugins/). The first Lovable tool call opens a sign-in window.

**Claude Code.**

```sh
/plugin install lovable@claude-plugins-official
```

Or from this repository directly:

```sh
/plugin marketplace add lovablelabs/mcp
/plugin install lovable@lovable
```

Restart Claude Code after installing. Verify with `/mcp` (look for `lovable`) or ask Claude to list your Lovable workspaces.

## Skills

Skills run when your request matches, or directly by name (for example `/lovable:launch`). Every skill reads the workspace's business profile first, asks before spending credits, reports what each call cost, and stops before publishing. The skills were run end to end against the live Lovable MCP before this release; what that changed is in [CHANGELOG.md](CHANGELOG.md) and [CONNECTORS.md](CONNECTORS.md).

### Start here

| Skill | What you say | What you get |
|---|---|---|
| `know-my-business` | "Set me up", "tell Lovable about my company" | A short business profile written into workspace knowledge. Every other skill reads it instead of asking again. Spends nothing. |

### Get the work done

| Skill | What you say | What you get |
|---|---|---|
| `launch` | "We're launching Tuesday" | A launch page and changelog on your site, live, plus three posts pointing at it. Copy is written and edited in Claude before anything is built. |
| `first-customers` | "Find 200 companies like our best customer" | A CRM you own, filled from the tools connected in Claude, every row with its source. |
| `customer-research` | "Talk to ten customers and tell me what they said" | An interview guide, a tracker for every answer, and a summary that quotes people. |
| `hiring-tracker` | "We're hiring. Track candidates." | A pipeline with stages and a scorecard every interviewer fills the same way. Sign-in required. |

### For apps you already have on Lovable

| Skill | What you say | What you get |
|---|---|---|
| `ask-my-app-data` | "How many signed up this week?" | The answer from the app's live database, with the query shown. Covers weekly numbers and the investor update. Read-only. |
| `review-my-workspace` | "Check every project for exposed keys and missing RLS" | A ranked table of what needs attention, with the file or table as evidence, for a human to act on. Read-only. |
| `fix-and-ship` | "Our storage bill tripled. Find out why and fix it." | Root cause with evidence, a plan, the fix applied, and live once you approve. Rollback commit included. |
| `use-my-project-knowledge` | "Write the brief for this app" | A brief, spec, README or handover grounded in the code and schema, and the project knowledge kept current. |

### New to Lovable

| Skill | What you say | What you get |
|---|---|---|
| `build-from-this-conversation` | "Make this real" | The idea already worked out in Claude becomes a live app with sign-in, a database and a URL. |
| `find-what-to-build` | "Look at my clients and what I do every week" | The repeatable work found in your calendar, inbox and sheets, three tools proposed, one built if you pick it. |
| `spreadsheet-to-app` | "This sheet runs the business. Make it an app." | The sheet as an app, its formulas as rules, its rows in a real database, counts verified. |
| `claude-design-to-app` | "Turn this Claude Design into the real thing" | The design imported as is, with data, sign-in and actions added behind it. |

## Commands

Three commands for people who want the direct route. They predate the skills and stay as they are.

| Command | What it does |
|---|---|
| `/lovable:build <description>` | Create a project from a prompt, follow the build, optionally deploy |
| `/lovable:iterate <project> — <change>` | Send a change request, plan first for big ones, review the diff |
| `/lovable:db <project> [SQL]` | Check or enable a project's database and run SQL with confirmation |

## Example workflows

**From an idea in Claude to a URL**

```
Make this real
```

`build-from-this-conversation` writes the brief from the conversation, you confirm, Lovable builds it in one call, Claude checks each promised feature exists, one follow-up closes gaps, and it publishes when you say so.

**Monday numbers**

```
How many people signed up last week, and how does that compare to the week before?
```

`ask-my-app-data` reads the schema, writes the SELECT, shows it, runs it for both weeks. Nothing is written and nothing is spent.

**Something broke**

```
Our storage bill tripled this month. Find out why and fix it.
```

`fix-and-ship` reads the edit history and the database sizes, names the commit and the table, proposes the fix, applies it in plan-then-apply with a yes for each, and deploys on a third yes.

## Credits and safety

Two tools spend Lovable credits: `create_project` and `send_message`. Everything else is free. Every skill says what it is about to build and waits for a yes before either call, one yes per call. Every skill stops before `deploy_project`, before any database write, and before creating a second project. `deploy_project` publishes a public URL on Free and Pro plans; apps that hold personal data are built with sign-in required. See [CONNECTORS.md](CONNECTORS.md) for the full table.

## Two things are called Lovable

This plugin's MCP server builds and manages projects. A published Lovable app can also expose its own MCP server for the data inside it; that is a separate connector you add in Claude from the app's Agent integrations menu. The skills name which one they mean and never assume an app's own server is connected.

## Works in

Claude (web and desktop), Cowork, and Claude Code. Skills that need to upload a file (`spreadsheet-to-app`) do so directly in Claude Code and Cowork; in Claude chat they pass the sheet's structure and sample rows and ask you to drop the file into the Lovable editor for the rest. The skill says which path it used.

## For admins

Enterprise workspaces enable third-party MCP clients under Settings, Security, Privacy and security before anyone can sign in from Claude. The server acts with each person's own Lovable permissions. Leave `deploy_project` and `query_database` on "ask" in Claude's connector settings to make the skills' own stops enforced.

---

## MCP server reference

### Endpoint

```
https://mcp.lovable.dev
```

Transport: [Streamable HTTP](https://modelcontextprotocol.io/specification/2025-06-18/basic/transports#streamable-http).

### Authentication

**OAuth 2.1.** MCP clients discover the authorization server via RFC 9728 metadata at `https://mcp.lovable.dev/.well-known/oauth-protected-resource` and obtain a bearer token automatically. The token is passed as `Authorization: Bearer <token>`. Connecting grants access to your whole Lovable account, not a single project.

### Other clients

Claude, Claude Code and ChatGPT need only the URL. Any other client must also pass Lovable's public OAuth client ID (`6d465f583e1e4ce5801b1616f735670c`).

Claude Desktop config file:

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

Cursor / Windsurf (`~/.cursor/mcp.json`, or Windsurf's `mcp_config.json`):

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

VS Code:

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

Codex CLI:

```sh
codex mcp add lovable --url https://mcp.lovable.dev
codex mcp login lovable
```

ChatGPT: add the URL directly, no client ID needed.

### Tools

- **Projects and workspaces**: list, create, deploy, remix, inspect; manage visibility
- **Agent interaction**: send messages to a project's AI agent and retrieve responses
- **Code inspection**: diffs, file tree, file contents, edit history
- **Knowledge**: read and write project and workspace AI instructions
- **Cloud database**: enable a Postgres database, check status, run SQL, get connection info
- **Connectors and MCP servers**: list, add and remove external integrations
- **Templates and libraries**: browse template and library projects
- **Analytics**: historical and real-time project traffic
- **File uploads**: generate upload URLs for attaching files

`create_project` and `send_message` consume Lovable build credits. `deploy_project` publishes a public URL. `query_database` runs SQL directly against your project's database.

Full tool reference: [Lovable MCP documentation](https://docs.lovable.dev/integrations/lovable-mcp-server).

### Common workflow

```
1. list_workspaces()                       → workspace_id
2. create_project(workspace_id, ...)       → project_id   (uses credits)
3. send_message(project_id, "Add ...")     → message_id   (uses credits)
4. get_diff(project_id, message_id)        → review changes
5. deploy_project(project_id)              → live URL
```

Most tools need a `workspace_id`; call `list_workspaces` first. `read_file` takes a git ref; get `latest_commit_sha` from `get_project`.

### Troubleshooting

| Symptom | Fix |
|---|---|
| `401 Unauthorized` or expired token | Re-run your client's login flow to refresh the OAuth token |
| OAuth window loops or never completes | Confirm the client passes the `auth.CLIENT_ID` shown above |
| "Transport not supported" | The client must support Streamable HTTP |
| Tool fails on a missing `workspace_id` | Call `list_workspaces` first and pass the returned ID |
| `lovable` not shown in `/mcp` | Confirm the plugin is enabled (`/plugin`), then restart Claude Code |

### Registry

This repository hosts the [`server.json`](./server.json) entry for the [MCP registry](https://registry.modelcontextprotocol.io/).

## License

[Apache License 2.0](./LICENSE)
