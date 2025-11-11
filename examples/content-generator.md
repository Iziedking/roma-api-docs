# Content Generator

Build an AI content generator using ROMA API.

## Code

```python
from roma_client import RomaClient

class ContentGenerator:
    def __init__(self, api_key):
        self.client = RomaClient(api_key=api_key)
    
    def generate_article(self, topic, tone="professional"):
        goal = f"Write a {tone} article about {topic} with introduction, body, and conclusion"
        
        execution = self.client.execute(
            goal=goal,
            config_profile="general",
            max_depth=3,
            config_overrides={
                "temperature": 0.8,
                "max_tokens": 5000
            }
        )
        
        result = self.client.wait_for_completion(execution["execution_id"])
        return result["result"]
    
    def generate_social_posts(self, topic):
        goal = f"Create 5 social media posts about {topic} for Twitter"
        
        execution = self.client.execute(
            goal=goal,
            config_profile="fast",
            max_depth=2
        )
        
        result = self.client.wait_for_completion(execution["execution_id"])
        return result["result"]

generator = ContentGenerator("sk_live_your_key")

article = generator.generate_article("Artificial Intelligence in Healthcare")
print(article)

posts = generator.generate_social_posts("AI trends 2025")
print(posts)
```
