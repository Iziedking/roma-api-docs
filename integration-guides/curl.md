# cURL Integration

Use ROMA API with cURL for testing and debugging.

## Basic Request

```bash
curl -X POST https://api.izieroma.xyz/api/v1/executions \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sk_live_your_key" \
  -d '{"goal": "What is AI?"}'
```

## Poll Status

```bash
EXEC_ID="exec_1a2b3c4d5e6f"

curl https://api.izieroma.xyz/api/v1/executions/$EXEC_ID/status \
  -H "X-API-Key: sk_live_your_key"
```

## Shell Script

```bash
#!/bin/bash

API_KEY="sk_live_your_key"
BASE_URL="https://api.izieroma.xyz"

GOAL="Research AI trends"

RESPONSE=$(curl -s -X POST "$BASE_URL/api/v1/executions" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: $API_KEY" \
  -d "{\"goal\": \"$GOAL\"}")

EXEC_ID=$(echo $RESPONSE | jq -r '.execution_id')
echo "Created execution: $EXEC_ID"

while true; do
  STATUS=$(curl -s "$BASE_URL/api/v1/executions/$EXEC_ID/status" \
    -H "X-API-Key: $API_KEY")
  
  STATE=$(echo $STATUS | jq -r '.status')
  PROGRESS=$(echo $STATUS | jq -r '.progress')
  
  echo "Status: $STATE ($PROGRESS%)"
  
  if [ "$STATE" == "completed" ]; then
    RESULT=$(echo $STATUS | jq -r '.result')
    echo "Result: $RESULT"
    break
  fi
  
  sleep 2
done
```
