---
name: perry-coding-agents
description: Send wake callbacks to Perry coding agents. Use when you need to notify a Perry workspace that a task is complete or send messages back to the user.
---

# Perry Coding Agents - Wake Callbacks

Perry coding agents can receive wake callbacks via HTTP webhooks. This allows agents to notify users when tasks are complete or send status updates.

## Webhook Endpoint

The wake callback endpoint is:

```
POST /hooks/wake
```

## Authentication

Wake callbacks use a separate token from the gateway auth. The token is configured in `hooks.token` (not `gateway.auth.token`).

## Sending a Wake Callback

```bash
# Basic wake callback
curl -X POST "http://${WAKE_IP}:18789/hooks/wake" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ${HOOKS_TOKEN}" \
  -d '{"text": "Task completed successfully", "mode": "now"}'
```

Where:
- `${WAKE_IP}` - The IP address or hostname of the Perry agent
- `${HOOKS_TOKEN}` - The token from config `hooks.token`
- `mode` - Either `"now"` for immediate delivery or other scheduling options

## Request Format

```json
{
  "text": "Your message here",
  "mode": "now"
}
```

## Example: Notify on Task Completion

```bash
# After completing a coding task, notify the user
curl -X POST "http://${WAKE_IP}:18789/hooks/wake" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ${HOOKS_TOKEN}" \
  -d '{"text": "Done: Refactored authentication module and added tests", "mode": "now"}'
```

## Example: Status Update

```bash
# Send a progress update
curl -X POST "http://${WAKE_IP}:18789/hooks/wake" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ${HOOKS_TOKEN}" \
  -d '{"text": "Progress: 3 of 5 files migrated", "mode": "now"}'
```

## Configuration

The hooks token is configured separately from gateway authentication:

```yaml
hooks:
  token: "your-hooks-token-here"
```

This is distinct from `gateway.auth.token` which is used for other API endpoints.

## Gotchas

- Use `/hooks/wake` not `/api/wake` - the endpoint changed
- Use `hooks.token` not `gateway.auth.token` - these are separate tokens
- The default port is `18789`
- Ensure the Perry agent is running and reachable on your network
