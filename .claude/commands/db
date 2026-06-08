---
description: Provision or query a Lovable project's Cloud (Postgres) database
argument-hint: <project> [SQL]
---

Work with a Lovable project's database. Input: **$ARGUMENTS**

1. Resolve the `project_id` (use `list_projects` if the user gave a name).
2. Call `get_database_status`. If no database exists and the user wants one, confirm and call `enable_database` (takes 30–60s; **one-time per project**).
3. If the user supplied SQL, run it with `query_database`.
   - ⚠️ `query_database` has **full read / write / schema** permissions. Show the user the exact SQL and get explicit confirmation before running anything that writes data or alters schema.
4. For connection details (connection string, API URL), call `get_database_connection_info`.
