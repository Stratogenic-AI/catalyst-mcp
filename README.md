# Catalyst MCP Server

[![Catalyst MCP server](https://smithery.ai/badge/markjsmith311/catalyst)](https://smithery.ai/servers/markjsmith311/catalyst)

Governance middleware for AI agents — hosted MCP server with both SSE and Streamable HTTP transports.

## What it does

Catalyst puts a governance layer between your AI agent and consequential actions. Before your agent writes files, sends messages, deploys code, or calls external APIs, it checks in with Catalyst. Depending on your governance mode, actions are allowed, routed to a human for approval, or denied.

**Core features:**
- Pre-execution permission gates with a progressive trust model: `observe → advisory → proposal → strict`
- Human-in-the-loop approval flows — agents submit proposals, humans approve/decline
- Compliance scanning across 8 frameworks: GDPR, SOC 2, ISO 27001, HIPAA, PCI-DSS, EU AI Act, Bribery Act, AML/KYC
- Hash-linked audit ledger — every agent action is recorded with `touched_by.source=mcp`
- Webhook notifications for `proposal.accepted` / `proposal.declined` events

## Use with Claude Code

Claude Code is a primary use case Catalyst was designed for. Claude Code governs itself session-locally — it asks before running dangerous commands, but those decisions leave no audit trail, survive no context reset, and sit outside your organisation's approval workflows.

Catalyst externalises that governance. Once connected, every consequential action Claude Code attempts is routed through your policy, logged in an immutable ledger, and (if required) held for human approval in the Catalyst dashboard before Claude Code proceeds.

**Connect in one command:**

```bash
claude mcp add catalyst \
  --transport sse \
  --url https://catalyst.stratogenic.ai/mcp/sse \
  --header "X-API-Key: SGC_your_key_here"
```

Or with Streamable HTTP (Claude Code 0.10+):
```bash
claude mcp add catalyst \
  --transport http \
  --url https://catalyst.stratogenic.ai/mcp \
  --header "X-API-Key: SGC_your_key_here"
```

Claude Code will automatically call `catalyst_check_action` before destructive operations and `catalyst_await_approval` when a proposal is required. Every action is ledgered with `touched_by.source=mcp` — you get a permanent, verifiable record of what your AI coding agent did and when.

**What governance looks like in practice:**

| Claude Code action | Catalyst response (proposal mode) |
|--------------------|-----------------------------------|
| `git push --force` | `proposal_required` — held for approval |
| `rm -rf ./dist` | `allow` — whitelisted build action |
| Deploy to production | `proposal_required` — held for approval |
| Edit source files | `allow` — within declared capabilities |
| Call external API | `proposal_required` — undeclared capability |

Set your governance mode to `observe` to start with a full audit trail only, `advisory` to flag deviations without blocking, `proposal` to require human sign-off on sensitive actions, or `strict` to allow only explicitly whitelisted capabilities.

Get your API key at [catalyst.stratogenic.ai](https://catalyst.stratogenic.ai).

---

## Connect

**Streamable HTTP** (preferred — required by Glama, Smithery, and newer clients):

```json
{
  "mcpServers": {
    "catalyst": {
      "url": "https://catalyst.stratogenic.ai/mcp",
      "transport": "http",
      "headers": { "X-API-Key": "<your-key>" }
    }
  }
}
```

**SSE** (for clients that don't yet support Streamable HTTP):

```json
{
  "mcpServers": {
    "catalyst": {
      "url": "https://catalyst.stratogenic.ai/mcp/sse",
      "transport": "sse",
      "headers": { "X-API-Key": "<your-key>" }
    }
  }
}
```

Get your API key at [catalyst.stratogenic.ai](https://catalyst.stratogenic.ai).

## Tools

| Tool | Description |
|------|-------------|
| `catalyst_my_governance` | Show your governance profile and plan capabilities |
| `catalyst_check_action` | Check if a capability is permitted before acting |
| `catalyst_log_task` | Record a task or action into the governance pipeline |
| `catalyst_pending_approvals` | List proposals awaiting human sign-off |
| `catalyst_await_approval` | Poll a specific proposal's approval status |
| `catalyst_register_approval_webhook` | Register a callback URL for approval events |
| `catalyst_run_compliance_scan` | Run an automated compliance scan (requires add-on) |
| `catalyst_list_compliance_frameworks` | List available compliance frameworks |
| `catalyst_get_dashboard` | Get the governance dashboard snapshot |
| `catalyst_export_ledger` | Export the hash-linked audit chain |

## Governance loop

```
catalyst_my_governance()            # understand your mode + capabilities
    ↓
catalyst_check_action("write_file") # allow | proposal_required | deny
    ↓ if proposal_required
catalyst_log_task("Deploy to prod") # creates proposal, returns proposal_id
    ↓
catalyst_await_approval(proposal_id) # poll until approved: true
    ↓ proceed
```

## Links

- Homepage: [catalyst.stratogenic.ai](https://catalyst.stratogenic.ai)
- Smithery: [smithery.ai/servers/markjsmith311/catalyst](https://smithery.ai/servers/markjsmith311/catalyst)
- Official MCP Registry: [registry.modelcontextprotocol.io](https://registry.modelcontextprotocol.io/v0.1/servers?search=Stratogenic-AI)
- mcp.so: [mcp.so/server/catalyst-governance](https://mcp.so/server/catalyst-governance)
- Glama: private gateway connector — 31 tools via Streamable HTTP
