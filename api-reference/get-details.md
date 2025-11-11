# Get Execution Details

Retrieve complete details about an execution including DAG, intermediate results, and metadata.

## Endpoint

```
GET /api/v1/executions/{execution_id}
```

## Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| execution_id | string | The execution ID |

## Headers

```http
X-API-Key: sk_live_your_api_key_here
```

## Response

Status Code: `200 OK`

```json
{
  "execution_id": "exec_1a2b3c4d5e6f",
  "status": "completed",
  "initial_goal": "Research AI trends in 2025",
  "result": "Comprehensive analysis of AI trends...",
  "config_profile": "high_quality",
  "max_depth": 5,
  "dag": {
    "nodes": [
      {
        "id": "node_1",
        "type": "decompose",
        "status": "completed",
        "output": "Task breakdown complete"
      },
      {
        "id": "node_2",
        "type": "research",
        "status": "completed",
        "output": "Research data gathered"
      }
    ],
    "edges": [
      {
        "from": "node_1",
        "to": "node_2"
      }
    ]
  },
  "metadata": {
    "execution_time": 45.2,
    "tokens_used": 8500,
    "agent_calls": 12,
    "user_metadata": {}
  },
  "created_at": "2025-11-11T10:30:00Z",
  "updated_at": "2025-11-11T10:31:00Z",
  "completed_at": "2025-11-11T10:31:00Z"
}
```

## Examples

### cURL

```bash
curl https://api.izieroma.xyz/api/v1/executions/exec_1a2b3c4d5e6f \
  -H "X-API-Key: sk_live_your_key"
```

### Python

```python
import requests

response = requests.get(
    "https://api.izieroma.xyz/api/v1/executions/exec_1a2b3c4d5e6f",
    headers={"X-API-Key": "sk_live_your_key"}
)

details = response.json()

print(f"Status: {details['status']}")
print(f"Result: {details.get('result', 'N/A')}")
print(f"Execution Time: {details['metadata']['execution_time']}s")
print(f"Tokens Used: {details['metadata']['tokens_used']}")
```

### JavaScript

```javascript
const response = await fetch(
  "https://api.izieroma.xyz/api/v1/executions/exec_1a2b3c4d5e6f",
  { headers: { "X-API-Key": "sk_live_your_key" } }
);

const details = await response.json();

console.log(`Status: ${details.status}`);
console.log(`Result: ${details.result || "N/A"}`);
console.log(`Execution Time: ${details.metadata.execution_time}s`);
console.log(`Tokens Used: ${details.metadata.tokens_used}`);
```

## Response Fields

| Field | Type | Description |
|-------|------|-------------|
| execution_id | string | Unique execution identifier |
| status | string | Current status |
| initial_goal | string | Original task description |
| result | string | Final result (if completed) |
| config_profile | string | Configuration profile |
| max_depth | integer | Maximum depth |
| dag | object | Directed acyclic graph of execution |
| metadata | object | Execution metadata |
| created_at | string | Creation timestamp |
| updated_at | string | Last update timestamp |
| completed_at | string | Completion timestamp |

## DAG Structure

The DAG (Directed Acyclic Graph) shows task decomposition:

```python
def analyze_dag(execution_id):
    details = get_execution_details(execution_id)
    dag = details["dag"]
    
    print(f"Total nodes: {len(dag['nodes'])}")
    
    for node in dag["nodes"]:
        print(f"Node {node['id']}: {node['type']} - {node['status']}")
    
    print(f"\nConnections: {len(dag['edges'])}")

analyze_dag("exec_1a2b3c4d5e6f")
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

## Next Steps

- [Get Execution DAG](get-dag.md)
- [Cancel Execution](cancel-execution.md)
- [Response Schemas](schemas.md)
