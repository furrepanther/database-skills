---
title: SQLite Performance Tuning Tips
description: Practical SQLite tuning tips for concurrent readers and large databases.
tags: sqlite, performance, wal, pragmas, checkpoint, vacuum
---

# SQLite Performance Tuning Tips

Source inspiration:
- https://phiresky.github.io/blog/2020/sqlite-performance-tuning/

Use these as starting points, then benchmark on your workload.

## Connection-Time PRAGMAs

Run at connection open (or enforce in your DB initialization path):

```sql
PRAGMA journal_mode = WAL;
PRAGMA synchronous = NORMAL;
PRAGMA temp_store = MEMORY;
PRAGMA mmap_size = 30000000000;
```

Notes:
- `journal_mode=WAL` enables concurrent readers while a writer is active.
- `synchronous=NORMAL` is a common WAL-mode balance between durability and latency.
- `temp_store=MEMORY` reduces temp file I/O for sort/temp structures.
- `mmap_size` can reduce syscall overhead on read-heavy workloads; tune to platform limits.

## Page Size

Consider larger page sizes for larger rows/blobs:

```sql
PRAGMA page_size = 32768;
```

Notes:
- Larger pages can improve throughput and sometimes file size for blob-heavy datasets.
- Set before DB creation (or VACUUM to rewrite) and test with your access pattern.

## WAL Hygiene

WAL can grow excessively under sustained write patterns. Add explicit checkpoint strategy:

```sql
PRAGMA wal_checkpoint(FULL);
-- or during maintenance windows:
PRAGMA wal_checkpoint(TRUNCATE);
```

Notes:
- `FULL` is less disruptive but may not shrink WAL if readers keep handles open.
- `TRUNCATE` can reset WAL file size but is more blocking.
- Monitor WAL file size and checkpoint latency; do not rely only on autocheckpoint defaults.

## Ongoing Maintenance

Use lightweight and heavyweight maintenance intentionally:

```sql
PRAGMA optimize;
PRAGMA incremental_vacuum;
```

And when needed:

```sql
VACUUM;
```

Notes:
- `PRAGMA optimize` is low-friction and useful as periodic/close-time maintenance.
- `auto_vacuum=INCREMENTAL` + `incremental_vacuum` helps reclaim space gradually.
- `VACUUM` rewrites the DB file; schedule during low-traffic windows.

## Safety and Validation

- Treat `synchronous=OFF` as a high-risk option; use only with explicit data-loss acceptance.
- Use `EXPLAIN QUERY PLAN` to verify index usage and avoid accidental temp-index reliance.
- Always benchmark before/after each pragma change, not all at once.
- Re-test tuning after major data volume growth or workload shifts.

