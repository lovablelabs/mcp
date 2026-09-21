# Install for testing

This is a local build of the Lovable plugin for Claude, version 0.2.0, with 13 skills. Nothing here is published. Install it on your own machine, sign in to Lovable once, and try the skills.

## Claude Code (two minutes)

Unzip, then from the folder that holds `.claude-plugin/`:

```sh
claude plugin marketplace add ~/Downloads/lovable-plugin
claude plugin install lovable@lovable
```

Restart Claude Code. The first Lovable tool call opens a browser window to sign in. Check with `/mcp` (look for `lovable`) or type `/lovable:` and pick a skill.

To try it for one session without installing:

```sh
claude --plugin-dir ~/Downloads/lovable-plugin
```

Remove it later with `claude plugin uninstall lovable@lovable` and `claude plugin marketplace remove lovable`.

## Claude desktop and Cowork

Open Customize in the sidebar, choose Plugins, then upload the zip as a custom plugin. Sign in to Lovable when the first tool runs.

If you already have the official Lovable plugin installed from the Anthropic marketplace, disable that one first so the two do not both answer to `lovable`.

## What to test

Say it the way you would say it, not the skill name. Each line below should fire the skill named in brackets.

- "Set me up. Tell Lovable about my company." [know-my-business]
- "How many people signed up to my app last week?" [ask-my-app-data]
- "Check every project in my workspace for exposed keys and missing RLS." [review-my-workspace]
- "Write the brief for my invoicing app from what's in the code." [use-my-project-knowledge]
- "Make this real." after describing an idea [build-from-this-conversation]
- "We're launching Tuesday. I need a launch page and a changelog." [launch]
- "Our storage bill tripled. Find out why and fix it." [fix-and-ship]
- In claude.ai/design: Share, Send to, Lovable, then answer the workspace question in the chat [claude-design-to-app]

The first four spend nothing. The rest ask before `create_project` or `send_message` and ask again before publishing. If a skill spends credits without asking, or publishes without asking, that is a bug: note which skill and what you said.

## Known harness limits

In Claude Code auto mode, two calls were refused by the permission classifier, not by Lovable: writing workspace knowledge (`set_workspace_knowledge`) and reading a project's `.env`. The skills say so when it happens and hand you the text to paste or skip the file. In claude.ai and Cowork these calls prompt you instead.

## Where to report

Post in #wg-claude-sept-launch with the skill name, what you said, and what happened. Screenshots welcome.
