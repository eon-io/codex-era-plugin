---
name: era
description: How to get onto Era and work in it from Codex - sign in to the era MCP server, provision a synthetic company (create_design_partner), reach its systems over REST or as custom MCP servers, and investigate across them. Use when a task needs a realistic company's Salesforce, Zendesk, Slack, Jira, Gong or Drive data.
---

# Era: a synthetic company's internal systems, opened to your agent

Era provisions a synthetic company and hands you the systems it runs on -
Salesforce, HubSpot, Zendesk, Jira/Confluence, Gong, Slack, Google Drive and the
file stores - each as its own MCP server and REST API, in the vendor's own
schema, query language and auth shape. Not a dataset to load: the company's
stack, queried the way you would query the real products. Every environment is
100% synthetic - no real records, no real people - and the data plane is
read-only, so the company does not change under the agent working in it.

## Get in

This plugin ships one MCP server, `era`, which is the console itself at
https://console.era.eon.io/mcp. It is OAuth-protected: connect it from Codex's
MCP settings and sign in through the browser flow.

- `whoami` - the account and the companies it already holds.
- `design_partner_options` / `list_connectors` - the industries, sizes and
  systems a company can be built from.
- `create_design_partner` - provisions one and returns the tenant-bound token
  together with every system's MCP `url`, the `name` to register it under
  (`<tenant>-<system>`) and the `headers` that product reads (Slack's `xoxb-`,
  PagerDuty's `Token token=`, ...). It runs for seconds to minutes.
- `rotate_token(tenant, connectors=["slack"])` - a credential scoped to part of
  the fleet; `remove_design_partner` deprovisions one; `usage` reports what a
  tenant has spent.

## Reach the systems

`create_design_partner` gives you both doors, and one token opens them:

- **HTTP.** Each system answers plain REST at its base URL with the same token
  in the vendor's own auth shape. This is the door to use from a session: curl
  or a script, no configuration step.
- **MCP.** Add a system as a custom MCP server - `era mcp <tenant> --client
  codex` prints the `codex mcp add` line plus the header table Codex reads from
  `~/.codex/config.toml` - with the `url` and `headers` the tool returned. Keep
  the `<tenant>-<system>` name it prints - one account holds several companies,
  and a bare `slack` answers from whichever was registered last.

## Work in it

- The systems are views of one company, not separate fixtures. An account is a
  Salesforce `Account`, a HubSpot company and a Zendesk organization; the person
  on it is a contact, a ticket requester, a Slack member and a voice on a Gong
  call. Each system keeps its own ids, so you join the way you would in a real
  company - on names, domains, emails and timing - not on a shared key.
- Don't survey the estate first. The tools are named for the vendor's own
  operations (`run_soql_query`, `slack_search_messages`, `search_tickets`,
  `search_files`); pick from what is listed and read the rest when a call needs
  them.
- Open with one scoped search - the largest open opportunities, the tickets
  touched this week, last month's calls. One page of rows tells you who the
  customers are, when "now" is in this company and what is in trouble.
- Resolve the entity in one system, then follow it outward: carry its name,
  domain and its people's emails into the next system, and search a time window
  rather than an id.
- Build the timeline before concluding. A conclusion resting on one system's
  records is usually the wrong one here.
- Answer with `system:id` attribution - a Salesforce id, a Slack channel and ts,
  a Zendesk ticket number. If a system returned nothing, say so rather than
  inferring it from another.
- Writes are refused (`403 writes_disabled`), so an evaluation scores every run
  against the same company.

The CLI path (`era init`, `era new`, `era mcp <tenant>`) does the same from a
terminal. Machine-readable facts and limits are at
https://console.era.eon.io/llms.txt; the reference is
https://console.era.eon.io/docs.html.
