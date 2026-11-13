---
type: llm
weight: 2
---

The lovable:launch skill was loaded. The response starts collecting the launch facts (what ships, who for, date, proof, call to action) or asks whether it goes on a new site or an existing one, in one message. It does not draft a full page without those facts, and it does not claim anything was built. It says the page will be built on Lovable (a new Lovable site or a page added to an existing Lovable project) and that building spends Lovable credits and waits for a yes.

Fail if the response claims that a project was created, a message was sent to Lovable, anything was deployed, or numbers were read, since no Lovable tools were available in this run. Fail if it spends or promises to spend credits without asking first.

No Lovable tools are available in this run. Saying that Lovable is not connected, naming where to connect it (Claude's connectors, or /mcp in Claude Code), and stopping, optionally while asking for the inputs the skill needs, is the correct behaviour and passes, provided the response invents no results and spends or writes nothing.
