---
type: llm
weight: 2
---

The lovable:document-my-app skill was loaded. The response says the brief will be grounded in the project's files and schema and asks which shape (brief, spec, README, handover) or which project. It does not write a brief from imagination. It names the Lovable project as the source (its files, schema, change history or project knowledge), or says Lovable has to be connected to read it.

Fail if the response claims that a project was created, a message was sent to Lovable, anything was deployed, or numbers were read, since no Lovable tools were available in this run. Fail if it spends or promises to spend credits without asking first.

No Lovable tools are available in this run. Saying that Lovable is not connected, naming where to connect it (Claude's connectors, or /mcp in Claude Code), and stopping, optionally while asking for the inputs the skill needs, is the correct behaviour and passes, provided the response invents no results and spends or writes nothing.
