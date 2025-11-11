# Cancel Execution

Cancel a running execution. Once cancelled, the execution cannot be resumed.

## Endpoint

```
POST /api/v1/executions/{execution_id}/cancel
```

## Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| execution_id | string | The execution ID to cancel |

## Headers

```http
X-API-Key: sk_live_your_api_key_here
```

## Response

Status Code: `200 OK`

```json
{
  "execution_id": "exec_1a2b3c4d5e6f",
  "status": "cancelled",
  "cancelled_at": "2025-11-11T10:30:45Z"
}
```

## Examples

### cURL

```bash
curl -X POST https://api.izieroma.xyz/api/v1/executions/exec_1a2b3c4d5e6f/cancel \
  -H "X-API-Key: sk_live_your_key"
```

### Python

```python
import requests

response = requests.post(
    "https://api.izieroma.xyz/api/v1/executions/exec_1a2b3c4d5e6f/cancel",
    headers={"X-API-Key": "sk_live_your_key"}
)

result = response.json()
print(f"Execution {result['execution_id']} cancelled")
```

### JavaScript

```javascript
const response = await fetch(
  "https://api.izieroma.xyz/api/v1/executions/exec_1a2b3c4d5e6f/cancel",
  {
    method: "POST",
    headers: { "X-API-Key": "sk_live_your_key" }
  }
);

const result = await response.json();
console.log(`Execution ${result.execution_id} cancelled`);
```

## Use Cases

### Timeout Protection

```python
import threading

def execute_with_timeout(goal, timeout=300):
    execution = client.execute(goal=goal)
    execution_id = execution["execution_id"]
    
    timer = threading.Timer(timeout, lambda: client.cancel(execution_id))
    timer.start()
    
    try:
        result = wait_for_completion(execution_id)
        timer.cancel()
        return result
    except TimeoutError:
        print(f"Execution {execution_id} timed out and was cancelled")
```

### User Cancellation

```python
def cancellable_execution(goal):
    execution = client.execute(goal=goal)
    execution_id = execution["execution_id"]
    
    print(f"Execution {execution_id} started")
    print("Press Ctrl+C to cancel")
    
    try:
        return wait_for_completion(execution_id)
    except KeyboardInterrupt:
        print("\nCancelling execution...")
        client.cancel(execution_id)
        print("Execution cancelled by user")
```

## Error Responses

### 404 Not Found

```json
{
  "error": {
    "code": "not_found",
    "message": "Execution not found"
  }
}
```

### 400 Bad Request

```json
{
  "error": {
    "code": "invalid_operation",
    "message": "Cannot cancel execution in completed state"
  }
}
```
