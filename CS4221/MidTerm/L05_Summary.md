# L05: SQL Tuning I - Study Summary

> **CS4221 Database Tuning** | Based on L05 (annotated).pdf (58 slides)
> Reference: *Database Tuning* by Dennis Shasha and Philippe Bonnet

> This lecture introduces how PostgreSQL executes queries under the hood. You'll learn how to use `EXPLAIN` to see the execution plan, understand different scan types (sequential, index, bitmap), and learn how to create and use indexes to speed up queries.

---

## Table of Contents

1. [TPC-C Schema (Partial)](#1-tpc-c-schema-partial)
2. [PostgreSQL Architecture](#2-postgresql-architecture)
3. [The EXPLAIN Command](#3-the-explain-command)
4. [EXPLAIN Estimates](#4-explain-estimates)
5. [Catalogue and Statistics](#5-catalogue-and-statistics)
6. [EXPLAIN ANALYZE](#6-explain-analyze)
7. [Performance Tuning Tips](#7-performance-tuning-tips)
8. [Sequential Scan](#8-sequential-scan)
9. [Database Index](#9-database-index)
10. [Index Scan](#10-index-scan)
11. [Bitmap Index Scan](#11-bitmap-index-scan)
12. [Table Clustering](#12-table-clustering)
13. [Multi-Column Index](#13-multi-column-index)
14. [Index-Only Scan](#14-index-only-scan)
15. [Quick Reference Tables](#15-quick-reference-tables)

---

## 1. TPC-C Schema (Partial)

The lecture uses a **partial TPC-C benchmark schema** as a running example: a wholesale supplier with warehouses, items, and stocks.

### Entity-Relationship

- **Warehouses** have: unique identifier, name, location (street, city, country)
- **Items** have: unique identifier, unique image identifier, name, price
- **Stocks** link warehouses to items with a quantity (if an item is not available in a warehouse, no entry exists)

### Schema Definitions

```sql
CREATE TABLE items (
    i_id INTEGER PRIMARY KEY,
    i_im_id CHAR(8) UNIQUE NOT NULL,
    i_name VARCHAR(64) NOT NULL,
    i_price NUMERIC NOT NULL
      CHECK (i_price > 0)
);

CREATE TABLE warehouses (
    w_id INTEGER PRIMARY KEY,
    w_name VARCHAR(16) NOT NULL,
    w_street VARCHAR(32) NOT NULL,
    w_city VARCHAR(32) NOT NULL,
    w_country VARCHAR(16) NOT NULL
);

CREATE TABLE stocks (
    w_id INTEGER REFERENCES warehouses(w_id),
    i_id INTEGER REFERENCES items(i_id),
    s_qty SMALLINT
      CHECK (s_qty > 0),
    PRIMARY KEY (w_id, i_id)
);
```

### Table Sizes

| Table | Rows |
|-------|------|
| items | 483 |
| warehouses | 1005 |
| stocks | 44912 |

### Running Example Query

> "How many units of Aspirin are in stock in Indonesia?"

```sql
SELECT SUM(s.s_qty)
FROM items i
  NATURAL JOIN stocks s
  NATURAL JOIN warehouses w
WHERE i.i_name = 'Aspirin'
  AND w.w_country = 'Indonesia';
```

Result: `35599` (1 row)

---

## 2. PostgreSQL Architecture

PostgreSQL processes a query through four major components:

| Component | Role |
|-----------|------|
| **SQL Parser and Rewriter** | Takes SQL query, converts to parse tree ready for optimization |
| **Planner/Optimizer** | Generates a query execution plan using catalogue and statistics; chooses plan with **least estimated cost** |
| **Execution Engine** | Executes the plan, accesses data and indexes, runs operators/functions/procedures |
| **Memory and Storage Management** | Manages memory; stores data, indexes, views, stored procedures, catalogue, statistics |

### Execution Plan

An **execution plan** is a **directed acyclic graph (DAG)** or tree of **physical algebraic operators**. Think of it as the step-by-step recipe the database follows to answer your query. In PostgreSQL it includes:

- Sequential / Index Scans
- Sorting
- Aggregation Operators
- Nested Loops
- Hash / Merge Joins

### Total Query Time

```
Total query time = Planning time + Execution time + Network latency + Client overhead
```

---

## 3. The EXPLAIN Command

The `EXPLAIN` command generates and displays the **annotated execution plan** that the PostgreSQL planner generates.

```sql
EXPLAIN <SQL statement>;
```

### Options

| Option | Effect |
|--------|--------|
| `VERBOSE` | Displays the plan with **more details** |
| `ANALYZE` | **Executes** the query and shows actual execution time and row count |
| `COSTS` | Show/hide cost estimates |
| `SETTINGS` | Show modified settings |
| `GENERIC_PLAN` | Show generic plan |
| `BUFFERS` | Show buffer usage |
| `WAL` | Show WAL usage |
| `TIMING` | Show/hide timing |
| `SUMMARY` | Show/hide summary |
| `FORMAT { TEXT \| XML \| JSON \| YAML }` | Output format |

### Supported SQL Constructs

`EXPLAIN` only supports: `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `EXECUTE` (prepared statement), `CREATE TABLE`, `DECLARE` (cursor).

### Example EXPLAIN Output

```
EXPLAIN SELECT SUM(s.s_qty)
FROM items i NATURAL JOIN stocks s NATURAL JOIN warehouses w
WHERE i.i_name = 'Aspirin' AND w.w_country = 'Indonesia';
```

```
Aggregate  (cost=849.87..849.88 rows=1 width=8)
  -> Nested Loop  (cost=11.33..849.65 rows=89 width=2)
       -> Hash Join  (cost=11.05..822.10 rows=93 width=6)
            Hash Cond: (s.i_id = i.i_id)
            -> Seq Scan on stocks s  (cost=0.00..692.12 rows=44912 width=10)
            -> Hash  (cost=11.04..11.04 rows=1 width=4)
                 -> Seq Scan on items i  (cost=0.00..11.04 rows=1 width=4)
                      Filter: ((i_name)::text = 'Aspirin'::text)
       -> Index Scan using warehouses_pkey on warehouses w  (cost=0.28..0.30 rows=1 width=4)
            Index Cond: (w_id = s.w_id)
            Filter: ((w_country)::text = 'Indonesia'::text)
```

**Reading the plan:** Indentation shows parent-child relationships. The `->` arrows indicate child nodes. Execution proceeds **bottom-up** (leaf nodes execute first, results flow up to the root). The topmost node produces the final result.

---

## 4. EXPLAIN Estimates

At each node of the plan, `EXPLAIN` gives **three estimates**: `cost`, `rows`, and `width`.

### Cost

- Format: `cost=<startup>..<total>`
- **Startup cost**: estimated cost before any output is produced (e.g., time to sort before returning sorted rows)
- **Total cost**: estimated cost of executing the node completely (including all children below it)
- Expressed in **arbitrary units** (roughly proportional to estimated time, but NOT in seconds/milliseconds)
- Lower costs generally indicate more efficient plans
- Includes estimated CPU processing cost and estimated I/O cost
- Does **NOT** include cost of transmission to the client

### Rows

- The **estimated number of rows** the node returns

### Width

- The **estimated average width** (in bytes) of the rows produced by the node

### Total Data Volume

```
rows x width = estimated total data volume
```

### Estimation Mismatch

- The estimates may **not always match the actual runtime behavior**
- The planner/optimizer uses estimates to select the plan
- Tuning insight: compare execution plan and estimates of different queries to learn how to tune SQL

---

## 5. Catalogue and Statistics

PostgreSQL query planner/optimizer uses the **catalogue and statistics** built and maintained by PostgreSQL.

### System Catalogue Tables/Views

| Catalogue | Description |
|-----------|-------------|
| `pg_tables` | View with useful info (name, indexes, triggers, etc.) about each table |
| `pg_attribute` | Stores info about table columns (one row per column per table) |
| `pg_statistics` | Stores statistical data about database contents; entries created by `ANALYZE`; data is inherently **approximate** |
| `pg_stats` | View providing access to `pg_statistic` catalog (only for tables the user has permission to read) |

### The ANALYZE Command

```sql
ANALYZE;          -- analyzes all tables
ANALYZE tablename; -- analyzes specific table
```

- Collects statistics about the contents of tables in the database
- Stores results in `pg_statistic` system catalogue
- **Important**: `ANALYZE` (the command) is **different** from `EXPLAIN (ANALYZE)`

### pg_stats Example

```sql
SELECT * FROM pg_stats
WHERE tablename = 'warehouses' AND attname = 'w_city';
```

| avg_width | most_common_vals | most_common_freqs |
|-----------|------------------|-------------------|
| 9 | {"Kota Kinabalu","Kuala Lumpur","Singapore",...} | {0.0059701493,0.0049751243,0.0049751243,...} |

- `most_common_vals`: most common values for a column
- `most_common_freqs`: corresponding estimated frequencies
- `avg_width`: average width in bytes (used in `width` attribute of EXPLAIN)

---

## 6. EXPLAIN ANALYZE

`EXPLAIN (ANALYZE)` **executes the query** and shows actual runtime measurements alongside estimates.

### Measurements

At each node, `EXPLAIN (ANALYZE)` gives **four measurements**:

| Measurement | Description |
|-------------|-------------|
| `actual time` | Average startup and total execution time of the node in **milliseconds** |
| `rows` | **Actual number of rows** the node returns |
| `loops` | **Number of times** the node (subplan) is executed |
| `Rows Removed by Filter` | Number of rows **filtered out** by the condition |

**Key formula**: `actual time x loops = total time of a node`

### Example

```sql
EXPLAIN (ANALYZE) SELECT SUM(s.s_qty)
FROM items i NATURAL JOIN stocks s NATURAL JOIN warehouses w
WHERE i.i_name = 'Aspirin' AND w.w_country = 'Indonesia';
```

```
Aggregate  (cost=849.87..849.88 rows=1 width=8)
           (actual time=4.122..4.125 rows=1 loops=1)
  -> Nested Loop  (cost=11.33..849.65 rows=89 width=2)
                   (actual time=0.138..4.113 rows=72 loops=1)
       -> Hash Join  (cost=11.05..822.10 rows=93 width=6)
                      (actual time=0.079..3.978 rows=76 loops=1)
            ...
Planning Time: 2.326 ms
Execution Time: 4.156 ms
```

### Additional Information

- `EXPLAIN (ANALYZE)` **adds overhead** to the overall time
- Execution time **varies** between executions; **average** multiple runs
- Costs and actual times are in **different units** (cannot directly compare numbers)
- They should be **approximately commensurate** if estimation was good
- More important: check whether **estimated row counts** are reasonably close to reality

### Timing Information

- `EXPLAIN (ANALYZE)` also gives **Planning Time** and **Execution Time**
- Execution time includes: start-up, shut-down, and time spent processing result rows

### JSON Format

```sql
EXPLAIN (ANALYZE, FORMAT JSON) (
  SELECT SUM(s.s_qty)
  FROM items i
    NATURAL JOIN stocks s
    NATURAL JOIN warehouses w
  WHERE i.i_name = 'Aspirin'
    AND w.w_country = 'Indonesia'
);
```

Returns structured JSON with fields like `Node Type`, `Strategy`, `Startup Cost`, `Total Cost`, `Plan Rows`, `Actual Startup Time`, `Actual Total Time`, `Actual Rows`, `Actual Loops`, `Planning Time`, `Execution Time`.

---

## 7. Performance Tuning Tips

- Run queries **many times** and look at the **average**
- Statistics are gathered; pages are brought to memory buffer
- Use `VACUUM`, `ANALYZE`, and `VACUUM ANALYZE` to:
  - **Reorganize data** and **collect statistics**
  - Costs, times, and plans change (improve) accordingly

---

## 8. Sequential Scan

### When Used

If the statistics indicate the percentage of data to retrieve is **large** or **scattered**, and if it is not possible or worth using another method, the optimizer uses a **sequential scan**.

- Reads through the **entire table** page by page (like reading a book cover to cover)
- PostgreSQL default page size is **8KB**
- This is the simplest scan: no index needed, just read every page and check each row against the filter

### Example: Simple Scan

```sql
SELECT w.w_name
FROM warehouses w
WHERE w.w_city = 'Singapore';
-- Returns 5 rows
```

```
EXPLAIN output:
Seq Scan on warehouses w  (cost=0.00..21.56 rows=5 width=7)
  Filter: ((w_city)::text = 'Singapore'::text)
```

```
EXPLAIN (ANALYZE) output:
Seq Scan on warehouses w  (cost=0.00..21.56 rows=5 width=7)
  (actual time=0.056..0.217 rows=5 loops=1)
  Filter: ((w_city)::text = 'Singapore'::text)
  Rows Removed by Filter: 1000
Planning Time: 0.180 ms
Execution Time: 0.276 ms
```

### Scanning + Sorting (ORDER BY)

```sql
EXPLAIN SELECT w.w_name
FROM warehouses w
WHERE w.w_city = 'Singapore'
ORDER BY w.w_name;
```

```
Sort  (cost=21.62..21.63 rows=5 width=7)
  Sort Key: w_name
  -> Seq Scan on warehouses w  (cost=0.00..21.56 rows=5 width=7)
       Filter: ((w_city)::text = 'Singapore'::text)
```

Pipeline: `warehouses -> Sort`

With `EXPLAIN (ANALYZE)`: Sort Method = **quicksort**, Memory = 25kB

### Scanning + Grouping (GROUP BY)

```sql
EXPLAIN SELECT w.w_name
FROM warehouses w
WHERE w.w_city = 'Singapore'
GROUP BY w.w_name;
```

```
Group  (cost=21.62..21.65 rows=5 width=7)
  Group Key: w_name
  -> Sort  (cost=21.62..21.63 rows=5 width=7)
       Sort Key: w_name
       -> Seq Scan on warehouses w  (cost=0.00..21.56 rows=5 width=7)
            Filter: ((w_city)::text = 'Singapore'::text)
```

Pipeline: `warehouses -> Sort -> Group`

### Scanning + Distinct (SELECT DISTINCT)

```sql
EXPLAIN SELECT DISTINCT w.w_name
FROM warehouses w
WHERE w.w_city = 'Singapore';
```

```
Unique  (cost=21.62..21.65 rows=5 width=7)
  Group Key: w_name
  -> Sort  (cost=21.62..21.63 rows=5 width=7)
       Sort Key: w_name
       -> Seq Scan on warehouses w  (cost=0.00..21.56 rows=5 width=7)
            Filter: ((w_city)::text = 'Singapore'::text)
```

Pipeline: `warehouses -> Sort -> Unique`

### GROUP BY vs DISTINCT

Both `GROUP BY` and `SELECT DISTINCT` can be used to get unique values. In PostgreSQL:
- `GROUP BY` produces a **Group** node (after Sort)
- `SELECT DISTINCT` produces a **Unique** node (after Sort)

Both require sorting first. The plans look very similar.

### Distinct on Primary Key vs Non-Key

- `SELECT DISTINCT w.w_name` (non-key): width=7, needs Sort + Unique
- `SELECT DISTINCT w.w_id` (primary key): width=4, still uses Sort + Unique but potentially a **good optimization** since PK is already unique

---

## 9. Database Index

### Definition

An **index** is a data structure that **guides access** to the data -- like an index at the back of a textbook that tells you which page to turn to instead of reading the whole book.

- An index **may or may not speed up** queries, deletions, and updates
- It generally **slows down insertions and updates** (since both data and index must be updated/reorganized)
- The trade-off: faster reads vs. slower writes

### PostgreSQL Index Types

| Index Type | Description |
|------------|-------------|
| **B-tree** | Default index type |
| **Hash** | For equality comparisons |
| **GiST** (Generalized Search Tree) | For geometric/spatial data |
| **SP-GiST** (Space-Partitioned GiST) | For partitioned search trees |
| **GIN** (Generalized Inverted) | For composite values (arrays, full-text) |
| **BRIN** (Block Range) | For large, naturally ordered tables |

### Secondary Index

- In PostgreSQL, indexes are **secondary** (stored **independently from the table**)
- When searching via index: data must be fetched from **both** the index and the table (the heap)
- Index entries meeting a condition are located near each other in the index, but corresponding table rows can be **scattered anywhere** on disk

> **Think of it this way:** The index is like a phone book sorted by name. You can quickly find "John Smith" in the phone book, but John Smith's actual house could be anywhere in the city. You still need to travel to the house (the heap) to get the full data.

### Index-Only Scans (Covering Index)

- PostgreSQL supports **index-only scans**: answer a query from the index alone
- Works for B-tree indexes (and sometimes others) when the query references **only columns stored in the index**
- A **covering index** can satisfy all requested columns **without further lookup** into the table

### Automatic vs Manual Index Creation

**Automatic (Candidate Keys):**
- PostgreSQL automatically creates a **B-tree unique index** for each `UNIQUE` and `PRIMARY KEY` constraint
- The index enforces uniqueness (at extra cost for insertions and updates)
- This index is a **covering index** for the unique/prime attributes

**Foreign Keys:**
- PostgreSQL does **NOT** create an index for `FOREIGN KEY` constraints
- It is a **design choice** whether to create an index on referencing columns
- Foreign key attributes are generally components of a composite key and therefore indexed as such

### CREATE INDEX Syntax

```sql
CREATE [ UNIQUE ] INDEX [ name ] ON table_name
[ USING method ]
( { column_name | ( expression ) } )
[ WHERE predicate ]
```

- `UNIQUE`: checks for duplicate values
- `method`: `btree` (default), `hash`, `gist`, or other index types
- `predicate`: defines a **partial index**

### Example: Creating an Index

```sql
CREATE INDEX items_i_price ON items(i_price);
```

Checking with `indexinfo` view:

| table_name | index_name | is_unique | is_primary | column_names |
|------------|------------|-----------|------------|--------------|
| items | items_pkey | true | true | i_id |
| items | items_i_im_id_key | true | false | i_im_id |
| items | items_i_price | false | false | i_price |

### Catalogue: Index Information

The catalogue table `pg_index` and view `pg_indexes` store information about indexes.

```sql
CREATE VIEW indexinfo AS
SELECT t.relname AS table_name,
       ix.relname AS index_name,
       i.indisunique AS is_unique,
       i.indisprimary AS is_primary,
       regexp_replace(pg_get_indexdef(i.indexrelid), '.*\((.*)\)', '\1') AS column_names
FROM pg_index i, pg_class t, pg_class ix
WHERE t.oid = i.indrelid AND ix.oid = i.indexrelid;
```

### Default Indexes per Table

| Table | Index Name | is_unique | is_primary | column_names |
|-------|-----------|-----------|------------|--------------|
| warehouses | warehouse_pkey | true | true | w_id |
| items | items_pkey | true | true | i_id |
| items | items_i_im_id_key | true | false | i_im_id |
| stocks | stocks_pkey | true | true | w_id, i_id |

---

## 10. Index Scan

### When Used

If the statistics indicate the percentage of data to retrieve is **tiny** and **an index is available**, the optimizer may provide **direct access** and use an **index scan**.

- Traverses the B-tree to find matching key(s)
- Then fetches the corresponding row(s) from the table (heap)
- **Randomly accesses** the index and table alternately (index lookup -> heap fetch -> index lookup -> heap fetch -> ...)
- Best for highly selective queries (returning very few rows)

### Example: Index Scan on Primary Key

```sql
-- Find the name of the warehouse with identifier 123
SELECT w.w_name
FROM warehouses w
WHERE w.w_id = '123';
-- Result: Janyx (1 row)
```

Difference from `w.w_city = 'Singapore'`: w_id has a primary key index, w_city does not (by default).

```
EXPLAIN output:
Index Scan using warehouses_pkey on warehouses w
  (cost=0.28..8.29 rows=1 width=7)
  Index Cond: (w_id = 123)
```

### Creating an Additional Index

```sql
CREATE INDEX warehouses_w_city ON warehouses(w_city);
```

After creating this index, a query on `w_city = 'Singapore'` may use a **Bitmap Index Scan** instead of a Sequential Scan.

---

## 11. Bitmap Index Scan

### When Used

If the statistics indicate the percentage of data to retrieve is **average** (not tiny, not huge) and **an index is available**, a bitmap built on the index may provide **somewhat direct access**. The optimizer uses a **bitmap heap scan**.

> **Think of it this way:** Instead of alternating between index and heap for each row (like Index Scan), Bitmap Index Scan first builds a "map" of *which pages* contain matching rows, then reads those pages in order. This avoids random I/O when many rows match.

- The bitmap is an **in-memory** data structure that encodes the pages containing the matching rows
- You may need **AND** and **OR** operations on the bitmap (for combining multiple index conditions)
- In PostgreSQL: **Bitmap Index Scan** (builds the bitmap) is always followed by **Bitmap Heap Scan** (reads the pages)
- The **Recheck Cond** in the plan means PostgreSQL re-verifies the condition when reading the heap page (because the bitmap only tells which pages to read, not which exact rows)

### Example

```sql
-- After: CREATE INDEX warehouses_w_city ON warehouses(w_city);
EXPLAIN SELECT w.w_name
FROM warehouses w
WHERE w.w_city = 'Singapore';
```

```
Bitmap Heap Scan on warehouses w  (cost=4.31..12.38 rows=5 width=37)
  Recheck Cond: ((w_city)::text = 'Singapore'::text)
  -> Bitmap Index Scan on warehouses_w_city
       (cost=0.00..4.31 rows=5 width=0)
       Index Cond: ((w_city)::text = 'Singapore'::text)
```

Pipeline: `warehouses_w_city (Bitmap Index Scan) -> warehouses (Bitmap Heap Scan)`

---

## 12. Table Clustering

### CLUSTER Command

We can **cluster the table** using an index. This **physically reorders** the rows of the underlying table to coincide with the key order of the given index.

```sql
CLUSTER warehouses USING warehouses_w_city;
```

- **Must be done regularly** (if there are updates) because PostgreSQL does **not dynamically maintain** the clustered order -- new inserts go wherever there's space, not in sorted order
- A table can only have **one cluster index** (you can only physically sort a table one way)

> **Think of it this way:** Without clustering, rows matching a condition might be scattered across many disk pages. After clustering, those rows are stored on consecutive pages, making sequential reads much faster.

---

## 13. Multi-Column Index

### Full Condition Match

A **multicolumn index** can be used for index scan when **all** columns of the index appear in the condition.

```sql
-- stocks_pkey is on (w_id, i_id)
EXPLAIN SELECT s.s_qty
FROM stocks s
WHERE s.w_id = '123' AND s.i_id = '7';
```

```
Index Scan using stocks_pkey on stocks s
  (cost=0.29..8.31 rows=1 width=2)
  Index Cond: ((w_id = 123) AND (i_id = 7))
```

### Partial Condition (Prefix)

A multicolumn index can be used for index scan **even with partial condition**, as long as it involves the **prefix** of the index.

```sql
-- Only w_id condition (prefix of composite key (w_id, i_id))
EXPLAIN SELECT s.s_qty
FROM stocks s
WHERE s.w_id = '123';
```

```
Bitmap Heap Scan on stocks s  (cost=5.53..235.38 rows=160 width=2)
  Recheck Cond: (w_id = 123)
  -> Bitmap Index Scan on stocks_pkey
       (cost=0.00..5.49 rows=160 width=0)
       Index Cond: (w_id = 123)
```

### Non-Prefix Condition (Falls Back to Seq Scan)

If the condition involves only **non-prefix** columns, the multicolumn index **cannot** be used.

```sql
-- Only i_id condition (NOT the prefix of (w_id, i_id))
EXPLAIN SELECT s.s_qty
FROM stocks s
WHERE s.i_id = '7';
```

```
Seq Scan on stocks s  (cost=0.00..804.40 rows=88 width=2)
  Filter: (i_id = 7)
```

**Key rule: Multicolumn index usage depends on the PREFIX of the index columns.** Think of it like a phone book sorted by (Last Name, First Name) -- you can look up all people named "Smith" (prefix), but you can't efficiently look up all people named "John" (non-prefix) without scanning the whole book.

---

## 14. Index-Only Scan

### When It Applies

If the query only requests columns that are **stored in the index**, the scan can be done **only within the index** without accessing the table data.

```sql
-- i_id is part of stocks_pkey (w_id, i_id)
EXPLAIN SELECT s.i_id
FROM stocks s
WHERE s.w_id = '123';
```

```
Index Only Scan using stocks_pkey on stocks s
  (cost=0.29..7.09 rows=160 width=4)
  Index Cond: (w_id = 123)
```

### Non-Prefix Still Cannot Use Index

```sql
-- w_id is the prefix, but querying by i_id (non-prefix)
EXPLAIN SELECT s.w_id
FROM stocks s
WHERE s.i_id = '7';
```

```
Seq Scan on stocks s  (cost=0.00..804.40 rows=88 width=4)
  Filter: (i_id = 7)
```

Again, it **depends on the prefix** of the multicolumn index.

### Tuning to Index-Only: INCLUDE Clause

We can make the optimizer favor index-only scans by **including more columns** in the index. The `INCLUDE` clause adds columns to the index **without** making them part of the search key -- they're just "along for the ride" so the index can answer queries without going to the heap.

```sql
CREATE INDEX stocks_s_qty ON stocks(s_qty) INCLUDE (w_id, i_id);
```

```sql
EXPLAIN SELECT *
FROM stocks s
WHERE s_qty = 999;
```

```
Index Only Scan using stocks_s_qty on stocks s
  (cost=0.29..5.01 rows=41 width=10)
  Index Cond: (s_qty = 999)
```

Without this index, this query would have used a Seq Scan (no index on `s_qty`).

---

## 15. Quick Reference Tables

### Scan Type Decision Summary

| Data % to Retrieve | Index Available? | Scan Type Used |
|--------------------|------------------|----------------|
| **Large** or scattered | N/A | **Sequential Scan** (Seq Scan) |
| **Average** | Yes | **Bitmap Index Scan** + Bitmap Heap Scan |
| **Tiny** | Yes | **Index Scan** (direct access) |
| Any (only index cols needed) | Yes (covering) | **Index-Only Scan** |

### EXPLAIN vs EXPLAIN ANALYZE

| Feature | `EXPLAIN` | `EXPLAIN (ANALYZE)` |
|---------|-----------|---------------------|
| Executes query? | No | **Yes** |
| Shows estimated cost | Yes | Yes |
| Shows estimated rows | Yes | Yes |
| Shows estimated width | Yes | Yes |
| Shows actual time | No | **Yes** (milliseconds) |
| Shows actual rows | No | **Yes** |
| Shows loops | No | **Yes** |
| Shows Rows Removed by Filter | No | **Yes** |
| Shows Planning Time | No | **Yes** |
| Shows Execution Time | No | **Yes** |

### Index Behavior Summary

| Scenario | Index Created? |
|----------|---------------|
| `PRIMARY KEY` constraint | Auto B-tree unique index |
| `UNIQUE` constraint | Auto B-tree unique index |
| `FOREIGN KEY` constraint | **No** automatic index |
| Custom `CREATE INDEX` | Manual |

### Multi-Column Index Prefix Rule

For a composite index on `(A, B, C)`:

| WHERE clause | Can use index? |
|--------------|---------------|
| `A = ?` | Yes (prefix) |
| `A = ? AND B = ?` | Yes (prefix) |
| `A = ? AND B = ? AND C = ?` | Yes (full match) |
| `B = ?` | **No** (not prefix) |
| `C = ?` | **No** (not prefix) |
| `B = ? AND C = ?` | **No** (not prefix) |

### Key Commands Summary

| Command | Purpose |
|---------|---------|
| `EXPLAIN` | Show execution plan (estimates only) |
| `EXPLAIN (VERBOSE)` | Show plan with more details |
| `EXPLAIN (ANALYZE)` | Execute query, show actual measurements |
| `EXPLAIN (ANALYZE, FORMAT JSON)` | JSON format with actual measurements |
| `ANALYZE` | Collect statistics (store in pg_statistic) |
| `VACUUM` | Reorganize data, reclaim storage |
| `VACUUM ANALYZE` | Reorganize data AND collect statistics |
| `CREATE INDEX` | Create a new index |
| `CLUSTER table USING index` | Physically reorder table rows by index |
