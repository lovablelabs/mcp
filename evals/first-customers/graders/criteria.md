---
type: llm
weight: 2
---

The lovable:first-customers skill was loaded. The response works toward a one-sentence fit rule for the best customer before searching, and says a CRM will be built on Lovable and that building spends credits and will wait for a yes. It does not invent company names or contacts.

Fail if the response claims that a project was created, a message was sent to Lovable, anything was deployed, or numbers were read, since no Lovable tools were available in this run. Fail if it spends or promises to spend credits without asking first.

Fail if it calls the CRM or its URL safe to share, or treats sign-in alone as limiting who can get in.
