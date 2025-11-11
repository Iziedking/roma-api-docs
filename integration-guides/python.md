# Python Integration

Integrate ROMA API into Python applications.

## Installation

```bash
pip install requests python-dotenv
```

## Setup

Create `.env`:

```bash
ROMA_API_KEY=sk_live_your_key
ROMA_BASE_URL=https://api.izieroma.xyz
```

## Client Implementation

```python
import os
import requests
import time
from typing import Dict, Optional
from dotenv import load_dotenv

load_dotenv()

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
    
    def wait_for_completion(self, execution_id: str, timeout: int = 300) -> Dict:
        start_time = time.time()
        
        while True:
            if time.time() - start_time > timeout:
                raise TimeoutError(f"Execution timed out after {timeout}s")
            
            status = self.get_status(execution_id)
            
            if status["status"] == "completed":
                return status
            
            if status["status"] == "failed":
                raise Exception(status.get("error", {}).get("message", "Execution failed"))
            
            print(f"Progress: {status.get('progress', 0)}%")
            time.sleep(2)
    
    def cancel(self, execution_id: str) -> Dict:
        response = requests.post(
            f"{self.base_url}/api/v1/executions/{execution_id}/cancel",
            headers=self.headers
        )
        response.raise_for_status()
        return response.json()
```

## Usage

```python
client = RomaClient()

execution = client.execute("Research AI trends in 2025")
print(f"Created: {execution['execution_id']}")

result = client.wait_for_completion(execution["execution_id"])
print(f"Result: {result['result']}")
```

## Advanced Usage

```python
execution = client.execute(
    goal="Analyze cryptocurrency market",
    max_depth=4,
    config_profile="crypto_agent",
    config_overrides={
        "temperature": 0.5,
        "max_tokens": 6000
    },
    metadata={
        "user_id": "user_123"
    }
)
```
