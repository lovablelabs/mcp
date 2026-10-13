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
| `create_project`, `send_message` | Yes | says what it is about to build, quotes the likely cost, and waits for a yes, one yes per call |
| `import-claude-design-from-url` | Yes | creates a project and runs a first build; says so with an estimate and waits for a yes |
| `respond_to_approval` | Yes, when approved | shows the paused action Lovable proposed and passes on the person's decision; never answers a pause itself |
| `deploy_project` | No | asks before publishing, and says that every unpublished edit goes live, now |
| `set_project_visibility` | No | asks first; `workspace_edit` gives every workspace member edit rights to the project's code and data |
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
| Call recorder (Gong, Fireflies, Zoom, Otter.ai) | `interview-tracker` | transcripts to load as interview answers |

Reads only. No skill writes to any of these.

## Behaviour seen in live runs

| What | What happened | What the skills do about it |
|---|---|---|
| Blocking build call | Timed out at the client after several minutes; the project existed anyway | `wait: false`, poll `get_project`, recover by owner and time |
| Project names | Lovable picks its own, whatever the message asked for | Use the returned id, never the requested name |
| Visibility | New projects start `restricted` in some workspaces and `workspace_edit` in others; `workspace_view` and `restricted` fail on Pro; project visibility is editor access, not app access | Read `visibility` from the create response, leave it unless teammates should edit, and ask first; app access is the publish audience |
| Publishing | `deploy_project` returns `pending`; Enterprise workspaces may publish to the workspace by default; `publish_audience` appears only once published | Poll `is_published`, report `publish_audience` |
| Workspace knowledge | 403 for members, works for admins | Keep the profile in the conversation and the person's Claude instructions |
| Sign-in | Some workspaces allow Lovable SSO only. Anyone can create an account by default, and default policies let every signed-in user read every row | Ask for sign-in as the workspace allows, plus an email allowlist enforced in the database policies for apps with people's data, confirmed in `pg_policies`; never call an app safe to share |
| Pauses | A paused turn can report `agentFinished` true; a new message rejects a pending approval; completed summaries can carry questions | Read `get_message` status, answer pauses with `respond_to_approval` after the person decides, show questions word for word |
| Allowed values | Status columns are usually text with a CHECK list, not enums | Read both, filtered to the `public` schema |
| Idle databases | `query_database` can return 504 on a project nobody has opened lately | Retry once, then read the migration files and say so |
| Writes | NULL into a NOT NULL column failed and rolled back the whole statement; rows carry `changed_by = unknown` | Read nullability and allowed values first; disclose attribution |
| Summaries | Lovable's summary can say "deployed" or "tested" when it was not | Confirm with a tool call before repeating it |
| Uploads | PUT without the returned headers was refused (400) | Send `Content-Type` plus every returned header |
| Big payloads | `get_message` can run past 100,000 characters; a few weeks of calendar past 250,000 | Read once, read fields, page small |

## For workspace admins

- On Enterprise workspaces, third-party MCP clients must be enabled under Settings, Security, Privacy and security before anyone can sign in from Claude.
- The server acts with the signed-in person's own Lovable permissions. It cannot reach a workspace they are not a member of.
- To keep publishing behind a human, leave `deploy_project` on "ask" in Claude's connector settings. Every skill already asks; the setting makes it enforced.
