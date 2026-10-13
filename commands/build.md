---
description: Build a new Lovable app from a prompt and optionally deploy it
argument-hint: <app description>
---

Build a new Lovable app described as: **$ARGUMENTS**

Use the Lovable MCP tools to do this end to end:

1. Call `get_me` to find the workspaces. If the user belongs to more than one, ask which to use.
2. Confirm with the user before spending credits: about 2 for a static page, 7 to 10 for an app with a database and sign-in. Then call `create_project` with `wait: false` in that workspace, with an initial build prompt derived from the description above. If the app stores people's data, the prompt says which email domain or addresses may sign up; by default anyone can.
3. Poll `get_project` every 30 to 60 seconds for `agentFinished`, then call `get_message` once and read `response.status`, `response.summary` and `response.cost_credits`. Show any question in the summary to the user word for word. If the status is `awaiting_input`, show the pending action and answer it with `respond_to_approval` only after the user decides.
4. Share the editor URL and preview URL from `get_project`. If `visibility` is `restricted` and a team will use it, offer `set_project_visibility` with `workspace_edit`.
5. Ask whether to publish. If yes, call `deploy_project`, poll `get_project` until `is_published` is true, and return the live URL and who can open it (`publish_audience`).

Notes:
- `create_project`, `send_message` and an approved `respond_to_approval` consume Lovable build credits; always confirm first.
- `deploy_project` publishes a publicly reachable URL on Free and Pro plans.
