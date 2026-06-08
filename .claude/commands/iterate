---
description: Send a change request to a Lovable project's agent and review the diff
argument-hint: <project> — <change request>
---

Iterate on a Lovable project. Request: **$ARGUMENTS**

1. Resolve the target project. If the user gave a name rather than an ID, call `list_projects` and confirm the `project_id` with them.
2. For a non-trivial change, call `send_message` with `plan_mode` first so the user can approve the plan before code is written. **This consumes build credits — confirm first.**
3. When the message completes (poll `get_message` if needed), call `get_diff` with the returned `message_id` and show the user the unified diff.
4. Summarize what changed. Offer to iterate further, view history with `list_edits`, or publish with `deploy_project`.
