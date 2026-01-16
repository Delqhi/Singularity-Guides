# API Reference

## Overview

Singularity Code provides a comprehensive HTTP API for programmatic access to all features, enabling integration with external tools and automation workflows.

## Base URL

```
https://api.singularity.ai/v1
```

## Authentication

### API Key Authentication

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
     https://api.singularity.ai/v1/sessions
```

### OAuth2 Flow

```typescript
// Client-side authentication
const auth = await fetch('/oauth/authorize', {
  method: 'POST',
  body: JSON.stringify({
    client_id: 'your-client-id',
    scope: 'read write'
  })
})

const token = await auth.json()
```

## Sessions API

### Create Session

```http
POST /sessions
Content-Type: application/json
Authorization: Bearer {token}

{
  "model": "opencode/big-pickle",
  "agent": "general",
  "permissions": {
    "bash": "allow",
    "read": "allow"
  }
}
```

**Response:**
```json
{
  "id": "session_123",
  "status": "active",
  "created_at": "2026-01-16T10:00:00Z",
  "model": "opencode/big-pickle",
  "agent": "general"
}
```

### Send Message

```http
POST /sessions/{session_id}/messages
Content-Type: application/json

{
  "content": "Create a React component for user login",
  "role": "user"
}
```

**Response:**
```json
{
  "id": "msg_456",
  "content": "// React login component code...",
  "role": "assistant",
  "timestamp": "2026-01-16T10:00:05Z"
}
```

### Get Session Status

```http
GET /sessions/{session_id}/status
```

**Response:**
```json
{
  "status": "active",
  "last_activity": "2026-01-16T10:00:05Z",
  "message_count": 42
}
```

## Tools API

### List Available Tools

```http
GET /tools
```

**Response:**
```json
{
  "tools": [
    {
      "name": "bash",
      "description": "Execute shell commands",
      "parameters": {
        "command": { "type": "string", "required": true },
        "timeout": { "type": "number", "default": 30000 }
      }
    }
  ]
}
```

### Execute Tool

```http
POST /tools/{tool_name}/execute
Content-Type: application/json

{
  "parameters": {
    "command": "ls -la"
  }
}
```

**Response:**
```json
{
  "success": true,
  "output": "total 48\ndrwxr-xr-x  12 user  staff   384 Jan 16 10:00 .\n...",
  "execution_time": 125
}
```

## Agents API

### List Agents

```http
GET /agents
```

**Response:**
```json
{
  "agents": [
    {
      "name": "general",
      "description": "General-purpose development agent",
      "mode": "primary",
      "permissions": ["read", "write", "bash"]
    }
  ]
}
```

### Create Custom Agent

```http
POST /agents
Content-Type: application/json

{
  "name": "my-agent",
  "description": "Custom agent",
  "prompt": "You are a specialized developer...",
  "permissions": {
    "bash": "allow",
    "read": "allow"
  },
  "model": "anthropic/claude-sonnet-4"
}
```

## Commands API

### Execute Command

```http
POST /commands/execute
Content-Type: application/json

{
  "command": "/review-pr",
  "args": ["https://github.com/owner/repo/pull/123"],
  "options": {
    "focus": "security"
  }
}
```

**Response:**
```json
{
  "result": "## PR Review\n\n### Security Issues\n- Potential XSS vulnerability in user input...",
  "execution_time": 2500
}
```

## Projects API

### Create Project

```http
POST /projects
Content-Type: application/json

{
  "name": "my-project",
  "description": "My awesome project",
  "template": "react-typescript",
  "settings": {
    "node_version": "18",
    "package_manager": "bun"
  }
}
```

### Get Project Status

```http
GET /projects/{project_id}/status
```

**Response:**
```json
{
  "id": "proj_123",
  "name": "my-project",
  "status": "active",
  "last_modified": "2026-01-16T10:30:00Z",
  "statistics": {
    "files": 42,
    "lines_of_code": 15420,
    "commits": 156
  }
}
```

## MCP (Model Context Protocol) API

### Register MCP Server

```http
POST /mcp/servers
Content-Type: application/json

{
  "name": "filesystem",
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-filesystem", "/allowed/path"],
  "env": {
    "NODE_ENV": "production"
  }
}
```

### List MCP Servers

```http
GET /mcp/servers
```

**Response:**
```json
{
  "servers": [
    {
      "name": "filesystem",
      "status": "connected",
      "capabilities": ["read", "write", "search"]
    }
  ]
}
```

### MCP Tool Call

```http
POST /mcp/{server_name}/tools/{tool_name}
Content-Type: application/json

