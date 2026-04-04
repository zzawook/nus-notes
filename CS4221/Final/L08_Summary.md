# Lecture 08: Normalization — Normal Forms and Decomposition

> **What this covers:** This lecture builds on L07 (Functional Dependencies) and addresses the central question: how do we design good relational schemas? You will learn why poor designs cause anomalies, how to define and check normal forms (2NF, 3NF, BCNF), and how to transform a schema into a better one using two key algorithms — the **Decomposition Algorithm** (Algorithm 3, for BCNF) and the **Synthesis Algorithm** (Algorithm 4, for 3NF). You will also learn the two critical criteria for a good decomposition — **losslessness** and **dependency preservation** — and how to verify them using **The Chase** and **projected functional dependencies**. A detailed case study (Sentosa University of Technology) ties everything together.

---

## 1. Anomalies: Why Bad Schema Design Hurts

**Context:** Consider an `employee` table with attributes `(number, name, department, position, salary)`. A trade union agreement imposes that salaries are determined by position — i.e., there is a functional dependency `{position} → {salary}`.

| number | name | department | position | salary |
|---|---|---|---|---|
| 1XU3 | Dewi Srijaya | sales | clerk | 2000 |
| 5CT4 | Axel Bayer | marketing | trainee | 1200 |
| 4XR2 | John Smith | accounting | clerk | 2000 |
| 7HG5 | Eric Wei | sales | assistant manager | 2200 |
| 4DE3 | Winnie Lee | accounting | manager | 3000 |
| 8HG5 | Sylvia Tok | marketing | manager | 4000 |
| NULL | NULL | NULL | engineer | 4000 |

This table suffers from four kinds of anomalies, all rooted in the fact that the FD `{position} → {salary}` is not properly enforced by the schema's key constraints:

### 1.1 Unenforced Functional Dependencies

The FD `{position} → {salary}` is violated: two managers (Winnie Lee and Sylvia Tok) have different salaries (3000 vs 4000). We cannot enforce this FD by making `position` the primary key because `position` is not a candidate key of the table — multiple employees can share the same position.

### 1.2 Redundancy

The fact that "a clerk earns 2000" is stored multiple times (rows for Dewi Srijaya and John Smith both record `clerk → 2000`). This wastes space and, more importantly, creates opportunities for inconsistency if one copy is updated but not the other.

### 1.3 NULL Values

If we want to record the salary of an engineer but have no engineer employee yet, we must insert a row with NULL values for `number`, `name`, and `department`. If `number` is the primary key, we cannot even do this — primary keys cannot be NULL.

### 1.4 Deletion Anomaly

Axel Bayer is the only trainee. If Axel resigns and we delete his row, we lose the information that a trainee's salary is 1200. The act of removing an employee accidentally destroys salary data.

**Key insight from the annotations:** FDs should ideally be enforced via PK / CK (candidate key), UNIQUE, and NOT NULL constraints. When the schema is poorly designed, FDs cannot be captured by these constraints alone, leading to all the anomalies above.

---

## 2. Normal Forms: Progressively Stronger Requirements

The lecture develops the normal form definitions through a sequence of ideas, each refining the previous one. Understanding this progression is essential — the exam can test why each refinement was needed.

### 2.1 Idea #1: Full Dependence on the Primary Key

**Initial idea:** Require that every attribute **fully depends** on the primary key.

**Definition:** Y **fully depends** on X in X → Y if Y is not dependent on any proper subset of X. In other words, X is minimal — you cannot remove any attribute from X and still determine Y.

**Problem:** Prime attributes (attributes that belong to some candidate key) do not fully depend on the primary key when there is a composite key. For example, if {A, B} is the primary key, then A → A is trivially true, and A is a proper subset of {A, B}.

**Refinement:** Require that every **non-prime** attribute fully depends on the primary key.

**Further problem:** There could be **more than one** candidate key!

**Final refinement for Idea #1:** Require that every **non-prime** attribute fully depends on **each** candidate key. This gives us **2NF**.

### 2.2 Second Normal Form (2NF)

**Definition:** A relation R with a set of functional dependencies Σ is in **Second Normal Form (2NF)** if for every functional dependency X → {A} ∈ Σ⁺:

