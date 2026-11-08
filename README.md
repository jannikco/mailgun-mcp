# Mailgun sending-domain checks for AI assistants

Bring sending-domain status into your operational checks without adding message-sending permissions to this connector.

[Start locally](#get-started) · [Connect your assistant](#connect-your-assistant) · [Available tools](#available-tools) · [Hosted PipMCP setup](#use-pipmcp-for-production)

## Built for useful workflows

- **Review domain coverage.** List the sending domains visible to your Mailgun key.
- **Investigate one domain.** Inspect a chosen domain’s configuration and verification details.
- **Build a read-only email operations brief.** Use the domain tools from a CLI or assistant; sending and delivery tests are outside this package.

## Why use this package?

Get a focused Mailgun MCP integration without assembling request mappings and a stdio transport yourself. The package provides documented input schemas, provider authentication handling and an example CLI, with the same tools available to your assistant.

The selected tools keep this Mailgun workflow read-only.

## Get started

**Requirements:** Node.js 22 or newer, npm, and access to the provider account you intend to use.

```sh
git clone https://github.com/jannikco/mailgun-mcp.git
cd mailgun-mcp
npm ci --ignore-scripts
npm test
npm start
```

`npm start` shows the tool inventory and workflow offline. It does not authenticate or call the provider. The tests use provider fixtures and stdio transport; they do not modify your account.

### Configure your provider account

The current release connects directly to the provider with **your own credentials**. It does not require a PipMCP key for local execution. Configure the environment of the process or MCP client that runs the server; `.env` files are not loaded automatically.

| Environment variable | What to supply |
| --- | --- |
| `MAILGUN_BASIC_USERNAME` | Set to `api`. The Basic authentication username specified in the provider setup below. |
| `MAILGUN_API_KEY` | The Basic authentication password/API key specified in the provider setup below. |

Set MAILGUN_BASIC_USERNAME to api and your own private API key. This manifest targets US api.mailgun.net; EU-region accounts require api.eu.mailgun.net in a separately reviewed manifest. Sending is intentionally absent because the current runtime does not implement Mailgun multipart/form-data.

[Provider credential/reference documentation](https://documentation.mailgun.com/docs/mailgun/api-reference/send/mailgun/domains). Keep credentials out of commits, issue reports and shared client configuration.

### Run your first provider read

After configuring the environment, run `mailgun_list_domains`:

```sh
npm start -- --call mailgun_list_domains '{"limit":10}'
```

Replace sample account, record, file and project IDs with values from your authorized account. This command makes a real provider request and returns its result for you to inspect.

## Available tools

| Capability | MCP tool | Effect | Required inputs |
| --- | --- | --- | --- |
| Read one page of US-region sending domains. | `mailgun_list_domains` | Read | None |
| Read sending-domain details; this is not a message delivery test. | `mailgun_get_domain` | Read | `domain` |

Tool schemas and bindings are in [connector.json](./connector.json). These are the selected operations this package ships.

### A practical tool sequence

1. Use `mailgun_list_domains` — Read one page of US-region sending domains.
2. Use `mailgun_get_domain` — Read sending-domain details; this is not a message delivery test.

## Connect your assistant

Add this stdio server to an MCP-compatible client. Replace the absolute path and credential placeholders. Use your client’s secret/environment controls instead of committing this configuration.

```json
{
  "mcpServers": {
    "mailgun-mcp": {
      "command": "node",
      "args": [
        "/absolute/path/mailgun-mcp/server.mjs"
      ],
      "env": {
        "MAILGUN_BASIC_USERNAME": "api",
        "MAILGUN_API_KEY": "YOUR_MAILGUN_API_KEY"
      }
    }
  }
}
```

`node server.mjs` starts the MCP server; `npm start` runs the example CLI. The server reserves stdout for MCP messages. If the client does not inherit your shell environment, pass the required variables explicitly.

## Read-only scope

The shipped Mailgun tools are read-only. This package does not provide a write command or silently expand the provider permissions.

## Provider-specific limits

- Read-only domain inspection. No message-sending tool: multipart/form-data is not supported by this runtime.

## Use PipMCP for production

The local package is useful when you want to run these selected Mailgun operations in your own process. PipMCP provides a hosted place to configure provider connections, publish tools, issue endpoint access tokens, apply server-side policy and approvals, and inspect execution/usage records. Provider credentials and hosted execution are configured in that service.

**[Set up Mailgun in PipMCP](https://pipmcp.com/github/start?app=mailgun&integration=mailgun-mcp&utm_source=github&utm_campaign=github-mailgun-mcp&utm_content=readme)**

A managed catalog template is listed, but that is not evidence that its tools match this repository or that your account is connected. Complete provider setup and inspect the endpoint’s actual tool inventory and permissions before use.

Local credentials are not uploaded by this repository. A hosted endpoint uses its own PipMCP endpoint access token and published tool inventory; the local and hosted credentials are separate.

## Scope and validation

- The installed package has HTTP request fixtures and MCP stdio `tools/call` tests.
- Fixture success checks client behavior; it does not establish provider authentication, paid-plan access or live account execution.
- No live provider execution is claimed for this package by these tests.
- Hosted tool names, schemas, permissions and availability can differ from this local package. A queued or accepted job/message is not proof of completion or delivery. Provider API plans, account rules and limits apply.
- See [integration.json](./integration.json) for operation-scoped evidence and the provider-specific limits above.

## FAQ

**Do I need a Mailgun account?**

You need the provider account, token and permissions required by these operations. Provider charges and API limits remain with that account.

**Does this version require a PipMCP API key?**

This package uses your provider credentials for local execution. To use the hosted service, configure the provider connection in PipMCP and use the endpoint access token issued there.

**Why is a tool missing or a request rejected?**

This repository exposes the selected tools above. Check the required environment variables, provider scopes, account/region requirements and input IDs first. Hosted endpoints have their own published inventory and may exclude tools by role or policy. Do not retry a failed write without checking its outcome.

**Is this an official provider SDK or a hosted-service guarantee?**

This is an independently maintained MCP integration. Its fixtures and source references do not imply provider endorsement, unrestricted API access, or identical hosted behavior.

## Guides and related workflows

- [Mailgun integration guide](https://jannikco.github.io/pipmcp-connectors/integrations/mailgun/)
- [Mailgun workflow and tool sequence](https://jannikco.github.io/pipmcp-connectors/workflows/mailgun/)
- [More integration workflows](https://jannikco.github.io/pipmcp-connectors/workflows/)
- [Typed LeadAdapter API client](https://github.com/jannikco/linkedin-typescript-sdk)
- [LeadAdapter Python API client](https://github.com/jannikco/linkedin-python-sdk)
- [LinkedIn API quickstart](https://github.com/jannikco/linkedin-api)
- [LinkedIn people and post search](https://github.com/jannikco/linkedin-search-api)
- [LinkedIn post engagers](https://github.com/jannikco/linkedin-post-engagers)
- [LeadAdapter LinkedIn MCP server](https://github.com/jannikco/linkedin-mcp)
- [OpenAPI to MCP import starter](https://github.com/jannikco/openapi-to-mcp)
- [Swagger to MCP import starter](https://github.com/jannikco/swagger-to-mcp)
- [Mailgun API and credential reference](https://documentation.mailgun.com/docs/mailgun/api-reference/send/mailgun/domains)

Maintained by [jannikco](https://github.com/jannikco). MIT for this repository’s code; dependencies retain their licenses. [Report a reproducible issue](https://github.com/jannikco/mailgun-mcp/issues) with redacted input and error details. For credentials or security issues, use [private contact](https://pipmcp.com/contact).
