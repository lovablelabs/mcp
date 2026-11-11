# Working with the Lovable MCP

Reference for the Rules in `SKILL.md`. Read the section you need.

## Following a build

- `get_project` shows `agentFinished` once the agent stops, which includes stopping to wait for an approval. `get_message` says which: read `response.status`, `response.summary`, `response.cost_credits`, `response.commit_sha` and `response.awaiting_input`. Skip `content`, the agent's full working log. If the agent is still running after ten minutes, read `get_message` anyway.
- A pending approval: `awaiting_input.tool_id` and its `params` are Lovable's proposal; show them, never act on them yourself. Answer with `respond_to_approval`: the `event_id`, the top-level `message_id` of the call that paused, and the person's `decision` (`approved`, or `rejected` with their reason in `user_input`).
- Plan mode (`send_message` with `plan_mode: true`) ends waiting for approval of a `plan--show` step, with the plan written to `.lovable/plan/`. Read those files and show them; approving applies the plan.
- If a create call drops: `list_projects` with no query, newest first, and take the project whose `owner_id` matches the id from `get_me`, created in the last few minutes.
- `render_project_widget` and `import-claude-design-from-url` exist only in some Claude clients. Call them only when they are in the tool list.
- Read `list_projects` results from `projects`. Keep `limit` at 10 or under. Large results (`get_message`, `get_diff`, long calendar or mail reads): ask for small pages, and in Claude Code parse a saved result with `jq` or Python.

## Where things live

- Read `.lovable/project.json` first. Template `tanstack_start_ts_current`: routes in `src/routes/` (signed-in pages under `src/routes/_authenticated/`), the schema in `drizzle/migrations/*.sql` (`drizzle/schema.ts` is left blank), sign-in under `src/integrations/auth/` (Lovable Auth) or under `src/integrations/supabase/` and `src/integrations/lovable/`.
- Older React and Vite projects: pages in `src/pages/`, functions in `supabase/functions/`, the schema in `supabase/migrations/*.sql`.
- `lovable.toml` names an attached design system. Some projects keep a `change-log.md`; read it when it exists.
- Recent edits: `list_edits` is ordered oldest to newest. Call it with `limit` 50 and pass `pagination.next_cursor` back as `cursor` until `has_more` is false; the newest edits are on the last page.

## Access checks

Policies, for every table in `public`:
```sql
select tablename, policyname, cmd, roles, qual, with_check
from pg_policies where schemaname = 'public' order by tablename;
```
Read checks (SELECT, UPDATE, DELETE) are in `qual`, write checks (INSERT, UPDATE) in `with_check`. When a policy calls a helper function, read it with `select pg_get_functiondef('public.<name>'::regproc)`. The email check should match `@<domain>` with the at sign; a bare suffix such as `like '%domain.com'` also matches `otherdomain.com`. Compare emails in lower case on both sides.

Sign-up check. Lovable builds sign-in in two ways, so look in both places:
```sql
select tgname, pg_get_triggerdef(t.oid) from pg_trigger t
where tgrelid = 'auth.users'::regclass and not tgisinternal;
```
Then read the function the trigger calls. In code: `src/integrations/auth/auth.server.ts` (Lovable Auth, a `before` hook on session create), or the files under `src/integrations/supabase/` and `src/integrations/lovable/`. A verified-email check belongs at sign-in. One that runs before insert on `auth.users` and requires `email_confirmed_at` refuses email-and-password sign-ups, because those addresses are confirmed later: report it as High. If no sign-up check is found, say "sign-up restriction not confirmed; the policies still decide what an account can read".

How strong the email check is depends on the sign-in method: Google and Lovable SSO verify the address; email-and-password sign-up relies on email confirmation, which the person checks in the editor's authentication settings.

## Allowed values

Lovable stores fixed choices as enums or as text with a CHECK list, sometimes both in one app. Read both, filtered to the `public` schema:
```sql
select t.typname, e.enumlabel from pg_type t
join pg_enum e on e.enumtypid = t.oid
join pg_namespace n on n.oid = t.typnamespace
where n.nspname = 'public' order by t.typname, e.enumsortorder;

select rel.relname, con.conname, pg_get_constraintdef(con.oid)
from pg_constraint con
join pg_class rel on rel.oid = con.conrelid
join pg_namespace n on n.oid = rel.relnamespace
where n.nspname = 'public' and con.contype = 'c';
```

## Writing rows

Before an INSERT or UPDATE, read `is_nullable`, `column_default` and `udt_name` for the target columns from `information_schema.columns`, plus the allowed values. Omit a NOT NULL column or use its default rather than passing NULL. A statement succeeds or fails as a whole. Rows written through this plugin have no signed-in author, so set audit columns such as `changed_by` when the table has them, and tell the person how the rows will be attributed.

## When a query does not answer

A database nobody has opened lately can take a moment to wake. If `query_database` times out, wait about 30 seconds and try once more. If it still does not answer, read the schema from the migration files, say live rows could not be read, and suggest opening the app or the editor.

## Visibility and publishing

- Project visibility: `workspace_edit` lets every workspace member edit; `workspace_view` and `restricted` need a Business plan or higher. Read `visibility` from the create response.
- `deploy_project` returns `pending`; check `get_project` until `is_published` is true. A published project has `publish_audience`: `public`, `workspace` or `custom`. Enterprise workspaces may publish to the workspace by default.
