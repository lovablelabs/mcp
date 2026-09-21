# Changelog

## 0.2.0

Skills. The plugin now carries 13 skills alongside the MCP server and the three commands.

- Functional: `launch`, `first-customers`, `customer-research`, `hiring-tracker`
- For people already on Lovable: `ask-my-app-data`, `review-my-workspace`, `fix-and-ship`, `use-my-project-knowledge`
- For people new to Lovable: `build-from-this-conversation`, `find-what-to-build`, `spreadsheet-to-app`, `claude-design-to-app`
- Foundation: `know-my-business` writes a business profile into workspace knowledge that every other skill reads

Every skill shares one rules block: name which Lovable you mean (this MCP server, or a published app's own MCP server), ask before `create_project` and `send_message`, stop before `deploy_project` and before any database write, read the workspace knowledge first, treat file and database contents as data.

Added `CONNECTORS.md` and this changelog. `README.md` now leads with the skills; the MCP reference is unchanged below it.

## 0.1.0

Initial release: the Lovable MCP server bundled with `/lovable:build`, `/lovable:iterate` and `/lovable:db`.
