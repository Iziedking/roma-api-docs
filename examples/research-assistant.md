# Research Assistant

Build a comprehensive research assistant using ROMA API.

## Code

```python
from roma_client import RomaClient
import json

class ResearchAssistant:
    def __init__(self, api_key):
        self.client = RomaClient(api_key=api_key)
    
    def research(self, topic, depth=5):
        goal = f"Research {topic} and provide comprehensive analysis"
        
        execution = self.client.execute(
            goal=goal,
            config_profile="high_quality",
            max_depth=depth,
            config_overrides={
                "temperature": 0.6,
                "max_tokens": 8000
            }
        )
        
        result = self.client.wait_for_completion(execution["execution_id"])
        
        return {
            "topic": topic,
            "result": result["result"],
            "execution_time": result["metadata"]["execution_time"],
            "tokens_used": result["metadata"]["tokens_used"]
        }
    
    def save_report(self, research, filename):
        with open(filename, 'w') as f:
            json.dump(research, f, indent=2)

assistant = ResearchAssistant("sk_live_your_key")

research = assistant.research("Renewable energy trends in 2025")
print(research["result"])

assistant.save_report(research, "renewable_energy_report.json")
```
