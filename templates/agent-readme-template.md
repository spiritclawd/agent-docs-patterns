# [Project Name] - Agent Documentation

> Quick reference for AI agents integrating with this project.

---

## TL;DR for Agents

| What | Value |
|------|-------|
| **Type** | [API / SDK / Library / Service] |
| **Language** | [Node.js / Python / REST / etc.] |
| **Auth Required** | [Yes/No - specify type] |
| **Rate Limit** | [X requests per Y] |
| **Base URL** | `https://...` |

---

## Authentication

```
Method: [Bearer token / API key / OAuth / None]
Header: Authorization: Bearer <token>
Where to get credentials: [URL or instructions]
```

**Example authenticated request:**
```bash
curl -H "Authorization: Bearer YOUR_TOKEN" \
  https://api.example.com/v1/endpoint
```

---

## Endpoints

### [Endpoint Name]

```
[METHOD] /path/to/endpoint
```

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `param1` | string | yes | Description |
| `param2` | integer | no | Default: 10 |

**Request:**
```json
{
  "param1": "value",
  "param2": 20
}
```

**Response (200 OK):**
```json
{
  "result": "success",
  "data": { ... }
}
```

**Errors:**
| Code | Meaning | Action |
|------|---------|--------|
| 400 | Bad request | Check parameters |
| 401 | Unauthorized | Refresh token |
| 429 | Rate limited | Wait and retry |

---

## Common Patterns

### Pattern 1: [Name]
When to use: ...
How to implement: ...

### Pattern 2: [Name]
When to use: ...
How to implement: ...

---

## Agent-Specific Notes

- **Idempotency:** [Which endpoints are idempotent?]
- **Retry Logic:** [What should agents do on failure?]
- **Webhooks:** [How to set up event notifications?]
- **Polling:** [If webhooks unavailable, how to poll?]

---

## Version Compatibility

| Version | Status | Notes |
|---------|--------|-------|
| v1.x | Current | Recommended for new integrations |
| v0.x | Deprecated | Migrate by [date] |

---

*This documentation follows [Agent Documentation Patterns](https://github.com/spiritclawd/agent-docs-patterns)*
