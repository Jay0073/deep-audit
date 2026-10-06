# Data-pipeline audit

Use for ETL, batch aggregation, sync, import/export, and report generation.

## Highest-value data checks

- Recompute important counts, totals, rates, and samples from source data and compare them with output. Check whether labels describe the actual population and window.
- Compare source and output cardinality. Account for every drop, duplicate, filter, failed parse, and join miss that materially changes the result.
- Verify deduplication at the write boundary and rerun representative input to test idempotency.
- Interrupt a disposable run after a representative partial write. Determine whether consumers can mistake incomplete output for a complete result and whether retry repairs it.
- Check incremental, backfill, and replay paths for the same filters, semantics, and boundary handling.
- Verify watermark, timezone, ordering, and late-arrival assumptions against what the source actually guarantees.
- Exercise current schema drift and coercion risks with representative real rows rather than invented exotic types.

## Priority discipline

Prioritize wrong decisions, silent record loss, duplicate effects, privacy exposure, and incomplete data presented as complete. A theoretical scale inefficiency belongs in `Watch` unless current runtime, cost, memory, or growth evidence shows the limit is near.