{
  "parameters": {
    "path": "/project/src",
    "pattern": "*.ts"
  }
}
```

## WebSocket API

### Real-time Session Updates

```javascript
const ws = new WebSocket('wss://api.singularity.ai/v1/sessions/{session_id}/stream')

ws.onmessage = (event) => {
  const data = JSON.parse(event.data)
  
  switch (data.type) {
    case 'message':
      console.log('New message:', data.message)
      break
    case 'tool_execution':
      console.log('Tool executed:', data.tool)
      break
    case 'status_change':
      console.log('Status changed:', data.status)
      break
  }
}

// Send message
ws.send(JSON.stringify({
  type: 'message',
  content: 'Hello AI assistant'
}))
```

## Plugins API

### Install Plugin

```http
POST /plugins/install
Content-Type: application/json

{
  "name": "my-plugin",
  "version": "1.0.0",
  "source": "npm"
}
```

### List Plugins

```http
GET /plugins
```

**Response:**
```json
{
  "plugins": [
    {
      "name": "my-plugin",
      "version": "1.0.0",
      "status": "active",
      "tools": ["custom-tool"],
      "hooks": ["onMessage"]
    }
  ]
}
```

## Streaming API

### Streaming Responses

```http
POST /sessions/{session_id}/messages/stream
Content-Type: application/json

{
  "content": "Write a complex algorithm",
  "stream": true
}
```

**Response (chunked):**
```
data: {"type": "start"}

data: {"type": "token", "content": "function"}

data: {"type": "token", "content": " complexAlgorithm"}

data: {"type": "token", "content": "(input)"}

...

data: {"type": "end"}
```

## Error Handling

### Error Response Format

```json
{
  "error": {
    "code": "INVALID_REQUEST",
    "message": "The request parameters are invalid",
    "details": {
      "field": "model",
      "reason": "Model not supported"
    },
    "request_id": "req_123456"
  }
}
```

### Common Error Codes

- `INVALID_REQUEST`: Bad request parameters
- `UNAUTHORIZED`: Authentication failed
- `FORBIDDEN`: Insufficient permissions
- `NOT_FOUND`: Resource not found
- `RATE_LIMITED`: Too many requests
- `INTERNAL_ERROR`: Server error

## Rate Limiting

### Rate Limit Headers

```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640995200
X-RateLimit-Retry-After: 60
```

### Rate Limit Response

```http
HTTP/1.1 429 Too Many Requests
Content-Type: application/json
Retry-After: 60

{
  "error": {
    "code": "RATE_LIMITED",
    "message": "Rate limit exceeded",
    "retry_after": 60
  }
}
```

## SDKs and Libraries

### JavaScript SDK

```bash
npm install @singularity-ai/sdk
```

```typescript
import { Singularity } from '@singularity-ai/sdk'

const client = new Singularity({
  apiKey: 'your-api-key'
})

const session = await client.sessions.create({
  model: 'opencode/big-pickle'
})

const response = await session.sendMessage('Hello world')
```

### Python SDK

```bash
pip install singularity-ai
```

```python
from singularity import Client

client = Client(api_key='your-api-key')

session = client.sessions.create(model='opencode/big-pickle')
response = session.send_message('Hello world')
```

### Go SDK

```bash
go get github.com/singularity-ai/go-sdk
```

```go
package main

import (
    "github.com/singularity-ai/go-sdk"
)

func main() {
    client := singularity.NewClient("your-api-key")
    
    session, _ := client.Sessions.Create(&singularity.SessionConfig{
        Model: "opencode/big-pickle",
    })
    
    response, _ := session.SendMessage("Hello world")
}
```

## Webhooks

### Webhook Configuration

```http
POST /webhooks
Content-Type: application/json

{
  "url": "https://your-app.com/webhook",
  "events": ["session.completed", "tool.executed"],
  "secret": "your-webhook-secret"
}
```

### Webhook Payload

```json
{
  "event": "session.completed",
  "data": {
    "session_id": "session_123",
    "completed_at": "2026-01-16T10:30:00Z",
    "summary": "Task completed successfully"
  },
  "signature": "sha256=..."
}
```

### Webhook Verification

```typescript
const verifyWebhook = (payload: string, signature: string, secret: string): boolean => {
  const expected = crypto
    .createHmac('sha256', secret)
    .update(payload)
    .digest('hex')
  
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(`sha256=${expected}`)
  )
}
```

This comprehensive API reference enables full integration with Singularity Code's capabilities for automated development workflows.