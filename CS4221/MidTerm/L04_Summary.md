# L04: Relational Algebra

**CS4221 - Database Tuning** | NUS Computer Science

---

## Table of Contents

1. [Running Example: Game Store](#1-running-example-game-store)
2. [Algebra: Definition](#2-algebra-definition)
3. [Relational Algebra](#3-relational-algebra)
4. [Propositional Logic and Relational Operators](#4-propositional-logic-and-relational-operators)
5. [Selection (σ)](#5-selection-σ)
6. [Projection (π)](#6-projection-π)
7. [Renaming (ρ)](#7-renaming-ρ)
8. [Set Operations (∪, ∩, −)](#8-set-operations-----)
9. [Cross Product (×)](#9-cross-product-)
10. [Optimization: Selection Equivalences](#10-optimization-selection-equivalences)
11. [Optimization: Projection Equivalences](#11-optimization-projection-equivalences)
12. [Other Optimization Techniques](#12-other-optimization-techniques)

---

## 1. Running Example: Game Store

**Apasaja Pte Ltd** -- online app store with the following data:

- **Customers:** first name, last name, date of birth, e-mail, date and country of registration, customer identifier
- **Games:** name, version, price (fixed per version)
- **Downloads:** which version of which game each customer has downloaded (download date not essential)

**ER Diagram:** Two entity sets (`customers`, `games`) connected by a `downloads` relationship.
- `customers` has attributes: first_name, last_name, email (key), customerid (key), dob, since, country
- `games` has attributes: name (key), version (key), price

---

## 2. Algebra: Definition

### Mathematical Algebra

An **algebra** is a mathematical system consisting of:
- **Operands:** variables or values from which new values can be constructed
- **Operators:** symbols denoting procedures that construct new values from the given values

### Equivalence and Query Optimization

With algebra, we can find **equivalent expressions**:

```
(a × b) + (a × c) ≡ a × (b + c)
```

Executing the expression on the right may be faster. This is the basis of **query optimization** in DBMS.

---

## 3. Relational Algebra

### Definition

A **relational algebra** is an algebra system for SQL queries consisting of:
- **Operands:** relations/tables
- **Operators:** transformations from one or more input relations into one output relation

### Key Properties

- Unlike SQL, relational algebra is an **imperative query language** (specifies *how* to retrieve data)
- It forms the mathematical foundation of relational database engines
- SQL queries are **processed** into relational algebra expressions and **evaluated**

### Query Pipeline

```
Declarative SQL query
        ↓           Query Planning
Imperative relational algebra expression
        ↓           Query Optimization
Optimized relational algebra expression    (viewable via EXPLAIN)
```

### Operators Overview

| Unary Operation | Symbol | Set Operation | Symbol | Binary Operation | Symbol |
|---|---|---|---|---|---|
| Selection | σ | Union | ∪ | Cross Product | × |
| Projection | π | Intersection | ∩ | Inner/Natural Join | ⋈ |
| Renaming | ρ | Set Difference | − | Outer Join | ⟕ ⟖ ⟗ |

### Semantics: Relation vs SQL

| | **Relational Algebra** | **SQL** |
|---|---|---|
| **Based on** | Relations (sets) | Multi-sets (bags) |
| **Duplicate rows** | **No** duplicates | Duplicates **allowed** |
| **Row** | Tuple | Row |
| **Column order** | Matters (tuples are ordered) | Matters |
| **Row order** | Does **not** matter (it's a set) | Does not matter |

---

## 4. Propositional Logic and Relational Operators

### Propositional Logic

The model semantics of **propositional logic** is defined by truth tables of connectives:
- **Conjunction** (AND, ∧)
- **Disjunction** (OR, ∨)
- **Negation** (NOT, ¬)

| p | q | p ∧ q | p ∨ q | ¬p | ¬(p ∧ ¬q) | ¬p ∨ q | p → q |
|---|---|---|---|---|---|---|---|
| T | T | T | T | F | T | T | T |
| F | T | F | T | T | T | T | T |
| T | F | F | T | F | **F** | **F** | **F** |
| F | F | F | F | T | T | T | T |

**Equivalence:** Two propositional formulae are **equivalent** if and only if they have the same truth table.

> Note: ¬(p ∧ ¬q) ≡ ¬p ∨ q ≡ p → q (all have the same truth table)

### Relational Operators

Standard relational operators: **equal to** (=), **not equal to** (≠), **less/greater than** (</>), and **less/greater than or equal to** (≤/≥).

**Ordering:**
- For `TEXT`: S1 < S2 means S1 is *lexicographically* smaller than S2
- For `DATE`: D1 < D2 means D1 happened before D2

> Without NULL values, we do not have to worry about the problem with = and ≠ operators.

---

## 5. Selection (σ)

### Definition

**σ[c](R)** selects all tuples/rows **t** from the relation **R** that **satisfies the selection condition c**. In other words, c(t) is true. This is similar to a `WHERE` clause in SQL.

### Visualization

From relation R, only rows where c(t) ≡ True are kept; rows where c(t) ≢ True are removed.

### Example #1: Simple Condition

**Query:** Find the different customers in Singapore.

```
σ[country = 'Singapore'](customers)
```

**SQL equivalent:**
```sql
SELECT *
FROM customers c
WHERE c.country = 'Singapore';
```

### Example #2: Compound Condition

**Query:** Find customers that (a) born in the year 2010, or (b) joined before 2024.

```
σ[(dob >= '2010-01-01' ∧ dob <= '2010-12-31')
  ∨ (since < '2024-01-01')](customers)
```

**SQL equivalent:**
```sql
SELECT *
FROM customers c
WHERE (c.dob >= '2010-01-01' AND c.dob <= '2010-12-31')
   OR (c.since < '2024-01-01');
```

---

## 6. Projection (π)

### Definition

**π[l](R)** keeps only the columns specified in the ordered list **l** and in the same order. This is similar to `SELECT` clause in SQL, but **cannot add more columns**. Duplicate rows are **automatically deduplicated**.

### Visualization

From relation R with columns (A1, A2, A3, A4), applying π[A1, A4, A2] keeps only those 3 columns in the specified order, with duplicates removed.

### Example #1

**Query:** Find the different customerid of customers that downloaded at least one game.

```
π[customerid](downloads)
```

**SQL equivalent:**
```sql
SELECT DISTINCT d.customerid
FROM downloads d;
```

### Example #2: Combined Selection + Projection

**Query:** Find the different first and last names of customers born in 2010 or joined before 2024.

```
π[first_name, last_name](
  σ[(dob >= '2010-01-01' ∧ dob <= '2010-12-31')
    ∨ (since < '2024-01-01')](customers))
```

**SQL equivalent:**
```sql
SELECT DISTINCT c.first_name, c.last_name
FROM customers c
WHERE (c.dob >= '2010-01-01' AND c.dob <= '2010-12-31')
   OR (c.since < '2024-01-01');
```

> **Note:** 514 rows with selection only, but 508 rows after projection (6 duplicates removed).

---

## 7. Renaming (ρ)

### Rename Relation

**ρ(R1, R2)** can be used to **rename** the relation. We can now refer to R1 as R2 **but only within the query**. This is similar to table alias in `FROM` clause in SQL.

### Rename Attributes

**ρ(R1, R2(A1 → B1))** can be used to **rename** the relation and some of its attributes. The attribute R1.A1 (or simply A1) can now be referred as R2.B1 (or simply B1, assuming no duplicate columns) **but only within the query**.

### SQL Mapping (SELECT-FROM-WHERE + Dot Notation)

For good practice, we prefer to have all tables always be renamed in SQL. This notation is also available in relational algebra. We also allow **dot notation** `r.attr` to simplify the writing.

```sql
SELECT DISTINCT r.attr          →    π[r.attr](
FROM rel r                       →      σ[c](
WHERE c;                         →        ρ(rel, r)))
```

> **Note:** Different sources may use different conventions/notation. The notation in this course adheres closer to good SQL notation.

---

## 8. Set Operations (∪, ∩, −)

### Operators

| Operation | Symbol | SQL | Visualization |
|---|---|---|---|
| **Union** | R ∪ S | `SELECT * FROM R UNION SELECT * FROM S` | All rows in R or S (or both) |
| **Intersection** | R ∩ S | `SELECT * FROM R INTERSECT SELECT * FROM S` | Only rows in both R and S |
| **Set Difference** | R − S | `SELECT * FROM R EXCEPT SELECT * FROM S` | Rows in R but not in S |

**Requirement:** The two relations must be **union-compatible** (basically, they must have the same column types).

### Example #1: Intersection

**Query:** Find customers who downloaded **both** version 1.0 and version 2.0 of game Aerified.

```
Q1 := π[customerid](σ[name = 'Aerified' ∧ version = '1.0'](downloads))
Q2 := π[customerid](σ[name = 'Aerified' ∧ version = '2.0'](downloads))
Q1 ∩ Q2
```

Result: 3 rows

### Example #2: Set Difference

**Query:** Find customers who downloaded version 1.0 **but not** version 2.0 of game Aerified.

```
Q1 := π[customerid](σ[name = 'Aerified' ∧ version = '1.0'](downloads))
Q2 := π[customerid](σ[name = 'Aerified' ∧ version = '2.0'](downloads))
Q1 − Q2
```

Result: 13 rows

> **Question:** In the equivalent SQL code using `EXCEPT`, do you need DISTINCT? No -- set operations in SQL already eliminate duplicates.

---

## 9. Cross Product (×)

### Definition

**R1 × R2** **combines** each row of R1 with each row of R2 and keeps the *n* columns of R1 and the *m* columns of R2.

**Properties:**
- The number of columns is **n + m**
- The number of rows is **n × m**

### Example

| R1: a | b | | R2: c | d | e |
|---|---|---|---|---|---|
| 1 | 2 | | A | B | C |
| 3 | 4 | | D | E | F |
| | | | G | H | I |

R1 × R2 produces **2 × 3 = 6 rows** with **2 + 3 = 5 columns**:

| a | b | c | d | e |
|---|---|---|---|---|
| 1 | 2 | A | B | C |
| 1 | 2 | D | E | F |
| 1 | 2 | G | H | I |
| 3 | 4 | A | B | C |
| 3 | 4 | D | E | F |
| 3 | 4 | G | H | I |

### Cross Product Example with Join Condition

**Query:** Find the different email addresses of customers who downloaded at least one version of game Aerified.

**Without DISTINCT (80 rows):**
```sql
SELECT c.email
FROM customers c, downloads d
WHERE c.customerid = d.customerid
  AND d.name = 'Aerified';
```

**With DISTINCT (63 rows):**
```sql
SELECT DISTINCT c.email
FROM customers c, downloads d
WHERE c.customerid = d.customerid
  AND d.name = 'Aerified';
```

**Relational Algebra:**
```
π[c.email](
  σ[c.customerid = d.customerid
    ∧ d.name = 'Aerified'](
    ρ(customers, c) × ρ(downloads, d)
))
```

### Important Identity

**Inner join as selection on cross product:**

```
R1 ⋈[c] R2  ≡  σ[c](R1 × R2)
```

### Cross Product Example #2: Self-Join

**Query:** Find the different pair of customerid (cid1, cid2) such that cid1 and cid2 are different customers that have downloaded at least one game with the same name.

```sql
SELECT DISTINCT d1.customerid, d2.customerid
FROM downloads d1, downloads d2
WHERE d1.customerid <> d2.customerid
  AND d1.name = d2.name;
```

Result: 189,056 rows

---

## 10. Optimization: Selection Equivalences

### Sigma-Cascade (Selection Reordering)

The following queries are equivalent:

```
σ[θ1 ∧ θ2](E)  ≡  σ[θ1](σ[θ2](E))  ≡  σ[θ2](σ[θ1](E))
```

**Key insight:** We can **reorder** the selection operations. Additionally, we can **combine** them into a conjunction.

- **Reordering:** More efficient if the inner sub-expression produces a smaller table
- **Conjunction:** Dependent on implementation; may be more efficient than multiple selections

### Selection Distributes over Cross Product

The following queries are equivalent:

```
σ[θ ∧ θ1 ∧ θ2](E1 × E2)  ≡  σ[θ](σ[θ1](E1) × σ[θ2](E2))
```

**Condition:** Only if θ1 uses only attributes in E1 and θ2 uses only attributes in E2.

**Reasoning:** If the intermediate operations (σ[θ1](E1) and σ[θ2](E2)) produce smaller tables, it is more efficient to filter before the cross product.

**Generalizes** to multiple cross products:
```
σ[θ](C × d × S)  →  σ[θ2](σ[θ1](C × d) × S)
                  →  σ[θ1](C × σ[θ2](d × S))
```

### Selection Distributes over Union

The following queries are equivalent:

```
σ[θ](E1 ∪ E2)  ≡  σ[θ](E1) ∪ σ[θ](E2)
```

**Condition:** θ needs to be valid for both E1 and E2 (union-compatibility may mean different attribute names).

**Reasoning:** The hope is the intermediate table is smaller.

> **Note:** Selection also distributes over set difference: σ[θ](E1 − E2) ≡ σ[θ](E1) − E2

---

## 11. Optimization: Projection Equivalences

### Pi-Cascade (Projection Cascade)

The following queries are equivalent:

```
π[L1](E)  ≡  π[L1](π[L2](...(π[Ln](E))))
```

**Condition:** Only if L1 ⊆ L2 ⊆ ... ⊆ Ln. Otherwise, it may be an error.

**Reasoning:** The end result is the same whether we remove attribute-by-attribute or remove all unnecessary attributes at the same time.

### Projection Distributes over Union, Intersection, Difference

The following queries are equivalent:

```
π[L](E1 ∪ E2)  ≡  π[L](E1) ∪ π[L](E2)
```

This assumes E1 and E2 are union-compatible.

---

## 12. Other Optimization Techniques

### Optimization Summary

The essence of optimization is to find **equivalent** queries such that the **expected runtime is shorter**.

> **Implementation Dependent:** The "expected runtime" is dependent on the implementation. Our relational algebra is currently not optimized and is limited in its available operations. Experiment with the DBMS you are using.

### Typical Optimization Steps

1. **Cardinality/Cost Estimation:** Estimate the size of the output or cost of operation (e.g., IO, etc.)
2. **Path/Query Rewriting:** Explore the possible equivalent relational algebra expressions and/or queries
3. **Other techniques:** Use of **index**, **caching**, **statistics**, etc.

---

## Quick Reference: All Operators

| Operation | Symbol | Type | SQL Equivalent |
|---|---|---|---|
| **Selection** | σ[c](R) | Unary | `WHERE c` |
| **Projection** | π[l](R) | Unary | `SELECT DISTINCT l` |
| **Renaming** | ρ(R, S) | Unary | `FROM R AS S` |
| **Union** | R ∪ S | Set | `R UNION S` |
| **Intersection** | R ∩ S | Set | `R INTERSECT S` |
| **Set Difference** | R − S | Set | `R EXCEPT S` |
| **Cross Product** | R × S | Binary | `FROM R, S` |
| **Inner/Natural Join** | R ⋈ S | Binary | `R NATURAL JOIN S` / `R JOIN S ON c` |

## Quick Reference: Optimization Rules

| Rule | Equivalence | Condition |
|---|---|---|
| **Sigma-Cascade** | σ[θ1 ∧ θ2](E) ≡ σ[θ1](σ[θ2](E)) | Always |
| **Sigma-Reorder** | σ[θ1](σ[θ2](E)) ≡ σ[θ2](σ[θ1](E)) | Always |
| **σ distributes over ×** | σ[θ ∧ θ1 ∧ θ2](E1 × E2) ≡ σ[θ](σ[θ1](E1) × σ[θ2](E2)) | θ1 uses only E1 attrs, θ2 uses only E2 attrs |
| **σ distributes over ∪** | σ[θ](E1 ∪ E2) ≡ σ[θ](E1) ∪ σ[θ](E2) | θ valid in both |
| **σ distributes over −** | σ[θ](E1 − E2) ≡ σ[θ](E1) − E2 | θ valid in E1 |
| **Pi-Cascade** | π[L1](E) ≡ π[L1](π[L2](...(π[Ln](E)))) | L1 ⊆ L2 ⊆ ... ⊆ Ln |
| **π distributes over ∪/∩/−** | π[L](E1 ∪ E2) ≡ π[L](E1) ∪ π[L](E2) | Union-compatible |
| **Join = σ on ×** | R ⋈[c] S ≡ σ[c](R × S) | Always |
