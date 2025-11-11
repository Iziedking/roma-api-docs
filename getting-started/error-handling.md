# Error Handling

Comprehensive guide to handling errors and implementing retry logic with ROMA API.

## HTTP Status Codes

| Code | Status | Description |
|------|--------|-------------|
| 200 | OK | GET requests successful |
| 202 | Accepted | Execution created successfully |
| 400 | Bad Request | Invalid request parameters |
| 401 | Unauthorized | Missing or invalid API key |
| 403 | Forbidden | API key lacks permissions |
| 404 | Not Found | Resource doesn't exist |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Server-side error |
| 503 | Service Unavailable | Service temporarily down |

## Error Response Format

All errors return consistent JSON structure:

```json
{
  "error": {
    "code": "invalid_request",
    "message": "The 'goal' field is required",
    "details": {
      "field": "goal",
      "issue": "missing_required_field"
    }
  },
  "request_id": "req_abc123"
}
```

## Common Errors

### 400 Bad Request

Missing or invalid parameters.

```json
{
  "error": {
    "code": "invalid_request",
    "message": "The 'goal' field is required"
  }
}
```

**Causes:**
- Missing required fields
- Invalid data types
- Out of range values

**Solution:**

```python
try:
    execution = client.execute(goal="Your task")
except requests.exceptions.HTTPError as e:
    if e.response.status_code == 400:
        error = e.response.json()
        print(f"Invalid request: {error['error']['message']}")
```

### 401 Unauthorized

Authentication failed.

```json
{
  "error": {
    "code": "unauthorized",
    "message": "Invalid or revoked API key"
  }
}
```

**Causes:**
- Missing API key
- Invalid API key
- Revoked API key

**Solution:**

```python
try:
    execution = client.execute(goal="Your task")
except requests.exceptions.HTTPError as e:
    if e.response.status_code == 401:
        print("Check your API key")
        print("Request new key: iziedking17@gmail.com")
```

### 429 Rate Limited

Too many requests.

```json
{
  "error": {
    "code": "rate_limit_exceeded",
    "message": "Daily limit of 1000 requests exceeded",
    "details": {
      "limit": 1000,
      "remaining": 0,
      "reset_at": "2025-11-12T00:00:00Z"
    }
  }
}
```

**Solution:**

```python
import time

def execute_with_retry(goal, max_retries=3):
    for attempt in range(max_retries):
        try:
            return client.execute(goal=goal)
        except requests.exceptions.HTTPError as e:
            if e.response.status_code == 429:
                reset_time = e.response.json()["error"]["details"]["reset_at"]
                print(f"Rate limited. Reset at: {reset_time}")
                if attempt < max_retries - 1:
                    time.sleep(60)
            else:
                raise
```

### 500 Internal Server Error

Server-side error.

```json
{
  "error": {
    "code": "internal_error",
    "message": "An unexpected error occurred",
    "request_id": "req_abc123"
  }
}
```

**Solution:**

```python
import time

def execute_with_exponential_backoff(goal, max_retries=3):
    for attempt in range(max_retries):
        try:
            return client.execute(goal=goal)
        except requests.exceptions.HTTPError as e:
            if e.response.status_code >= 500:
                if attempt < max_retries - 1:
                    wait_time = 2 ** attempt
                    print(f"Server error. Retrying in {wait_time}s...")
                    time.sleep(wait_time)
                else:
                    raise
            else:
                raise
```

## Retry Strategies

### Exponential Backoff

```python
import time
import random

def exponential_backoff(
    func,
    max_retries=5,
    base_delay=1,
    max_delay=60
):
    for attempt in range(max_retries):
        try:
            return func()
        except Exception as e:
            if attempt == max_retries - 1:
                raise
            
            delay = min(base_delay * (2 ** attempt), max_delay)
            jitter = random.uniform(0, delay * 0.1)
            total_delay = delay + jitter
            
            print(f"Retry {attempt + 1}/{max_retries} in {total_delay:.2f}s")
            time.sleep(total_delay)

execution = exponential_backoff(
    lambda: client.execute(goal="Your task")
)
```

### Retry with Specific Errors

```python
def should_retry(exception):
    if isinstance(exception, requests.exceptions.HTTPError):
        status_code = exception.response.status_code
        return status_code in [429, 500, 502, 503, 504]
    return False

def execute_with_smart_retry(goal, max_retries=3):
    for attempt in range(max_retries):
        try:
            return client.execute(goal=goal)
        except Exception as e:
            if should_retry(e) and attempt < max_retries - 1:
                time.sleep(2 ** attempt)
            else:
                raise
```

### Circuit Breaker Pattern

```python
import time
from datetime import datetime, timedelta

class CircuitBreaker:
    def __init__(self, failure_threshold=5, timeout=60):
        self.failure_threshold = failure_threshold
        self.timeout = timeout
        self.failures = 0
        self.last_failure_time = None
        self.state = "closed"
    
    def call(self, func):
        if self.state == "open":
            if datetime.now() - self.last_failure_time > timedelta(seconds=self.timeout):
                self.state = "half-open"
            else:
                raise Exception("Circuit breaker is open")
        
        try:
            result = func()
            self.on_success()
            return result
        except Exception as e:
            self.on_failure()
            raise
    
    def on_success(self):
        self.failures = 0
        self.state = "closed"
    
    def on_failure(self):
        self.failures += 1
        self.last_failure_time = datetime.now()
        if self.failures >= self.failure_threshold:
            self.state = "open"

breaker = CircuitBreaker()

def execute_with_breaker(goal):
    return breaker.call(lambda: client.execute(goal=goal))
```

## Timeout Handling

### Request Timeout

