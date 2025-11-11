# Test Requests

Test ROMA API endpoints in Postman.

## Create Execution

1. Select "Create Execution" request
2. Verify Headers tab has X-API-Key: {{api_key}}
3. In Body tab, modify goal
4. Click "Send"
5. Copy execution_id from response

## Check Status

1. Select "Get Status" request
2. Replace {{execution_id}} with copied ID
3. Click "Send"
4. Repeat until status is "completed"

## Get Results

1. Select "Get Details" request
2. Use same execution_id
3. Click "Send"
4. View full results in response

## Testing Tips

- Use Tests tab for assertions
- Save responses as examples
- Use Collection Runner for automation
- Enable Postman Console for debugging
