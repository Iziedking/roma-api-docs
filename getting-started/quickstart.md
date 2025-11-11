# Quick Start Guide

Get up and running with ROMA API in under 5 minutes. This guide walks you through making your first API call and understanding the response.

## Prerequisites

- Valid API key ([request here](request-api-key.md))
- HTTP client (curl, Postman, or code library)
- Internet connection

## Your First Request

### Using cURL

```bash
curl -X POST https://api.izieroma.xyz/api/v1/executions \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sk_live_your_api_key_here" \
  -d '{
    "goal": "What is the capital of France?"
  }'
```

### Response

```json
{
  "execution_id": "exec_1a2b3c4d5e6f",
  "status": "running",
  "initial_goal": "What is the capital of France?",
  "created_at": "2025-11-11T10:30:00Z",
  "updated_at": "2025-11-11T10:30:00Z"
}
```

## Understanding the Response

### Execution ID

```json
"execution_id": "exec_1a2b3c4d5e6f"
```

This unique identifier tracks your request. Save it to check status and retrieve results.

### Status

```json
"status": "running"
```

Possible values:

- `running`: Task is being processed
- `completed`: Task finished successfully
- `failed`: Task encountered an error
- `cancelled`: Task was cancelled by user

## Checking Status

Use the execution ID to poll for completion:

```bash
curl https://api.izieroma.xyz/api/v1/executions/exec_1a2b3c4d5e6f/status \
  -H "X-API-Key: sk_live_your_api_key_here"
```

### Response When Running

```json
{
  "execution_id": "exec_1a2b3c4d5e6f",
  "status": "running",
  "progress": 45,
  "current_step": "Analyzing query",
  "updated_at": "2025-11-11T10:30:15Z"
}
```

### Response When Complete

```json
{
  "execution_id": "exec_1a2b3c4d5e6f",
  "status": "completed",
  "progress": 100,
  "result": "The capital of France is Paris.",
  "metadata": {
    "execution_time": 8.5,
    "tokens_used": 450
  },
  "updated_at": "2025-11-11T10:30:25Z"
}
```

## Getting Full Details

Retrieve complete execution information:

```bash
curl https://api.izieroma.xyz/api/v1/executions/exec_1a2b3c4d5e6f \
  -H "X-API-Key: sk_live_your_api_key_here"
```

### Complete Response

```json
{
  "execution_id": "exec_1a2b3c4d5e6f",
  "status": "completed",
  "initial_goal": "What is the capital of France?",
  "result": "The capital of France is Paris.",
  "dag": {
    "nodes": [
      {
        "id": "node_1",
        "type": "research",
        "status": "completed"
      }
    ]
  },
  "metadata": {
    "execution_time": 8.5,
    "tokens_used": 450,
    "agent_calls": 3
  },
  "created_at": "2025-11-11T10:30:00Z",
  "updated_at": "2025-11-11T10:30:25Z",
  "completed_at": "2025-11-11T10:30:25Z"
}
```

## Code Examples

### Python

```python
import requests
import time

api_key = "sk_live_your_api_key_here"
base_url = "https://api.izieroma.xyz/api/v1"

headers = {
    "Content-Type": "application/json",
    "X-API-Key": api_key
}

data = {
    "goal": "What is the capital of France?"
}

response = requests.post(
    f"{base_url}/executions",
    headers=headers,
    json=data
)

execution = response.json()
execution_id = execution["execution_id"]

print(f"Created execution: {execution_id}")

while True:
    status_response = requests.get(
        f"{base_url}/executions/{execution_id}/status",
        headers=headers
    )
    
    status = status_response.json()
    
    if status["status"] == "completed":
        print(f"Result: {status['result']}")
        break
    
    print(f"Status: {status['status']} - {status.get('current_step', '')}")
    time.sleep(2)
```

### JavaScript

