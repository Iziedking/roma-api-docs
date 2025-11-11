# List Executions

Retrieve a list of all executions with optional filtering, sorting, and pagination.

## Endpoint

```
GET /api/v1/executions
```

## Headers

```http
X-API-Key: sk_live_your_api_key_here
```

## Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| limit | integer | 20 | Results per page (1-100) |
| offset | integer | 0 | Number of results to skip |
| status | string | - | Filter by status |
| profile | string | - | Filter by config profile |
| created_after | string | - | Filter by creation date |
| created_before | string | - | Filter by creation date |
| sort | string | created_at | Field to sort by |
| order | string | desc | Sort order (asc/desc) |

## Response

Status Code: `200 OK`

```json
{
  "data": [
    {
      "execution_id": "exec_1a2b3c4d5e6f",
      "status": "completed",
      "initial_goal": "Research AI trends",
      "config_profile": "high_quality",
      "created_at": "2025-11-11T10:30:00Z",
      "completed_at": "2025-11-11T10:35:00Z"
    },
    {
      "execution_id": "exec_2b3c4d5e6f7g",
      "status": "running",
      "initial_goal": "Analyze market data",
      "config_profile": "general",
      "created_at": "2025-11-11T10:25:00Z"
    }
  ],
  "pagination": {
    "total": 150,
    "limit": 20,
    "offset": 0,
    "has_more": true
  }
}
```

## Examples

### Basic List

```bash
curl https://api.izieroma.xyz/api/v1/executions \
  -H "X-API-Key: sk_live_your_key"
```

### With Pagination

```bash
curl "https://api.izieroma.xyz/api/v1/executions?limit=50&offset=0" \
  -H "X-API-Key: sk_live_your_key"
```

### Filter by Status

```bash
curl "https://api.izieroma.xyz/api/v1/executions?status=completed" \
  -H "X-API-Key: sk_live_your_key"
```

### Filter by Date Range

```bash
curl "https://api.izieroma.xyz/api/v1/executions?created_after=2025-11-01T00:00:00Z&created_before=2025-11-11T23:59:59Z" \
  -H "X-API-Key: sk_live_your_key"
```

### Sort by Updated Date

```bash
curl "https://api.izieroma.xyz/api/v1/executions?sort=updated_at&order=desc" \
  -H "X-API-Key: sk_live_your_key"
```

### Python

```python
import requests

headers = {"X-API-Key": "sk_live_your_key"}

params = {
    "limit": 50,
    "status": "completed",
    "sort": "created_at",
    "order": "desc"
}

response = requests.get(
    "https://api.izieroma.xyz/api/v1/executions",
    headers=headers,
    params=params
)

data = response.json()
print(f"Total: {data['pagination']['total']}")

for execution in data['data']:
    print(f"{execution['execution_id']}: {execution['status']}")
```

### JavaScript

```javascript
const params = new URLSearchParams({
  limit: "50",
  status: "completed",
  sort: "created_at",
  order: "desc"
});

const response = await fetch(
  `https://api.izieroma.xyz/api/v1/executions?${params}`,
  { headers: { "X-API-Key": "sk_live_your_key" } }
);

const data = await response.json();
console.log(`Total: ${data.pagination.total}`);

data.data.forEach(execution => {
  console.log(`${execution.execution_id}: ${execution.status}`);
});
```

## Pagination Example

```python
def get_all_executions():
    all_executions = []
    offset = 0
    limit = 100
    
    while True:
        response = requests.get(
            "https://api.izieroma.xyz/api/v1/executions",
            headers={"X-API-Key": "sk_live_your_key"},
            params={"limit": limit, "offset": offset}
        )
        
        data = response.json()
        all_executions.extend(data["data"])
        
        if not data["pagination"]["has_more"]:
            break
        
        offset += limit
    
    return all_executions

executions = get_all_executions()
print(f"Total executions: {len(executions)}")
```

## Filter Combinations

```python
def list_recent_completed_executions():
    from datetime import datetime, timedelta
    
    yesterday = (datetime.now() - timedelta(days=1)).isoformat()
    
    response = requests.get(
        "https://api.izieroma.xyz/api/v1/executions",
        headers={"X-API-Key": "sk_live_your_key"},
        params={
            "status": "completed",
            "created_after": yesterday,
            "profile": "high_quality",
            "sort": "created_at",
            "order": "desc",
            "limit": 50
        }
    )
    
    return response.json()["data"]
```

## Response Fields

| Field | Type | Description |
|-------|------|-------------|
| execution_id | string | Unique execution identifier |
| status | string | Execution status |
| initial_goal | string | Original task description |
| config_profile | string | Configuration profile used |
| max_depth | integer | Maximum decomposition depth |
| created_at | string | Creation timestamp |
| updated_at | string | Last update timestamp |
| completed_at | string | Completion timestamp (if completed) |

## Next Steps

- [Get Execution Details](get-details.md)
- [Get Execution Status](get-status.md)
- [Response Schemas](schemas.md)
