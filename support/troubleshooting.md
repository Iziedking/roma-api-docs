# Troubleshooting

Common issues and solutions.

## Authentication Issues

**Problem:** 401 Unauthorized

**Solutions:**
- Verify API key is correct
- Check X-API-Key header is set
- Ensure key hasn't been revoked

## Rate Limiting

**Problem:** 429 Too Many Requests

**Solutions:**
- Implement exponential backoff
- Monitor X-RateLimit headers
- Request higher limits

## Execution Timeout

**Problem:** Execution takes too long

**Solutions:**
- Break into smaller tasks
- Reduce max_depth
- Use fast profile
- Contact support

## Connection Errors

**Problem:** Connection refused or timeout

**Solutions:**
- Check internet connection
- Verify base URL is correct
- Try with curl to isolate issue
