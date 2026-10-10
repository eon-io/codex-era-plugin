---
name: era
description: How to get onto Era and work in it from Codex - check the era MCP server is connected (and tell the user exactly what to do when it is not), provision a synthetic company (`create_design_partner`), reach its systems over REST or as custom MCP servers, and investigate across them. Use when a task needs a realistic company's Salesforce, Zendesk, Slack, Jira, Gong or Drive data, or when a call to the era MCP server fails with 401.
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

The `era` MCP server is the console itself at https://console.era.eon.io/mcp. It is OAuth-protected and the sign-in is per person, so installing the plugin is not enough. To connect it, run `codex mcp login era` and sign in in the browser.

Start by calling `whoami`. If it answers, you are in. If it fails with `401 Unauthorized`, the connection was never made or has expired: reconnect the same way and retry `whoami` once. If you don't have an Era account yet, request one at https://console.era.eon.io/access.html; Era emails a first sign-in once an operator approves it.

Two doors need no console sign-in, both for a company that already exists:

- The user hands you an environment's token as a secret (`ERA_TENANT_TOKEN`
  and the `*_BASE_URL` / `*_MCP_URL` lines that `era new --write-env` wrote
  on their machine). That token opens the systems directly - go to
  "Reach the systems"; the console is not needed. It is a *tenant* token:
  the CLI's account verbs read `ERA_TOKEN` (a console credential) and reject
  it, so never export it under that name.
- Client credentials for a pipeline: `era client <tenant> --reveal --json` on
  the user's machine (or `register_agent_client` once connected) gives a
  `client_id`/`client_secret`; `POST <token_endpoint>` with
  `grant_type=client_credentials` mints a tenant token with no browser.

Neither provisions a new company: that always needs the console credential,
i.e. the connected MCP server, or `era new` run by the user on their own
laptop.

Once in, the console tools:

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
- `add_design_partner_systems` adds systems to an existing company; `design_partner_build` reports a build's progress.

## Reach the systems

### Through the console, no setup

`list_collections(tenant, connector)` lists a system's read-only tools with
their arguments. `read_collection(tenant, connector, collection,
arguments={...})` calls one and returns structured `data`, joined `text` and a
`refused` flag. Everything goes through the already-connected `era` server:
there is no custom MCP server or token handling, and a tool that writes is never
listed. The console throttles these reads per account.

With the token and URLs `create_design_partner` returns, two more doors open:

- **HTTP.** Each system answers plain REST at its base URL with the same token
  in the vendor's own auth shape. This is the door to use from a session: curl
  or a script, no configuration step.
- **MCP.** Add a system as a custom MCP server - `era mcp
  <tenant> --client codex` prints the `codex mcp add` line plus the header table
  Codex reads from `~/.codex/config.toml` - with the `url` and `headers` the tool
  returned. Keep the `<tenant>-<system>` name it prints - one account holds
  several companies, and a bare `slack` answers from whichever was registered
  last.

## Work in it

- Don't survey the estate before working it. The tools are named for the vendor's
  own operations (`run_soql_query`, `slack_search_messages`, `search_tickets`,
  `search_files`), so pick from the ones your client already lists and read the
  rest when a call needs them - listing every tool on every server first is
  minutes of silence that answers nothing. Call `tools/list` for a server when
  you are code rather than a client, or when nothing in the list looks right.
- Open with one scoped search, not a whole-company survey: the largest open
  opportunities, the tickets touched this week, last month's calls. One page of
  rows tells you who the customers are, when "now" is in this company and what is
  in trouble - and it is on screen in seconds, before the join begins.
- Say what you are doing while you do it: name the server and tool before you
  call it, and give the finding from each system as you get it rather than after
  the whole investigation. Somebody is usually watching this run. That is
  progress, not preamble - the final answer still leads with the finding.
- Resolve the entity in one system first, then follow it outward: carry its name,
  domain and the emails of its people into the next system, and search a time
  window rather than an id. Expect each system to call it something else.
- Build the timeline before concluding. Put what every system returned on one
  axis by timestamp, and read the order: what came first, what it turned into,
  where the story stops. A conclusion that rests on one system's records is
  usually the wrong one here.
- Answer with `system:id` attribution - a Salesforce id, a Slack channel and ts, a
  Zendesk ticket number. If a system returned nothing, say so rather than
  inferring it from another.
- Answer the question that was asked, then stop. Lead with the finding, put the
  evidence under it, and leave out what was not asked for: no preamble, no
  restatement of the question, no summary of the work, no extra sections. Report
  the part of the investigation the question needs, not everything it turned up.
- Writes are refused (`403 writes_disabled`), so an evaluation scores every run
  against the same company. Advancing an environment a quarter regenerates it
  from its recipe; ids and anything a client wrote are the recipe's again.
- The same systems answer plain HTTP at `_BASE_URL` with the same token, in each
  vendor's own auth shape, when the agent is code rather than a client.


The CLI path (`era init`, `era new`, `era mcp <tenant>`) does the
same from your own terminal. Run `era init` on the machine where you can open
the browser.

Machine-readable facts and limits are at
https://console.era.eon.io/llms.txt; the reference is
https://console.era.eon.io/docs.html.
