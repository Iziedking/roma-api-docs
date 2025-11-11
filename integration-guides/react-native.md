# React Native Integration

Integrate ROMA API into React Native mobile applications.

## Setup

```bash
npm install axios @react-native-async-storage/async-storage
```

## Client Hook

```javascript
import { useState, useCallback } from 'react';
import axios from 'axios';
import AsyncStorage from '@react-native-async-storage/async-storage';

const useRoma = () => {
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [result, setResult] = useState(null);
  const [progress, setProgress] = useState(0);
  
  const execute = useCallback(async (goal, options = {}) => {
    setLoading(true);
    setError(null);
    setResult(null);
    setProgress(0);
    
    try {
      const apiKey = await AsyncStorage.getItem('ROMA_API_KEY');
      const baseUrl = 'https://api.izieroma.xyz';
      
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
  }, []);
  
  return { execute, loading, error, result, progress };
};

export default useRoma;
```

## Component

```javascript
import React, { useState } from 'react';
import { View, TextInput, Button, Text, ActivityIndicator } from 'react-native';
import useRoma from './hooks/useRoma';

export default function App() {
  const [goal, setGoal] = useState('');
  const { execute, loading, error, result, progress } = useRoma();
  
  const handleExecute = async () => {
    try {
      await execute(goal);
    } catch (err) {
      console.error(err);
    }
  };
  
  return (
    <View style={{ padding: 20 }}>
      <TextInput
        value={goal}
        onChangeText={setGoal}
        placeholder="Enter your task"
        style={{ borderWidth: 1, padding: 10, marginBottom: 10 }}
      />
      <Button
        title={loading ? "Processing..." : "Execute"}
        onPress={handleExecute}
        disabled={loading}
      />
      
      {loading && (
        <View style={{ marginTop: 20 }}>
          <ActivityIndicator />
          <Text>Progress: {progress}%</Text>
        </View>
      )}
      
      {error && <Text style={{ color: 'red' }}>{error}</Text>}
      {result && <Text>{result}</Text>}
    </View>
  );
}
```
