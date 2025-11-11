# Response Schemas

Complete type definitions for all API responses.

## Execution Object

```typescript
interface Execution {
  execution_id: string;
  status: "running" | "completed" | "failed" | "cancelled";
  initial_goal: string;
  result?: string;
  config_profile: string;
  max_depth: number;
  dag?: DAG;
  metadata: Metadata;
  created_at: string;
  updated_at: string;
  completed_at?: string;
  failed_at?: string;
  cancelled_at?: string;
}
```

## Status Object

```typescript
interface Status {
  execution_id: string;
  status: "running" | "completed" | "failed" | "cancelled";
  progress: number;
  current_step?: string;
  result?: string;
  error?: Error;
  metadata?: Metadata;
  updated_at: string;
}
```

## DAG Object

```typescript
interface DAG {
  nodes: Node[];
  edges: Edge[];
  stats: DAGStats;
}

interface Node {
  id: string;
  type: string;
  status: "pending" | "running" | "completed" | "failed";
  input?: string;
  output?: string;
  started_at?: string;
  completed_at?: string;
}

interface Edge {
  from: string;
  to: string;
  type: string;
}

interface DAGStats {
  total_nodes: number;
  completed_nodes: number;
  failed_nodes: number;
  total_edges: number;
}
```

## Metadata Object

```typescript
interface Metadata {
  execution_time?: number;
  tokens_used?: number;
  agent_calls?: number;
  user_metadata?: Record<string, any>;
}
```

## Error Object

```typescript
interface Error {
  code: string;
  message: string;
  details?: Record<string, any>;
}
```

## Pagination Object

```typescript
interface Pagination {
  total: number;
  limit: number;
  offset: number;
  has_more: boolean;
}
```