```javascript
const apiKey = "sk_live_your_api_key_here";
const baseUrl = "https://api.izieroma.xyz/api/v1";

async function executeTask() {
  const response = await fetch(`${baseUrl}/executions`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "X-API-Key": apiKey
    },
    body: JSON.stringify({
      goal: "What is the capital of France?"
    })
  });

  const execution = await response.json();
  const executionId = execution.execution_id;

  console.log(`Created execution: ${executionId}`);

  while (true) {
    const statusResponse = await fetch(
      `${baseUrl}/executions/${executionId}/status`,
      {
        headers: { "X-API-Key": apiKey }
      }
    );

    const status = await statusResponse.json();

    if (status.status === "completed") {
      console.log(`Result: ${status.result}`);
      break;
    }

    console.log(`Status: ${status.status} - ${status.current_step || ""}`);
    await new Promise(resolve => setTimeout(resolve, 2000));
  }
}

executeTask();
```

## Complex Example

### Research Task

```bash
curl -X POST https://api.izieroma.xyz/api/v1/executions \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sk_live_your_api_key_here" \
  -d '{
    "goal": "Research the top 3 programming languages for web development in 2025 and explain why",
    "max_depth": 4,
    "config_profile": "high_quality"
  }'
```

### Advanced Configuration

```bash
curl -X POST https://api.izieroma.xyz/api/v1/executions \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sk_live_your_api_key_here" \
  -d '{
    "goal": "Analyze Bitcoin price trends",
    "max_depth": 3,
    "config_profile": "crypto_agent",
    "config_overrides": {
      "temperature": 0.7,
      "max_tokens": 4000
    },
    "metadata": {
      "user_id": "user_123",
      "source": "mobile_app"
    }
  }'
```

## Common Patterns

### Fire and Forget

Create execution and handle results asynchronously:

```python
response = requests.post(f"{base_url}/executions", headers=headers, json=data)
execution_id = response.json()["execution_id"]

print(f"Task started: {execution_id}")
```

### Blocking Wait

Wait for completion before continuing:

```python
execution_id = create_execution(goal)
result = wait_for_completion(execution_id)
process_result(result)
```

### Webhook Callback

Set up webhook for automatic notifications:

```python
data = {
    "goal": "Research market trends",
    "metadata": {
        "webhook_url": "https://yourapp.com/webhook",
        "callback_token": "secret_token"
    }
}
```

## Best Practices

### Poll Responsibly

```python
import time

def poll_status(execution_id, interval=2, max_attempts=150):
    attempts = 0
    
    while attempts < max_attempts:
        status = get_status(execution_id)
        
        if status["status"] in ["completed", "failed", "cancelled"]:
            return status
        
        time.sleep(interval)
        attempts += 1
    
    raise TimeoutError("Execution timed out")
```

### Handle Errors

```python
try:
    response = requests.post(f"{base_url}/executions", headers=headers, json=data)
    response.raise_for_status()
    execution = response.json()
except requests.exceptions.HTTPError as e:
    if e.response.status_code == 401:
        print("Invalid API key")
    elif e.response.status_code == 429:
        print("Rate limit exceeded")
    else:
        print(f"Error: {e.response.json()}")
```

### Timeout Protection

```python
import signal

class TimeoutException(Exception):
    pass

def timeout_handler(signum, frame):
    raise TimeoutException("Request timed out")

signal.signal(signal.SIGALRM, timeout_handler)
signal.alarm(300)

try:
    result = wait_for_completion(execution_id)
finally:
    signal.alarm(0)
```

## Next Steps

Now that you've made your first request:

1. [Learn about authentication](authentication.md)
2. [Explore configuration profiles](configuration.md)
3. [Read the complete API reference](../api-reference/README.md)
4. [Try integration guides](../integration-guides/README.md)
5. [Browse real examples](../examples/README.md)

## Troubleshooting

### Common Issues

**401 Unauthorized**
- Check API key is correct
- Verify key is active
- Ensure X-API-Key header is set

**429 Rate Limited**
- Reduce request frequency
- Implement exponential backoff
- Contact us for higher limits

**500 Server Error**
- Retry with exponential backoff
- Check system status
- Contact support if persistent

## Support

Need help getting started?

- Email: iziedking17@gmail.com
- X: [@Iziedking](https://x.com/Iziedking)
- [FAQ](../support/faq.md)
- [Troubleshooting Guide](../support/troubleshooting.md)
