# Get Execution Status

Poll the current status of an execution. Use this endpoint for real-time progress tracking.

## Endpoint

```
GET /api/v1/executions/{execution_id}/status
```

## Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| execution_id | string | The execution ID returned from create |

## Headers

```http
X-API-Key: sk_live_your_api_key_here
```

## Response

Status Code: `200 OK`

### Running

```json
{
  "execution_id": "exec_1a2b3c4d5e6f",
  "status": "running",
  "progress": 45,
  "current_step": "Analyzing data",
  "updated_at": "2025-11-11T10:30:15Z"
}
```

### Completed

```json
{
  "execution_id": "exec_1a2b3c4d5e6f",
  "status": "completed",
  "progress": 100,
  "result": "Task completed successfully with detailed results...",
  "metadata": {
    "execution_time": 12.5,
    "tokens_used": 1250
  },
  "completed_at": "2025-11-11T10:30:30Z"
}
```

### Failed

```json
{
  "execution_id": "exec_1a2b3c4d5e6f",
  "status": "failed",
  "error": {
    "code": "execution_error",
    "message": "Task execution failed",
    "details": "Detailed error information"
  },
  "failed_at": "2025-11-11T10:30:25Z"
}
```

## Examples

### cURL

```bash
curl https://api.izieroma.xyz/api/v1/executions/exec_1a2b3c4d5e6f/status \
  -H "X-API-Key: sk_live_your_key"
```

### Python with Polling

```python
import requests
import time

def wait_for_completion(execution_id, timeout=300):
    start_time = time.time()
    
    while True:
        if time.time() - start_time > timeout:
            raise TimeoutError("Execution timed out")
        
        response = requests.get(
            f"https://api.izieroma.xyz/api/v1/executions/{execution_id}/status",
            headers={"X-API-Key": "sk_live_your_key"}
        )
        
        status = response.json()
        
        if status["status"] == "completed":
            return status["result"]
        
        if status["status"] == "failed":
            raise Exception(status["error"]["message"])
        
        print(f"Progress: {status['progress']}% - {status.get('current_step', '')}")
        time.sleep(2)

result = wait_for_completion("exec_1a2b3c4d5e6f")
print(result)
```

### JavaScript with Async/Await

```javascript
async function waitForCompletion(executionId, timeout = 300000) {
  const startTime = Date.now();
  
  while (true) {
    if (Date.now() - startTime > timeout) {
      throw new Error("Execution timed out");
    }
    
    const response = await fetch(
      `https://api.izieroma.xyz/api/v1/executions/${executionId}/status`,
      { headers: { "X-API-Key": "sk_live_your_key" } }
    );
    
    const status = await response.json();
    
    if (status.status === "completed") {
      return status.result;
    }
    
    if (status.status === "failed") {
      throw new Error(status.error.message);
    }
    
    console.log(`Progress: ${status.progress}% - ${status.current_step || ""}`);
    await new Promise(resolve => setTimeout(resolve, 2000));
  }
}

const result = await waitForCompletion("exec_1a2b3c4d5e6f");
console.log(result);
```

## Response Fields

| Field | Type | Description |
|-------|------|-------------|
| execution_id | string | Unique execution identifier |
| status | string | Current status (running/completed/failed/cancelled) |
| progress | integer | Progress percentage (0-100) |
| current_step | string | Current execution step description |
| result | string | Final result (only when completed) |
| error | object | Error details (only when failed) |
| metadata | object | Execution metadata |
| updated_at | string | Last update timestamp |
| completed_at | string | Completion timestamp (only when completed) |
| failed_at | string | Failure timestamp (only when failed) |

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

### 401 Unauthorized

```json
{
  "error": {
    "code": "unauthorized",
    "message": "Invalid or revoked API key"
  }
}
```

## Polling Best Practices

### Exponential Backoff

```python
def poll_with_backoff(execution_id):
    delay = 1
    max_delay = 30
    
    while True:
        status = get_status(execution_id)
        
        if status["status"] in ["completed", "failed", "cancelled"]:
            return status
        
        time.sleep(delay)
        delay = min(delay * 1.5, max_delay)
```

### Rate-Limited Polling

```python
from datetime import datetime, timedelta

class RateLimitedPoller:
    def __init__(self, max_requests_per_minute=30):
        self.max_requests = max_requests_per_minute
        self.request_times = []
    
    def poll(self, execution_id):
        self._enforce_rate_limit()
        return get_status(execution_id)
    
    def _enforce_rate_limit(self):
        now = datetime.now()
        cutoff = now - timedelta(minutes=1)
        self.request_times = [t for t in self.request_times if t > cutoff]
        
        if len(self.request_times) >= self.max_requests:
            sleep_time = 60 - (now - self.request_times[0]).seconds
            time.sleep(sleep_time)
        
        self.request_times.append(now)

poller = RateLimitedPoller()
status = poller.poll("exec_1a2b3c4d5e6f")
```

## Next Steps

- [Get Execution Details](get-details.md)
- [Cancel Execution](cancel-execution.md)
- [Response Schemas](schemas.md)
