---
name: sqlite
description: SQLite performance and operational guidance for embedded and edge workloads. Load when tuning SQLite PRAGMAs, WAL behavior, checkpoints, or maintenance tasks.
license: MIT
metadata:
  author: planetscale
  version: "1.0.0"
---

# SQLite

Use this skill when the user is working with SQLite and needs practical, measurable performance tuning.

## Workflow
1. Identify workload shape: read-heavy vs write-heavy, concurrency level, DB size, and durability requirements.
2. Apply minimal PRAGMA changes first, then benchmark.
3. Validate with query plans and WAL/checkpoint metrics.
4. Add periodic maintenance (`optimize`, checkpoint policy, vacuum strategy) for long-running deployments.
5. Call out durability tradeoffs explicitly before changing sync/checkpoint behavior.

## References

| Topic | Reference | Use for |
| --- | --- | --- |
| SQLite Performance Tuning | [references/sqlite-performance-tuning.md](https://raw.githubusercontent.com/planetscale/database-skills/main/skills/sqlite/references/sqlite-performance-tuning.md) | Connection-time PRAGMAs, WAL/checkpoint tuning, `mmap_size`, `page_size`, `optimize`, vacuum policy |

