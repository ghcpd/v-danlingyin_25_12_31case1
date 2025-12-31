# Retry Client Library

## Quick Start

```ts
import { RetryClient } from "./input";

(async () => {
  // Example: task fails once then succeeds (demonstrates retry)
  const client = new RetryClient({ retryCount: 2 });

  let attempts = 0;
  try {
    await client.run(async () => {
      attempts++;
      if (attempts === 1) {
        throw new Error("fail once");
      }
      console.log("Task succeeded on attempt", attempts);
    });
  } catch (err) {
    console.error("Task ultimately failed:", err);
  }

  // Example: task always fails (demonstrates final failure after retries)
  const failingClient = new RetryClient({ retryCount: 2 });
  try {
    await failingClient.run(async () => {
      throw new Error("always fails");
    });
  } catch (err) {
    console.error("Expected failure:", (err as Error).message); // "Task failed after retries"
  }
})();```