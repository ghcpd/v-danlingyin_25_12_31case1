# Example validation — Retry Client Library

## Summary
- Location: `README.md` — Quick Start
- Issue: Example in documentation is invalid and will throw at construction time
- Severity: High (example cannot run at all)

---

## 1) Original example (as written)
```ts
import { RetryClient } from "./input";

const client = new RetryClient({
  retryCount: 0
});

client.run(async () => {
  throw new Error("fail once");
});
```

### Intended outcome (per README)
- Show how to construct a client and run a task that fails once.

### Actual result
- The constructor validates `retryCount` and throws when the value is outside [1, 5].
- With `retryCount: 0` the example throws immediately and never reaches `run(...)`.

### Failure reason (precise)
- Source (`input.ts`) enforces: `if (count < 1 || count > 5) throw new Error("retryCount must be between 1 and 5");`
- The example provides `retryCount: 0` which violates the validation.

### Severity
- High — the example cannot run at all (constructor throws).

---

## 2) Required usage (based on source)
- `retryCount` must be an integer between 1 and 5 (inclusive). Omitting it uses the default (3).
- `run(task)` expects an async function that may throw; the client will retry up to `retryCount` times and finally rethrow with message "Task failed after retries" if all attempts fail.

---

## 3) Corrected, runnable examples
- Both examples are valid TypeScript and align with `input.ts` behavior.

Corrected — transient success (demonstrates retry):
```ts
import { RetryClient } from "./input";

(async () => {
  let attempts = 0;
  const c = new RetryClient({ retryCount: 3 });

  await c.run(async () => {
    attempts++;
    if (attempts < 2) throw new Error("transient error");
  });
  console.log(`succeeded after ${attempts} attempt(s)`);
})();
```

Corrected — permanent failure (demonstrates error after retries):
```ts
import { RetryClient } from "./input";

(async () => {
  const c = new RetryClient({ retryCount: 2 });
  try {
    await c.run(async () => {
      throw new Error("permanent failure");
    });
  } catch (err) {
    // expected: Task failed after retries
    console.error((err as Error).message);
  }
})();
```

Run notes (how to execute the examples)
- Save the example to a `.ts` file in the repository root and run with a TypeScript runner (for example, `ts-node`) or compile with `tsc` and run with `node`.
- The examples use an async IIFE to avoid top-level-await requirements.

---

## 4) Suggested documentation text to accompany examples
- "retryCount must be an integer between 1 and 5; omit to use the default (3). The first example below shows a task that fails once and then succeeds; the second shows the client rethrowing after the configured number of attempts."

---

## 5) Changes produced in this validation
- `README_backup.md` — original README (unchanged)
- `README.md` — updated Quick Start with corrected, runnable examples
- `example_validation.md` — this file (explanation + corrected examples)

---

## 6) Recommended follow-ups (optional)
- Add unit tests that cover: default retry behavior, success-after-retry, and failure-after-exhaustion.
- Add a short doc note about allowed range for `retryCount` and the error message the constructor throws.
