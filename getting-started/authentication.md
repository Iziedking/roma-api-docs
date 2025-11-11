# Authentication

ROMA API uses API key authentication via HTTP headers. Every request must include a valid API key.

## Authentication Method

### Header-Based Authentication

Include your API key in the `X-API-Key` header:

```http
POST /api/v1/executions HTTP/1.1
Host: api.izieroma.xyz
Content-Type: application/json
X-API-Key: sk_live_your_api_key_here
```

## Implementation

### cURL

```bash
curl https://api.izieroma.xyz/api/v1/executions \
  -H "X-API-Key: sk_live_your_api_key_here"
```

### Python (requests)

```python
import requests

headers = {
    "X-API-Key": "sk_live_your_api_key_here",
    "Content-Type": "application/json"
}

response = requests.post(
    "https://api.izieroma.xyz/api/v1/executions",
    headers=headers,
    json={"goal": "Your task"}
)
```

### JavaScript (fetch)

```javascript
const headers = {
  "X-API-Key": "sk_live_your_api_key_here",
  "Content-Type": "application/json"
};

const response = await fetch("https://api.izieroma.xyz/api/v1/executions", {
  method: "POST",
  headers: headers,
  body: JSON.stringify({ goal: "Your task" })
});
```

### Python (httpx)

```python
import httpx

async with httpx.AsyncClient() as client:
    response = await client.post(
        "https://api.izieroma.xyz/api/v1/executions",
        headers={"X-API-Key": "sk_live_your_api_key_here"},
        json={"goal": "Your task"}
    )
```

### Node.js (axios)

```javascript
const axios = require('axios');

const response = await axios.post(
  'https://api.izieroma.xyz/api/v1/executions',
  { goal: 'Your task' },
  {
    headers: {
      'X-API-Key': 'sk_live_your_api_key_here',
      'Content-Type': 'application/json'
    }
  }
);
```

## Environment Variables

Store API keys in environment variables, never in code.

### Setup

Create `.env` file:

```bash
ROMA_API_KEY=sk_live_your_actual_key_here
ROMA_BASE_URL=https://api.izieroma.xyz
```

### Python

```python
import os
from dotenv import load_dotenv

load_dotenv()

api_key = os.getenv("ROMA_API_KEY")
base_url = os.getenv("ROMA_BASE_URL")
```

### JavaScript

```javascript
require('dotenv').config();

const apiKey = process.env.ROMA_API_KEY;
const baseUrl = process.env.ROMA_BASE_URL;
```

### React

```javascript
const apiKey = process.env.REACT_APP_ROMA_API_KEY;
const baseUrl = process.env.REACT_APP_ROMA_BASE_URL;
```

### Next.js

```javascript
const apiKey = process.env.NEXT_PUBLIC_ROMA_API_KEY;
const baseUrl = process.env.NEXT_PUBLIC_ROMA_BASE_URL;
```

## Client Class

Create a reusable client class:

### Python Client

```python
import os
import requests
from typing import Dict, Optional

class RomaClient:
    def __init__(self, api_key: Optional[str] = None):
        self.api_key = api_key or os.getenv("ROMA_API_KEY")
        self.base_url = os.getenv("ROMA_BASE_URL", "https://api.izieroma.xyz")
        
        if not self.api_key:
            raise ValueError("API key is required")
        
        self.headers = {
            "X-API-Key": self.api_key,
            "Content-Type": "application/json"
        }
    
    def execute(self, goal: str, **kwargs) -> Dict:
        data = {"goal": goal, **kwargs}
        response = requests.post(
            f"{self.base_url}/api/v1/executions",
            headers=self.headers,
            json=data
        )
        response.raise_for_status()
        return response.json()
    
    def get_status(self, execution_id: str) -> Dict:
        response = requests.get(
            f"{self.base_url}/api/v1/executions/{execution_id}/status",
            headers=self.headers
        )
        response.raise_for_status()
        return response.json()

client = RomaClient()
execution = client.execute("What is 2+2?")
```

### JavaScript Client

