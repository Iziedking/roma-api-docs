# Security Guidelines

Best practices for secure ROMA API usage.

## API Key Storage

```python
import os
from dotenv import load_dotenv

load_dotenv()
api_key = os.getenv("ROMA_API_KEY")
```

## Environment Variables

```bash
export ROMA_API_KEY=sk_live_your_key
export ROMA_BASE_URL=https://api.izieroma.xyz
```

## Backend Proxy

```javascript
app.post("/api/roma", async (req, res) => {
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

## Input Validation

```python
def validate_goal(goal):
    if not goal or len(goal) > 10000:
        raise ValueError("Invalid goal")
    
    if any(char in goal for char in ["<", ">", "script"]):
        raise ValueError("Invalid characters")
```
