# Connectors

## What this plugin connects

One server, pre-wired in `.mcp.json`:

| Server | URL | Auth | What it does |
|---|---|---|---|
| Lovable | `https://mcp.lovable.dev` | OAuth 2.1, sign in once | Creates, reads, changes and publishes Lovable projects; reads and queries their cloud databases; reads and writes workspace and project knowledge |

Every skill in this plugin runs on this server. No skill works without it.

## Two things are called Lovable

- **The Lovable MCP server** (this plugin). It builds and manages projects. Its tools are the ones the skills name.
- **A published app's own MCP server.** Any published Lovable app can expose its own tools for the data inside it (for example `list_open_requests` or `create_request`). That is a separate connector the person copies from the app's Agent integrations menu and adds in Claude. This plugin does not add it, and no skill assumes it is present. See [Publish your app as an MCP server](https://docs.lovable.dev/features/agent-integrations).

## Credits and safety

| Tool | Spends credits | The skill... |
|---|---|---|
| `create_project`, `send_message` | Yes | says what it is about to build and waits for a yes, one yes per call |
| `deploy_project` | No | asks before publishing |
| `query_database` with INSERT, UPDATE, DELETE or DDL | No | shows the statement and asks before running it |
| `set_workspace_knowledge`, `set_project_knowledge` | No | shows current and proposed text, asks, then replaces |
| Everything else (`get_*`, `list_*`, `read_file`, `get_diff`, SELECT queries) | No | runs without asking |

## Connectors some skills read, if the person has them in Claude

These are the person's own Claude connectors. They are not bundled here, and every skill works without them; the output is thinner and the skill says what it could not see.

| Category | Skills that read it | What it adds |
|---|---|---|
| CRM (HubSpot, Salesforce, Close, and others) | `first-customers` | the exclusion list of current customers and open deals |
| Data enrichment (Clay, ZoomInfo, Apollo, Lusha, Crunchbase) | `first-customers` | candidate companies matching the fit rule |
| Web search | `first-customers`, `know-my-business`, `find-what-to-build` | verification and company facts |
| Calendar, email, chat | `find-what-to-build` | the repeated meetings, threads and questions that point at the first tool to build |
| Docs and knowledge base (Google Drive, Notion, and others) | `find-what-to-build`, `know-my-business` | sheets edited weekly, company facts |
| Call recorder (Gong, Fireflies, Zoom, Otter.ai) | `customer-research` | transcripts to load as interview answers |

Reads only. No skill writes to any of these.

## Behaviour seen in live runs

| What | What happened | What the skills do about it |
|---|---|---|
| Blocking build call | Timed out at the client after several minutes; the project existed anyway | `wait: false`, poll `get_project`, recover by owner and time |
| Project names | Lovable picks its own ("HireFlow Tracker" for "Hiring tracker (plugin test)") | Use the returned id, never the requested name |
| Visibility | New projects are `restricted` | `set_project_visibility` for team apps |
| Publishing | `deploy_project` returns `pending`; Enterprise workspaces published to `workspace` by default | Poll `is_published`, report `publish_audience` |
| Workspace knowledge | 403 for members, works for admins | Fall back to project knowledge |
| Sign-in | Enterprise workspace allowed Lovable SSO only | Ask for "sign-in as the workspace allows" |
| Writes | NULL into a NOT NULL column failed and rolled back the whole statement; rows carry `changed_by = unknown` | Read nullability and enums first; disclose attribution |
| Uploads | PUT without the returned headers was refused (400) | Send `Content-Type` plus every returned header |
| Big payloads | `get_message` ran past 100,000 characters; three weeks of calendar 258,000 | Read once, read fields, page small |

## For workspace admins

- On Enterprise workspaces, third-party MCP clients must be enabled under Settings, Security, Privacy and security before anyone can sign in from Claude.
- The server acts with the signed-in person's own Lovable permissions. It cannot reach a workspace they are not a member of.
- To keep publishing behind a human, leave `deploy_project` on "ask" in Claude's connector settings. Every skill already asks; the setting makes it enforced.
