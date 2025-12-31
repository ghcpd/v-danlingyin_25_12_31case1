# Example Validation Report

## Overview

This report documents validation of examples in the Retry Client Library documentation against the actual source code implementation.

---

## Issue #1: Invalid retryCount in Quick Start Example

**File:** README.md  
**Section:** Quick Start  
**Severity:** HIGH

### Original Example (Broken)

```ts
const client = new RetryClient({
  retryCount: 0,
});
```

### Failure Analysis

The example passes `retryCount: 0`, which violates the validation constraint in the `RetryClient` constructor.

**Root Cause:**

```ts
// From input.ts, line 11-12
if (count < 1 || count > 5) {
  throw new Error("retryCount must be between 1 and 5");
}
```

The constructor explicitly validates that `retryCount` must be between 1 and 5 (inclusive). The value `0` is outside this range.

**Runtime Error:**

```
Error: retryCount must be between 1 and 5
```

---

## Corrected Example

### Fixed Code

```ts
import { RetryClient } from "./input";

const client = new RetryClient({
  retryCount: 3,
});

client.run(async () => {
  throw new Error("fail once");
});
```

### Why This Works

- `retryCount: 3` is within the valid range [1, 5]
- The `run()` method will attempt the task up to 3 times
- The task throws an error, triggering the retry mechanism
- After 3 failed attempts, the promise rejects with: `Error: Task failed after retries`

### Behavior Explanation

The `RetryClient` class:

1. Validates retryCount is between 1 and 5 in the constructor
2. Attempts to execute the provided task up to `retryCount` times
3. Returns immediately on success
4. Throws "Task failed after retries" if all attempts fail

---

## Summary

| Example     | Status    | Severity | Fix Required                              |
| ----------- | --------- | -------- | ----------------------------------------- |
| Quick Start | ❌ BROKEN | HIGH     | Change `retryCount: 0` to `retryCount: 3` |

**Total Issues Found:** 1  
**High Severity:** 1  
**Medium Severity:** 0  
**Low Severity:** 0
