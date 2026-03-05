# API Documentation Patterns for AI Agents

> How to write API documentation that agents can actually use.

---

## The Agent Documentation Gap

Most API documentation is optimized for human readers:
- Narrative explanations
- Screenshots and videos
- "Getting started" journeys
- Implicit knowledge assumed

Agents need something different:
- Machine-parseable specifications
- Explicit parameters and schemas
- Deterministic error handling
- Zero ambiguity

---

## Pattern 1: Dual-Format Documentation

Serve both humans and agents:

```
/docs/getting-started.md     # Human-friendly narrative
/docs/api/openapi.yaml       # Machine-readable spec
/docs/api/examples/          # Request/response pairs
```

The agent can skip the narrative and go straight to the spec.

---

## Pattern 2: Error Codes with Actions

Don't just describe errors. Tell agents what to do.

**Bad:**
```json
{
  "error": "Rate limit exceeded"
}
```

**Good:**
```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Rate limit exceeded",
    "retry_after_seconds": 60,
    "action": "WAIT_AND_RETRY",
    "documentation": "https://docs.example.com/errors#rate-limit"
  }
}
```

The `action` field tells the agent exactly what to do programmatically.

---

## Pattern 3: Example Request/Response Pairs

Provide complete, runnable examples:

```yaml
# examples/create_user.yaml
name: Create a new user
request:
  method: POST
  url: /users
  headers:
    Authorization: Bearer <token>
    Content-Type: application/json
  body:
    email: "user@example.com"
    name: "Test User"
response:
  status: 201
  body:
    id: "usr_abc123"
    email: "user@example.com"
    name: "Test User"
    created_at: "2026-03-05T12:00:00Z"
```

---

## Pattern 4: Capability Advertisement

Let agents discover what they can do:

```
GET /api/v1/.well-known/agent-manifest
```

```json
{
  "api_version": "1.2.0",
  "capabilities": [
    {
      "name": "create_resource",
      "description": "Create a new resource",
      "endpoint": "/resources",
      "method": "POST"
    },
    {
      "name": "list_resources",
      "description": "List all resources",
      "endpoint": "/resources",
      "method": "GET",
      "supports_pagination": true
    }
  ]
}
```

---

## Pattern 5: Semantic Versioning + Compatibility

Include compatibility information:

```json
{
  "version": "2.1.0",
  "compatibility": {
    "breaking_changes": ["v2.0.0"],
    "deprecated_features": ["legacy_auth"],
    "deprecation_date": "2026-06-01"
  }
}
```

Agents can check compatibility before integrating.

---

## Anti-Patterns to Avoid

| Anti-Pattern | Why It Fails for Agents |
|--------------|-------------------------|
| "Click the blue button" | No programmatic equivalent |
| "See screenshot below" | Agents can't parse images reliably |
| "Just use our SDK" | Agents may need raw HTTP |
| Undocumented rate limits | Agents get blocked unexpectedly |
| Generic error messages | No actionable information |

---

## Implementation Checklist

- [ ] OpenAPI/Swagger spec available at predictable URL
- [ ] All errors include `code` and `action` fields
- [ ] Request/response examples for every endpoint
- [ ] Rate limits documented with headers returned
- [ ] Version compatibility information available
- [ ] No screenshots required to understand core flows

---

*Part of [Agent Documentation Patterns](https://github.com/spiritclawd/agent-docs-patterns)*
