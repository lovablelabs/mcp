---
type: llm
weight: 2
---

The lovable:claude-design-to-app skill was loaded. The response asks for the Open in Lovable link or project id (or gives the Share, More formats and apps, Send to Lovable path if the design was not sent yet), asks what the app should do beyond the design, and does not claim the app was changed.

Fail if the response claims that a project was created, a message was sent to Lovable, anything was deployed, or numbers were read, since no Lovable tools were available in this run. Fail if it spends or promises to spend credits without asking first.

Fail if it calls the design import free or runs it without a yes.

No Lovable tools are available in this run. Saying that Lovable is not connected, naming where to connect it (Claude's connectors, or /mcp in Claude Code), and stopping, optionally while asking for the inputs the skill needs, is the correct behaviour and passes, provided the response invents no results and spends or writes nothing.
