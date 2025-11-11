# API Reference

Complete reference for all ROMA API endpoints with detailed request/response specifications.

## Base URL

```
https://api.izieroma.xyz
```

## API Version

Current version: `v1`

All endpoints are prefixed with `/api/v1`

## Authentication

All requests require API key authentication:

```http
X-API-Key: sk_live_your_api_key_here
```

## Content Type

All requests must include:

```http
Content-Type: application/json
```

## Endpoints Summary

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/executions` | Create new execution |
| GET | `/api/v1/executions` | List all executions |
| GET | `/api/v1/executions/{id}` | Get execution details |
| GET | `/api/v1/executions/{id}/status` | Get execution status |
| POST | `/api/v1/executions/{id}/cancel` | Cancel execution |
| GET | `/api/v1/executions/{id}/dag` | Get execution DAG |

## Request Format

All POST requests must send JSON:

```bash
curl -X POST https://api.izieroma.xyz/api/v1/executions \
  -H "Content-Type: application/json" \
  -H "X-API-Key: your_api_key" \
  -d '{
    "goal": "Your task description"
  }'
```

## Response Format

All responses are JSON with consistent structure:

### Success Response

```json
{
  "execution_id": "exec_abc123",
  "status": "running",
  "data": {}
}
```

### Error Response

```json
{
  "error": {
    "code": "error_code",
    "message": "Error description",
    "details": {}
  }
}
```

## Rate Limiting

Rate limit headers are included in every response:

```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 847
X-RateLimit-Reset: 1699632000
```

## Status Codes

| Code | Status | Description |
|------|--------|-------------|
| 200 | OK | Request successful |
| 202 | Accepted | Execution created |
| 400 | Bad Request | Invalid parameters |
| 401 | Unauthorized | Invalid API key |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Resource not found |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Server error |

## Common Parameters

### Execution ID

Format: `exec_` + 16 character alphanumeric string

Example: `exec_1a2b3c4d5e6f7g8h`

### Timestamps

All timestamps are ISO 8601 format in UTC:

```
2025-11-11T10:30:00Z
```

### Status Values

Possible execution statuses:

- `running`: Execution in progress
- `completed`: Execution finished successfully
- `failed`: Execution encountered error
- `cancelled`: Execution cancelled by user

## Pagination

List endpoints support pagination:

```bash
GET /api/v1/executions?limit=50&offset=0
```

Parameters:

- `limit`: Results per page (1-100, default 20)
- `offset`: Number of results to skip (default 0)

Response includes pagination metadata:

```json
{
  "data": [],
  "pagination": {
    "total": 150,
    "limit": 50,
    "offset": 0,
    "has_more": true
  }
}
```

## Filtering

List endpoints support filtering:

```bash
GET /api/v1/executions?status=completed&profile=high_quality
```

Available filters:

- `status`: Filter by execution status
- `profile`: Filter by configuration profile
- `created_after`: Filter by creation date
- `created_before`: Filter by creation date

## Sorting

List endpoints support sorting:

```bash
GET /api/v1/executions?sort=created_at&order=desc
```

Parameters:

- `sort`: Field to sort by
- `order`: `asc` or `desc` (default: `desc`)

Available sort fields:

- `created_at`: Creation timestamp
- `updated_at`: Last update timestamp
- `status`: Execution status

## Webhooks

Configure webhooks in request metadata:

```json
{
  "goal": "Your task",
  "metadata": {
    "webhook_url": "https://yourapp.com/webhook",
    "webhook_token": "secret_token"
  }
}
```

Webhook payload on completion:

```json
{
  "execution_id": "exec_abc123",
  "status": "completed",
  "result": "Task result",
  "metadata": {
    "webhook_token": "secret_token"
  }
}
```

## Idempotency

Use idempotency keys to safely retry requests:

```bash
curl -X POST https://api.izieroma.xyz/api/v1/executions \
  -H "X-Idempotency-Key: unique_key_123" \
  -H "X-API-Key: your_api_key" \
  -d '{"goal": "Your task"}'
```

Same idempotency key within 24 hours returns original response.

## CORS

CORS is enabled for browser-based requests:

```http
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, OPTIONS
Access-Control-Allow-Headers: Content-Type, X-API-Key
```

## Request ID

Every response includes a unique request ID:

```json
{
  "request_id": "req_abc123",
  "execution_id": "exec_def456"
}
```

Use request ID when contacting support.

## Compression

Responses support gzip compression:

```bash
curl -H "Accept-Encoding: gzip" \
  https://api.izieroma.xyz/api/v1/executions
```

## Next Steps

Explore detailed endpoint documentation:

- [Create Execution](create-execution.md)
- [Get Execution Status](get-status.md)
- [List Executions](list-executions.md)
- [Get Execution Details](get-details.md)
- [Cancel Execution](cancel-execution.md)
- [Get Execution DAG](get-dag.md)
- [Response Schemas](schemas.md)
- [Error Codes](error-codes.md)

## Support

API questions?

- Email: iziedking17@gmail.com
- X: [@Iziedking](https://x.com/Iziedking)
