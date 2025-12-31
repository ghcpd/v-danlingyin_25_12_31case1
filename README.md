# Retry Client Library

## Quick Start

```ts
import { RetryClient } from "./input";

(async () => {
  // Example 1 — transient failure: succeeds on 2nd attempt
  let attempts = 0;
  const client1 = new RetryClient({ retryCount: 3 });

  await client1.run(async () => {
    attempts++;
    if (attempts < 2) throw new Error("transient error");
    // resolves on 2nd attempt
  });
  console.log(`succeeded after ${attempts} attempt(s)`);

  // Example 2 — permanent failure: throws after retries exhausted
  const client2 = new RetryClient({ retryCount: 2 });
  try {
    await client2.run(async () => {
      throw new Error("permanent failure");
    });
  } catch (err) {
    console.error("expected failure:", (err as Error).message);
  }
})();
```