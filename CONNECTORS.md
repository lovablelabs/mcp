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
| Reading tools (`get_*`, `list_*`, `read_file`, `get_diff`, SELECT queries) | No | runs without asking |

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

## How the skills work with the server

| Area | What the skills do |
|---|---|
| Builds | Start `create_project` and `send_message` with `wait: false`, check progress with `get_project`, and read the result from `get_message` once. If a call drops, they find the person's newest project before creating again |
| Project names | Lovable names each project; the skills use the returned id |
| Visibility | Read it from the create response and change it only when teammates should edit, after a yes. `workspace_view` and `restricted` need a Business plan or higher. Project visibility (who edits) and publish audience (who opens the app) are kept apart |
| Publishing | `deploy_project` returns `pending`; the skills check `is_published` and report `publish_audience`. Enterprise workspaces may publish to the workspace by default |
| Workspace knowledge | Admins write it; members keep the profile in the conversation and their Claude instructions |
| Access | Team apps get an email allowlist enforced in the database, public apps get policies that keep each person to their own rows, and every build confirms this in the database. No skill calls an app safe to share |
| Approvals | Answered with `respond_to_approval` after the person decides. Questions in a summary go to the person word for word |
| Allowed values | Read from enums and CHECK lists in the `public` schema |
| Quiet databases | One retry after a short wait, then the schema from the migration files |
| Writes | Nullability and allowed values are read first; rows written through the plugin are labelled as having no signed-in author |
| Summaries | Before repeating that something is deployed or tested, a skill confirms it with a tool call |
| Uploads | `Content-Type` plus every header the upload URL returns |
| Large results | Read once, keep the fields needed, page small |

## For workspace admins

- On Enterprise workspaces, third-party MCP clients must be enabled under Settings, Security, Privacy and security before anyone can sign in from Claude.
- The server acts with the signed-in person's own Lovable permissions. It cannot reach a workspace they are not a member of.
- To keep publishing behind a human, leave `deploy_project` on "ask" in Claude's connector settings. Every skill already asks; the setting makes it enforced.
