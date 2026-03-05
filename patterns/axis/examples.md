# Axis API Examples

> Real request/response patterns for the Eternum agent CLI

---

## 1. World Discovery

### Request (CLI)
```bash
axis discover
```

### Response (JSON)
```json
{
  "worlds": [
    {
      "name": "eternum-slot-1",
      "chain": "slot",
      "status": "active",
      "address": "0x1234...",
      "tick_interval": 1000,
      "player_count": 12
    },
    {
      "name": "eternum-sepolia",
      "chain": "sepolia", 
      "status": "active",
      "address": "0x5678...",
      "tick_interval": 5000,
      "player_count": 45
    }
  ]
}
```

---

## 2. Headless Agent Start

### Request
```bash
axis run --headless --world=eternum-slot-1 --method=password
```

### Response (NDJSON stream)
```json
{"type":"log","level":"info","message":"Connecting to world eternum-slot-1..."}
{"type":"log","level":"info","message":"Authenticating via password..."}
{"type":"status","status":"authenticated","agent_id":"0xabcd..."}
{"type":"log","level":"info","message":"Loading contract ABIs..."}
{"type":"log","level":"info","message":"Starting tick loop (interval: 30s)"}
{"type":"tick","number":1,"state":{"realms":[],"resources":{}}}
{"type":"action","name":"claim_realm","params":{"realm_id":42}}
{"type":"tx","hash":"0xdef...","status":"pending"}
{"type":"tx","hash":"0xdef...","status":"confirmed"}
{"type":"tick","number":2,"state":{"realms":[{"id":42,"owner":"0xabcd..."}],"resources":{"food":100}}}
```

---

## 3. HTTP Control - Get Status

### Request
```http
GET http://localhost:3000/status
```

### Response
```json
{
  "status": "running",
  "world": "eternum-slot-1",
  "tick": 157,
  "agent_address": "0xabcd...",
  "last_action": "build_farm",
  "last_action_time": "2026-03-05T12:30:00Z",
  "pending_actions": 0,
  "resources": {
    "food": 450,
    "wood": 230,
    "stone": 100,
    "iron": 50,
    "gold": 25
  },
  "realms_owned": 2,
  "armies": 1
}
```

---

## 4. HTTP Control - Send Command

### Request
```http
POST http://localhost:3000/command
Content-Type: application/json

{
  "command": "execute",
  "params": {
    "action": "build_structure",
    "realm_id": 42,
    "structure_type": "farm"
  }
}
```

### Response (Success)
```json
{
  "status": "queued",
  "command_id": "cmd_abc123",
  "estimated_execution": "2026-03-05T12:31:00Z"
}
```

### Response (Error)
```json
{
  "status": "error",
  "error": {
    "code": "INSUFFICIENT_RESOURCES",
    "message": "Not enough wood to build farm",
    "required": {"wood": 50},
    "available": {"wood": 30},
    "action": "Wait for resource production or trade"
  }
}
```

---

## 5. Get Agent Memory

### Request
```http
GET http://localhost:3000/memory
```

### Response
```json
{
  "memory": {
    "strategy": "expansionist",
    "current_goal": "Claim 3 more realms in the north",
    "priority_actions": [
      "Move army to coordinate (150, 200)",
      "Build farm on realm 42",
      "Recruit knights for army 1"
    ],
    "threats": [
      {"type": "enemy_army", "location": [140, 195], "size": 500}
    ],
    "allies": [],
    "tick_history": [
      {"tick": 156, "action": "claim_realm", "result": "success"},
      {"tick": 155, "action": "move_army", "result": "success"}
    ]
  },
  "size": 2500,
  "last_updated": "2026-03-05T12:30:00Z"
}
```

---

## 6. Update Agent Memory

### Request
```http
POST http://localhost:3000/memory
Content-Type: application/json

{
  "updates": {
    "current_goal": "Defend realm 42 from incoming attack",
    "priority_actions": [
      "Recruit additional defenders",
      "Fortify realm 42"
    ],
    "threats": [
      {"type": "enemy_army", "location": [140, 195], "size": 500, "target": 42}
    ]
  }
}
```

### Response
```json
{
  "status": "updated",
  "memory_size": 2600,
  "updates_applied": 3
}
```

---

## 7. Error Handling

### Authentication Failed
```json
{
  "type": "error",
  "error": {
    "code": "AUTH_FAILED",
    "message": "Failed to authenticate with Cartridge Controller",
    "action": "Check credentials in ~/.eternum-agent/.env",
    "retry": false
  }
}
```

### World Not Found
```json
{
  "type": "error",
  "error": {
    "code": "WORLD_NOT_FOUND",
    "message": "World 'eternum-missing' not found",
    "action": "Run 'axis discover' to list available worlds",
    "available_worlds": ["eternum-slot-1", "eternum-sepolia"],
    "retry": false
  }
}
```

### Transaction Reverted
```json
{
  "type": "error",
  "error": {
    "code": "TX_REVERTED",
    "message": "Transaction 0xdef... reverted",
    "reason": "Cannot build on realm you don't own",
    "action": "Verify realm ownership before building",
    "retry": false
  }
}
```

---

## 8. LLM Tick Decision

### Internal Prompt Structure
```
You are playing Eternum, an onchain strategy game.

Current State:
- Realms: [{"id": 42, "owner": "you", "resources": {...}}]
- Armies: [{"id": 1, "location": [150, 200], "size": 100}]
- Resources: {"food": 450, "wood": 230, "stone": 100}

Available Actions:
1. claim_realm(realm_id) - Claim an unowned realm
2. build_structure(realm_id, type) - Build on your realm
3. move_army(army_id, destination) - Move army
4. attack(army_id, target_realm) - Attack enemy

Memory:
- Strategy: expansionist
- Current goal: Claim 3 more realms in north

What actions should you take this tick? Respond with JSON array.
```

### LLM Response
```json
[
  {"action": "move_army", "params": {"army_id": 1, "destination": [160, 210]}},
  {"action": "claim_realm", "params": {"realm_id": 47}}
]
```

---

*Part of [Agent Documentation Patterns](https://github.com/spiritclawd/agent-docs-patterns)*
