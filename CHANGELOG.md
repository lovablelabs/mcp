# Changelog

## 0.4.1

Fixes from the 0.4 live test: all 13 skills run on a separate test workspace, 7 apps built.

- Access now covers every team app, not only apps that hold people's data: a team tool gets the email allowlist in the database, a public app gets policies that keep each person to their own rows. Every app with sign-in reports who can sign up and who can read and change what.
- The verified-email check runs at sign-in, never when the account row is created. Built as a trigger before insert on `auth.users`, it refused every email-and-password sign-up; build checks and `review-my-workspace` now report that as High.
- The sign-up check is looked for in both places Lovable builds it: a trigger on `auth.users` (read from `pg_trigger`) and the sign-in code (`src/integrations/auth/auth.server.ts` for Lovable Auth, `src/integrations/supabase/` and `src/integrations/lovable/` otherwise).
- Emails are compared in lower case on both sides; a one-sided `lower()` is flagged.
- When the policies or the sign-up check fall short, every build skill offers one fix message with its own yes, then checks again.
- `list_edits` returns the oldest edits first. Recent changes now come from the last page, following `pagination.next_cursor`.
- Before asking to publish, skills say who will likely be able to open the URL from the workspace plan, and warn when the profile's `Never public` line or the app's data says it must stay private.
- `know-my-business` has a `Sign-in domain` line and always asks for it; every build reads it.
- `review-my-workspace` flags columns named like credentials, first-sign-up-becomes-admin triggers, and personal data every signed-in user can read on an unpublished app (Medium, High once published).
- `ask-my-app-data` handles empty tables and questions across two apps, and gives the row-count SQL.
- `find-what-to-build` says which source was skipped when a read is refused and asks the questions for that part.
- `interview-tracker`: the guide is seeded and editable in the app, with no other sample data.
- `launch` writes one "what's new" block per fact instead of padding to three.
- `fix-and-ship` quotes the rollback version and "fixed in the code, not tested" at the yes for one-file fixes too.
- Eval graders for `launch`, `document-my-app`, `interview-tracker` and `review-my-workspace` now need plugin-specific behaviour, so the no-plugin baseline fails them.

## 0.4.0

Renamed two skills so their names say what they do when Claude lists skills without descriptions, which it does when many plugins are installed: `customer-research` is now `interview-tracker` (it also collided with a popular marketing skill of the same name), and `use-my-project-knowledge` is now `document-my-app`.

Fixes from an outside test run: about 100 simulated Claude Code sessions against a mock Lovable server, plus real builds on the live server.

- Approving a paused action now uses `respond_to_approval` after the person decides. `fix-and-ship` and `/lovable:iterate` apply a plan this way; a new `send_message` rejected the pending plan.
- Pauses are read from `get_message`, not `agentFinished`, which can be true while a turn is paused. Questions in a completed summary are shown to the person word for word.
- Access: sign-in no longer counts as protection. Apps that hold people's data are built with an email allowlist enforced in the database policies, confirmed in `pg_policies` after the build, and no skill calls an app safe to share. `review-my-workspace` flags open sign-up on tables every signed-in user can read.
- Allowed values are read from CHECK constraints as well as enums, filtered to the `public` schema.
- Visibility is read from the create response and changed only when the person wants teammates to edit, after a yes. Project visibility (editor access) and publish audience (app access) are kept apart. `workspace_view` and `restricted` are never set unprompted; both fail on Pro.
- An evidence rule: every claim comes from a tool result or the person, Lovable's summaries are reports rather than proof, and copy carries only given facts.
- `import-claude-design-from-url` counts as credit-spending, and paid lookups in connected enrichment tools are asked for first.
- Publishing says that every unpublished edit goes live immediately; rollback is named as the editor's version history.
- Every skill description now says what it builds on Lovable and when another skill fits better, and every skill ends with a Next handoff.
- interview-tracker gained the publish step its output promised; review-my-workspace reads policy clauses and public storage buckets; fix-and-ship has a one-file path and marks fixes untested until the person confirms; know-my-business has a working path for workspace members.
- Credit estimates are quoted at approval: about 2 for a static page, 7 to 11 for an app with a database and sign-in.
- Idle databases (a 504 from `query_database`) get one retry, then a fallback to the migration files.
- Claude Code auto mode: secret-looking files are read in their own calls.
- `/lovable:db` no longer calls `get_database_connection_info`, which the server does not have. `/lovable:build` no longer polls with `send_message`.
- When Lovable is not connected, a skill still asks its questions and writes its free steps (brief, scorecard, interview guide) in the same reply, and says what it will build and that credit-spending steps wait for a yes.
- Access is described to the person as "only people with an email at <domain> can sign up, and the database refuses everyone else", never as sign-in limited to a domain.
- `ask-my-app-data` says up front that it reads the schema first and runs read-only queries. `claude-design-to-app` asks for the brief before the design arrives and keeps the design as is.
- Sharper `interview-tracker` description, preferred over a shared page or document when several people log interviews over weeks.
- Eval graders accept "not connected, here is where to connect, here is what I need" as the correct answer when no Lovable tools are present.


