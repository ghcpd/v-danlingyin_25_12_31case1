# Example Validation Report

## Broken Example Details

- **Documentation file**: README.md
- **Section**: Quick Start
- **Example code**:

  ```ts
  import { RetryClient } from "./input";

  const client = new RetryClient({
    retryCount: 0,
  });

  client.run(async () => {
    throw new Error("fail once");
  });
  ```

- **Failure reason**: The `retryCount` is set to 0, but the `RetryClient` constructor validates that `retryCount` must be between 1 and 5. Setting it to 0 causes the constructor to throw an error: "retryCount must be between 1 and 5".
- **Severity**: High - Example cannot run at all (constructor fails).

## Corrected Example

- **Corrected code**:

  ```ts
  import { RetryClient } from "./input";

  const client = new RetryClient({
    retryCount: 3,
  });

  client.run(async () => {
    throw new Error("fail once");
  });
  ```

- **How it aligns with code behavior**: The corrected example sets `retryCount` to 3, which is within the valid range (1-5). The `run` method will attempt to execute the task up to 3 times. Since the task always throws an error, it will fail all 3 attempts and then throw "Task failed after retries". This demonstrates the retry functionality correctly.