- X → {A} is **trivial**, or
- A is a **prime attribute**, or
- X is **not** a proper subset of a **candidate key**

**Equivalently, NOT in 2NF** if there is a functional dependency X → {A} ∈ Σ⁺ where:

- X → {A} is **non-trivial**, **and**
- A is **not** a prime attribute, **and**
- X **is** a proper subset of a candidate key

Think of it this way: 2NF disallows partial key dependencies — a non-prime attribute must not depend on just *part* of a candidate key.

### 2.3 Idea #2: Eliminating Transitive Dependence

**Idea #2:** Require that no attribute is **transitively dependent** on any candidate key.

**Definition:** Z is **transitively dependent** on X in X → Z if there is another attribute Y that is **not** a candidate key such that X → Y and Y → Z.

**Why this matters:** Transitive dependence is exactly what caused the anomaly in our employee table. We have `{number} → {position}` and `{position} → {salary}`, so salary is transitively dependent on number through position (and position is not a candidate key). This is the root cause of redundancy and the other anomalies.

Eliminating transitive dependence leads us to **BCNF**.

### 2.4 Boyce-Codd Normal Form (BCNF)

Named after **Edgar F. Codd** (as annotated by the lecturer).

**Definition:** A relation R with a set of functional dependencies Σ is in **Boyce-Codd Normal Form (BCNF)** if for every functional dependency X → {A} ∈ Σ⁺:

- X → {A} is **trivial**, or
- X is a **superkey**

**Equivalently, NOT in BCNF** if there is a functional dependency X → {A} ∈ Σ⁺ where:

- X → {A} is **non-trivial**, **and**
- X is **not** a superkey

BCNF is very strict: the only non-trivial FDs allowed are those where the left side is a superkey. This eliminates all redundancy caused by FDs.

**Example (NOT in BCNF):**

R = {A, B, C}, Σ = { {A,B} → {C}, {C} → {B} }

Candidate keys: We need to check. {A,B}⁺ = {A,B,C} = R, so {A,B} is a superkey. Can we reduce? {A}⁺ = {A} ≠ R. {B}⁺ = {B} ≠ R. So {A,B} is a candidate key. Also, {A,C}⁺ = {A,C,B} = R (using {C} → {B}), so {A,C} is also a candidate key. These are the only two candidate keys.

Check {C} → {B}: non-trivial, and {C} is not a superkey (since {C}⁺ = {B,C} ≠ R). So this violates BCNF.

### 2.5 Idea #3: Relaxing BCNF for Prime Attributes → 3NF

BCNF is sometimes too strict. In the example above, the FD {C} → {B} violates BCNF, but B is a prime attribute (it belongs to candidate key {A,B}). This is a relatively harmless FD — it does not cause the severe anomalies we saw with non-prime attributes.

**Idea #3:** Relax the BCNF requirements for **prime attributes** to cater for such cases. This gives us **3NF**.

### 2.6 Third Normal Form (3NF)

**Definition:** A relation R with a set of functional dependencies Σ is in **Third Normal Form (3NF)** if for every functional dependency X → {A} ∈ Σ⁺:

- X → {A} is **trivial**, or
- A is a **prime attribute**, or
- X is a **superkey**

**Equivalently, NOT in 3NF** if there is a functional dependency X → {A} ∈ Σ⁺ where:

- X → {A} is **non-trivial**, **and**
- A is **not** a prime attribute, **and**
- X is **not** a superkey

### 2.7 The Hierarchy

```
2NF ⊃ 3NF ⊃ BCNF
```

Every relation in BCNF is also in 3NF. Every relation in 3NF is also in 2NF. But not vice versa. BCNF is the strictest, 2NF is the weakest.

The difference between 3NF and BCNF: 3NF allows a non-trivial FD X → {A} where X is not a superkey, **as long as A is a prime attribute**. BCNF does not allow this at all.

---

## 3. Normalization Methods

The three common methods for relational database schema design are:

1. **Decomposition method** — Start with a universal relation (a single relation containing all attributes of the database) and decompose it into smaller relations called **fragments** to remove redundant data. The algorithms make each fragment satisfy the desired normal form.

