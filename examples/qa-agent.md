# Simple Q&A Agent

Build a question-answering agent using ROMA API.

## Code

```python
from roma_client import RomaClient

class QAAgent:
    def __init__(self, api_key):
        self.client = RomaClient(api_key=api_key)
    
    def ask(self, question):
        execution = self.client.execute(
            goal=question,
            config_profile="fast",
            max_depth=2
        )
        
        result = self.client.wait_for_completion(execution["execution_id"])
        return result["result"]

agent = QAAgent("sk_live_your_key")

questions = [
    "What is machine learning?",
    "How does photosynthesis work?",
    "What are the benefits of meditation?"
]

for question in questions:
    print(f"Q: {question}")
    answer = agent.ask(question)
    print(f"A: {answer}\n")
```

## Output

```
Q: What is machine learning?
A: Machine learning is a branch of artificial intelligence...

Q: How does photosynthesis work?
A: Photosynthesis is the process by which plants...

Q: What are the benefits of meditation?
A: Meditation offers numerous benefits including...
```
