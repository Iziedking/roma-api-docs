# Next.js Integration

Integrate ROMA API into Next.js applications using API routes and server components.

## Setup

```bash
npm install axios
```

Create `.env.local`:

```bash
ROMA_API_KEY=sk_live_your_key
ROMA_BASE_URL=https://api.izieroma.xyz
```

## API Route

Create `app/api/roma/route.ts`:

```typescript
import { NextResponse } from 'next/server';
import axios from 'axios';

export async function POST(request: Request) {
  try {
    const { goal, ...options } = await request.json();
    
    const response = await axios.post(
      `${process.env.ROMA_BASE_URL}/api/v1/executions`,
      { goal, ...options },
      {
        headers: {
          "X-API-Key": process.env.ROMA_API_KEY,
          "Content-Type": "application/json"
        }
      }
    );
    
    return NextResponse.json(response.data);
  } catch (error) {
    return NextResponse.json(
      { error: error.message },
      { status: 500 }
    );
  }
}
```

## Client Component

```typescript
"use client";

import { useState } from 'react';

export default function RomaClient() {
  const [goal, setGoal] = useState('');
  const [result, setResult] = useState('');
  const [loading, setLoading] = useState(false);
  
  const execute = async () => {
    setLoading(true);
    
    const response = await fetch('/api/roma', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ goal })
    });
    
    const execution = await response.json();
    
    while (true) {
      const statusResponse = await fetch(`/api/roma/status/${execution.execution_id}`);
      const status = await statusResponse.json();
      
      if (status.status === 'completed') {
        setResult(status.result);
        setLoading(false);
        break;
      }
      
      await new Promise(r => setTimeout(r, 2000));
    }
  };
  
  return (
    <div>
      <input value={goal} onChange={e => setGoal(e.target.value)} />
      <button onClick={execute} disabled={loading}>Execute</button>
      {result && <p>{result}</p>}
    </div>
  );
}
```