```python
import requests

def execute_with_timeout(goal, timeout=30):
    try:
        response = requests.post(
            f"{base_url}/api/v1/executions",
            headers=headers,
            json={"goal": goal},
            timeout=timeout
        )
        return response.json()
    except requests.exceptions.Timeout:
        print(f"Request timed out after {timeout}s")
        raise
```

### Execution Timeout

```python
import time

def wait_for_completion(execution_id, timeout=300):
    start_time = time.time()
    
    while True:
        if time.time() - start_time > timeout:
            client.cancel_execution(execution_id)
            raise TimeoutError(f"Execution timed out after {timeout}s")
        
        status = client.get_status(execution_id)
        
        if status["status"] in ["completed", "failed", "cancelled"]:
            return status
        
        time.sleep(2)
```

## Validation

### Request Validation

```python
def validate_request(goal: str, max_depth: int = 3):
    errors = []
    
    if not goal or not goal.strip():
        errors.append("Goal cannot be empty")
    
    if len(goal) > 10000:
        errors.append("Goal exceeds 10000 characters")
    
    if not isinstance(max_depth, int):
        errors.append("max_depth must be an integer")
    
    if max_depth < 1 or max_depth > 10:
        errors.append("max_depth must be between 1 and 10")
    
    if errors:
        raise ValueError("; ".join(errors))

validate_request(goal="Your task", max_depth=3)
execution = client.execute(goal="Your task", max_depth=3)
```

### Response Validation

```python
def validate_execution_response(response: dict):
    required_fields = ["execution_id", "status", "created_at"]
    
    for field in required_fields:
        if field not in response:
            raise ValueError(f"Missing required field: {field}")
    
    valid_statuses = ["running", "completed", "failed", "cancelled"]
    if response["status"] not in valid_statuses:
        raise ValueError(f"Invalid status: {response['status']}")
    
    return response

response = client.execute(goal="Your task")
validated = validate_execution_response(response)
```

## Error Recovery

### Graceful Degradation

```python
def execute_with_fallback(goal, fallback_response="Unable to complete task"):
    try:
        execution = client.execute(goal=goal)
        result = wait_for_completion(execution["execution_id"])
        return result["result"]
    except Exception as e:
        print(f"Error: {e}")
        return fallback_response

result = execute_with_fallback("Your task")
```

### Partial Results

```python
def get_partial_result(execution_id):
    try:
        details = client.get_details(execution_id)
        
        if details["status"] == "failed":
            completed_nodes = [
                node for node in details.get("dag", {}).get("nodes", [])
                if node["status"] == "completed"
            ]
            
            if completed_nodes:
                return {
                    "status": "partial",
                    "completed_steps": len(completed_nodes),
                    "partial_result": details.get("intermediate_results", {})
                }
        
        return details
    except Exception as e:
        print(f"Failed to get partial results: {e}")
        return None
```

## Logging

### Comprehensive Logging

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

def execute_with_logging(goal):
    logger.info(f"Creating execution for goal: {goal[:50]}...")
    
    try:
        execution = client.execute(goal=goal)
        execution_id = execution["execution_id"]
        logger.info(f"Execution created: {execution_id}")
        
        result = wait_for_completion(execution_id)
        logger.info(f"Execution completed: {execution_id}")
        
        return result
    except requests.exceptions.HTTPError as e:
        logger.error(f"HTTP error: {e.response.status_code} - {e.response.text}")
        raise
    except Exception as e:
        logger.error(f"Unexpected error: {str(e)}", exc_info=True)
        raise
```

### Error Tracking

```python
from collections import defaultdict
from datetime import datetime

class ErrorTracker:
    def __init__(self):
        self.errors = defaultdict(list)
    
    def log_error(self, error_type, message, execution_id=None):
        self.errors[error_type].append({
            "timestamp": datetime.now().isoformat(),
            "message": message,
            "execution_id": execution_id
        })
    
    def get_summary(self):
        return {
            error_type: len(errors)
            for error_type, errors in self.errors.items()
        }

tracker = ErrorTracker()

try:
    execution = client.execute(goal="Your task")
except requests.exceptions.HTTPError as e:
    tracker.log_error(
        error_type="http_error",
        message=str(e),
        execution_id=None
    )
```

## Best Practices

### Always Use Try-Except

```python
try:
    execution = client.execute(goal="Your task")
    result = wait_for_completion(execution["execution_id"])
except requests.exceptions.HTTPError as e:
    if e.response.status_code == 429:
        print("Rate limited. Please wait.")
    elif e.response.status_code >= 500:
        print("Server error. Please retry.")
    else:
        print(f"Error: {e.response.json()}")
except Exception as e:
    print(f"Unexpected error: {str(e)}")
```

### Implement Timeouts

```python
execution = client.execute(goal="Your task")
result = wait_for_completion(
    execution["execution_id"],
    timeout=300
)
```

### Use Exponential Backoff

```python
result = exponential_backoff(
    lambda: client.execute(goal="Your task"),
    max_retries=5
)
```

### Monitor Rate Limits

```python
response = requests.post(url, headers=headers, json=data)

remaining = int(response.headers.get("X-RateLimit-Remaining", 1000))
reset_time = response.headers.get("X-RateLimit-Reset")

if remaining < 10:
    print(f"Warning: Only {remaining} requests remaining")
    print(f"Resets at: {reset_time}")
```

## Next Steps

- [API Reference](../api-reference/README.md)
- [Best Practices](../best-practices/performance.md)
- [Troubleshooting](../support/troubleshooting.md)

## Support

Need help with errors?

- Email: iziedking17@gmail.com
- X: [@Iziedking](https://x.com/Iziedking)
