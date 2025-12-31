# Example Validation — Retry Client Library

## Summary
- File: `README.md`
- Section: **Quick Start**

## Original example (as written)
```ts
import { RetryClient } from "./input";

const client = new RetryClient({
  retryCount: 0
});

client.run(async () => {
  throw new Error("fail once");
});
```

## Failure reason
- The constructor in `input.ts` validates `retryCount` to be between 1 and 5 and throws an error if it's outside that range:

```ts
const count = options.retryCount ?? 3;
if (count < 1 || count > 5) {
  throw new Error("retryCount must be between 1 and 5");
}
```

- Because the example passes `retryCount: 0`, constructing `RetryClient` will throw immediately and the example will not run at all.

## Severity
- **High** — the example cannot run at all as written.

## Actual required usage (based on code)
- Use `retryCount` between 1 and 5, or omit the option to use the default of 3.
- `run` returns a `Promise<void>`; the example should `await` it (or handle the returned promise).

## Corrected runnable examples
1) Demonstrate retrying once then succeeding:

```ts
import { RetryClient } from "./input";

(async () => {
  const client = new RetryClient({ retryCount: 2 });
  let attempts = 0;

  await client.run(async () => {
    attempts++;
    if (attempts === 1) throw new Error("fail once");
    console.log("Task succeeded on attempt", attempts);
  });
})();
```

- This works because `retryCount` is 2 (valid) and the `run` implementation will retry the task once after the first failure and then succeed on the second attempt.

2) Demonstrate final failure after retries:

```ts
import { RetryClient } from "./input";

(async () => {
  const client = new RetryClient({ retryCount: 2 });
  try {
    await client.run(async () => {
      throw new Error("always fails");
    });
  } catch (err) {
    console.error("Expected failure:", (err as Error).message); // "Task failed after retries"
  }
})();
```

- This example shows that when the task always fails, `run` throws with message `"Task failed after retries"` once retries are exhausted.

## Notes / Recommendations
- Keep the constructor validation as-is (it's intentional). Update the README example(s) to use valid `retryCount` values and `await` the call to `run` in examples.
- The corrected examples are TypeScript snippets that will run when executed in a TypeScript runtime (for example, `ts-node`) or after compiling with `tsc` and running with `node`.

---

If you want, I can also add a small `examples/` directory with a runnable `example.ts` and a short npm script in `package.json` that demonstrates these examples locally (no source code changes required).