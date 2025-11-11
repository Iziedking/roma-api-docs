# Configuration Profiles

ROMA provides predefined configuration profiles optimized for different use cases. Profiles control model selection, temperature, token limits, and agent behavior.

## Available Profiles

### general

Default profile for most use cases. Balanced between speed and quality.

```json
{
  "config_profile": "general"
}
```

**Use For:**
- Q&A tasks
- General research
- Standard content generation
- Conversational agents

**Settings:**
- Model: `claude-3.5-sonnet`
- Temperature: `0.7`
- Max Tokens: `4000`
- Max Depth: `3`

### high_quality

Thorough research with detailed analysis. Slower but more comprehensive.

```json
{
  "config_profile": "high_quality"
}
```

**Use For:**
- Research reports
- Academic papers
- Technical documentation
- Detailed analysis

**Settings:**
- Model: `claude-3.5-sonnet`
- Temperature: `0.6`
- Max Tokens: `8000`
- Max Depth: `5`

### fast

Quick responses for simple tasks. Optimized for speed.

```json
{
  "config_profile": "fast"
}
```

**Use For:**
- Simple questions
- Fact checking
- Quick lookups
- Real-time chat

**Settings:**
- Model: `claude-3.5-haiku`
- Temperature: `0.8`
- Max Tokens: `2000`
- Max Depth: `2`

### crypto_agent

Specialized for cryptocurrency and blockchain analysis.

```json
{
  "config_profile": "crypto_agent"
}
```

**Use For:**
- Price analysis
- Market trends
- Blockchain research
- Token analysis

**Settings:**
- Model: `claude-3.5-sonnet`
- Temperature: `0.5`
- Max Tokens: `6000`
- Max Depth: `4`
- Includes crypto-specific tools

## Using Profiles

### Basic Usage

```bash
curl -X POST https://api.izieroma.xyz/api/v1/executions \
  -H "Content-Type: application/json" \
  -H "X-API-Key: your_api_key" \
  -d '{
    "goal": "Research AI trends",
    "config_profile": "high_quality"
  }'
```

### Python

```python
client = RomaClient(api_key="your_api_key")

execution = client.execute(
    goal="Analyze market data",
    config_profile="crypto_agent"
)
```

### JavaScript

```javascript
const execution = await client.execute(
  "Research AI trends",
  { config_profile: "high_quality" }
);
```

## Configuration Overrides

Override specific settings while keeping profile defaults:

```json
{
  "goal": "Your task",
  "config_profile": "general",
  "config_overrides": {
    "temperature": 0.9,
    "max_tokens": 5000
  }
}
```

## Available Overrides

### temperature

Controls randomness in responses.

```json
{
  "config_overrides": {
    "temperature": 0.7
  }
}
```

**Range:** `0.0` - `1.0`
- `0.0`: Deterministic, focused
- `0.5`: Balanced
- `1.0`: Creative, varied

**Recommendations:**
- Analysis: `0.3-0.5`
- Content: `0.7-0.9`
- Code: `0.2-0.4`

### max_tokens

Maximum tokens in response.

```json
{
  "config_overrides": {
    "max_tokens": 8000
  }
}
```

**Range:** `100` - `16000`

**Recommendations:**
- Short answers: `500-1000`
- Medium content: `2000-4000`
- Long reports: `6000-10000`

### model

Override the model used.

```json
{
  "config_overrides": {
    "model": "openrouter/anthropic/claude-3.5-sonnet"
  }
}
```

**Available Models:**
- `openrouter/anthropic/claude-3.5-sonnet`
- `openrouter/anthropic/claude-3.5-haiku`
- `openrouter/meta-llama/llama-3.1-70b-instruct`
- `openrouter/google/gemini-pro-1.5`

## Max Depth Control

Control task decomposition depth:

```json
{
  "goal": "Complex research task",
  "max_depth": 5
}
```

**Range:** `1` - `10`

**Depth Guide:**

| Depth | Use Case | Example |
|-------|----------|---------|
| 1 | Simple Q&A | "What is X?" |
| 2 | Basic research | "Explain Y" |
| 3 | Standard tasks | "Compare A and B" |
| 4-5 | Complex research | "Analyze market trends" |
| 6-8 | Deep analysis | "Research and write report" |
| 9-10 | Extreme complexity | "Full market study" |

