# Request API Key

To use the ROMA API, you need a valid API key. Since ROMA is a free community service, getting your key is straightforward.

## How to Request

Send your request through either of these channels:

### Email Request

Send an email to: **iziedking17@gmail.com**

**Subject:** ROMA API Key Request

**Include:**
- Your name or organization
- Primary use case description
- GitHub profile (optional but recommended)
- Expected usage volume

**Example Email:**

```
Subject: ROMA API Key Request

Hi,

I'd like to request a ROMA API key for the following project:

Name: John Smith
Organization: TechStartup Inc
Use Case: Building an AI-powered research assistant for academic papers
GitHub: github.com/johnsmith
Expected Usage: ~100 requests/day

Thank you!
```

### X (Twitter) Request

Send a DM to: **[@Iziedking](https://x.com/Iziedking)**

**Include the same information as email request**

## Processing Time

API key requests are typically processed within:

- **24-48 hours** for standard requests
- **Same day** for urgent production needs (mention in request)

## What You'll Receive

After approval, you'll receive:

```json
{
  "api_key": "example_api_key",
  "client_name": "johnsmith_techstartup",
  "rate_limit": 1000,
  "expires": null
}
```

## Key Details

### API Key Format

```
sk_live_<64-character-hex-string>
```

### Key Characteristics

- **Length**: 71 characters total
- **Prefix**: Always starts with `sk_live_`
- **Unique**: Each key is cryptographically unique
- **Permanent**: Keys don't expire unless revoked
- **Secret**: Treat like a password

## Security Best Practices

### Store Securely

Never hardcode API keys in source code:

```python
# Bad: Hardcoded key
api_key = "sk_live_abc123..."

# Good: Environment variable
import os
api_key = os.getenv("ROMA_API_KEY")
```

### Environment Variables

Create a `.env` file:

```bash
ROMA_API_KEY=sk_live_your_actual_key_here
ROMA_BASE_URL=https://api.izieroma.xyz
```

Add to `.gitignore`:

```
.env
.env.local
```

### Use in Code

```python
from dotenv import load_dotenv
import os

load_dotenv()
api_key = os.getenv("ROMA_API_KEY")
```

```javascript
require('dotenv').config();
const apiKey = process.env.ROMA_API_KEY;
```

## Key Management

### Rotate Keys

Request new keys periodically:

- Every 90 days for production
- Immediately if exposed
- After team member departure

### Multiple Keys

Request separate keys for:

- Development environment
- Staging environment
- Production environment
- Different projects

### Revocation

If your key is compromised:

1. Email iziedking17@gmail.com immediately
2. Subject: "URGENT: API Key Revocation"
3. Include the compromised key's client name
4. Request a new key

## Testing Your Key

Once you receive your key, test it immediately:

```bash
curl -X POST https://api.izieroma.xyz/api/v1/executions \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sk_live_your_key_here" \
  -d '{"goal": "Test connection"}'
```

Expected response:

```json
{
  "execution_id": "exec_...",
  "status": "running",
  "initial_goal": "Test connection",
  "created_at": "2025-11-11T10:30:00Z"
}
```

## Rate Limits

Free tier includes:

| Limit | Value |
|-------|-------|
| Daily Requests | 1,000 |
| Concurrent Executions | 10 |
| Max Execution Time | 5 minutes |
| Request Size | 10 MB |

## Upgrading Limits

Need higher limits? Contact us:

- Email: iziedking17@gmail.com
- Subject: "Rate Limit Increase Request"
- Include current usage and requirements

## Terms of Use

By using ROMA API, you agree to:

- Not abuse the service
- Not resell API access
- Respect rate limits
- Use for legal purposes only
- Credit ROMA in public projects

## Next Steps

After receiving your API key:

1. [Set up authentication](authentication.md)
2. [Make your first request](quickstart.md)
3. [Explore integration guides](../integration-guides/README.md)

## Support

Questions about your API key request?

- Email: iziedking17@gmail.com
- X: [@Iziedking](https://x.com/Iziedking)
