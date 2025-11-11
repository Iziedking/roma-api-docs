# Create Execution

Create a new task execution. The task is processed asynchronously, and you receive an `execution_id` for tracking.

## Endpoint

```
POST /api/v1/executions
```

## Headers

```http
Content-Type: application/json
X-API-Key: sk_live_your_api_key_here
```

## Request Body

```json
{
  "goal": "string",
  "max_depth": 3,
  "config_profile": "general",
  "config_overrides": {},
  "metadata": {}
}
```

### Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| goal | string | Yes | - | Task description |
| max_depth | integer | No | 3 | Decomposition depth (1-10) |
| config_profile | string | No | general | Configuration preset |
| config_overrides | object | No | {} | Override configuration |
| metadata | object | No | {} | Custom metadata |

## Response

Status Code: `202 Accepted`

```json
{
  "execution_id": "exec_1a2b3c4d5e6f",
  "status": "running",
  "initial_goal": "Your task description",
  "config_profile": "general",
  "max_depth": 3,
  "created_at": "2025-11-11T10:30:00Z",
  "updated_at": "2025-11-11T10:30:00Z"
}
```

## Examples

### Basic Request

```bash
curl -X POST https://api.izieroma.xyz/api/v1/executions \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sk_live_your_key" \
  -d '{
    "goal": "What is the capital of France?"
  }'
```

### With Configuration

```bash
curl -X POST https://api.izieroma.xyz/api/v1/executions \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sk_live_your_key" \
  -d '{
    "goal": "Research AI trends in 2025",
    "max_depth": 5,
    "config_profile": "high_quality"
  }'
```

### With Overrides

```bash
curl -X POST https://api.izieroma.xyz/api/v1/executions \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sk_live_your_key" \
  -d '{
    "goal": "Analyze Bitcoin price",
    "config_profile": "crypto_agent",
    "config_overrides": {
      "temperature": 0.5,
      "max_tokens": 6000
    },
    "metadata": {
      "user_id": "user_123"
    }
  }'
```

### Python

```python
import requests

headers = {
    "Content-Type": "application/json",
    "X-API-Key": "sk_live_your_key"
}

data = {
    "goal": "Create a market analysis report",
    "max_depth": 4,
    "config_profile": "high_quality"
}

response = requests.post(
    "https://api.izieroma.xyz/api/v1/executions",
    headers=headers,
    json=data
)

execution = response.json()
print(f"Created: {execution['execution_id']}")
```

### JavaScript

```javascript
const response = await fetch("https://api.izieroma.xyz/api/v1/executions", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "X-API-Key": "sk_live_your_key"
  },
  body: JSON.stringify({
    goal: "Create a market analysis report",
    max_depth: 4,
    config_profile: "high_quality"
  })
});

const execution = await response.json();
console.log(`Created: ${execution.execution_id}`);
```

## Error Responses

### 400 Bad Request

```json
{
  "error": {
    "code": "invalid_request",
    "message": "The 'goal' field is required"
  }
}
```

### 401 Unauthorized

```json
{
  "error": {
    "code": "unauthorized",
    "message": "Invalid or revoked API key"
  }
}
```

### 429 Rate Limited

```json
{
  "error": {
    "code": "rate_limit_exceeded",
    "message": "Daily limit of 1000 requests exceeded"
  }
}
```

## Validation Rules

### goal

- Required
- String type
- 1-10,000 characters
- Cannot be empty or whitespace only

### max_depth

- Optional
- Integer type
- Range: 1-10
- Default: 3

### config_profile

- Optional
- String type
- Valid values: `general`, `high_quality`, `fast`, `crypto_agent`
- Default: `general`

### config_overrides

- Optional
- Object type
- Valid keys: `temperature`, `max_tokens`, `model`

### metadata

- Optional
- Object type
- Max 10 key-value pairs
- Keys: 1-50 characters
- Values: 1-500 characters

## Next Steps

After creating an execution:

1. [Get Execution Status](get-status.md)
2. [Get Execution Details](get-details.md)
3. [Cancel Execution](cancel-execution.md)