## Metadata

Add custom metadata to executions:

```json
{
  "goal": "Your task",
  "metadata": {
    "user_id": "user_123",
    "session_id": "sess_456",
    "source": "mobile_app",
    "version": "1.0.0"
  }
}
```

Metadata is returned with execution results:

```json
{
  "execution_id": "exec_abc",
  "result": "...",
  "metadata": {
    "user_id": "user_123",
    "session_id": "sess_456"
  }
}
```

## Profile Comparison

| Feature | general | high_quality | fast | crypto_agent |
|---------|---------|--------------|------|--------------|
| Speed | Medium | Slow | Fast | Medium |
| Quality | Good | Excellent | Basic | Good |
| Tokens | 4000 | 8000 | 2000 | 6000 |
| Depth | 3 | 5 | 2 | 4 |
| Cost | Medium | High | Low | Medium |

## Examples

### Research Report

```python
execution = client.execute(
    goal="Create comprehensive report on renewable energy",
    config_profile="high_quality",
    max_depth=6,
    config_overrides={
        "temperature": 0.5,
        "max_tokens": 10000
    },
    metadata={
        "project": "energy_research",
        "deadline": "2025-12-01"
    }
)
```

### Quick Q&A

```python
execution = client.execute(
    goal="What is machine learning?",
    config_profile="fast",
    max_depth=1
)
```

### Crypto Analysis

```python
execution = client.execute(
    goal="Analyze Bitcoin price trends and predict short-term movement",
    config_profile="crypto_agent",
    max_depth=4,
    config_overrides={
        "temperature": 0.4
    }
)
```

### Custom Configuration

```python
execution = client.execute(
    goal="Generate creative content",
    config_profile="general",
    config_overrides={
        "temperature": 0.9,
        "max_tokens": 6000,
        "model": "openrouter/anthropic/claude-3.5-sonnet"
    },
    max_depth=3
)
```

## Best Practices

### Choose Right Profile

```python
def select_profile(task_type: str) -> str:
    profiles = {
        "qa": "fast",
        "research": "general",
        "report": "high_quality",
        "crypto": "crypto_agent"
    }
    return profiles.get(task_type, "general")

profile = select_profile("research")
execution = client.execute(goal, config_profile=profile)
```

### Optimize for Cost

```python
execution = client.execute(
    goal="Simple question",
    config_profile="fast",
    max_depth=1,
    config_overrides={
        "max_tokens": 500
    }
)
```

### Balance Quality and Speed

```python
execution = client.execute(
    goal="Moderate complexity task",
    config_profile="general",
    max_depth=3,
    config_overrides={
        "temperature": 0.6,
        "max_tokens": 3000
    }
)
```

## Advanced Patterns

### Dynamic Profile Selection

```python
def smart_execute(goal: str) -> dict:
    if len(goal.split()) < 10:
        profile = "fast"
        depth = 1
    elif "research" in goal.lower() or "analyze" in goal.lower():
        profile = "high_quality"
        depth = 5
    else:
        profile = "general"
        depth = 3
    
    return client.execute(
        goal=goal,
        config_profile=profile,
        max_depth=depth
    )
```

### Profile Inheritance

```python
class CustomClient(RomaClient):
    def research_execute(self, goal: str) -> dict:
        return self.execute(
            goal=goal,
            config_profile="high_quality",
            max_depth=6,
            config_overrides={
                "temperature": 0.5,
                "max_tokens": 10000
            }
        )
    
    def quick_execute(self, goal: str) -> dict:
        return self.execute(
            goal=goal,
            config_profile="fast",
            max_depth=1
        )
```

## Next Steps

- [Quick Start Guide](quickstart.md)
- [Error Handling](error-handling.md)
- [API Reference](../api-reference/README.md)
- [Integration Guides](../integration-guides/README.md)

## Support

Questions about configuration?

- Email: iziedking17@gmail.com
- X: [@Iziedking](https://x.com/Iziedking)
