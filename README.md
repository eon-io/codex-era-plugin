# Era Codex Plugin

Connect Codex to [Era](https://console.era.eon.io) via this plugin. Bundles the Era MCP server so an agent can sign in, provision a synthetic design partner, and manage its connector fleet.

## Capabilities

- **Era MCP server** — sign in (or complete OAuth), list the connectors a design partner can be built across, provision one, add systems to it, watch its build, rotate or scope its tokens, check usage, and remove it — all as MCP tools.

This plugin currently ships the MCP server only. Guided skills are not included yet.

## Installation

Install via the Codex plugin marketplace using `.agents/plugins/marketplace.json`.

## MCP Server

The plugin connects to `https://console.era.eon.io/mcp` via HTTP. Authentication is handled by Era's own sign-in flow.

## Links

- [Era](https://console.era.eon.io)
- [Eon](https://eon.io)

## License

Licensed under the [Apache License, Version 2.0](LICENSE). See [NOTICE](NOTICE) for attribution and the scope of the license — the Era service, API, and trademarks are not covered.