```javascript
class RomaClient {
  constructor(apiKey = process.env.ROMA_API_KEY) {
    if (!apiKey) {
      throw new Error("API key is required");
    }
    
    this.apiKey = apiKey;
    this.baseUrl = process.env.ROMA_BASE_URL || "https://api.izieroma.xyz";
    this.headers = {
      "X-API-Key": this.apiKey,
      "Content-Type": "application/json"
    };
  }
  
  async execute(goal, options = {}) {
    const response = await fetch(`${this.baseUrl}/api/v1/executions`, {
      method: "POST",
      headers: this.headers,
      body: JSON.stringify({ goal, ...options })
    });
    
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${await response.text()}`);
    }
    
    return response.json();
  }
  
  async getStatus(executionId) {
    const response = await fetch(
      `${this.baseUrl}/api/v1/executions/${executionId}/status`,
      { headers: this.headers }
    );
    
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${await response.text()}`);
    }
    
    return response.json();
  }
}

const client = new RomaClient();
const execution = await client.execute("What is 2+2?");
```

## Authentication Errors

### 401 Unauthorized

Missing or invalid API key:

```json
{
  "detail": "Invalid or revoked API key"
}
```

**Causes:**
- API key not provided
- API key is incorrect
- API key has been revoked
- Using wrong header name

**Solution:**

```python
headers = {
    "X-API-Key": "sk_live_correct_key_here"
}
```

### 403 Forbidden

API key exists but lacks permissions:

```json
{
  "detail": "Insufficient permissions"
}
```

**Causes:**
- Key is deactivated
- Rate limit exceeded
- IP restriction

**Solution:** Contact support at iziedking17@gmail.com

## Security Best Practices

### Never Expose Keys

```python
# Bad: Hardcoded key
api_key = "sk_live_1a2b3c4d5e6f..."

# Good: Environment variable
api_key = os.getenv("ROMA_API_KEY")
```

### Use .gitignore

```
.env
.env.local
.env.production
config.json
secrets.yml
```

### Rotate Keys Regularly

Request new keys every 90 days:

```python
# Store multiple keys for zero-downtime rotation
primary_key = os.getenv("ROMA_API_KEY_PRIMARY")
fallback_key = os.getenv("ROMA_API_KEY_FALLBACK")
```

### Separate Keys Per Environment

```bash
ROMA_API_KEY_DEV=sk_live_dev_key
ROMA_API_KEY_STAGING=sk_live_staging_key
ROMA_API_KEY_PROD=sk_live_prod_key
```

### Backend Proxy Pattern

For frontend apps, proxy through your backend:

```javascript
// Frontend: No API key exposed
const response = await fetch("/api/roma/execute", {
  method: "POST",
  body: JSON.stringify({ goal: "Your task" })
});

// Backend: Handle authentication
app.post("/api/roma/execute", async (req, res) => {
  const response = await fetch("https://api.izieroma.xyz/api/v1/executions", {
    method: "POST",
    headers: {
      "X-API-Key": process.env.ROMA_API_KEY
    },
    body: JSON.stringify(req.body)
  });
  
  res.json(await response.json());
});
```

## Key Validation

Validate API key before deployment:

```python
def validate_api_key(api_key: str) -> bool:
    try:
        response = requests.post(
            "https://api.izieroma.xyz/api/v1/executions",
            headers={"X-API-Key": api_key},
            json={"goal": "test"}
        )
        return response.status_code in [200, 202]
    except Exception:
        return False

if not validate_api_key(os.getenv("ROMA_API_KEY")):
    raise ValueError("Invalid ROMA API key")
```

## Rate Limiting

Authentication includes rate limit tracking:

```http
HTTP/1.1 200 OK
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 847
X-RateLimit-Reset: 1699632000
```

Monitor headers to avoid rate limit errors:

```python
response = requests.post(url, headers=headers, json=data)

remaining = int(response.headers.get("X-RateLimit-Remaining", 0))
if remaining < 10:
    print("Warning: Approaching rate limit")
```

## Troubleshooting

### Invalid API Key Format

```python
def is_valid_format(api_key: str) -> bool:
    return (
        api_key.startswith("sk_live_") and
        len(api_key) == 71
    )

if not is_valid_format(api_key):
    raise ValueError("Invalid API key format")
```

### Testing Authentication

```bash
# Test API key
curl -I https://api.izieroma.xyz/api/v1/executions \
  -H "X-API-Key: sk_live_your_key_here"

# Expected: 405 Method Not Allowed (GET not allowed, but auth passed)
# Error: 401 Unauthorized (invalid key)
```

### Debug Headers

```python
import requests

response = requests.post(url, headers=headers, json=data)
print(f"Request headers: {response.request.headers}")
print(f"Response status: {response.status_code}")
```

## Next Steps

- [Quick Start Guide](quickstart.md)
- [Configuration Profiles](configuration.md)
- [Error Handling](error-handling.md)
- [API Reference](../api-reference/README.md)

## Support

Authentication issues?

- Email: iziedking17@gmail.com
- X: [@Iziedking](https://x.com/Iziedking)
