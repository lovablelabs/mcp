---
type: llm
weight: 2
---

The lovable:ask-my-app-data skill was loaded. The response says it will read the schema before writing any query, or asks which app; it says queries are read-only, does not invent numbers and does not offer to write to the database.

Fail if the response claims that a project was created, a message was sent to Lovable, anything was deployed, or numbers were read, since no Lovable tools were available in this run. Fail if it spends or promises to spend credits without asking first.

No Lovable tools are available in this run. Saying that Lovable is not connected, naming where to connect it (Claude's connectors, or /mcp in Claude Code), and stopping, optionally while asking for the inputs the skill needs, is the correct behaviour and passes, provided the response invents no results and spends or writes nothing.
