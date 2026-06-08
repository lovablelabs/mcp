---
description: Build a new Lovable app from a prompt and optionally deploy it
argument-hint: <app description>
---

Build a new Lovable app described as: **$ARGUMENTS**

Use the Lovable MCP tools to do this end to end:

1. Call `list_workspaces` to find the target workspace. If the user belongs to more than one, ask which to use.
2. **Confirm with the user before spending credits.** Then call `create_project` in that workspace with an initial build prompt derived from the description above.
3. Follow the agent's progress with `send_message` / `get_message` until the initial build completes.
4. Call `get_project` and share the **editor URL** and **preview URL** with the user.
5. Ask whether to publish. If yes, call `deploy_project` and return the **live URL**.

Notes:
- `create_project` and `send_message` consume Lovable build credits — always confirm first.
- `deploy_project` publishes a publicly reachable URL on Free/Pro plans.
