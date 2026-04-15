# Quiz 2 Practice Paper (Version 2 — Harder)

**CS4221/CS5421 — Database Application Design and Tuning**
**Semester 2 AY25/26 — Final Exam Preparation**

Time Allowed: **60 minutes**
Total Marks: **50 points**

---

## Instructions

1. This assessment paper contains **15 questions** across three sections.
2. Write all your answers in the provided space.
3. This is a **CLOSED-BOOK** assessment. One double-sided A4 cheatsheet allowed.
4. All SQL queries are executed on PostgreSQL 16.

---

## Scenario for Questions 1–5

Your team at **Apasaja Private Limited** is redesigning the relational schema for **MuziqStream**, an online music-streaming platform. They track users, songs, playlists, and listening history.

| UserID | UserName | Email | Country | SongID | Title | PlaylistID |
|--------|----------|-------|---------|--------|-------|------------|
| A | B | C | D | E | F | G |

The following business constraints have been identified:

1. A user is identified by UserID. UserID determines the user's name and email.
2. Email addresses are unique across all users and also identify a user.
3. Given a user's email, their country is determined (accounts are regional).
4. A song is identified by SongID and has a single title.
5. Each playlist has a unique PlaylistID. Given a user and a song, we can uniquely determine which playlist the user first added that song to.
6. A user's country also determines certain streaming restrictions, but for this question, country is independent.

Let R = {A, B, C, D, E, F, G} and let Σ be the set of functional dependencies obtained by translating the constraints above into FDs.

---

## Multiple Response Questions (10 points)

For each question, shade your answer on the answer sheet. If multiple answers are equally appropriate, pick one. Shade **X** if none apply.

### 1. (2 points) Which of the following FDs are logically entailed by Σ?

- A. {A} → {D}
- B. {C} → {B}
- C. {E, C} → {G}
- D. {B} → {C}

### 2. (2 points) Which of the following is a candidate key of R with Σ?

- A. {A, E}
- B. {B, E}
- C. {D, E}
- D. {C, E, F}

### 3. (2 points) Which FD of the form X → {A} violates the BCNF property of R with Σ?

- A. {A} → {B}
- B. {E} → {F}
- C. {A, E} → {G}
- D. {C} → {D}

### 4. (2 points) Which FD of the form X → {A} violates the 3NF property of R with Σ?

- A. {A} → {C}
- B. {C} → {A}
- C. {E} → {F}
- D. {A, E} → {G}

### 5. (2 points) Which normal form is satisfied by R with Σ?

- A. 1NF
- B. 2NF
- C. 3NF
- D. BCNF

---

## Short Answer (24 points)

### 6. (8 points) Armstrong's Axioms — Proofs

Prove the following using only **Reflexivity, Augmentation, Transitivity** (and *derived* rules if clearly justified). Use only the lines provided.

#### (a) (4 points) If { X → Y, Z → W } and Y ⊆ Z, then X → W.

```
1.  X → Y                                    [Given]
2.  Z → W                                    [Given]
3.  ___________________________________     [___________________________________]
4.  ___________________________________     [___________________________________]
5.  ___________________________________     [___________________________________]
```

#### (b) (4 points) If { X → Y, X → Z, (Y ∪ Z) → W }, then X → W.

```
1.  X → Y                                    [Given]
2.  X → Z                                    [Given]
3.  (Y ∪ Z) → W                              [Given]
4.  ___________________________________     [___________________________________]
5.  ___________________________________     [___________________________________]
6.  ___________________________________     [___________________________________]
```

---

### 7. (16 points) Schema Analysis

For this question, use the following:

- R = {A, B, C, D, E, F}
- Σ = { {A, B} → {C}, {B, C} → {A, D}, {C, D} → {E, F}, {D, E} → {A, B}, {A, F} → {C, E} }

#### (a) (3 points) Find all candidate keys of R with Σ.

(Space provided)

#### (b) (8 points) Consider the decomposition of R with Σ into:

δ = { {A, B, C}, {B, C, D}, {A, D, E}, {C, E, F} }

Show that this decomposition is **not** a lossless-join decomposition by constructing a **minimal counterexample**. You may use integers for all values. You do not need to use all the rows.

```
 A  B  C            A  D  E           B  C  D           C  E  F
┌──┬──┬──┐        ┌──┬──┬──┐        ┌──┬──┬──┐        ┌──┬──┬──┐
│  │  │  │        │  │  │  │        │  │  │  │        │  │  │  │
├──┼──┼──┤        ├──┼──┼──┤        ├──┼──┼──┤        ├──┼──┼──┤
│  │  │  │        │  │  │  │        │  │  │  │        │  │  │  │
├──┼──┼──┤        ├──┼──┼──┤        ├──┼──┼──┤        ├──┼──┼──┤
│  │  │  │        │  │  │  │        │  │  │  │        │  │  │  │
├──┼──┼──┤        ├──┼──┼──┤        ├──┼──┼──┤        ├──┼──┼──┤
│  │  │  │        │  │  │  │        │  │  │  │        │  │  │  │
└──┴──┴──┘        └──┴──┴──┘        └──┴──┴──┘        └──┴──┴──┘
```

#### (c) (4 points) Find one lossless-join **dependency-preserving** decomposition of R with Σ in **BCNF** (if any). Otherwise, find one in **3NF**.

(Space provided)

#### (d) (1 point) Is your decomposition in part (c) in BCNF or in 3NF? Justify briefly.

(Space provided)

---

## Long Answer (16 points)

### 8. (16 points) Canonical Cover and SQL Schema Design

For this question, use the following:

- R = {A, B, C, D, E, F}
- Σ = { {B} → {A}, {C} → {B}, {A} → {C, D, E}, {D, E} → {F}, {F} → {D} }

#### (a) (2 points) Find one canonical cover of Σ with respect to R using the algorithm introduced in class.

(Space provided)

#### (b) (4 points) Find one lossless-join dependency-preserving decomposition of R with Σ in **3NF** using the canonical cover computed in part (a) and Algorithm 4 (the synthesis algorithm) introduced in class.

(Space provided)

#### (c) (4 points) Find **another** lossless-join dependency-preserving decomposition of R with Σ in 3NF with the **fewest number of fragments**.

(Space provided)

#### (d) (6 points) Create SQL tables for the decomposition in part (c), enforcing **all** FDs in Σ. You may use `INT` for all types. Name each table after its attributes in lowercase (e.g., a table containing {A, B, C} would be `abc`). If an FD cannot be enforced directly via table constraints, introduce an additional table.

(Space provided)

---

## Appendix — Armstrong's Axioms

- **Reflexivity**: ∀X ⊆ R : ∀Y ⊆ R : ((Y ⊆ X) ⇒ (X → Y))
- **Augmentation**: ∀X, Y, Z ⊆ R : ((X → Y) ⇒ ((X ∪ Z) → (Y ∪ Z)))
- **Transitivity**: ∀X, Y, Z ⊆ R : (((X → Y) ∧ (Y → Z)) ⇒ (X → Z))

### Derived Rules (may be used with justification)

- **Union**: ((X → Y) ∧ (X → Z)) ⇒ (X → (Y ∪ Z))
- **Decomposition**: (X → (Y ∪ Z)) ⇒ ((X → Y) ∧ (X → Z))
- **Pseudo-transitivity**: ((X → Y) ∧ ((W ∪ Y) → Z)) ⇒ ((W ∪ X) → Z)

---

**END OF PAPER**

---

*This paper is a practice paper. While it is intended to be similar in structure and spirit to the actual assessment, the questions are original and the difficulty is tuned to be moderately harder than the typical Quiz 2.*
