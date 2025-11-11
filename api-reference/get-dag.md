# Get Execution DAG

Retrieve the Directed Acyclic Graph (DAG) showing task decomposition and execution flow.

## Endpoint

```
GET /api/v1/executions/{execution_id}/dag
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
  "dag": {
    "nodes": [
      {
        "id": "node_1",
        "type": "decompose",
        "status": "completed",
        "input": "Research AI trends",
        "output": "Task broken into subtasks",
        "started_at": "2025-11-11T10:30:00Z",
        "completed_at": "2025-11-11T10:30:05Z"
      },
      {
        "id": "node_2",
        "type": "research",
        "status": "completed",
        "input": "Gather AI trend data",
        "output": "Research complete",
        "started_at": "2025-11-11T10:30:05Z",
        "completed_at": "2025-11-11T10:30:20Z"
      }
    ],
    "edges": [
      {
        "from": "node_1",
        "to": "node_2",
        "type": "depends_on"
      }
    ],
    "stats": {
      "total_nodes": 2,
      "completed_nodes": 2,
      "failed_nodes": 0,
      "total_edges": 1
    }
  }
}
```

## Examples

### cURL

```bash
curl https://api.izieroma.xyz/api/v1/executions/exec_1a2b3c4d5e6f/dag \
  -H "X-API-Key: sk_live_your_key"
```

### Python Visualization

```python
import requests
import json

response = requests.get(
    "https://api.izieroma.xyz/api/v1/executions/exec_1a2b3c4d5e6f/dag",
    headers={"X-API-Key": "sk_live_your_key"}
)

dag = response.json()["dag"]

print("Execution Flow:")
for node in dag["nodes"]:
    status_icon = "✓" if node["status"] == "completed" else "•"
    print(f"{status_icon} {node['id']}: {node['type']}")

print(f"\nStats: {dag['stats']}")
```