2. **Synthesis method** — Start from a given set of attributes and functional dependencies. Synthesize 3NF (or occasionally BCNF) relation fragments based on the given set of dependencies.

3. **Entity-relationship approach** — Design the schema using ER diagrams first, then map to relations.

---

## 4. Two Criteria for Good Decomposition

The two **main** criteria for both decomposition and synthesis methods are:

### 4.1 Losslessness (Reconstructability)

A decomposition is a **lossless-join** decomposition if the **natural join** of all the fragments is **equivalent** to the original relation:

```
R = R₁ ⋈ R₂ ⋈ ... ⋈ Rₙ
```

This is also called a **join dependency**. The key point: when we project the original table onto the fragments and then join them back together, we must get exactly the original table — no extra spurious tuples, no lost tuples.

**Important assumption:** We assume no NULL values (universal relation assumption). If we do take NULLs into account, losslessness requires that the **full outer join** of the two tables (with the condition that their primary and foreign keys are equal) reconstitutes the initial table.

**Formal note (annotation):** We always have R ⊆ R₁ ⋈ R₂ (the join can only produce extra tuples, never lose original ones). Losslessness means equality holds.

### 4.2 Dependency Preservation (Covering)

A decomposition is **dependency preserving** if the information captured by the functional dependencies is retained. Formally, the union of the **projected sets of functional dependencies** is **equivalent** to the original set Σ:

```
Σ ≡ (Σ|_{R₁} ∪ Σ|_{R₂} ∪ ... ∪ Σ|_{Rₙ})
```

where Σ|_{Rᵢ} denotes the projected functional dependencies on fragment Rᵢ.

If a decomposition is not dependency preserving, some FDs can only be checked by joining multiple tables — which is expensive and impractical.

---

## 5. Lossless-Join Decomposition: Detailed Treatment

### 5.1 Lossless-Join with NULLs (Practical Example)

Consider the Employees table being decomposed into:

**Employees** (number, name, department, position) — with `{number} → {name, department, position}`

**Salaries** (position, salary) — with `{position} → {salary}`

The decomposition is done by:
```sql
INSERT INTO salaries (SELECT DISTINCT position, salary FROM employees);
-- alter table to remove `salary` from Employees and remove duplicates,
-- set the foreign key, clean `NULL`, etc
```

To verify losslessness:
```sql
SELECT * FROM employees e FULL OUTER JOIN salaries s
WHERE e.position = s.position;
```

This should reconstitute the original table.

### 5.2 Lemma 1: Binary Decomposition

A **binary decomposition** of R is a pair of tables δ = {R₁, R₂} such that R = R₁ ∪ R₂.

**It is a lossless-join decomposition if:**

```
(R₁ ∩ R₂) → R₁     or     (R₁ ∩ R₂) → R₂
```

In words: the common attributes of R₁ and R₂ must functionally determine all the attributes of at least one of the two fragments. This is a sufficient condition (and, for binary decompositions, also necessary given the FDs).

### 5.3 Lemma 2: General Decomposition

A decomposition of R into R₁, R₂, ..., Rₙ is a lossless-join decomposition if there exists **at least one sequence** of binary lossless-join decompositions that generates the decomposition.

Think of it as a tree: start with R at the root, and at each step split one relation into two using a lossless binary decomposition. If you can reach all the Rᵢ as leaves, the overall decomposition is lossless.

### 5.4 The Chase for Losslessness

The Chase is the primary algorithm for testing whether a decomposition is lossless. It was introduced in L07 for testing FD entailment; here it is applied specifically for losslessness testing.

**Algorithm:**

1. Create a table *r* with schema R with **n** tuples (one tuple per fragment Rᵢ).
2. For each attribute A ∈ Rᵢ, **distinguish** the A-value in the i-th tuple (i.e., set it as **α** — the "distinguished" variable). All other cells get subscripted variables (e.g., a₂, d₁, e₃ — indicating "we don't know this value").
3. **Chase** by applying the FDs: whenever two rows agree on the left side of an FD, make them agree on the right side (preferring α over subscripted variables).
4. If any row becomes a **row of all distinguished variables** (all α), the decomposition is **lossless**. If no more changes can be made and no such row exists, it is **not lossless**.

### 5.5 Chase Example 1 (Lossless ✓)

