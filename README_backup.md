# Retry Client Library

## Quick Start

```ts
import { RetryClient } from "./input";

const client = new RetryClient({
  retryCount: 0
});

client.run(async () => {
  throw new Error("fail once");
});
```