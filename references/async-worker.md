# Async-worker audit

Use for background jobs, queue consumers, cron tasks, schedulers, and event handlers.

## Highest-value lifecycle checks

- Trace every path from accepted work to success, retry, permanent failure, cancellation, and recovery. Confirm the associated record reaches a truthful terminal state even on early returns.
- Separate transient failures from permanent ones such as invalid credentials, quota, billing, or malformed payloads. Compute wall-clock retry duration only when it informs a current operational decision.
- Verify timeouts on each outbound call, including SDK retries. Test what happens when shutdown or cancellation arrives during the call.
- Run duplicate work. Check whether idempotency and concurrency guards are atomic with the state change they protect.
- Compare stale-recovery thresholds with observed or configured legitimate runtimes. Test more than one process only when multi-instance deployment exists or is imminent.
- Inspect queue age, retries, terminal failures, orphaned records, and dead-letter handling in available non-production or read-only operational data.
- Check batch waits and ordering for head-of-line blocking, and confirm errors are not converted into successful completion.

## Priority discipline

Do not promote hypothetical multi-instance or extreme-throughput races when the worker is intentionally single-instance and no deployment change is planned. Put a proven risk in `Watch` with the deployment or volume trigger. Current orphaning, duplicate external effects, silent success, or unbounded public cost usually deserves present attention.
