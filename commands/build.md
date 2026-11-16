---
description: Build a new Lovable app from a prompt and optionally deploy it
argument-hint: <app description>
---

Build a new Lovable app described as: **$ARGUMENTS**

Use the Lovable MCP tools to do this end to end:

1. Call `get_me` to find the workspaces. If the user belongs to more than one, ask which to use.
2. Confirm with the user before spending credits: about 2 for a static page, 7 to 11 for an app with a database and sign-in. Then call `create_project` with `wait: false` in that workspace, with an initial build prompt derived from the description above.
3. If the app is for the user's team, the prompt also says which email domain or addresses may sign up, and asks Lovable to enforce it in the database: row-level security policies that check the signed-in user's email in lower case (`@<domain>`, with the at sign), plus a sign-up check that verifies the email at sign-in, never when the account is created. If anyone may sign up, it asks for policies that keep each person to their own rows. By default anyone can create an account and every signed-in user can read, change and delete every row.
4. Follow the build with `get_message` on the returned message id, passing `wait_seconds` up to 55 and repeating until `response.status` is `completed`, `awaiting_input`, `stopped` or `error`. Do not wait on `get_project.agentFinished`; it stays false while a build waits for an approval. Then read `response.status`, `response.summary` and `response.cost_credits`. Show any question in the summary to the user word for word. If the status is `awaiting_input`, show the pending action and answer it with `respond_to_approval` only after the user decides.
5. Share the editor URL and preview URL from `get_project`. For any app with sign-in, read `pg_policies` with `query_database`, and the sign-up trigger on `auth.users` from `pg_trigger` (or `src/integrations/auth/auth.server.ts`), and report who can sign up and who can read and change what; never call the app safe to share. Leave `visibility` as it came back unless the user wants teammates to edit the project, and ask before changing it.
6. Ask whether to publish. `deploy_project` publishes every unpublished edit, immediately. If yes, call it. Its `pending` result and URL, and `get_project.is_published`, do not confirm the new version is live, so open the URL and check the new content is there before calling it live; if you cannot check, say publishing is in progress. Return the URL and who can open it (`publish_audience`).

Notes:
- `create_project`, `send_message` and an approved `respond_to_approval` consume Lovable build credits; always confirm first.
- `deploy_project` publishes a publicly reachable URL on Free and Pro plans.
