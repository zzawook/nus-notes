# L07: Normalization — Functional Dependencies

**CS4221 — Database Tuning** | NUS Computer Science

> This lecture introduces **functional dependencies (FDs)** — the formal foundation of database normalization. You will learn how to express real-world constraints as FDs, check and enforce them in SQL, reason about them using **Armstrong's Axioms** (with proofs of soundness and completeness), compute **attribute closures**, determine **candidate keys**, find **minimal and canonical covers**, check **equivalence** of FD sets, and apply **The Chase** algorithm. Mastering the Attribute Closure Algorithm (Algorithm 1) is essential — it is the single most important tool for FD reasoning.

---

## Table of Contents

1. [Motivating Case: The Employee Table](#1-motivating-case-the-employee-table)
2. [Functional Dependencies — Definition](#2-functional-dependencies--definition)
3. [Checking and Enforcing FDs in SQL](#3-checking-and-enforcing-fds-in-sql)
4. [Trivial, Non-Trivial, and Completely Non-Trivial FDs](#4-trivial-non-trivial-and-completely-non-trivial-fds)
5. [Superkey, Candidate Key, and Prime Attribute](#5-superkey-candidate-key-and-prime-attribute)
6. [Logical Entailment](#6-logical-entailment)
7. [Armstrong's Axioms](#7-armstrongs-axioms)
8. [Soundness of Armstrong's Axioms](#8-soundness-of-armstrongs-axioms)
9. [Derived Rules: Union and Decomposition](#9-derived-rules-union-and-decomposition)
10. [Completeness of Armstrong's Axioms](#10-completeness-of-armstrongs-axioms)
11. [Closure of a Set of FDs (Σ⁺)](#11-closure-of-a-set-of-fds-σ)
12. [Attribute Closure (S⁺) and the Attribute Closure Algorithm](#12-attribute-closure-s-and-the-attribute-closure-algorithm)
13. [Equivalence of Sets of FDs](#13-equivalence-of-sets-of-fds)
14. [Minimal Cover](#14-minimal-cover)
15. [Compact and Canonical Cover](#15-compact-and-canonical-cover)
16. [Worked Exercise: Candidate Keys, Minimal Cover, Canonical Cover](#16-worked-exercise-candidate-keys-minimal-cover-canonical-cover)
17. [The Chase Algorithm](#17-the-chase-algorithm)
18. [Completeness Proof of Armstrong's Axioms (Appendix)](#18-completeness-proof-of-armstrongs-axioms-appendix)

---

## 1. Motivating Case: The Employee Table

**Scenario:** The `employee` table records salaries in an organization. A trade-union agreement imposes that **salaries are determined by position** — e.g., clerk = $2000/month, manager = $3000/month, etc.

| number | name | department | position | salary |
|---|---|---|---|---|
| 1XU3 | Dewi Srijaya | sales | clerk | 2000 |
| 5CT4 | Axel Bayer | marketing | trainee | 1200 |
| 4XR2 | John Smith | accounting | clerk | 2000 |
| 7HG5 | Eric Wei | sales | assistant manager | 2200 |
| 4DE3 | Winnie Lee | accounting | manager | 3000 |
| 8HG5 | Sylvia Tok | marketing | manager | 3000 |

**The dependency:** "Salaries are determined by position" is a real-world business rule. This kind of business rule can be translated into an integrity constraint called a **functional dependency**: {position} → {salary}.

**Valid vs. invalid instances:**

- If two tuples have the **same position** but **different salaries** (e.g., both are "manager" but one earns 3000 and the other earns 4000), that instance **violates** the FD.
- If two tuples have the **same position** and the **same salary**, no violation.
- If two tuples have **different positions**, they may have **any** salary values (same or different) — no violation, because the FD only constrains tuples that agree on the left-hand side.

> **Key insight:** An FD is a constraint on the *schema*, not on any particular instance. It says: "for **all valid** instances of this table, whenever two rows agree on position, they must agree on salary."

---

## 2. Functional Dependencies — Definition

### Formal Definition

An **instance** *r* (a table) of a relation schema *R* **satisfies** the functional dependency σ of the form **X → Y** with X ⊆ R and Y ⊆ R if and only if:

> If two tuples of *r* **agree** on their X-values, then they agree on their Y-values.

Formally:

```
r ⊨ X → Y   ↔   ∀ t1 ∈ r : ∀ t2 ∈ r : ((t1.X = t2.X) ⇒ (t1.Y = t2.Y))
```

### Terminology

X → Y can be read as any of:

- X **functionally determines** Y
- X **determines** Y
- X **implies** Y

### Important Observations

- The **empty instance** (a table with zero rows) trivially satisfies **every** FD — there are no pairs of tuples to violate anything.
- An instance with only **one row** also satisfies every FD.
- To show an FD is **violated**, you need to find two specific tuples that agree on X but disagree on Y.
- To show an FD **holds** in an instance, you must verify it for **all** pairs of tuples.
- **Critical distinction:** An instance satisfying an FD does **not** prove the FD is "true" for the schema — it just means this particular instance doesn't violate it. The FD is a constraint we *impose* based on the real-world semantics.

---

## 3. Checking and Enforcing FDs in SQL

### Checking for Violations

To check if {position} → {salary} is violated in the `employees` table:

```sql
SELECT *
FROM employees e1, employees e2
WHERE e1.position = e2.position AND e1.salary <> e2.salary;
```

If this query returns any rows, the FD is violated.

### Enforcement — What Doesn't Work

**Attempt 1: `CHECK` constraint with a subquery**

```sql
CHECK NOT EXISTS (
  SELECT * FROM employees e1, employees e2
  WHERE e1.position = e2.position AND e1.salary <> e2.salary)
```

Problem: SQL does **not** allow subqueries inside `CHECK` constraints.

**Attempt 2: Trigger function**

```sql
CREATE OR REPLACE FUNCTION fd()
RETURNS TRIGGER AS $$ BEGIN
  IF EXISTS ( SELECT *
    FROM employees e1, employees e2
    WHERE e1.position = e2.position
      AND e1.salary <> e2.salary )
  THEN RAISE EXCEPTION 'fd!'; END IF;
  RETURN NEW;
END; $$ LANGUAGE plpgsql;

CREATE CONSTRAINT TRIGGER tr
AFTER INSERT OR UPDATE OR DELETE ON employees
DEFERRABLE INITIALLY DEFERRED
FOR EACH ROW
EXECUTE FUNCTION fd();
```

This technically works, but it is **not efficient** — it re-scans the entire table on every row change.

### Enforcement — What Works (When Applicable)

We **may** enforce an FD when it corresponds to a **key** of the table by declaring a `PRIMARY KEY` or `UNIQUE` constraint:

```sql
CREATE TABLE employees (
  -- code omitted
  position VARCHAR(32) PRIMARY KEY,
  -- code omitted
);
```

However, this only works if `position` **is** a key. If position is not a key (i.e., there are other attributes not determined by position), this approach fails. The proper solution in that case is **decomposition** — splitting the table so that the FD becomes a key constraint in a smaller table. (This is the topic of normalization, covered in L08.)

---

## 4. Trivial, Non-Trivial, and Completely Non-Trivial FDs

A functional dependency X → Y is:

| Type | Condition | Intuition |
|---|---|---|
| **Trivial** | Y ⊆ X | The RHS is already "inside" the LHS — always true, tells us nothing new |
| **Non-trivial** | Y ⊄ X | At least some attribute in Y is not already in X |
| **Completely non-trivial** | Y ≠ ∅ **and** Y ∩ X = ∅ | Y has no overlap with X at all — purely new information |

**Relationship:** Completely non-trivial is a strictly stronger condition than non-trivial. Every completely non-trivial FD is non-trivial, but not vice versa.

**Convention:** Our notation allows the following (possibly degenerate) cases: ∅ → ∅, ∅ → Y, X → ∅. These are all trivially satisfied.

### Quiz Example

Given *R* = {A, B, C}, which FD is **non-trivial but not completely non-trivial**?

| FD | Classification | Reason |
|---|---|---|
| {A,C} → {A,B,C} | **Non-trivial but NOT completely non-trivial** | {A,B,C} ⊄ {A,C} (so non-trivial), but {A,B,C} ∩ {A,C} = {A,C} ≠ ∅ (so NOT completely non-trivial) |
| {A,C} → {B} | Completely non-trivial | {B} ∩ {A,C} = ∅ |
| {A,B} → {A,B} | Trivial | {A,B} ⊆ {A,B} |
| {A} → ∅ | Trivial | ∅ ⊆ {A} |

---

## 5. Superkey, Candidate Key, and Prime Attribute

Given a relation schema *R* and a set of functional dependencies Σ, a set of attributes S ⊆ R is a:

| Concept | Condition | Intuition |
|---|---|---|
| **Superkey** | S → R | S functionally determines *all* attributes in the relation |
| **Candidate key** | S → R **and** for all T ⊂ S : T →/ R | S is a **minimal** superkey — no proper subset of S is also a superkey |

A **prime attribute** is an attribute that appears in **at least one** candidate key of *R* with Σ. An attribute that does not appear in any candidate key is a **non-prime attribute**.

### How to Find Candidate Keys

The brute-force approach:

1. Compute attribute closures of all **singletons** {A}⁺, {B}⁺, {C}⁺, ... — check if any single attribute is a candidate key.
2. Compute attribute closures of all **pairs** {A,B}⁺, {A,C}⁺, ... — but **skip any pair that is a superset of an already-found candidate key** (it would be a superkey, not a candidate key).
3. Continue with **triplets**, **quadruplets**, etc., always skipping supersets of known candidate keys.
4. Stop when no new candidate keys are found at the current size level.

> **Optimization:** Any attribute that does **not appear on the right-hand side** of any FD in Σ **must** be in every candidate key (because no FD can "generate" it).

---

## 6. Logical Entailment

Let Σ be a set of functional dependencies of a relation schema *R*. Let X → Y be a functional dependency.

**X → Y is logically entailed by Σ** if and only if for all relation instances *r* satisfying all FDs σ ∈ Σ, *r* also satisfies X → Y.

Formally:

```
Σ ⊨ X → Y   ↔   ∀ r : (∀ σ ∈ Σ : r ⊨ σ) ⇒ r ⊨ X → Y
```

**Intuition:** The FD X → Y is a *logical consequence* of Σ. If you enforce all the FDs in Σ, then X → Y is automatically guaranteed — you get it "for free."

**The problem:** Armed with only the definition of functional dependencies, checking entailment directly (by considering all possible instances) is a daunting task. We need inference rules.

---

## 7. Armstrong's Axioms

Let *R* be a set of attributes. The following **inference rules** are the **Armstrong Axioms**:

### Reflexivity
```
∀ X ⊆ R : ∀ Y ⊆ R : ((Y ⊆ X) ⇒ (X → Y))
```
*Trivial functional dependencies are always valid.*

### Augmentation
```
∀ X ⊆ R : ∀ Y ⊆ R : ∀ Z ⊆ R : ((X → Y) ⇒ ((X ∪ Z) → (Y ∪ Z)))
```
*You can add the same attribute set to **both** left-hand side and right-hand side at the same time.*

### Transitivity
```
∀ X ⊆ R : ∀ Y ⊆ R : ∀ Z ⊆ R : ((X → Y ∧ Y → Z) ⇒ (X → Z))
```
*You can "short-circuit" the arrows: if X determines Y and Y determines Z, then X determines Z.*

### Notation

We can represent FDs as **production rules**. Armstrong's axioms allow us to derive other items that can be **produced**.

If a functional dependency X → Y can be derived using Armstrong's axioms from Σ, we denote this as:

```
Σ ⊢ X → Y
```

(Read: "Σ derives X → Y" or "X → Y is provable from Σ.")

---

## 8. Soundness of Armstrong's Axioms

### Theorem

All three Armstrong Axiom inference rules are **sound** (i.e., correct/valid):

```
Σ ⊢ X → Y   ⇒   Σ ⊨ X → Y
```

**In plain English:** If you can derive it using Armstrong's axioms, it is logically valid.

### Proof of Transitivity (as given in lecture)

Suppose X → Y and Y → Z are in Σ (or derived from Σ). We want to show X → Z.

1. For all valid instances *r* of *R* with Σ: ∀ t1 ∈ r : ∀ t2 ∈ r : ((t1.X = t2.X) ⇒ (t1.Y = t2.Y)) — by definition of X → Y.
2. For all valid instances *r* of *R* with Σ: ∀ t1 ∈ r : ∀ t2 ∈ r : ((t1.Y = t2.Y) ⇒ (t1.Z = t2.Z)) — by definition of Y → Z.
3. Therefore, for all valid instances *r* of *R* with Σ: ∀ t1 ∈ r : ∀ t2 ∈ r : ((t1.X = t2.X) ⇒ (t1.Z = t2.Z)) — by chaining implications.
4. Therefore, X → Z by definition of FD.

(The proofs for reflexivity and augmentation follow similar reasoning from the FD definition.)

---

## 9. Derived Rules: Union and Decomposition

Armstrong's three axioms can be combined to derive additional useful rules:

### Union Rule

```
∀ X ∈ R : ∀ Y ∈ R : ∀ Z ∈ R : (((X → Y) ∧ (X → Z)) ⇒ (X → (Y ∪ Z)))
```

**Proof sketch:** From X → Y, augment with Z to get (X ∪ Z) → (Y ∪ Z). From X → Z, augment with X to get X → (X ∪ Z). By transitivity, X → (Y ∪ Z). Then use the fact that X ⊆ X ∪ Z to simplify.

**Intuition:** If X determines Y and X determines Z separately, then X determines both together.

### Decomposition Rule

```
∀ X ∈ R : ∀ Y ∈ R : ∀ Z ∈ R : ((X → (Y ∪ Z)) ⇒ ((X → Y) ∧ (X → Z)))
```

**Intuition:** If X determines a combined set, it determines each part individually. This is the "reverse" of union.

### Why These Rules Matter

Together, union and decomposition tell us that X → {A, B, C} is equivalent to X → {A} ∧ X → {B} ∧ X → {C}. This means we can always break down FDs so the right-hand side is a single attribute, and we can always combine them back.

---

## 10. Completeness of Armstrong's Axioms

### Theorem (Hard!)

The Armstrong's axioms are **complete**:

```
(Σ ⊨ X → Y)   ⇒   (Σ ⊢ X → Y)
```

**In plain English:** If it is logically valid, you can derive it using Armstrong's axioms.

### Contrapositive Form

```
(Σ ⊬ X → Y)   ⇒   (Σ ⊭ X → Y)
```

**In plain English:** If you **cannot** derive it, it is **not** valid.

### Combined Implication of Soundness + Completeness

| Property | Statement |
|---|---|
| **Soundness** | If you can derive it, it is valid |
| **Completeness** | If you cannot derive it, it is not valid |

Together, they give us a **decision procedure**: to check whether Σ ⊨ X → Y, just try to derive X → Y using Armstrong's axioms. If you succeed, it's valid; if you fail, it's not. There is no "gap" — the axioms capture all and only the valid FDs.

---

## 11. Closure of a Set of FDs (Σ⁺)

### Definition

Let Σ be a set of functional dependencies of a relation schema *R*. The **closure** of Σ is the set of **all** functional dependencies logically entailed by Σ:

```
Σ⁺ = { X → Y | Σ ⊨ X → Y }
```

The closure contains:

- **Trivial** FDs in Σ⁺ (e.g., {A,B} → {A})
- **Non-trivial but not completely non-trivial** FDs in Σ⁺ (e.g., {A,C} → {A,B,C} where there is overlap)
- **Completely non-trivial** FDs in Σ⁺ (e.g., {A,C} → {B} where there is no overlap)

> **Practical issue:** Σ⁺ is typically enormous (exponential in the number of attributes), so we rarely compute it explicitly. Instead, we use **attribute closure**.

---

## 12. Attribute Closure (S⁺) and the Attribute Closure Algorithm

### Definition

Let Σ be a set of FDs of a relation schema *R*. The **closure of the set of attributes** S ⊆ R is the set of all attributes **functionally determined** by S:

```
S⁺ = { A ∈ R | ∃ (S → {A}) ∈ Σ⁺ }
```

### Key Implications

- S → S⁺ (by definition of functionally determined)
- ∀ A ∈ S⁺ : S → {A} (using the decomposition rule)
- If A ∈ S⁺, then S → {A}
- If A ∉ S⁺, then S does **not** functionally determine {A}

### Algorithm 1: Attribute Closure Algorithm

This is **the most important algorithm** in the FD chapter.

```
Input:  S, Σ
Output: S⁺

begin
    Ω := Σ;          // unused FDs
    Γ := S;           // closure (starts with S itself)
    while (X → Y) and (X ⊆ Γ) do     // find an unused FD whose LHS is contained in current closure
        Ω := Ω - {X → Y};            // mark it as used
        Γ := Γ ∪ Y;                  // add RHS to closure
    return Γ;
end
```

**How it works step by step:**

1. Start with Γ = S (you always determine yourself).
2. Scan through the FDs in Σ. Whenever you find X → Y where X ⊆ Γ (all attributes on the left are already in your closure), add Y to Γ.
3. Repeat until no more FDs can fire.

### Worked Example

- *R* = {A, B, C, D}
- Σ = { {A} → {B}, {C} → {A} }
- Compute {C}⁺:
  - Start: Γ = {C}
  - Use {C} → {A} (since {C} ⊆ {C}): Γ = {C, A}
  - Use {A} → {B} (since {A} ⊆ {C, A}): Γ = {C, A, B}
  - No more FDs apply.
  - Result: {C}⁺ = {A, B, C}

### Applications of Attribute Closure

| Task | How to use S⁺ |
|---|---|
| **Check if S is a superkey** | S⁺ = R? |
| **Check if Σ ⊨ X → Y** | Y ⊆ Algo1(X, Σ)? |
| **Check if Σ ⊨ X → {A}** | A ∈ Algo1(X, Σ)? |
| **Check equivalence of Σ1 and Σ2** | For all S ⊆ R, does Algo1(S, Σ1) = Algo1(S, Σ2)? (Or equivalently: for each X → Y in Σ1, check Y ⊆ Algo1(X, Σ2), and vice versa.) |

> **Algo1(S, Σ) is the most important algorithm for FD.** Almost every FD problem reduces to running this algorithm.

---

## 13. Equivalence of Sets of FDs

### Definition

Two sets of functional dependencies Σ1 and Σ2 are **equivalent** if and only if they have **the same closure**:

```
Σ1 ≡ Σ2   ↔   Σ1⁺ = Σ2⁺
```

We say that Σ1 is a **cover** of Σ2 (and also Σ2 is a cover of Σ1).

### How to Check Equivalence

Two practical methods:

1. **Per-FD check:** For each (X → Y) ∈ Σ1, verify Σ2 ⊨ (X → Y) (i.e., Y ⊆ Algo1(X, Σ2)). **AND** for each (X → Y) ∈ Σ2, verify Σ1 ⊨ (X → Y) (i.e., Y ⊆ Algo1(X, Σ1)).

2. **Attribute closure check:** For each S ⊆ R, Algo1(S, Σ1) = Algo1(S, Σ2).

Method 1 is usually much more practical.

---

## 14. Minimal Cover

### Definition

A set of functional dependencies Σ is **minimal** if and only if all three conditions hold:

1. **The right-hand side of every dependency is minimal.** Every FD is of the form X → {A} (singleton RHS).

2. **The left-hand side of every dependency is minimal.** For every FD X → {A}, there is **no** FD Y → {A} in Σ⁺ such that Y ⊂ X. (You cannot remove any attribute from the LHS and still derive the same RHS.)

3. **The set itself is minimal.** None of the FDs in Σ can be derived from the other FDs in Σ. (No redundant FDs.)

### Algorithm 2: Minimal Cover Algorithm

```
Input:  Σ
Output: Minimal Cover of Σ

Step 1: Simplify (minimize) the RIGHT-HAND SIDE of every σ in Σ.
        Decompose each (X → Y) ∈ Σ into (X → {A}) for each A ∈ Y.

Step 2: Simplify (minimize) the LEFT-HAND SIDE of every σ in Σ.
        For each (X → {A}), if Σ ⊨ (X - {B} → {A}) for some B ∈ X,
        then replace (X → {A}) with ((X - {B}) → {A}).

Step 3: Simplify (minimize) the SET Σ.
        For each (X → {A}), if (Σ - (X → {A})) ⊨ (X → {A}),
        then remove (X → {A}) from Σ.
```

> **IMPORTANT:** The three simplification steps **must be done in the given order** (RHS first, then LHS, then set). Changing the order may yield an incorrect result.

### Key Properties

- Every set of FDs Σ has **at least one** minimal cover.
- The minimal cover is **not necessarily unique** — different orderings of the FDs during the algorithm can yield different (but all valid) minimal covers.
- Some minimal covers may be **unreachable** by this particular algorithm.

### Quiz Example

*R* = {A, B, C}, Σ = { {A} → {B}, {B} → {C}, {A} → {C} }. Which is a minimal cover?

| Option | Verdict | Why |
|---|---|---|
| { {A} → {B}, {A,B} → {C} } | NO | Violated condition 2 — LHS {A,B} is not minimal (since {A} → {B} → {C}, so {A} → {C}) |
| { {A} → {B,C}, {A} → {C} } | NO | Violated condition 1 — RHS {B,C} is not a singleton |
| **{ {A} → {B}, {B} → {C} }** | **YES** | Minimal and equivalent to Σ (since {A} → {C} is derivable by transitivity) |
| { {B} → {C}, {A} → {C} } | NO | Not a cover of Σ — we lose {A} → {B} |

---

## 15. Compact and Canonical Cover

### Compact

A set of FDs Σ is **compact** if and only if there is **no** different FDs with the **same left-hand side**.

- Σ = { {A} → {B}, {A} → {C} } is **not** compact (two FDs with LHS = {A}).
- Σ = { {A} → {B,C} } **is** compact (single FD with LHS = {A}).

### Canonical Cover

A set of FDs Σ is **canonical** (also called a **canonical cover**) if and only if:

1. The set is **compact** (no duplicate LHS).
2. The **left-hand side** of every dependency is **minimal**.
3. The **set itself** is **minimal** (no redundant FDs).

Equivalently: start from a minimal cover, then apply the **union rule** to merge FDs with the same LHS.

### Existence Theorems

| Type | Existence |
|---|---|
| **Minimal cover** | Every Σ has at least one |
| **Compact cover** | Every Σ has at least one |
| **Canonical cover** | Every Σ has at least one |

**Proof idea:**

- **Minimal:** Run Algorithm 2 on Σ. The output could be Σ itself (if already minimal).
- **Compact:** Apply the union rule to all FDs with the same LHS.
- **Canonical:** Run Algorithm 2, then apply union rule.

---

## 16. Worked Exercise: Candidate Keys, Minimal Cover, Canonical Cover

**Given:**
- *R* = {A, B, C, D, E}
- Σ = { {A,B} → {C,D,E}, {A,C} → {B,D,E}, {B} → {C}, {C} → {B}, {C} → {D}, {B} → {E}, {C} → {E} }

### Part 1: Finding Candidate Keys

**Singletons:**

| Set | Closure | Superkey? |
|---|---|---|
| {A}⁺ | {A} | No |
| {B}⁺ | {B,C,D,E} | No |
| {C}⁺ | {B,C,D,E} | No |
| {D}⁺ | {D} | No |
| {E}⁺ | {E} | No |

No singleton is a candidate key.

**Pairs (skipping supersets of known candidate keys):**

| Set | Closure | Superkey? |
|---|---|---|
| {A,B}⁺ | **{A,B,C,D,E}** | **Yes → Candidate key** |
| {A,C}⁺ | **{A,B,C,D,E}** | **Yes → Candidate key** |
| {A,D}⁺ | {A,D} | No |
| {A,E}⁺ | {A,E} | No |
| {B,C}⁺ | {B,C,D,E} | No |
| {B,D}⁺ | {B,C,D,E} | No |
| {B,E}⁺ | {B,C,D,E} | No |
| {C,D}⁺ | {B,C,D,E} | No |
| {C,E}⁺ | {B,C,D,E} | No |
| {D,E}⁺ | {D,E} | No |

**Triplets** (only those not containing {A,B} or {A,C}):

| Set | Closure | Superkey? |
|---|---|---|
| {A,D,E}⁺ | {A,D,E} | No |
| {B,C,D}⁺ | {B,C,D,E} | No |
| {B,C,E}⁺ | {B,C,D,E} | No |
| {B,D,E}⁺ | {B,C,D,E} | No |
| {C,D,E}⁺ | {B,C,D,E} | No |

**Quadruplet** (only {B,C,D,E}):
- {B,C,D,E}⁺ = {B,C,D,E} — No.

No need to check quintuplet (it would be R itself, which is always a superkey but contains both candidate keys as proper subsets).

**Candidate Keys: {A,B} and {A,C}**

Any superset of a candidate key is a superkey but **not** a candidate key.

### Part 2: Computing the Minimal Cover

**Step 1 — Simplify RHS** (decompose to singletons):

```
{A,B} → {C},  {A,B} → {D},  {A,B} → {E}
{A,C} → {B},  {A,C} → {D},  {A,C} → {E}
{B} → {C},  {C} → {B},  {C} → {D},  {B} → {E},  {C} → {E}
```

**Step 2 — Simplify LHS:**

For each FD with a composite LHS, check if any attribute can be removed:

- {A,B} → {C}: Can we remove A? Check {B}⁺ = {B,C,D,E} ∋ C. **Yes!** Replace with {B} → {C}.
- {A,B} → {D}: Can we remove A? Check {B}⁺ ∋ D. **Yes!** Replace with {B} → {D}.
- {A,B} → {E}: Can we remove A? Check {B}⁺ ∋ E. **Yes!** Replace with {B} → {E}.
- {A,C} → {B}: Can we remove A? Check {C}⁺ ∋ B. **Yes!** Replace with {C} → {B}.
- {A,C} → {D}: Can we remove A? Check {C}⁺ ∋ D. **Yes!** Replace with {C} → {D}.
- {A,C} → {E}: Can we remove A? Check {C}⁺ ∋ E. **Yes!** Replace with {C} → {E}.

After removing duplicates:

```
{B} → {C},  {B} → {D},  {B} → {E}
{C} → {B},  {C} → {D},  {C} → {E}
```

**Step 3 — Simplify the set** (remove transitive/redundant FDs):

- {B} → {D}: Since {B} → {C} and {C} → {D}, we can derive {B} → {D} from the remaining FDs. **Remove.**
- {B} → {E}: Since {B} → {C} and {C} → {E}, we can derive {B} → {E} from the remaining FDs. **Remove.**

Result — **one possible minimal cover:**

```
Σ_min = { {B} → {C}, {C} → {B}, {C} → {D}, {C} → {E} }
```

> **Note:** Other minimal covers are possible (e.g., { {B} → {C}, {B} → {D}, {B} → {E}, {C} → {B} } or { {B} → {C}, {B} → {D}, {C} → {B}, {C} → {E} }). They can be achieved by considering the FDs in different order during step 3.

### Part 3: Computing the Canonical Cover

Starting from the minimal cover, apply the **union rule** to merge FDs with the same LHS:

From Σ_min = { {B} → {C}, {C} → {B}, {C} → {D}, {C} → {E} }:

- Merge {C} → {B}, {C} → {D}, {C} → {E} into {C} → {B,D,E}

**Canonical cover:** { {B} → {C}, {C} → {B,D,E} }

An alternative canonical cover (from a different minimal cover): { {B} → {C,D}, {C} → {B,E} }

---

## 17. The Chase Algorithm

### What is The Chase?

Let Σ be a set of functional dependencies on a relation schema *R*. **The Chase** is an algorithm that solves the **decision problem** of whether a functional dependency σ is satisfied by *R* with Σ. In other words, it checks:

```
(R with Σ) ⊨ σ
```

The Chase also works for multi-valued dependencies, but the lecture focuses on FDs.

### How The Chase Works (for FDs)

**Goal:** Determine if Σ ⊨ X → Y (i.e., {C,D} → {A,B} given Σ = { {A} → {B}, {C} → {A} }).

**Step 1 — Setup:** Create an instance *r* of *R* with **two tuples** and **distinct values** for all attributes.

| A | B | C | D |
|---|---|---|---|
| α | α | α | α |
| a₁ | b₁ | c₁ | d₁ |

The first row uses **distinguished variables** (all α). The second row uses **subscripted variables** (a₁, b₁, c₁, d₁).

**Step 2 — Initialization:** We want to chase X → Y, so make all X-attributes (here {C,D}) the **same** in both tuples:

| A | B | C | D |
|---|---|---|---|
| α | α | α | α |
| a₁ | b₁ | **α** | **α** |

**Step 3 — Chasing:** Repeatedly apply FDs from Σ. For each FD V → W: whenever two tuples agree on V-attributes, make their W-attributes the same (always prefer the distinguished variable α over subscripted variables).

- Use {C} → {A}: Both tuples have C = α, so make A the same. Change a₁ to α.

| A | B | C | D |
|---|---|---|---|
| α | α | α | α |
| **α** | b₁ | α | α |

- Use {A} → {B}: Both tuples have A = α, so make B the same. Change b₁ to α.

| A | B | C | D |
|---|---|---|---|
| α | α | α | α |
| α | **α** | α | α |

**Step 4 — Check result:** Examine whether the table satisfies X → Y (here {C,D} → {A,B}). Both tuples agree on {C,D} and now also agree on {A,B}. So **yes, Σ ⊨ {C,D} → {A,B}**.

> **Important caveat:** During the chase, *r* might also end up satisfying other FDs (like {B} → {A}). But this is a **coincidence** — The Chase can only answer the specific question it was set up for.

### Theorems about The Chase

| Theorem | Statement |
|---|---|
| **Sound and Complete** | *r* ⊨ σ is equivalent to Σ ⊨ σ |
| **Termination** | The Chase always terminates for FDs (and also for multi-valued dependencies) |
| **Counter-example** | If The Chase does NOT make the two tuples agree on Y, then the final table is a **counter-example** instance that satisfies all of Σ but violates σ |

### Lossless-Join

The Chase will be used to check **lossless-join decomposition** — this will be covered in L08.

---

## 18. Completeness Proof of Armstrong's Axioms (Appendix)

This proof shows the contrapositive: if Σ ⊬ X → {A} (you cannot derive X → {A} from Σ using Armstrong's axioms), then Σ ⊭ X → {A} (there exists an instance satisfying Σ but violating X → {A}).

### Proof (Contrapositive)

**Given:** Σ ⊬ X → {A} (i.e., A ∉ X⁺ where X⁺ is computed by Algo1).

1. We know that Σ ⊬ X → {A}. *(Assumption.)*
2. Therefore, {A} ∉ X. *(Otherwise, we could prove it using Reflexivity.)*
3. **Construct** instance *r* of *R*: Let S = { B | Σ ⊢ X → {B} } (the attribute closure of X). Build a table with **two tuples**:
   - Row 1: all 1s across all attributes.
   - Row 2: 1s for attributes in S, 0s for attributes in R - S.
   - By construction, {A} ∉ S (since Σ ⊬ X → {A}).
   - The two tuples differ only in R - S attributes.

4. **Claim:** *r* satisfies every FD in Σ. Suppose (for contradiction) that there exists Σ ⊢ V → W such that *r* ⊭ V → W.
   - A. Therefore, V ⊆ S. *(Otherwise, the two tuples differ on V, so V → W can't be violated.)*
   - B. Therefore, Σ ⊢ X → V by construction of S and the Union rule.
   - C. Therefore, Σ ⊢ X → W by transitivity of (B) and V → W.
   - D. Therefore, W ⊆ S by construction of S.
   - E. Therefore, *r* ⊨ V → W since (V ∪ W) ⊆ S, and both tuples agree on all S-attributes. *(Contradiction with our assumption.)*
   - F. Hence, if Σ ⊢ V → W, then *r* ⊨ V → W. So *r* satisfies all FDs in Σ.

5. Since Σ ⊢ X → Y implies *r* ⊨ X → Y (from step 4), *r* satisfies all of Σ.
6. We know ∀ (X → Y) ∈ Σ : (Σ ⊢ X → Y) *(trivially, by the empty derivation).*
7. Therefore *r* ⊨ X → Y for all (X → Y) ∈ Σ by step 5.
8. We observe that *r* ⊭ X → {A} by construction *(A ∉ S means the two tuples agree on X (since X ⊆ S) but disagree on A).*
9. Therefore, X → {A} ∉ Σ⁺ by definition of entailment — we found an instance satisfying Σ but not X → {A}.

Hence, all instances that verify Σ must verify X → {A} for it to be entailed. We constructed one that does not. **QED.**

---

## Quick Reference: Key Algorithms Summary

| Algorithm | Input | Output | Purpose |
|---|---|---|---|
| **Algo 1: Attribute Closure** | S, Σ | S⁺ | Compute all attributes determined by S under Σ |
| **Algo 2: Minimal Cover** | Σ | Minimal Σ' | Simplify FD set: singleton RHS, minimal LHS, no redundancy |
| **The Chase** | Σ, σ | Yes/No (+ counter-example) | Decide if Σ ⊨ σ for a specific FD σ |

---

## Exam Preparation Checklist

- Be able to determine whether a given table instance **satisfies** or **violates** a given FD.
- Know the **definitions** of trivial, non-trivial, completely non-trivial FDs and be able to classify examples.
- Be able to compute **attribute closures** using Algorithm 1 quickly and accurately.
- Be able to find **all candidate keys** systematically (singleton → pairs → triplets, pruning supersets).
- Know Armstrong's Axioms (reflexivity, augmentation, transitivity) and derived rules (union, decomposition) — both the formal statements and intuitive meanings.
- Understand the difference between Σ ⊢ (derivability) and Σ ⊨ (entailment), and why soundness + completeness makes them equivalent.
- Be able to run the **Minimal Cover Algorithm** (3 steps, in order) and then produce a **Canonical Cover**.
- Be able to run **The Chase** algorithm step by step.
- Understand the **completeness proof** at a high level (construct a counter-example instance from the attribute closure).
