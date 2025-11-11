# Rate Limiting

Handle rate limits effectively.

## Monitor Headers

```python
response = requests.post(url, headers=headers, json=data)

remaining = int(response.headers.get("X-RateLimit-Remaining", 1000))
reset_time = int(response.headers.get("X-RateLimit-Reset", 0))

if remaining < 10:
    print(f"Warning: {remaining} requests remaining")
```

## Exponential Backoff

```python
def execute_with_backoff(goal, max_retries=5):
    for attempt in range(max_retries):
        try:
            return client.execute(goal=goal)
        except requests.exceptions.HTTPError as e:
            if e.response.status_code == 429:
                wait_time = 2 ** attempt
                time.sleep(wait_time)
            else:
                raise
```
