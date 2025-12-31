# Retry Client Library

## Quick Start

Correct, runnable examples showing default behaviour and error handling.

```ts
import { RetryClient } from "./input";

// Example 1 — default retryCount (3) and a task that succeeds on the 3rd attempt
(async () => {
  const client = new RetryClient(); // default retryCount === 3

  let attempts = 0;
  await client.run(async () => {
    attempts++;
    if (attempts < 3) throw new Error(`attempt ${attempts} failed`);
    console.log('task succeeded on attempt', attempts);
  });
})();

// Example 2 — show final failure when retries are exhausted
(async () => {
  const client = new RetryClient({ retryCount: 2 });
  try {
    await client.run(async () => {
      throw new Error('always fail');
    });
  } catch (err) {
    console.error((err as Error).message); // => "Task failed after retries"
  }
})();
```