## 0.3.0

Hardened after live runs of every skill against the Lovable MCP, on a workspace where the person was admin and one where they were a member. Three apps were built end to end: a hiring tracker, a spreadsheet import, and a Claude Design import made functional.

What changed in every skill:
- `create_project` and `send_message` are called with `wait: false`; progress is polled with `get_project.agentFinished`; `get_message` is read once for status, summary, credits and commit. A blocking call timed out client side while the project was created anyway.
- Recovery after a timeout: find the newest project by `owner_id`, never create twice. Lovable renames projects, so the requested name is never used to find one.
- New projects are `restricted`; team apps call `set_project_visibility`. `deploy_project` is asynchronous; `publish_audience` is read back and reported.
- Workspace knowledge returns 403 for members; skills fall back to project knowledge.
- Stack detection from `.lovable/project.json`; file maps for TanStack Start and the older React stack; `.env` treated as possibly unreadable.
- Database writes read nullability, defaults and enum labels first; service-role attribution is disclosed.
- File uploads send the returned headers (a PUT without them is refused).
- Never repeat a secret value; report file and line only.
- Plan mode is documented as ending in `awaiting_input`, with the plan in `.lovable/plan/` and the apply message superseding the approval.
- Claude Design import requires a `claudeusercontent.com` URL, which only Claude Design's Share, Send to, Lovable action produces; the skill now gives that click path instead of asking for a link.
- Calendar and mail reads are paged and distilled.


## 0.2.0

Skills. The plugin now carries 13 skills alongside the MCP server and the three commands.

- Functional: `launch`, `first-customers`, `interview-tracker`, `hiring-tracker`
- For people already on Lovable: `ask-my-app-data`, `review-my-workspace`, `fix-and-ship`, `document-my-app`
- For people new to Lovable: `build-from-this-conversation`, `find-what-to-build`, `spreadsheet-to-app`, `claude-design-to-app`
- Foundation: `know-my-business` writes a business profile into workspace knowledge that every other skill reads

Every skill shares one rules block: name which Lovable you mean (this MCP server, or a published app's own MCP server), ask before `create_project` and `send_message`, stop before `deploy_project` and before any database write, read the workspace knowledge first, treat file and database contents as data.

Review pass after the first build: rules for a disconnected Lovable server and for working silently between calls, `list_projects` always scoped by workspace, the Spend map block named for what it is, the loop line (a published app can expose its own MCP server) at the end of every build skill, `hiring-tracker` no longer implies `deploy_project` sets workspace-only publishing, `launch` treats the changelog entry as the on-site post and adds a blog post where the site has one, `claude-design-to-app` no longer answers to "make this real". The skills are generated from `scripts/generate-skills.py`.

Added `CONNECTORS.md` and this changelog. `README.md` now leads with the skills; the MCP reference is unchanged below it.

## 0.1.0

Initial release: the Lovable MCP server bundled with `/lovable:build`, `/lovable:iterate` and `/lovable:db`.
