# Polling Strategies

Efficient status polling patterns.

## Progressive Delays

```python
def progressive_poll(execution_id):
    delays = [1, 2, 3, 5, 10, 15, 30]
    
    for delay in delays:
        status = client.get_status(execution_id)
        if status["status"] in ["completed", "failed"]:
            return status
        time.sleep(delay)
```

## Adaptive Polling

```python
def adaptive_poll(execution_id):
    consecutive_low_progress = 0
    current_progress = 0
    
    while True:
        status = client.get_status(execution_id)
        
        if status["status"] in ["completed", "failed"]:
            return status
        
        progress = status["progress"]
        
        if progress - current_progress < 5:
            consecutive_low_progress += 1
        else:
            consecutive_low_progress = 0
        
        current_progress = progress
        
        delay = min(2 * (consecutive_low_progress + 1), 30)
        time.sleep(delay)
```
