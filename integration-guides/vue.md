# Vue.js Integration

Integrate ROMA API into Vue.js applications using Composition API.

## Setup

```bash
npm install axios
```

## Composable

Create `composables/useRoma.js`:

```javascript
import { ref } from 'vue';
import axios from 'axios';

export const useRoma = () => {
  const loading = ref(false);
  const error = ref(null);
  const result = ref(null);
  const progress = ref(0);
  
  const apiKey = import.meta.env.VITE_ROMA_API_KEY;
  const baseUrl = import.meta.env.VITE_ROMA_BASE_URL;
  
  const execute = async (goal, options = {}) => {
    loading.value = true;
    error.value = null;
    result.value = null;
    progress.value = 0;
    
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
        progress.value = status.progress || 0;
        
        if (status.status === "completed") {
          result.value = status.result;
          loading.value = false;
          return status.result;
        }
        
        if (status.status === "failed") {
          throw new Error(status.error?.message || "Execution failed");
        }
        
        await new Promise(resolve => setTimeout(resolve, 2000));
      }
    } catch (err) {
      error.value = err.message;
      loading.value = false;
      throw err;
    }
  };
  
  return { execute, loading, error, result, progress };
};
```

## Component

```vue
<template>
  <div>
    <form @submit.prevent="handleSubmit">
      <input v-model="goal" placeholder="Enter your task" />
      <button type="submit" :disabled="loading">
        {{ loading ? 'Processing...' : 'Execute' }}
      </button>
    </form>
    
    <div v-if="loading">Progress: {{ progress }}%</div>
    <div v-if="error">Error: {{ error }}</div>
    <div v-if="result">Result: {{ result }}</div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useRoma } from '../composables/useRoma';

const goal = ref('');
const { execute, loading, error, result, progress } = useRoma();

const handleSubmit = async () => {
  await execute(goal.value);
};
</script>
```
