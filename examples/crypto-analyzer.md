# Crypto Market Analyzer

Build a cryptocurrency market analyzer using ROMA API.

## Code

```python
from roma_client import RomaClient
from datetime import datetime

class CryptoAnalyzer:
    def __init__(self, api_key):
        self.client = RomaClient(api_key=api_key)
    
    def analyze_asset(self, asset):
        goal = f"Analyze {asset} cryptocurrency: price trends, market sentiment, and predictions"
        
        execution = self.client.execute(
            goal=goal,
            config_profile="crypto_agent",
            max_depth=4,
            metadata={
                "asset": asset,
                "timestamp": datetime.now().isoformat()
            }
        )
        
        result = self.client.wait_for_completion(execution["execution_id"])
        return result["result"]

analyzer = CryptoAnalyzer("sk_live_your_key")

assets = ["Bitcoin", "Ethereum", "Solana"]

for asset in assets:
    print(f"\n{'='*50}")
    print(f"Analysis: {asset}")
    print(f"{'='*50}")
    
    analysis = analyzer.analyze_asset(asset)
    print(analysis)
```