**Given:**
- R = {A, B, C, D, E}
- Σ = { {A,B} → {C}, {A,C} → {B,D}, {C} → {A,B}, {C} → {D}, {D} → {A,E}, {B,D} → {A,C} }
- Decomposition: { {A,B,C}, {B,C,D}, {B,D,E} }

**Initial table** (distinguish attributes in each fragment):

| | A | B | C | D | E |
|---|---|---|---|---|---|
| R₁ = {A,B,C} | α | α | α | d₁ | e₁ |
| R₂ = {B,C,D} | a₂ | α | α | α | e₂ |
| R₃ = {B,D,E} | a₃ | α | c₃ | α | α |

**Chase steps:**

1. Apply {C} → {A,B}: Rows 1 and 2 both have C = α. So their A values must agree → a₂ becomes α. Their B values already agree.
2. Apply {D} → {A,E}: Rows 2 and 3 both have D = α. So a₂ (now α) and a₃ must agree → a₃ becomes α. Also e₂ and α must agree → e₂ becomes α.

After these steps, **Row 2** becomes (α, α, α, α, α) — a complete row of distinguished variables!

**Conclusion:** The decomposition is **lossless**. ✓

### 5.6 Chase Example 2 (Not Lossless ✗)

**Same R and Σ, but different decomposition:** { {A,B,C}, {B,C,D}, {A,E} }

The initial table is set up similarly, but after exhaustively applying all FDs, no row becomes entirely distinguished. The Chase terminates without producing a row of all α values.

**Conclusion:** This decomposition is **not lossless**. ✗

---

## 6. Projected Functional Dependencies

### 6.1 Definition

Consider a relation R with a set of functional dependencies Σ. A set Σ' of **projected functional dependencies** on R' from R with Σ, where R' ⊆ R, is the set of functional dependencies **equivalent** to the set of functional dependencies X → Y in Σ⁺ such that X ⊆ R' and Y ⊆ R'.

**In other words:**

- **X ⊆ R'**: All attributes in the left side come from R'.
- **Y ⊆ R'**: All attributes in the right side come from R'.
- **X → Y in Σ⁺**: The functional dependency can be logically derived from Σ.

