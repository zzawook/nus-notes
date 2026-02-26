# L06: SQL Tuning II - Study Summary

> **CS4221 Database Tuning** | Based on L06 (annotated).pdf (50 slides)
> Reference: *Database Tuning* by Dennis Shasha and Philippe Bonnet

> This lecture covers join algorithms (Nested Loop, Hash Join, Merge Join), maintenance commands (VACUUM, ANALYZE), advanced join patterns (Semi-Join, Anti-Join), and strategies for improving query performance (denormalization, views, materialized views).

---

## Table of Contents

1. [TPC-C Schema Recap](#1-tpc-c-schema-recap)
2. [Nested Loop Join](#2-nested-loop-join)
3. [Hash Join](#3-hash-join)
4. [Merge Join](#4-merge-join)
5. [VACUUM and ANALYZE](#5-vacuum-and-analyze)
6. [Semi-Join](#6-semi-join)
7. [Anti-Join](#7-anti-join)
8. [Join Order](#8-join-order)
9. [Denormalization](#9-denormalization)
10. [Views and Materialized Views](#10-views-and-materialized-views)
11. [Arguably Good or Bad Things to Do](#11-arguably-good-or-bad-things-to-do)
12. [Take Home Messages](#12-take-home-messages)
13. [Quick Reference Tables](#13-quick-reference-tables)

---

## 1. TPC-C Schema Recap

Same partial TPC-C schema as L05, now with **additional indexes** created in L05.

### Indexes (including those added in L05)

| table_name | index_name | is_unique | is_primary | column_names |
|---|---|---|---|---|
| items | items_pkey | true | true | i_id |
| items | items_i_im_id_key | true | false | i_im_id |
| items | **items_i_price** | false | false | i_price |
| warehouses | warehouses_pkey | true | true | w_id |
| warehouses | **warehouses_w_city** | false | false | w_city |
| stocks | stocks_pkey | true | true | w_id |
| stocks | **stocks_s_qty** | false | false | s_qty |

> The three **bolded** indexes were added in L05 and are used throughout L06.

### Table Sizes

| Table | Rows |
|-------|------|
| items | 483 |
| warehouses | 1,005 |
| stocks | 44,912 |

### Running Example Query

> "Find the names of the warehouses in Singapore that have stocks for item 33."

```sql
SELECT w.w_name
FROM warehouses w NATURAL JOIN stocks s
WHERE w.w_city = 'Singapore' AND s.i_id = 33;
```

Result: 4 rows (Crescent Oaks, Schemedeman, Namekagon, Briar Crest)

---

## 2. Nested Loop Join

The fundamental join algorithm: for each row in the **outer table**, scan the **inner table** for matching rows. It's like a nested for-loop in programming: the outer loop iterates over one table, and for each row, the inner loop searches the other table.

> **Key rule**: The outer table is usually the **smallest** (w.r.t. fit into memory).

### 2.1 With Index Scans (Default Behavior)

When indexes are available on both tables:

```sql
EXPLAIN SELECT w.w_name
FROM warehouses w NATURAL JOIN stocks s
WHERE w.w_city = 'Singapore' AND s.i_id = 33;
```

```
Nested Loop (cost=0.56..49.96 rows=2 width=7)
  -> Index Scan using warehouses_w_city on warehouses w
     (cost=0.28..8.36 rows=5 width=11)
     Index Cond: ((w_city)::text = 'Singapore'::text)
  -> Index Only Scan using stocks_pkey on stocks s
     (cost=0.29..8.31 rows=1 width=4)
     Index Cond: ((w_id = w.w_id) AND (i_id = 33))
```

- **Outer**: Index Scan on `warehouses` via `warehouses_w_city`
- **Inner**: Index Only Scan on `stocks` via `stocks_pkey`

### 2.2 Temporary Tables (No Indexes)

To study join behavior without indexes, create temporary copies:

```sql
CREATE TEMPORARY TABLE stocks1 AS SELECT * FROM stocks;
CREATE TEMPORARY TABLE warehouses1 AS SELECT * FROM warehouses;
```

- Temporary tables have **no indexes**
- PostgreSQL does **not** create statistics for temporary tables unless told to

### 2.3 With Inner Sequential Scan

Force pure nested loop by disabling other join methods:

```sql
SET enable_material=off;
SET enable_hashjoin=off;
SET enable_mergejoin=off;
```

```sql
EXPLAIN (ANALYZE) SELECT w.w_name
FROM warehouses1 w NATURAL JOIN stocks1 s
WHERE w.w_city = 'Singapore' AND s.i_id = 33;
```

```
Nested Loop (cost=0.00..1909.85 rows=3 width=50)
(actual time=7.172..33.408 rows=4 loops=1)
  Join Filter: (w.w_id = s.w_id)
  Rows Removed by Join Filter: 1501
  -> Seq Scan on warehouses1 w
     Filter: ((w_city)::text = 'Singapore'::text)
     Rows Removed by Join Filter: 1000
  -> Seq Scan on stocks1 s
     (actual time=0.005..6.592 rows=301 loops=5)
     Filter: (i_id = 33)
     Rows Removed by Join Filter: 44611
```

**Execution Time: 33.450 ms** (slow!)

> Why so slow? Without materialization, the inner table (`stocks1`) is re-scanned from disk for **every** outer row. With 5 outer rows and 44,912 inner rows, that's 5 full sequential scans of the inner table.

### 2.4 With Materialized Inner Sequential Scan

Re-enable materialization:

```sql
RESET enable_material;
```

```
Nested Loop (cost=0.00..970.43 rows=3 width=50)
(actual time=0.398..7.513 rows=4 loops=1)
  -> Seq Scan on stocks1 s  (loops=1)
  -> Materialize (cost=0.00..21.41 rows=2 width=54)
     (actual time=0.001..0.001 rows=5 loops=301)
     -> Seq Scan on warehouses1 w  (loops=1)
```

**Execution Time: 5.130 ms** (6.5x faster!)

> **Materialize** caches the inner table result in memory so it doesn't need to be re-scanned from disk for each outer row. The table order may also be reordered.

### 2.5 With Inner Bitmap Index Scan

When an index is available on the inner table, the optimizer may choose a **Nested Loop Join with an inner Bitmap Index Scan**.

> "Find the identifier of items and their quantity in stock in warehouses called 'Agimba'."

```sql
EXPLAIN (ANALYZE) SELECT s.i_id, s.s_qty
FROM warehouses w, stocks s
WHERE w.w_id = s.w_id AND w.w_name = 'Agimba';
```

```
Nested Loop
  -> Seq Scan on warehouses w
  -> Bitmap Heap Scan on stocks s
     -> Bitmap Index Scan on stocks_pkey
```

- Outer table: `warehouses` (Seq Scan, filtered by w_name)
- Inner table: `stocks` (Bitmap Index Scan on `stocks_pkey`)
- The index on the inner table allows efficient lookup per outer row

### 2.6 With Memoized Inner Index Scan (PostgreSQL 16)

**Memoize** caches the results of parameterized scans so repeated lookups with the same key skip the underlying scan entirely.

```sql
EXPLAIN (ANALYZE)
  SELECT w.w_name, s.i_id, s.s_qty
  FROM stocks s, warehouses w
  WHERE w.w_id = s.w_id;
```

```
Nested Loop
  -> Seq Scan on stocks s
  -> Memoize
     Cache Key: s.w_id
     Cache Mode: logical
     Hits: 43907   Misses: 1005
     Evictions: 0  Overflows: 0
     Memory Usage: 112kB
     -> Index Scan using warehouses_pkey on warehouses w
```

- **Hits: 43,907** / **Misses: 1,005** - most lookups served from cache
- Added in **PostgreSQL 16**
- Avoids redundant index scans when many outer rows share the same join key

---

## 3. Hash Join

**In general, the optimizer will choose Hash Join.**

### Algorithm

1. **Build phase**: Hash the **inner** (smaller) table into hash buckets in memory
2. **Probe phase**: Scan the **outer** (larger) table, and for each row, look up the hash table for matches

> **Think of it this way:** Instead of comparing every row with every other row (like Nested Loop), Hash Join first organizes one table into a hash map, then looks up each row of the other table in O(1) time. This is much faster for large equi-joins.

### Example

```sql
EXPLAIN (ANALYZE)
  SELECT w.w_name, s.i_id, s.s_qty
  FROM warehouses w, stocks s
  WHERE w.w_id = s.w_id;
```

```
Hash Join
Hash Cond: (s.w_id = w.w_id)
  -> Seq Scan on stocks s
  -> Hash
     Buckets: 1024  Batches: 1  Memory Usage: 53kB
     -> Seq Scan on warehouses w
```

- **Buckets**: number of hash buckets (1024)
- **Batches**: number of passes (1 = fits in memory)
- **Memory Usage**: 53kB for the hash table

### Hash Join Without Statistics

Even **without statistics** (e.g., on temporary tables), PostgreSQL may choose Hash Join:

```sql
EXPLAIN (ANALYZE) SELECT w1.w_name
FROM warehouses1 w1, warehouses1 w2
WHERE w1.w_name = w2.w_name;
```

```
Hash Join
Hash Cond: ((w1.w_name)::text = (w2.w_name)::text)
  -> Seq Scan on warehouses1 w1
  -> Hash
     Buckets: 1024  Batches: 1  Memory Usage: 48kB
     -> Seq Scan on warehouses1 w2
```

### Hash Join on Ordered Table

Even on an **ordered table**, Hash Join may still be preferred over Merge Join:

```sql
CREATE TABLE warehouses2 AS
  SELECT * FROM warehouses ORDER BY w_name;

EXPLAIN (ANALYZE) SELECT w1.w_name
FROM warehouses2 w1, warehouses2 w2
WHERE w1.w_name = w2.w_name;
```

Result: **Hash Join** (not Merge Join), even though data is ordered.

---

## 4. Merge Join

### When Is Merge Join Chosen?

When a **join is followed by sorting** (ORDER BY), PostgreSQL may combine both into a Merge Join. Since Merge Join requires sorted input anyway, and the result also needs to be sorted, it "kills two birds with one stone."

```sql
EXPLAIN (ANALYZE) SELECT w1.w_name
FROM warehouses1 w1, warehouses1 w2
WHERE w1.w_name = w2.w_name
ORDER BY w1.w_name;
```

```
Merge Join
Merge Cond: ((w1.w_name)::text = (w2.w_name)::text)
  -> Sort
     Sort Key: w1.w_name
     Sort Method: quicksort  Memory: 43kB
     -> Seq Scan on warehouses1 w1
  -> Sort
     Sort Key: w1.w_name
     Sort Method: quicksort  Memory: 43kB
     -> Seq Scan on warehouses1 w2
```

> Works like a **merge sort**: sort both tables by the join key, then merge them in a single pass.

### Merge Join with Index

If there is an available **index** on the join column, PostgreSQL may switch to Merge Join (even without ORDER BY):

```sql
CREATE INDEX w2_w_name ON warehouses2(w_name);

EXPLAIN (ANALYZE) SELECT w1.w_name
FROM warehouses2 w1, warehouses2 w2
WHERE w1.w_name = w2.w_name;
```

Result: **Merge Join** (because the index provides sorted access).

---

## 5. VACUUM and ANALYZE

### VACUUM and VACUUM FULL

The `VACUUM` and `VACUUM FULL` commands:
- **Recover or reuse** disk space occupied by **updated or deleted** rows
- **Update data statistics** used by the PostgreSQL query planner
- **Identify opportunities** for index-only scans
- **Protect** against loss of very old data

```sql
VACUUM;                    -- all tables
VACUUM FULL;               -- all tables, reclaims more space
VACUUM warehouses2;        -- specific table
VACUUM FULL warehouses2;   -- specific table, full reclaim
```

> **Warning**: VACUUM and VACUUM FULL **cannot** be executed inside a transaction.

### Why VACUUM Is Needed: MVCC

The reason for issues with **update** and **delete** is **Multiversion Concurrency Control (MVCC)**. This allows multiple transactions to read/write data simultaneously without blocking each other by maintaining **multiple versions** of the data.

> **Think of it this way:** When you UPDATE a row in PostgreSQL, it doesn't modify the row in-place. Instead, it marks the old row as "dead" and creates a new row with the updated values. Similarly, DELETE just marks rows as dead. Over time, these dead rows (called "dead tuples") pile up, wasting space and slowing down scans. VACUUM cleans up these dead tuples.

### ANALYZE

`ANALYZE` **gathers and updates** statistics used by the PostgreSQL query planner.

```sql
ANALYZE;                    -- all tables
ANALYZE warehouses2;        -- specific table
VACUUM FULL ANALYZE;        -- vacuum + analyze all
VACUUM ANALYZE warehouses2; -- vacuum + analyze specific table
```

### Auto-Vacuum Daemon

PostgreSQL has an **auto-vacuum** daemon that can issue `VACUUM` and `ANALYZE` commands **adaptively** based on table activity.

---

## 6. Semi-Join

> When the inner table is only used for **including** results, PostgreSQL can use **Semi-Join**.

**Question**: Find the name of the warehouses with at least one item.

### All three syntaxes produce the SAME plan: Nested Loop Semi Join

#### Using WHERE EXISTS

```sql
EXPLAIN (ANALYZE)
  SELECT w.w_name FROM warehouses w
  WHERE EXISTS (
    SELECT * FROM stocks s
    WHERE s.w_id = w.w_id
  );
```

#### Using WHERE IN

```sql
EXPLAIN (ANALYZE)
  SELECT w.w_name FROM warehouses w
  WHERE w.w_id IN (
    SELECT s.w_id FROM stocks s
  );
```

#### Using WHERE = ANY

```sql
EXPLAIN (ANALYZE)
  SELECT w.w_name FROM warehouses w
  WHERE w.w_id = ANY (
    SELECT s.w_id FROM stocks s
  );
```

#### All produce:

```
Nested Loop Semi Join
  -> Seq Scan on warehouses w
  -> Index Only Scan using stocks_pkey on stocks s
     Index Cond: (w_id = w.w_id)
     Heap Fetches: 0
```

Result: **1005 rows** (all warehouses have at least one item)

> **Key insight:** Semi-Join stops scanning the inner table as soon as the **first match** is found. It only cares about *whether* a match exists, not *how many* matches there are. This makes it more efficient than a regular join when you only need existence checking.

---

## 7. Anti-Join

> When the inner table is only used for **excluding** results, PostgreSQL can use **Anti-Join** (also called **Anti-Semi-Join**).

**Question**: Find the name of the warehouses without any item.

### Efficient Anti-Join Patterns

#### Using WHERE NOT EXISTS

```sql
EXPLAIN (ANALYZE)
  SELECT w.w_name FROM warehouses w
  WHERE NOT EXISTS (
    SELECT * FROM stocks s
    WHERE s.w_id = w.w_id
  );
```

```
Nested Loop Anti Join
  -> Seq Scan on warehouses w
  -> Index Only Scan using stocks_pkey on stocks s
     Index Cond: (w_id = w.w_id)
     Heap Fetches: 0
```

#### Using LEFT JOIN ... WHERE IS NULL

```sql
EXPLAIN (ANALYZE)
  SELECT w.w_name FROM warehouses w
    LEFT JOIN stocks s ON w.w_id = s.w_id
  WHERE s.w_id IS NULL;
```

```
Nested Loop Anti Join           -- Same efficient plan!
  -> Seq Scan on warehouses w
  -> Index Only Scan using stocks_pkey on stocks s
     Index Cond: (w_id = w.w_id)
     Heap Fetches: 0
```

### INEFFICIENT Anti-Join Patterns (WARNING!)

#### NOT IN - Does NOT produce Anti Join

```sql
EXPLAIN (ANALYZE)
  SELECT w.w_name FROM warehouses w
  WHERE w.w_id NOT IN (
    SELECT s.w_id FROM stocks s
  );
```

```
Seq Scan on warehouses w
Filter: (NOT (hashed SubPlan 1))
  SubPlan 1
    -> Seq Scan on stocks s
```

> **PostgreSQL does not do well with `NOT IN`** -- it falls back to Seq Scan with hashed subplan because `NOT IN` has tricky NULL semantics (if any value in the subquery is NULL, the entire `NOT IN` returns no rows). PostgreSQL must handle this edge case, preventing it from using the more efficient Anti Join.

#### <> ALL - Does NOT produce Anti Join

```sql
EXPLAIN (ANALYZE)
  SELECT w.w_name FROM warehouses w
  WHERE w.w_id <> ALL (
    SELECT s.w_id FROM stocks s
  );
```

```
Seq Scan on warehouses w
Filter: (SubPlan 1)
  SubPlan 1
    -> Materialize
       -> Seq Scan on stocks s
```

> **PostgreSQL does not do well with `ALL`** - falls back to Seq Scan with materialized subplan.

### Anti-Join Summary

| Syntax | Produces Anti Join? | Recommended? |
|--------|-------------------|-------------|
| `WHERE NOT EXISTS (...)` | Yes | Yes |
| `LEFT JOIN ... WHERE IS NULL` | Yes | Yes |
| `WHERE NOT IN (...)` | **No** (Seq Scan) | **No** |
| `WHERE <> ALL (...)` | **No** (Seq Scan) | **No** |

---

## 8. Join Order

### 8.1 Outer Join (Left/Right)

PostgreSQL implements **(Left/Right) Outer Join**. It may replace a **Left Join** with a **Right Join** if it's more efficient.

```sql
EXPLAIN (ANALYZE) SELECT i.i_name, s.w_id
FROM items i LEFT JOIN stocks s ON s.i_id = i.i_id
WHERE i.i_name = 'MECLIZIE HYDROCHLORIDE';
```

```
Hash Right Join                    -- PostgreSQL swapped Left -> Right
Hash Cond: (s.i_id = i.i_id)
  -> Seq Scan on stocks s
  -> Hash
     -> Seq Scan on items i
```

### 8.2 Cross Join (Implicit Join)

The optimizer chooses one amongst several possible **join orders**.

```sql
-- All three FROM orderings produce the SAME plan:
EXPLAIN (ANALYZE) SELECT w.w_name, i.i_name, s.s_qty
FROM warehouses w, stocks s, items i WHERE w.w_id = s.w_id AND i.i_id = s.i_id;

EXPLAIN (ANALYZE) SELECT w.w_name, i.i_name, s.s_qty
FROM stocks s, warehouses w, items i WHERE w.w_id = s.w_id AND i.i_id = s.i_id;

EXPLAIN (ANALYZE) SELECT w.w_name, i.i_name, s.s_qty
FROM items i, stocks s, warehouses w WHERE w.w_id = s.w_id AND i.i_id = s.i_id;
```

All produce the same plan:
```
stocks -> Hash Inner Join (with warehouses Hash) -> Hash Inner Join (with items Hash)
```

> Reordering the FROM clause **typically does not change** the query plan. The optimizer finds the optimal order.

> In some cases, it can **(indirectly) be forced** to choose a preferred join order.

### 8.3 Natural Join

**Natural Join** order can also be **(indirectly) coerced**:

```sql
-- items first:
FROM items i NATURAL JOIN stocks s NATURAL JOIN warehouses w;

-- warehouses first (different FROM order, but same plan):
FROM warehouses w NATURAL JOIN stocks s NATURAL JOIN items i;
```

Both produce the same execution plan with the same join order.

---

## 9. Denormalization

### Normalized Schema

A **normalized schema** requires us to join tables on the **equality** of their primary and foreign keys. Joins can be **expensive**, especially when many tables need to be joined.

### Denormalized Schema

We can **denormalize** the schema by "joining back" some tables together -- essentially pre-computing the join and storing the result as a single table.

**Trade-offs**:
- Insertions, deletions, and updates are **more complicated** and risky (some constraints cannot be maintained)
- They are more costly because of **manual propagation** (e.g., using triggers to keep redundant data in sync)
- Some but not all queries are typically **faster** (avoid joins at query time)

> **Think of it this way:** Normalization = many small tables with no redundancy (clean writes, expensive reads). Denormalization = fewer larger tables with some redundancy (messy writes, cheap reads). Choose based on your workload.

---

## 10. Views and Materialized Views

### Database Views

- Created for **convenience only** (like saving a complex query as a shortcut)
- Do **not change performance** from that of the underlying normalized schema
- The `VIEW` definition is used by the optimizer as would a **subquery** -- the optimizer "inlines" the view definition and optimizes the combined query

### Materialized Views

Materialized views are a **middle ground** between a normalized and a denormalized schema. They store the result of a query physically on disk (like a cached table), but can be refreshed to stay in sync with the base tables.

**Properties**:
- Insertions, deletions, and updates are **more costly** (currently manual with triggers and refresh)
- PostgreSQL **may not** use the materialized view definition to optimize the query (you must query the materialized view explicitly)
- Can be **refreshed** with:

```sql
REFRESH MATERIALIZED VIEW mvall;
```

- Materialized views can also be **indexed**

| Approach | Read Performance | Write Complexity | Data Freshness |
|----------|-----------------|------------------|----------------|
| Normalized + Views | Slower (joins) | Simple | Always fresh |
| Materialized Views | Faster (pre-joined) | Moderate (refresh) | Stale until refresh |
| Denormalized | Fastest (no joins) | Complex (triggers) | Manual maintenance |

---

## 11. Arguably Good or Bad Things to Do

### 11.1 PREPARE Statements

When the `PREPARE` statement is executed, the specified query is:
- **parsed**
- **analyzed**
- **rewritten**

When an `EXECUTE` command is subsequently issued, the prepared query is:
- **planned**
- **executed**

```sql
PREPARE q AS
  SELECT s.i_id
  FROM stocks s
  WHERE s.s_qty > 500;

EXPLAIN (ANALYZE) EXECUTE q;

DEALLOCATE q;
```

```
Index Scan using stocks_s_qty on stocks s
Index Cond: (s_qty > 500)
```

> **Potential Issue**: If a prepared statement is executed enough times, the server may eventually decide to save and reuse a **generic plan** rather than re-planning each time. This generic plan may not be optimal for all parameter values.

### 11.2 Planner Method Configuration

It is **not recommended** to configure the optimizer by turning off some methods.

```sql
SET enable_seqscan=off;      -- on = true,  off = false
SET enable_bitmapscan=off;
SET enable_hashjoin=off;
-- etc

RESET enable_seqscan;        -- restore default
```

### 11.3 Hints

Several systems (e.g., **MariaDB**) allow the designer/programmer to give **hints** to the optimizer.

#### MariaDB Syntax

```sql
-- Force use of an index:
SELECT /*+ INDEX (items_i_price) */
  i.i_name
FROM warehouses w, stocks s, items i
WHERE w.w_id = s.w_id AND i.i_id = s.i_id AND i.i_price < 100;

-- Force NOT using an index:
SELECT /*+ NO_INDEX (items_i_price) */
  i.i_name
FROM warehouses w, stocks s, items i
WHERE w.w_id = s.w_id AND i.i_id = s.i_id AND i.i_price < 100;
```

> May be available in PostgreSQL with the **pg_hints** extension.

> **Warning**: It is not recommended to use hints unless you are confident that:
> 1. The **statistics will never change**
> 2. The plan the optimizer finds with your hints will **always** be the optimal plan

### Wise Words

> *"... Forcing postgres to do it your way usually means you've done it wrong. 9/10 Times the planner will beat anything you can come up with. The other 1 time its because you made it wrong."*
> -- Kent Fredric at StackOverflow, Nov 21, 2008

---

## 12. Take Home Messages

### Why Are Queries Slow?

| # | Cause | Fix |
|---|-------|-----|
| 1 | **Wrong design** (normalized vs denormalized) | Redesign schema, consider denormalization or materialized views |
| 2 | **Poor configuration** (e.g., insufficient `work_mem`) | Tune PostgreSQL configuration parameters |
| 3 | **Tuples are scattered**, tables and indexes are **bloated** | VACUUM, CLUSTER, VACUUM FULL, reindexing |
| 4 | **Missing indexes** | CREATE INDEX on frequently queried columns |
| 5 | **PostgreSQL does not choose the best plan** | ANALYZE to update statistics |

### In Conclusion

1. **Understand** the optimizer -- know how it works so you can help it
2. **Tune the data** (normalize, denormalize, index, views, materialized) for everyone
3. **Help the system maintain good statistics** (ANALYZE, VACUUM)
4. **Hard-tune the queries as a last resort** and at every users' (current and future) risk -- this is fragile and can break when data changes

---

## 13. Quick Reference Tables

### Join Types Comparison

| Join Type | Algorithm | When Chosen | Key Characteristics |
|-----------|-----------|------------|---------------------|
| **Nested Loop** | For each outer row, scan inner | Small outer table, index on inner | Good with selective filters; outer = smallest table |
| **Hash Join** | Build hash on inner, probe with outer | Default choice in general | Fast for equi-joins; needs memory for hash table |
| **Merge Join** | Sort both, merge | Join + ORDER BY; index available on join key | Efficient when data is pre-sorted or ORDER BY required |

### Nested Loop Join Variants

| Variant | Inner Access | Performance | When Used |
|---------|------------|------------|-----------|
| Index Scan | Index lookup | Best | Index available on join column |
| Bitmap Index Scan | Bitmap + heap | Good | Index on inner, multiple rows per outer |
| Memoized Index Scan | Cached index lookup | Great | Many duplicate join keys (PG 16+) |
| Materialized Seq Scan | Cached seq scan | OK | No index, materialize enabled |
| Sequential Scan | Full table scan | Worst | No index, no materialization |

### Semi-Join vs Anti-Join Syntax

| Goal | Recommended Syntax | Plan Produced |
|------|-------------------|---------------|
| Include if exists | `WHERE EXISTS (subquery)` | Semi Join |
| Include if exists | `WHERE IN (subquery)` | Semi Join |
| Include if exists | `WHERE = ANY (subquery)` | Semi Join |
| Exclude if exists | `WHERE NOT EXISTS (subquery)` | Anti Join |
| Exclude if exists | `LEFT JOIN ... WHERE IS NULL` | Anti Join |
| Exclude if exists | `WHERE NOT IN (subquery)` | **Seq Scan** (bad!) |
| Exclude if exists | `WHERE <> ALL (subquery)` | **Seq Scan** (bad!) |

### Data Organization Strategies

| Strategy | Reads | Writes | Maintenance | Use When |
|----------|-------|--------|-------------|----------|
| Normalized | Slower (joins) | Simple | Auto | Default design |
| Views | Same as normalized | Same | None | Convenience/abstraction |
| Materialized Views | Faster | Need REFRESH | Moderate | Read-heavy, tolerate staleness |
| Denormalized | Fastest | Complex (triggers) | High | Performance-critical reads |

### Planner Configuration Methods (NOT Recommended)

| Method | Example | Risk Level |
|--------|---------|-----------|
| SET enable_* | `SET enable_seqscan=off;` | High - affects all queries in session |
| Hints (MariaDB) | `/*+ INDEX(name) */` | Medium - per-query but may become stale |
| pg_hints (PG ext) | Extension-based | Medium - per-query but may become stale |
| PREPARE | `PREPARE q AS ...` | Low-Medium - generic plan may be suboptimal |

> **Best practice**: Trust the optimizer, maintain good statistics with ANALYZE, and use data tuning (indexes, materialized views) before resorting to hard-tuning queries.
