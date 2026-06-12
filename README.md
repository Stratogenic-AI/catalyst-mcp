# Catalyst MCP Server

Governance middleware for AI agents — hosted SSE endpoint.

## What it does

Catalyst puts a governance layer between your AI agent and consequential actions. Before your agent writes files, sends messages, deploys code, or calls external APIs, it checks in with Catalyst. Depending on your governance mode, actions are allowed, routed to a human for approval, or denied.

**Core features:**
- Pre-execution permission gates with a progressive trust model: `observe → advisory → proposal → strict`
- Human-in-the-loop approval flows — agents submit proposals, humans approve/decline
- Compliance scanning across 8 frameworks: GDPR, SOC 2, ISO 27001, HIPAA, PCI-DSS, EU AI Act, Bribery Act, AML/KYC
- Hash-linked audit ledger — every agent action is recorded with `touched_by.source=mcp`
- Webhook notifications for `proposal.accepted` / `proposal.declined` events

## Connect

Add to your MCP client config:

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
