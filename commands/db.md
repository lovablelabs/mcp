---
description: Provision or query a Lovable project's Cloud (Postgres) database
argument-hint: <project> [SQL]
---

Work with a Lovable project's database. Input: **$ARGUMENTS**

1. Resolve the `project_id`. If the user gave a name, call `list_projects` with the workspace id, show up to five matches with dates, and confirm one.
2. Call `get_database_status`. If no database exists and the user wants one, confirm and call `enable_database` (takes 30 to 60 seconds; one time per project).
3. If the user supplied SQL, run it with `query_database`.
   - `query_database` has full read, write and schema permissions. Show the user the exact SQL and get explicit confirmation before running anything that writes data or alters schema.
   - Before a write, read the target columns' nullability and defaults from `information_schema.columns`, and allowed values from enums and CHECK constraints in the `public` schema.
   - A database nobody has opened lately can take a moment to wake. If a query does not answer, wait about 30 seconds and try once more; if it still does not, say so and suggest opening the app or the editor.
4. Connection details (connection string, API URL) are not exposed by the Lovable MCP server. Point the user to the Cloud tab in the Lovable editor (`editor_url` from `get_project`).
