# Customer Support Bot

Build a customer support bot using ROMA API.

## Code

```python
from roma_client import RomaClient

class SupportBot:
    def __init__(self, api_key):
        self.client = RomaClient(api_key=api_key)
        self.context = []
    
    def handle_query(self, query):
        context_str = "\n".join(self.context[-3:]) if self.context else ""
        goal = f"Provide customer support for: {query}\nContext: {context_str}"
        
        execution = self.client.execute(
            goal=goal,
            config_profile="fast",
            max_depth=2
        )
        
        result = self.client.wait_for_completion(execution["execution_id"])
        response = result["result"]
        
        self.context.append(f"Q: {query}\nA: {response}")
        return response

bot = SupportBot("sk_live_your_key")

queries = [
    "How do I reset my password?",
    "What is your refund policy?",
    "How long does shipping take?"
]

for query in queries:
    print(f"Customer: {query}")
    response = bot.handle_query(query)
    print(f"Bot: {response}\n")
```
