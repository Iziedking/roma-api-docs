# JavaScript/Node.js Integration

Integrate ROMA API into JavaScript and Node.js applications.

## Installation

```bash
npm install axios dotenv
```

## Setup

Create `.env`:

```bash
ROMA_API_KEY=sk_live_your_key
ROMA_BASE_URL=https://api.izieroma.xyz
```

## Client Implementation

```javascript
require('dotenv').config();
const axios = require('axios');

class RomaClient {
  constructor(apiKey = process.env.ROMA_API_KEY) {
    if (!apiKey) {
      throw new Error("API key is required");
    }
    
    this.apiKey = apiKey;
    this.baseUrl = process.env.ROMA_BASE_URL || "https://api.izieroma.xyz";
    this.headers = {
      "X-API-Key": this.apiKey,
      "Content-Type": "application/json"
    };
  }
  
  async execute(goal, options = {}) {
    const response = await axios.post(
      `${this.baseUrl}/api/v1/executions`,
      { goal, ...options },
      { headers: this.headers }
    );
    return response.data;
  }
  
  async getStatus(executionId) {
    const response = await axios.get(
      `${this.baseUrl}/api/v1/executions/${executionId}/status`,
      { headers: this.headers }
    );
    return response.data;
  }
  
  async waitForCompletion(executionId, timeout = 300000) {
    const startTime = Date.now();
    
    while (true) {
      if (Date.now() - startTime > timeout) {
        throw new Error(`Execution timed out after ${timeout}ms`);
      }
      
      const status = await this.getStatus(executionId);
      
      if (status.status === "completed") {
        return status;
      }
      
      if (status.status === "failed") {
        throw new Error(status.error?.message || "Execution failed");
      }
      
      console.log(`Progress: ${status.progress || 0}%`);
      await new Promise(resolve => setTimeout(resolve, 2000));
    }
  }
  
  async cancel(executionId) {
    const response = await axios.post(
      `${this.baseUrl}/api/v1/executions/${executionId}/cancel`,
      {},
      { headers: this.headers }
    );
    return response.data;
  }
}

module.exports = RomaClient;
```

## Usage

```javascript
const RomaClient = require('./roma-client');

const client = new RomaClient();

async function main() {
  const execution = await client.execute("Research AI trends in 2025");
  console.log(`Created: ${execution.execution_id}`);
  
  const result = await client.waitForCompletion(execution.execution_id);
  console.log(`Result: ${result.result}`);
}

main().catch(console.error);
```
