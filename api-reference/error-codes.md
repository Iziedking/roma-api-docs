# Error Codes

Complete reference of error codes returned by the API.

## Format

```json
{
  "error": {
    "code": "error_code",
    "message": "Human-readable description",
    "details": {}
  }
}
```

## Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| invalid_request | 400 | Request parameters invalid |
| unauthorized | 401 | Authentication failed |
| forbidden | 403 | Insufficient permissions |
| not_found | 404 | Resource not found |
| rate_limit_exceeded | 429 | Too many requests |
| execution_error | 500 | Task execution failed |
| internal_error | 500 | Server error |
| service_unavailable | 503 | Service temporarily down |

## Examples

### invalid_request

```json
{
  "error": {
    "code": "invalid_request",
    "message": "The 'goal' field is required",
    "details": {
      "field": "goal",
      "issue": "missing_required_field"
    }
  }
}
```

### rate_limit_exceeded

```json
{
  "error": {
    "code": "rate_limit_exceeded",
    "message": "Daily limit of 1000 requests exceeded",
    "details": {
      "limit": 1000,
      "remaining": 0,
      "reset_at": "2025-11-12T00:00:00Z"
    }
  }
}
```
