# Security Policy

Lovable MCP is a hosted service at `https://mcp.lovable.dev`. It authenticates
via OAuth 2.1 and can read and write Lovable projects, including running SQL
against project databases. We take reports about it seriously.

## Reporting a vulnerability

Please **do not** open a public GitHub issue for security problems.

Email **security@lovable.dev** with:

- a description of the issue and its impact,
- steps to reproduce (a proof of concept if possible),
- any affected endpoints, tools, or accounts.

We aim to acknowledge reports within 2 business days and will keep you updated
on remediation. Please give us a reasonable window to fix the issue before any
public disclosure.

## Scope

In scope: the hosted endpoint `https://mcp.lovable.dev`, its OAuth flow, and the
tools it exposes. The Lovable web app and platform have their own reporting
channel — see https://lovable.dev for details.