So Σ|_{R'} contains all FDs that are entailed by Σ and involve only attributes of R'.

### 6.2 Example

**Given:**
- R = {A, B, C, D, E}
- Σ = { {A,B} → {C,D,E}, {A,C} → {B,D,E}, {B} → {C}, {C} → {B}, {C} → {D}, {B} → {E}, {C} → {E} }

**Question:** What is Σ|_{R'} on R' = {A, B, D, E}?

**Approach:** We need all FDs X → Y derivable from Σ where both X and Y use only attributes from {A, B, D, E}. We systematically check attribute closures of subsets of R' (with respect to Σ), then restrict the results to R'.

- {B}⁺ = {B, C, D, E} (using B→C, C→D, B→E). Restricted to R': {B} → {D, E}
- {A,B}⁺ = {A, B, C, D, E} = R. Restricted to R': {A,B} → {D, E} (but this is subsumed by {B} → {D,E})

**Answer:** Σ|_{R'} = { {A,B} → {D,E}, {B} → {D,E} }

(The lecturer's annotation highlights that {B} → {D} is derivable, which is key.)

---

## 7. Dependency Preservation: Detailed Treatment

### 7.1 Checking Dependency Preservation

**Example:** Employees decomposed into:
- R₁ = Employees(number, name, department, position)
- R₂ = Salaries(position, salary)

Original Σ = { {number} → {name, department, position, salary}, {position} → {salary} }

Compute projected FDs:
- Σ|_{R₁} = { {number} → {name, department, position} }
- Σ|_{R₂} = { {position} → {salary} }

Check: Σ|_{R₁} ∪ Σ|_{R₂} = { {number} → {name, department, position}, {position} → {salary} }

Is this equivalent to Σ? Yes! From {number} → {position} and {position} → {salary}, by transitivity we get {number} → {salary}. So we can derive {number} → {name, department, position, salary}, which is the original FD.

**Conclusion:** Σ ≡ Σ|_{R₁} ∪ Σ|_{R₂}. The decomposition is **dependency preserving**. ✓

---

## 8. Algorithm 3: Decomposition Algorithm (for BCNF)

This is the primary algorithm for achieving BCNF decomposition.

### 8.1 The Algorithm

```
Algorithm 3: Decomposition Algorithm

input:    R, Σ
output:   δ (a set of fragments)

if (R is NOT in the required normal form) then
    Let X → Y be a violation;
    Decompose R into two relations:
        R₁ := X⁺                    (with respect to Σ)
        R₂ := (R - X⁺) ∪ X         (with respect to Σ)
    return { Algo3(R₁, Σ|_{R₁}),  Algo3(R₂, Σ|_{R₂}) };
else
    return { R };
```

**How to read this:** Find any FD that violates the desired normal form. Compute the closure of its left side (X⁺). One fragment gets all the attributes determined by X, the other fragment gets everything else plus X itself (so they share X as the common attributes). Then recursively decompose each fragment with their respective projected FDs.

### 8.2 Properties

The decomposition algorithm:

- Works for many normal forms (BCNF, 3NF, etc.), and
- **Always terminates** (each step strictly reduces the size of at least one fragment), and
- Is **guaranteed to find a lossless-join decomposition**.

**Proof of losslessness:** At each step, R₁ ∩ R₂ = X (by construction). Since R₁ = X⁺, we have X → X⁺ by definition, which means (R₁ ∩ R₂) → R₁. By Lemma 1, this is a lossless binary decomposition. By Lemma 2 (applied inductively), the overall decomposition is lossless.

**However:** The decomposition **may not be dependency preserving**. This is a fundamental limitation of BCNF decomposition — some FDs may be lost across fragments.

---

## 9. Algorithm 4: Synthesis Algorithm (for 3NF)

This is the primary algorithm for achieving 3NF decomposition, and it guarantees both losslessness and dependency preservation.

### 9.1 The Algorithm

```
Algorithm 4: Synthesis Algorithm

input:    R, Σ
output:   δ (a set of fragments)

Σ⁻ = Algo2(Σ);           // compute minimal cover
δ = ∅                     // start with empty set

for each (X → Y ∈ Σ⁻) do
    if (there is NO Rᵢ ∈ δ such that (X ∪ Y) ⊆ Rᵢ) then
        // i.e., not subsumed by any existing fragment
        δ = δ ∪ (X ∪ Y);     // construct a new fragment

if (there is NO Rᵢ ∈ δ that contains one of the keys) then
    Add one key to δ

return δ;
```

### 9.2 How It Works (Step by Step)

1. **Compute the minimal cover** Σ⁻ using Algorithm 2 from L07 (the three-step process: decompose RHS to singletons, minimize LHS, remove redundant FDs).

2. **For each FD in the minimal cover**, create a fragment from (X ∪ Y) — unless that set of attributes is already a subset of some existing fragment (i.e., it is "subsumed").

3. **Check if any fragment contains a candidate key.** If not, add a fragment consisting of one candidate key. This step ensures losslessness.

### 9.3 Properties

The synthesis algorithm:

- Works for **3NF** (and may find BCNF if lucky), and
- **Always terminates**, and
- Is guaranteed to find a **lossless-join decomposition** (the key-adding step ensures this), and
- Is guaranteed to find a **dependency preserving decomposition**.

**Proof of dependency preservation:** Each fragment Rᵢ is constructed from (X ∪ Y) for some FD X → Y in Σ⁻. Therefore Rᵢ ⊨ X → Y (since all attributes of the FD are in Rᵢ). Since this is done for **all** X → Y ∈ Σ⁻, we can reconstruct all of Σ⁻ from the union of projected FDs. But Σ⁻ ≡ Σ (the minimal cover is equivalent to Σ). Hence, the decomposition is dependency preserving.

**Proof of losslessness:** If no fragment contains a key, we add a key fragment. The presence of a key fragment in δ guarantees that the natural join of all fragments can recover every tuple of R. (The key functionally determines all attributes, so the join with the key fragment "anchors" the reconstruction.)

### 9.4 Improvement: Use Canonical Cover

The lecture notes that we should always use **canonical cover** instead of **minimal cover** to reduce the number of tables created. Recall from L07: canonical cover groups FDs with the same LHS (e.g., X → A and X → B become X → AB), resulting in fewer, larger fragments.

---

## 10. Case Study: Sentosa University of Technology

This extended example walks through the entire normalization process from ER diagram to final schema.

### 10.1 The Setup

**ER Diagram entities:** faculty, department, student, programming language

**Relationships:**
- faculty **in** department (faculty is (0,n), department is (1,1))
- student **registered** in department ((1,1) for student, (0,n) for department)
- student **proficient** in programming language ((0,n) for both)
- faculty has attribute: domain
- department has attribute: department (name)
- student has attributes: name, userid
- programming language has attribute: language

**Attribute-to-letter mapping:**

| Attribute | Letter | Attribute | Letter |
|---|---|---|---|
| name | A | department | D |
| userid | B | faculty | E |
| domain | C | language | F |

**Universal relation:** R = {A, B, C, D, E, F}

**Functional Dependencies (derived from the ER diagram):**

```
Σ = {
    {C} → {E},      // domain determines faculty
    {E} → {C},      // faculty determines domain
    {D} → {C, E},   // department determines domain and faculty
    {B,C} → {A, D}, // userid + domain determines name and department
    {B,E} → {A, D}  // userid + faculty determines name and department
}
```

### 10.2 BCNF Decomposition (Algorithm 3)

**Step 1:** Check if R = {A, B, C, D, E, F} is in BCNF. The FD {C} → {E} is non-trivial and {C} is not a superkey (since {C}⁺ = {C, E} ≠ R). **Violation found.**

**Using {C} → {E} on {A, B, C, D, E, F}:**
- {C}⁺ = {C, E}
- R₁ = {C, E} with Σ|_{R₁} = { {C} → {E}, {E} → {C} }
- R₂ = {A, B, C, D, F} with Σ|_{R₂} = { {B,C} → {A,D}, {B,D} → {A,C}, {D} → {C} }

**{C, E} is in BCNF** ✓ (both {C} → {E} and {E} → {C} have superkeys as LHS, since both {C} and {E} are candidate keys of this two-attribute relation)

**Step 2:** Check {A, B, C, D, F}. The FD {D} → {C} is non-trivial and {D} is not a superkey. **Violation.**

**Using {D} → {C} on {A, B, C, D, F}:**
- {D}⁺ = {C, D} (in the context of Σ|_{R₂})
- R₃ = {C, D} with Σ|_{R₃} = { {D} → {C} } (annotation shows {C} ↔ {D}, but more precisely {D} → {C})
- R₄ = {A, B, D, F} with Σ|_{R₄} = { {B,D} → {A} }

**{C, D} is in BCNF** ✓

**Step 3:** Check {A, B, D, F}. The FD {B,D} → {A} — is {B,D} a superkey? {B,D}⁺ = {A, B, D}. Since F ∉ {B,D}⁺, {B,D} is not a superkey. **Violation.**

**Using {B,D} → {A} on {A, B, D, F}:**  (actually using the annotation: {B,C}→{A})
- {B,D}⁺ = {A, B, D}
- R₅ = {A, B, D} with Σ|_{R₅} = { {B,D} → {A} }
- R₆ = {B, D, F} with Σ|_{R₆} = ∅

**{A, B, D} is in BCNF** ✓ (B,D is a superkey here)
**{B, D, F} is in BCNF** ✓ (no non-trivial FDs, so trivially BCNF)

### 10.3 BCNF Result

**Final fragments:** {C, E}, {C, D}, {A, B, D}, {B, D, F}

With their projected FDs:
- {C, E}: { {C} → {E}, {E} → {C} }
- {C, D}: { {D} → {C} } (annotation: {C} → {D} crossed out, confirmed only {D} → {C})
- {A, B, D}: { {B,D} → {A} }
- {B, D, F}: ∅

**Is it dependency preserving?** Check the original FDs:
- {C} → {E} ✓ (in {C,E})
- {E} → {C} ✓ (in {C,E})
- {D} → {C,E}: {D} → {C} is in {C,D} ✓, but {D} → {E} must be derived — it requires joining {C,D} and {C,E}, which means it is not directly in any single fragment's projected FDs. However, we can derive it transitively.
- **{B,C} → {A,D}** ✗ — This FD involves attributes B, C, A, D but no single fragment contains all four. It cannot be checked without joining tables.
- **{B,E} → {A,D}** ✗ — Same issue.

**Conclusion:** The BCNF decomposition is **NOT dependency preserving**. ✗

### 10.4 3NF Decomposition (Algorithm 4)

**Step 1 — Find keys and canonical cover:**

Keys: {B, C, D}, {B, C, F}, {B, E, F}

Canonical Cover: { {B,E} → {A,D}, {C} → {E}, {D} → {E}, {E} → {C} }

**Step 2 — Construct fragments from each FD:**

| FD | Fragment (X ∪ Y) | Result |
|---|---|---|
| {B,E} → {A,D} | {A, B, D, E} | **Created** |
| {C} → {E} | {C, E} | **Created** |
| {D} → {E} | {D, E} | **Subsumed** by {C,E}? No — {D,E} ⊄ {C,E}. But the slide says "subsumed". This is because {D,E} ⊆ {A,B,D,E}? No... Actually from the slide: {D} → {E} gives {D,E}, and {E} → {C} gives {C,E}. The slide marks both as ".. subsumed .." — meaning they are subsumed by the existing fragments |

Looking at the slides more carefully:

- {B,E} → {A,D} produces fragment **{A, B, D, E}** (annotation: "ABDE")
- {C} → {E} produces fragment **{C, E}** (annotation: "CE")
- {D} → {E} produces {D, E} — **subsumed** (since {D, E} ⊆ {A, B, D, E})
- {E} → {C} produces {C, E} — **subsumed** (since {C, E} already exists)

**Step 3 — Check key containment:**

None of the fragments {A,B,D,E} or {C,E} contains any key ({B,C,D}, {B,C,F}, or {B,E,F}). So we must **add one key**. The slide adds **{B, C, F}** (but notes that we can also add {B,C,D} or {B,E,F}).

### 10.5 3NF Result

**Final fragments:** {A, B, D, E}, {C, E}, {B, C, F}

With their projected FDs:
- {A, B, D, E}: { {B,E} → {A,D}, {D} → {E} }
- {C, E}: { {C} → {E}, {E} → {C} }
- {B, C, F}: ∅

**Is it dependency preserving?** All original FDs can be checked:
- {C} → {E} ✓ (in {C,E})
- {E} → {C} ✓ (in {C,E})
- {D} → {C,E}: {D} → {E} can be derived from {A,B,D,E}, and {E} → {C} from {C,E} — ✓
- {B,C} → {A,D}: {C} → {E} from {C,E}, then {B,E} → {A,D} from {A,B,D,E} — ✓
- {B,E} → {A,D} ✓ (directly in {A,B,D,E})

**Conclusion:** The 3NF decomposition **IS dependency preserving**. ✓

### 10.6 Mapping to ERD

The 3NF fragments map nicely back to the ER diagram:
- {A, B, D, E} corresponds to the "registered" relationship (student in department, with faculty)
- {C, E} corresponds to the faculty-domain relationship
- {B, C, F} corresponds to the "proficient" relationship (student proficient in programming language)

### 10.7 What If We "Unsubsumed"?

If we do not apply the subsumption check and keep all fragments, we get: {A, B, D, E}, {C, E}, {B, C, F}, **{D, E}**

The extra fragment {D, E} with Σ|_{D,E} = { {D} → {E} } can still map to the ER diagram (it corresponds to the department-faculty "in" relationship). The question is whether this extra fragment adds value or is redundant. In this case, it makes the mapping to the ERD cleaner.

---

## 11. Pathological Case: Enforcing FDs in SQL Schema

This section illustrates a subtle issue when implementing normalization results in an actual SQL schema.

### 11.1 The Problem

Consider R = {A, B, D, E} with Σ = { {B,E} → {A,D}, {D} → {E} }

Candidate keys: {B,E} and {B,D} (since {B,D}⁺ = {B,D,E,A} via {D} → {E} then {B,E} → {A,D}).

**Task:** Enforce the functional dependencies using SQL's 5 basic constraints (PRIMARY KEY, UNIQUE, NOT NULL, FOREIGN KEY, CHECK).

### 11.2 Subsumed Approach (Single Table)

```sql
CREATE TABLE r1 (
    A  INT  NOT NULL,
    B  INT  NOT NULL,
    D  INT  NOT NULL,
    E  INT  NOT NULL,
    PRIMARY KEY (B, E),
    UNIQUE (B, D)
);
```

**Issue:** This schema enforces {B,E} → {A,D} (via PRIMARY KEY) and {B,D} → {A,E} (via UNIQUE). But it does **NOT** enforce {D} → {E}!

**Counterexample:**

| A | B | D | E |
|---|---|---|---|
| 1 | 1 | 1 | 1 |
| 2 | 2 | 1 | 2 |

This table satisfies the PRIMARY KEY (B,E) and UNIQUE (B,D) constraints, but violates {D} → {E}: D=1 appears with both E=1 and E=2.

### 11.3 Not-Subsumed Approach (Separate Tables)

To properly enforce {D} → {E}, we need a separate table:

```sql
CREATE TABLE r4 (
    D  INT  NOT NULL,
    E  INT  NOT NULL,
    PRIMARY KEY (D, E)   -- actually PRIMARY KEY (D) to enforce D → E
);

CREATE TABLE r1 (
    A  INT  NOT NULL,
    B  INT  NOT NULL,
    D  INT  NOT NULL,
    E  INT  NOT NULL,
    PRIMARY KEY (B, E),
    UNIQUE (B, D),
    FOREIGN KEY (D, E) REFERENCES r4(D, E)
);
```

The annotation clarifies: r4 should have `PRIMARY KEY (D)` (not `(D,E)`) with a UNIQUE constraint, since we want to enforce that D uniquely determines E. The FOREIGN KEY from r1 to r4 then ensures that every (D, E) pair in r1 exists in r4, which enforces the FD {D} → {E}.

**Key takeaway:** When FDs are "subsumed" during synthesis (i.e., the fragment for that FD is dropped because it is a subset of a larger fragment), the FD may not be enforceable by SQL constraints alone on the larger table. Creating the "unsubsumed" separate table allows proper enforcement via PRIMARY KEY and FOREIGN KEY.

---

## 12. Summary and Comparison

### Normal Forms Comparison

| Normal Form | Condition for X → {A} ∈ Σ⁺ (must satisfy at least one) |
|---|---|
| **2NF** | Trivial, OR A is prime, OR X is not a proper subset of a CK |
| **3NF** | Trivial, OR A is prime, OR X is a superkey |
| **BCNF** | Trivial, OR X is a superkey |

### Algorithms Comparison

| Property | Algorithm 3 (Decomposition) | Algorithm 4 (Synthesis) |
|---|---|---|
| Target NF | BCNF (or others) | 3NF (may achieve BCNF) |
| Lossless-join | **Always** ✓ | **Always** ✓ |
| Dependency preserving | **Not guaranteed** ✗ | **Always** ✓ |
| Approach | Top-down (split violations) | Bottom-up (build from FDs) |
| Input to FD processing | Uses X⁺ and Σ\|_{Rᵢ} | Uses minimal/canonical cover |

### Key Takeaways

1. **Anomalies** (redundancy, NULL issues, deletion anomaly) arise from unenforced FDs in a poorly designed schema.

2. **2NF ⊃ 3NF ⊃ BCNF** — BCNF is the strictest. The difference between 3NF and BCNF is that 3NF allows non-superkey determinants as long as the dependent attribute is prime.

3. **Losslessness** means we can reconstruct the original table by joining fragments. Verified by The Chase or Lemma 1.

4. **Dependency preservation** means all original FDs can be checked within individual fragments without joining. Verified by computing projected FDs.

5. **BCNF decomposition** (Algorithm 3) always gives lossless results but may sacrifice dependency preservation.

6. **3NF synthesis** (Algorithm 4) always gives both lossless and dependency-preserving results, making it the safer choice in practice.

7. **Use canonical cover** instead of minimal cover in Algorithm 4 to minimize the number of fragments.

8. **Subsuming** fragments during synthesis is a trade-off: it reduces the number of tables but may make certain FDs unenforceable by SQL constraints alone (the pathological case).
