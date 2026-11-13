---
description: Send a change request to a Lovable project's agent and review the diff
argument-hint: <project> - <change request>
---

Iterate on a Lovable project. Request: **$ARGUMENTS**

1. Resolve the target project. If the user gave a name rather than an ID, call `list_projects` with the workspace id and confirm the `project_id` with them.
2. Messages spend Lovable build credits (about 1 for a plan or a small change). Confirm first.
3. For a non-trivial change, call `send_message` with `plan_mode: true` and `wait: false`. Wait on `get_message` (`wait_seconds` up to 55, repeated) until `response.status` is `completed`, `awaiting_input`, `stopped` or `error`; `get_project.agentFinished` stays false while a plan waits, so do not wait on it. A plan ends with `response.status` `awaiting_input`; the plan files are in `.lovable/plan/`. Read them with `read_file` and show the user.
4. If the user approves, call `respond_to_approval` with the `awaiting_input.event_id`, the top-level `message_id` from the plan `send_message`, and `decision: "approved"`. That applies the plan. Do not apply with a new `send_message`: a new message rejects the pending plan. If the user declines, call it with `decision: "rejected"`.
5. For a small change, call `send_message` directly with `wait: false`, then wait on `get_message` the same way. If `get_message` shows a completed turn whose summary asks the user something, show the question word for word.
6. Call `get_diff` with the `message_id` and show the user what changed, summarized per file when the diff is large.
7. Offer to iterate further, view history with `list_edits` (the first page is the newest; sort by `created_at`), or publish with `deploy_project`.
