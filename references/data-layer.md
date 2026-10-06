# Data-layer audit

Use for SQL scripts, schemas, migrations, stored procedures, and query modules.

## Highest-value correctness checks

- Recompute important aggregates and inspect representative rows. Check NULL behavior, joins, integer division, rounding, units, timezones, and date boundaries.
- Verify uniqueness, foreign keys, checks, and ownership constraints at the layer where concurrent writes can bypass application assumptions.
- Trace transaction scope, isolation, lock order, and failure rollback around money, inventory, permissions, and other irreversible or contested state.
- For migrations, examine existing production-shaped data, deploy ordering, compatibility during rolling deployment, resumability, and destructive or irreversible operations.
- Use the actual query shape and a representative query plan when evaluating indexes. A missing index name alone is not a performance finding.
- Check pagination and ordering for stability, and verify limits are applied after or before aggregation as intended.

## Priority discipline

Data corruption, cross-tenant reads, unsafe destructive migrations, and materially wrong current results are present concerns. Missing indexes and future table growth belong in `Watch` unless observed latency, rows scanned, lock time, data growth, or a near-term migration establishes urgency.
