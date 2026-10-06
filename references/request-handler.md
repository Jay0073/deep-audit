# Request-handler audit

Use for HTTP routes, RPC methods, GraphQL resolvers, webhook receivers, and serverless handlers. Apply only checks relevant to the mapped endpoint and current threat model.

## Highest-value boundaries

- Verify authentication and authorization independently on every reachable entry point. Test tenant and ownership isolation on reads, writes, related objects, exports, and indirect identifiers.
- Trace validation from transport input to the final sink. Exercise realistic malformed identifiers, missing fields, duplicates, and current maximum values. Distinguish a clean client error from a server failure or misleading success.
- Follow user-controlled values into queries, paths, commands, templates, response headers, redirects, and logs. Confirm output encoding for the consumer that opens it.
- For writes, verify idempotency, duplicate submission, partial failure, transaction boundaries, and the response returned when persistence or a downstream call fails.
- For expensive operations, verify pagination or bounds, query count, timeouts, cancellation, and any public abuse control. A missing limit is reportable now only when a reachable request can cause material current cost or availability harm.
- For webhooks, verify signature checking, replay behavior, event ordering, duplicate delivery, and acknowledgement timing.

## Evidence

Call the endpoint with representative credentials and data when safe. Prove the response, side effect, ownership boundary, and stored outcome. Do not label a route vulnerable merely because middleware is absent if an equivalent guard is proven elsewhere.
