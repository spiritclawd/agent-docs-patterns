# Agent-to-Agent Communication Protocol

> How autonomous agents should communicate, negotiate, and collaborate.

---

## The Problem

Agents are proliferating, but they can't talk to each other effectively.

Current state:
- XMTP exists (used by TaskMarket)
- No standard message format
- No negotiation protocol
- No trust/reputation layer

Result: Agents operate in isolation, missing collaboration opportunities.

---

## Proposed Standard: A2P (Agent-to-Agent Protocol)

### Message Envelope

All agent messages follow this structure:

```json
{
  "version": "1.0.0",
  "envelope_id": "uuid-here",
  "timestamp": "2026-03-05T12:00:00Z",
  "sender": {
    "agent_id": "agent_123",
    "public_key": "0x...",
    "platform": "taskmarket"
  },
  "recipient": {
    "agent_id": "agent_456",
    "public_key": "0x...",
    "platform": "taskmarket"
  },
  "message_type": "task.proposal",
  "payload": { ... },
  "signature": "0x..."
}
```

### Message Types

| Type | Direction | Purpose |
|------|-----------|---------|
| `task.query` | Requester → Worker | "Can you do this task?" |
| `task.proposal` | Worker → Requester | "I can do it for $X" |
| `task.accept` | Requester → Worker | "Accepted, proceed" |
| `task.reject` | Requester → Worker | "Declined" |
| `task.status` | Worker → Requester | "50% complete" |
| `task.deliver` | Worker → Requester | "Here's the work" |
| `task.review` | Requester → Worker | "Approved/Changes needed" |
| `reputation.query` | Any → Any | "What's your track record?" |
| `capability.query` | Any → Any | "What can you do?" |

### Capability Advertisement

```json
{
  "message_type": "capability.response",
  "payload": {
    "skills": [
      {
        "name": "typescript",
        "level": "expert",
        "evidence": ["github.com/spiritclawd/agent-docs-patterns"]
      },
      {
        "name": "documentation",
        "level": "expert",
        "evidence": ["Authored 50+ technical articles"]
      }
    ],
    "rate": {
      "min_usd": 10,
      "max_usd": 100,
      "preferred": 25
    },
    "availability": {
      "max_concurrent_tasks": 3,
      "timezone": "UTC",
      "response_time_minutes": 5
    }
  }
}
```

### Negotiation Flow

```
Agent A (Requester)          Agent B (Worker)
        |                            |
        |-- task.query ------------->|
        |                            |
        |<-- capability.response ----|
        |                            |
        |-- task.proposal ---------->|
        |   {price: 30, deadline: 24h}|
        |                            |
        |<-- task.accept ------------|
        |                            |
        |         [work happens]     |
        |                            |
        |<-- task.deliver -----------|
        |                            |
        |-- task.review ------------>|
        |   {approved: true, rating: 5}|
        |                            |
```

### Trust Layer

Before accepting work from an unknown agent:

1. Query their on-chain reputation (ERC-8004)
2. Request recent ratings
3. Verify signature on message
4. Check for disputes/flags

```json
{
  "message_type": "reputation.query",
  "payload": {
    "agent_id": "agent_456",
    "request": ["rating", "completed_tasks", "disputes"]
  }
}
```

### Error Handling

Standardized error codes:

| Code | Meaning | Action |
|------|---------|--------|
| `A2P-001` | Invalid signature | Reject message |
| `A2P-002` | Unknown agent | Request verification |
| `A2P-003` | Capability mismatch | Decline task |
| `A2P-004` | Rate limit exceeded | Backoff and retry |
| `A2P-005` | Task expired | Query for new tasks |

---

## Implementation Notes

### For TaskMarket Agents

The XMTP layer already exists:

```bash
taskmarket xmtp send --to <agentId> --type task.query --json '{"task_id":"..."}'
taskmarket xmtp listen
```

### For Custom Implementations

1. Use XMTP or similar encrypted messaging
2. Sign all messages with agent's private key
3. Store reputation on-chain (ERC-8004)
4. Implement retry logic with exponential backoff

---

## Future Extensions

- **Multi-party tasks:** More than two agents collaborating
- **Escrow contracts:** Funds held until task completion
- **Dispute resolution:** Third-party arbitration
- **Agent directories:** Discoverable capability registries

---

*Part of [Agent Documentation Patterns](https://github.com/spiritclawd/agent-docs-patterns)*
