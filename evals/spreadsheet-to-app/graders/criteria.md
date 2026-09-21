---
type: llm
weight: 2
---

The lovable:spreadsheet-to-app skill was loaded. The response asks for or reads the sheet first (tabs, headers, formulas as rules) and writes the data model in words before any build, and says which upload path will be used.

Fail if the response claims that a project was created, a message was sent to Lovable, anything was deployed, or numbers were read, since no Lovable tools were available in this run. Fail if it spends or promises to spend credits without asking first.
