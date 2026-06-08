# Lovable for Claude Code

> Build, iterate on, deploy, and manage [Lovable](https://lovable.dev) apps without leaving Claude Code.

The official Claude Code plugin for [Lovable](https://lovable.dev), the AI-powered full-stack app builder. It bundles the official [Lovable MCP server](https://mcp.lovable.dev) — pre-wired, with automatic OAuth — plus focused commands for the workflows developers reach for most.

## Why use it

Lovable builds and hosts full-stack apps; Claude Code is your local agent. This plugin connects the two so you can drive Lovable from your terminal: describe an app and ship it, request a change and review the diff, or provision and query a database — all in natural language, with credit- and publish-safety prompts built in.

## What's included

| Command | What it does | Lovable tools used |
|---|---|---|
| `/lovable:build <description>` | Create a new project from a prompt, follow the build, and optionally deploy to a live URL | `list_workspaces`, `create_project`, `send_message`, `get_project`, `deploy_project` |
| `/lovable:iterate <project> — <change>` | Send a change request to a project's agent (plan-first for big changes) and review the unified diff | `list_projects`, `send_message`, `get_message`, `get_diff`, `list_edits` |
| `/lovable:db <project> [SQL]` | Check/enable a Lovable Cloud Postgres database and run SQL with confirmation | `get_database_status`, `enable_database`, `query_database`, `get_database_connection_info` |

Every other Lovable MCP tool (analytics, knowledge/governance, connectors, file inspection, templates, …) is also available to Claude Code once the plugin is installed — the commands above just package the common flows.

## Requirements

- Claude Code (a version with plugin + remote-MCP support)
- A [Lovable](https://lovable.dev) account

## Install

```sh
/plugin marketplace add lovablelabs/mcp
/plugin install lovable@lovable
```

Restart Claude Code (or reload plugins). The first time a Lovable tool runs, Claude Code opens a browser window to sign in to Lovable.

Verify with `/mcp` (look for the `lovable` server) or ask Claude Code to "list my Lovable workspaces."

## Authentication

No keys or config. The bundled server is a remote Streamable HTTP endpoint (`https://mcp.lovable.dev`) using **OAuth 2.1**. Claude Code discovers the authorization server automatically (RFC 9728) and manages the bearer token. Connecting grants access to your whole Lovable account, not a single project.

## Safety & cost

This plugin operates on **real projects** and the commands prompt before any irreversible or billable action:

- 💳 **Credits** — `create_project` and `send_message` (i.e. `/lovable:build` and `/lovable:iterate`) consume Lovable build credits. All other tools are free.
- 🚀 **Public deploys** — `deploy_project` publishes a publicly reachable URL on Free/Pro plans.
- ⚠️ **Full-permission SQL** — `query_database` (`/lovable:db`) can read, write, and alter schema. The command shows the SQL and asks for confirmation before any write or schema change.

## Examples

```
/lovable:build a habit tracker with weekly streaks and a dark theme
/lovable:iterate invoice-tracker — add CSV export to the invoices table
/lovable:db invoice-tracker SELECT count(*) FROM invoices;
```

Or just talk to Claude Code: "deploy my landing-page project and give me the live URL."

## Troubleshooting

| Symptom | Fix |
|---|---|
| `lovable` not shown in `/mcp` | Confirm the plugin is enabled (`/plugin`), then restart Claude Code |
| Auth window never completes | Sign in at lovable.dev first, then re-trigger a Lovable command |
| "Transport not supported" | Update Claude Code — the plugin needs remote (Streamable HTTP) MCP support |
| A tool fails on a missing `workspace_id` | Ask Claude Code to "list my workspaces" first |

## Links

- [Lovable MCP server & docs](https://docs.lovable.dev/integrations/lovable-mcp-server)
- [Source repository](https://github.com/lovablelabs/mcp)
- [Claude Code plugins](https://code.claude.com/docs/en/plugins)

## License

[Apache License 2.0](./LICENSE)

