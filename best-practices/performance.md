# Performance Optimization

Optimize ROMA API usage for best performance.

## Connection Pooling

```python
import requests
from requests.adapters import HTTPAdapter
from requests.packages.urllib3.util.retry import Retry

session = requests.Session()
retry = Retry(total=3, backoff_factor=0.5)
adapter = HTTPAdapter(max_retries=retry, pool_connections=10, pool_maxsize=20)
session.mount('https://', adapter)

class RomaClient:
    def __init__(self, api_key):
        self.session = session
        self.headers = {"X-API-Key": api_key}
```

## Caching

```python
from functools import lru_cache

@lru_cache(maxsize=100)
def cached_execute(goal):
    return client.execute(goal=goal)
```

## Batch Processing

```python
import asyncio
import aiohttp

async def batch_execute(goals):
    async with aiohttp.ClientSession() as session:
        tasks = [execute_async(session, goal) for goal in goals]
        return await asyncio.gather(*tasks)
```

## Polling Optimization

```python
def smart_poll(execution_id):
    delays = [1, 2, 3, 5, 5, 10, 10, 15]
    
    for delay in delays:
        status = client.get_status(execution_id)
        if status["status"] in ["completed", "failed"]:
            return status
        time.sleep(delay)
```
