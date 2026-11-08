# Mailgun MCP server

Inspect configured sending domains and their verification details without sending messages.

A direct-provider MCP server with useful local execution. No PipMCP account is required for local use. Provider API access and any provider charges are your responsibility.

## Quick start

Requires Node.js22+. Clone this repository, then run:

```sh
npm ci --ignore-scripts
npm test
npm start
```

The default command inspects the tools offline. Configure your own credentials in your environment:

- `MAILGUN_BASIC_USERNAME`
- `MAILGUN_API_KEY`



Credential help: https://documentation.mailgun.com/docs/mailgun/api-reference/send/mailgun/domains

## Provider setup

Set MAILGUN_BASIC_USERNAME to api and your own private API key. This manifest targets US api.mailgun.net; EU-region accounts require api.eu.mailgun.net in a separately reviewed manifest. Sending is intentionally absent because the current runtime does not implement Mailgun multipart/form-data.

## Supported scope

- Read-only domain inspection. No message-sending tool: multipart/form-data is not supported by this runtime.

## Execute a real read

Replace example record IDs with records you are authorized to access.

```sh
npm start -- --call mailgun_list_domains '{"limit":10}'
```

## Use with an MCP client

Launch `node /absolute/path/mailgun-mcp/server.mjs` using stdio transport; pass credentials through the client environment. Stdout is reserved for JSON-RPC.

## Tools

| Tool | Effect | Purpose |
| --- | --- | --- |
| mailgun_list_domains | Read | Read one page of US-region sending domains. |
| mailgun_get_domain | Read | Read sending-domain details; this is not a message delivery test. |

## Concrete workflow

Inspect configured sending domains and their verification details without sending messages.

1. mailgun_list_domains
2. mailgun_get_domain

## Optional writes

Writes are disabled by default. Set CONNECTOR_ALLOW_WRITES=true and supply `_approval: {approved:true,idempotencyKey:"unique-request-id"}` on the specific call. Do not reuse a key for a different request. Uncertain outcomes are never automatically replayed; inspect the provider before another attempt.

## Capability evidence

Local: **Tested — HTTP fixtures and real stdio tools/call**. Managed: **Documented**. Provider authentication and live operations have not been established by these fixture tests. Evidence applies per operation and execution mode; setup discovery does not imply provider execution.

## Managed production

[Activate with PipMCP](https://pipmcp.com/github/start?app=mailgun&integration=mailgun-mcp&utm_source=github&utm_campaign=github-mailgun-mcp&utm_content=readme) when you want the hosted service.  Local credentials are not uploaded by this code. Review provider permissions in the managed onboarding. [Directory and related projects](https://jannikco.github.io/pipmcp-connectors/integrations/mailgun/).

## Provenance and maintenance

- https://documentation.mailgun.com/docs/mailgun/api-reference/send/mailgun/domains

Maintainer:jannikco. MIT license for this code. See connector.json for selected operations and integration.json for evidence. Runtime dependencies retain their own licenses. Report sensitive issues through https://pipmcp.com/contact; do not post credentials or customer data in public issues.
