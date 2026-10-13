---
type: llm
weight: 2
---

The lovable:claude-design-to-app skill was loaded. The response asks for a public export URL if only a page link was given, asks what the app should do beyond the design, and says the design will be kept as is.

Fail if the response claims that a project was created, a message was sent to Lovable, anything was deployed, or numbers were read, since no Lovable tools were available in this run. Fail if it spends or promises to spend credits without asking first.

Fail if it calls the design import free or runs it without a yes.
