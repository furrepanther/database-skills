---
title: PgBouncer Configuration
description: Pool sizing and connection limits
tags: postgres, pgbouncer, connection-pooling, configuration
---

# PgBouncer Configuration

## default_pool_size

Server connections allowed **per database/user pair**.

**Multiplication:** 2 roles × 3 databases = `6 × default_pool_size` connections  
**Example:** `default_pool_size=45` with 2 roles and 3 databases = 270 backend connections

**Values:** OLTP: `20-50`, High concurrency: `50-100`, Memory-intensive: `10-20`

## max_user_connections

**Maximum total connections across all databases and roles.** Acts as global ceiling.

Set to `max_connections × 0.7 to 0.85` to leave headroom for direct connections and emergency access.

**Default:** `0` (unlimited) — dangerous with multiple roles/databases.

## Examples

Single database/role:
```
default_pool_size = 45
max_user_connections = 0
```

Multiple roles/databases:
```
default_pool_size = 25
max_user_connections = 150
```

## Monitoring

```sql
SELECT datname, usename, COUNT(*) FROM pg_stat_activity 
WHERE backend_type = 'client backend' GROUP BY datname, usename;
```

PgBouncer: `SHOW POOLS;`
