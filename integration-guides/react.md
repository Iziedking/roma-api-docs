# React Integration

Integrate ROMA API into React applications.

## Setup

```bash
npm install axios
```

Create `.env`:

```bash
REACT_APP_ROMA_API_KEY=sk_live_your_key
REACT_APP_ROMA_BASE_URL=https://api.izieroma.xyz
```

## Custom Hook

```javascript
import { useState, useCallback } from 'react';
import axios from 'axios';

const useRoma = () => {
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [result, setResult] = useState(null);
  const [progress, setProgress] = useState(0);
  
  const apiKey = process.env.REACT_APP_ROMA_API_KEY;
  const baseUrl = process.env.REACT_APP_ROMA_BASE_URL;
  
  const execute = useCallback(async (goal, options = {}) => {
    setLoading(true);
    setError(null);
    setResult(null);
    setProgress(0);
    
    try {
      const response = await axios.post(
        `${baseUrl}/api/v1/executions`,
        { goal, ...options },
        { headers: { "X-API-Key": apiKey } }
      );
      
      const executionId = response.data.execution_id;
      
      while (true) {
        const statusResponse = await axios.get(
          `${baseUrl}/api/v1/executions/${executionId}/status`,
          { headers: { "X-API-Key": apiKey } }
        );
        
        const status = statusResponse.data;
        setProgress(status.progress || 0);
        
        if (status.status === "completed") {
          setResult(status.result);
          setLoading(false);
          return status.result;
        }
        
        if (status.status === "failed") {
          throw new Error(status.error?.message || "Execution failed");
        }
        
        await new Promise(resolve => setTimeout(resolve, 2000));
      }
    } catch (err) {
      setError(err.message);
      setLoading(false);
      throw err;
    }
  }, [apiKey, baseUrl]);
  
  return { execute, loading, error, result, progress };
};

export default useRoma;
```

## Component Usage

```javascript
import React, { useState } from 'react';
import useRoma from './hooks/useRoma';

function App() {
  const [goal, setGoal] = useState('');
  const { execute, loading, error, result, progress } = useRoma();
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      await execute(goal);
    } catch (err) {
      console.error(err);
    }
  };
  
  return (
    <div>
      <form onSubmit={handleSubmit}>
        <input
          value={goal}
          onChange={(e) => setGoal(e.target.value)}
          placeholder="Enter your task"
        />
        <button type="submit" disabled={loading}>
          {loading ? "Processing..." : "Execute"}
        </button>
      </form>
      
      {loading && <div>Progress: {progress}%</div>}
      {error && <div>Error: {error}</div>}
      {result && <div>Result: {result}</div>}
    </div>
  );
}

export default App;
```
