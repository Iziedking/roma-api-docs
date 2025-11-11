# Data Analysis Agent

Build a data analysis agent using ROMA API.

## Code

```python
from roma_client import RomaClient
import json

class DataAnalyzer:
    def __init__(self, api_key):
        self.client = RomaClient(api_key=api_key)
    
    def analyze_data(self, data_description):
        goal = f"Analyze this data and provide insights: {data_description}"
        
        execution = self.client.execute(
            goal=goal,
            config_profile="high_quality",
            max_depth=4
        )
        
        result = self.client.wait_for_completion(execution["execution_id"])
        return result["result"]

analyzer = DataAnalyzer("sk_live_your_key")

data = "Sales data showing Q1: $1M, Q2: $1.5M, Q3: $1.8M, Q4: $2.2M"
analysis = analyzer.analyze_data(data)
print(analysis)
```
