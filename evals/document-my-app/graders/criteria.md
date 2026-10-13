---
type: llm
weight: 2
---

The lovable:document-my-app skill was loaded. The response says the brief will be grounded in the project's files and schema and asks which shape (brief, spec, README, handover) or which project. It does not write a brief from imagination.

Fail if the response claims that a project was created, a message was sent to Lovable, anything was deployed, or numbers were read, since no Lovable tools were available in this run. Fail if it spends or promises to spend credits without asking first.
