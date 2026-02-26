# L02: Conceptual Modelling with Entity-Relationship (ER) Model and Diagram

**CS4221 - Database Tuning** | NUS Computer Science

> The **Entity-Relationship (ER) model** is a conceptual tool for designing databases. Before writing any SQL, we first draw an ER diagram to capture **what** exists in the real world (entities), **what describes** them (attributes), and **how** they relate to each other (relationships). The ER diagram then gets translated into actual SQL tables using a systematic set of rules.

---

## Table of Contents

1. [Running Example: Game Store](#1-running-example-game-store)
2. [Entities](#2-entities)
3. [Attributes](#3-attributes)
4. [Relationships](#4-relationships)
5. [Aggregation](#5-aggregation)
6. [Design Considerations](#6-design-considerations)
7. [Identities (Keys)](#7-identities-keys)
8. [Participation Constraints](#8-participation-constraints)
9. [ER-to-Relational Translation Rules](#9-er-to-relational-translation-rules)
10. [Translation Exceptions](#10-translation-exceptions)
11. [Limitations](#11-limitations)

---

## 1. Running Example: Game Store

**Apasaja Pte Ltd** wants to build an online app store with the following requirements:

- Store **customer** info: first name, last name, date of birth, e-mail, date of registration, country of registration, customer identifier
- Manage **games**: name, version, price (fixed per version)
- Record which version of which game each customer has **downloaded**
- Download date is not essential

**Schema (SQL):**

```sql
CREATE TABLE customers (
    first_name   VARCHAR(64) NOT NULL,
    last_name    VARCHAR(64) NOT NULL,
    email        VARCHAR(64) UNIQUE NOT NULL,
    dob          DATE NOT NULL,
    since        DATE NOT NULL,
    customerid   VARCHAR(16) PRIMARY KEY,
    country      VARCHAR(16) NOT NULL
);

CREATE TABLE games (
    name     VARCHAR(32),
    version  CHAR(3),
    price    NUMERIC NOT NULL,
    PRIMARY KEY (name, version)
);

CREATE TABLE downloads (
    customerid VARCHAR(16) REFERENCES customers(customerid)
        ON UPDATE CASCADE ON DELETE CASCADE,
    name       VARCHAR(32),
    version    CHAR(3),
    PRIMARY KEY (customerid, name, version),
    FOREIGN KEY (name, version) REFERENCES games(name, version)
        ON UPDATE CASCADE ON DELETE CASCADE
);
```

---

## 2. Entities

### Entities and Entity Sets

| Concept | Definition |
|---|---|
| **Entity** | An **identifiable thing** in the real world |
| **Entity Set** | The set of all entities of the **same type** |

- Entities typically correspond to **nouns** in the requirement description
- **ER Diagram notation:** Named **rectangle (box)**

> Example: "We want to store information about our **customers**" --> `Customers` entity set

### Conceptual View

Think of an entity set like a set in mathematics -- it contains individual entity instances (e.g., individual customers), and the box in the diagram represents the entire set. For example, the `Customers` box doesn't represent one customer; it represents the *collection of all customers*.

---

## 3. Attributes

### Attributes, Values, and Value Sets

- The ER model is **value-oriented**: values are **atomic** data types (INTEGER, TEXT, DATE, etc.)
- All entities in one entity set share the **same attributes**, but attribute **values differ** per entity
- **ER Diagram notation:** **Circle** with name written **outside**, connected to entity box by a line

> Example: Customers has attributes: customerid, first name, email, date of birth, etc.

### Derived Attributes

- Some attribute values can be **computed/derived** from other attributes
- Example: `age` can be derived from `date of birth` and current date
- **ER Diagram notation:** Connected with a **dashed line** (instead of solid line)

---

## 4. Relationships

### Relationships and Relationship Sets

| Concept | Definition |
|---|---|
| **Relationship** | An association between **two (or more) entities** |
| **Relationship Set** | The set of all relationships of the same type (no duplicates) |

- Relationships typically correspond to **verbs** in the description
- **ER Diagram notation:** Named **diamond**
- In practice, over **90%** of relationships connect exactly two entities

> Example: "customers **download** games" --> `download` relationship set

### Key Properties of Relationship Sets

1. **It is a set** --> no duplicate entries (same customer cannot download same game more than once in a basic binary relationship)
2. Relationships are **NOT** distinguished by their attributes, but by their **participating entities**
3. Relationship attributes are **dependent** on the entities being associated

### Attributes of Relationships

- Relationships can have their own attributes (e.g., `deviceid` on download)
- All relationships in one set have the same attributes
- Even with relationship attributes, the same pair of entities cannot appear twice (it's still a set)

### Same Entity Set Relationships

- A relationship can associate entities from the **same entity set**
- In this case, **participation (role)** names must be specified
- Example: Customer **refers** Customer (with roles: `referee` and `referrer`)
- **Note:** Participation **can always be named** even when not associating the same entity set

### N-ary Relationships (n > 2)

| Arity | Name |
|---|---|
| 1 | Unary |
| 2 | Binary |
| 3 | Ternary |
| n | N-ary |

- Relationship sets can associate **more than 2 entity sets**
- **All participating entities must be present** -- none can be missing (no NULLs)
- Example: Customers download Games **on** Devices (ternary)

### Attribute Promotion

- **Problem:** In a binary relationship (Customers-Games), adding `deviceid` as a relationship attribute still doesn't allow a customer to download the same game on different devices. Why? Because a relationship set is a **set** -- each pair of participating entities can appear at most once, regardless of different attribute values. The relationship is identified by its participating entities only, not by its attributes.
- **Solution:** **Promote** the attribute to a full **entity set** (e.g., create a `Devices` entity set), making it a ternary relationship. Now the relationship is identified by three entities (customer, game, device), so the same customer can download the same game on different devices.

---

## 5. Aggregation

### Relationship Sets as Entity Sets

- Sometimes we need to associate an **entity set** with a **relationship set**
- **ER Diagram notation:** **Wrap** the relationship set diamond **in a box** (rectangle)
- Example: The `download` relationship (Customers-Games) is wrapped in a box, then connected to `Devices` via an `on` relationship

### Rules for Aggregation

- An aggregate is **firstly a relationship set** -- all relationship set rules apply:
  - No duplicate entries (same customer cannot download same game twice)
  - Identified by participating entities
- But it **can also be used as an entity set** -- its keys can be referenced by another relationship set

> **Think of it this way:** Aggregation lets you "wrap up" a relationship and treat it as a thing that other relationships can point to. For example, the act of "downloading a game" becomes an entity that can then participate in an "on (device)" relationship.

---

## 6. Design Considerations

### Which Design to Choose?

Given three alternatives for modeling "download on device":

| Alternative | Description | Implication |
|---|---|---|
| **#1** | `deviceid` as attribute of binary `download` relationship | Customer can download same game **at most once** (regardless of device) |
| **#2** | `Devices` as separate entity in ternary `download` | Customer can download same game on different devices |
| **#3** | Aggregation: `download` (binary) wrapped + `on` relationship to `Devices` | Customer can download same game **at most once** (same as #1, but Devices is a proper entity) |

**Key insight:** Alternatives #1 and #3 both restrict a customer to downloading the same game at most once. Alternative #2 allows downloading the same game on different devices.

---

## 7. Identities (Keys)

### Entity Identifiers

- One or more attributes can **uniquely identify** an entity within an entity set
- **ER Diagram notation:**
  - **Single attribute key:** **Filled (black) circle** (replacing the hollow circle)
  - **Composite key:** **Connected black circles** (linked together)

> Example: `customerid` uniquely identifies a Customer (single key, black circle)
> Example: `(name, version)` uniquely identifies a Game (composite key, connected black circles)

### Multiple Identifiers / Candidate Keys

- An entity set can have **more than one** set of identifying attributes
- Each such set is a **candidate key**
- They are shown as **separate groupings** in the ER diagram
- **Every entity set must have at least one candidate key** (consequence of being a set with no duplicates)

> Example: Customers has two candidate keys: `customerid` and `email`

### Minimality Condition

- Identifying attribute sets must be **minimal**: removing any attribute from the set should make it no longer uniquely identifying
- **Minimal != Minimum:**
  - **Minimal** = cannot remove any element while maintaining uniqueness (like a *local minima*)
  - **Minimum** = smallest possible (like a *global minima*)

> Example (Not Minimal): {customerid, first_name} for Customers -- `customerid` alone suffices
> Example (Already Minimal): {faculty, username, domain} for Students if both {matric} and {username, domain} are minimal candidate keys

### Relationship Identifiers

- Relationship sets are identified by their **participating entities** (i.e., the keys of participating entity sets)
- **Black circles on relationship attributes are PROHIBITED** (incorrect)
- You CANNOT add identifying attributes to a relationship set

> Example: Putting a black circle on `deviceid` in the download relationship is **INVALID** because (customer1, name1, device1) and (customer1, name1, device2) still cannot coexist -- the relationship is identified by (customerid, name, version) only.

### Partial Key (Weak Entity Sets)

- Some entities can only be **identified within the scope of a relationship** with another entity set
- **ER Diagram notation:** Connect the partial identifier to the participation of the **dominant entity** (blue filled circle on the participation line)

> **Think of it this way:** A partial key is like a room number in a building. Room "101" only uniquely identifies a room *within a specific building*. If you have multiple buildings, "Room 101" alone is ambiguous -- you need to say "Room 101 in Building A." The building is the dominant entity providing the scope.

**Terminology:**
| Term | Meaning |
|---|---|
| **Weak entity set** | Entity set with at least one partial key |
| **Identifying relationship set** | The relationship through which partial identification occurs |
| **Dominant entity set** | The entity set that provides the "scope" for identification |

> Example: `Customers` identified by `customerid` only within scope of `App Stores`. Same `customerid` can exist in different app stores. The `on` relationship is the identifying relationship, `App Stores` is the dominant entity set.

**Variants:**
- A weak entity set can also have other (non-partial) keys -- still called a weak entity set
- A partial key can have multiple dominant entity sets (rare in practice)

---

## 8. Participation Constraints

### Definition

The **participation constraint** specifies the number of times an entity can appear in a relationship. Written as **(minimum, maximum)** between the entity set and the relationship set.

### Example Notation

```
Customers --(1, n)-- download --(0, n)-- Games
```

- Customer must download **at least 1** game, may download **many** (1, n)
- Game **need not** be downloaded, can be downloaded by **many** customers (0, n)

### Classification (for Binary Relationships)

| Constraint | Name | Meaning |
|---|---|---|
| **(1, _)** | **Mandatory** participation | Entity MUST participate at least once |
| **(0, _)** | **Optional** participation | Entity MAY participate (or not) |
| **(_, 1)** for all | **One-to-one** | Each entity participates at most once on each side |
| **(_, 1)** for one side, **(_, n)** for other | **One-to-many** | One side has max 1, the other side has no max limit |
| **(_, n)** for all | **Many-to-many** | No maximum limit on either side |

### Default

If participation constraint is **omitted** (no min/max at all), the default is **(0, n)** -- optional many-to-many.

### Weak Entity Set Constraint

Weak entities must have a **(1, 1)** constraint on the identifying relationship -- this means each weak entity must belong to **exactly one** dominant entity (mandatory, one-to-one from the weak entity's side). This makes sense: if a weak entity's identity depends on a dominant entity, it must have exactly one such dominant entity to be identifiable.

### Examples

**Example 1 -- One-to-One:**
Citizens (0,1) -- has -- (1,1) Passports
- A citizen may have 0 or 1 passport; a passport belongs to exactly 1 citizen
- Avoids NULL values vs. storing passport info directly in Citizens

**Example 2 -- One-to-Many:**
Staffs (0,5) -- supervise -- (1,1) Students
- Staff can supervise 0 to 5 students; each student has exactly 1 supervisor

**Example 3 -- Weak Entity:**
Universities (0,n) -- study -- (1,1) Students
- Students identified by (username, domain) only within scope of a university

### Participation with Ternary Relationships

For a ternary relationship (e.g., Customers-download-Games-Devices with aggregation):
- The constraint applies to **how many times** each entity appears in the relationship set
- With aggregation, the aggregate (download) is treated as an entity set, and constraints on the `on` relationship between the aggregate and Devices are separate

---

## 9. ER-to-Relational Translation Rules

### Rule #1: Value Sets --> Domains

- Value sets are mapped to **domains** (SQL data types)
- ER attributes become relation attributes with **meaningful types**
- **Default: `NOT NULL`** -- avoid NULL values; justify every instance where NULL is needed

```sql
first_name VARCHAR(64) NOT NULL
```

### Rule #2: Entity Sets --> Relations (Tables)

- Each entity set becomes a **relation (table)**
- Entity attributes become columns
- **Candidate keys** become **PRIMARY KEY** and **UNIQUE NOT NULL**

**4 Components:**
1. Translate attributes as value sets using Rule #1
2. Add `PRIMARY KEY` constraint to identity attributes
3. Add `UNIQUE NOT NULL` constraint to other candidate keys (if any)
4. Add `CREATE TABLE` with the entity set name (use snake_case)

```sql
CREATE TABLE customers (
    first_name   VARCHAR(64) NOT NULL,
    email        VARCHAR(64) UNIQUE NOT NULL,  -- candidate key
    customerid   VARCHAR(16) PRIMARY KEY,      -- primary key
    -- other attributes omitted
);
```

**Composite Keys:** Specified using **table constraints** (not column constraints).

```sql
CREATE TABLE games (
    name     VARCHAR(32),
    version  CHAR(3),
    price    NUMERIC NOT NULL,
    PRIMARY KEY (name, version)    -- composite primary key
);
```

### Rule #3: Relationship Sets --> Relations (Tables)

- Relationship sets are mapped to **relations**
- Attributes = relationship's own attributes + **keys of participating entities**
- **Primary key = keys of the participating entity sets** (since relationships are identified by participating entities)

**4 Components:**
1. The relationship set's own attributes (if any)
2. The keys of participating entity sets (choose **primary key** for portability)
3. `FOREIGN KEY ... REFERENCES` to participating entity sets (optionally with `ON <event> <action>`)
4. `PRIMARY KEY` = combination of participating entity keys

```sql
CREATE TABLE downloads (
    customerid  VARCHAR(16) REFERENCES customers(customerid),
    name        VARCHAR(32),
    version     CHAR(3),
    deviceid    VARCHAR(64) NOT NULL,     -- relationship attribute
    PRIMARY KEY (customerid, name, version),
    FOREIGN KEY (name, version) REFERENCES games(name, version)
);
```

**Aggregation:** An aggregate is a relationship set, so Rule #3 applies. Its keys can be referenced by other relationship sets as if it were an entity set.

---

## 10. Translation Exceptions

### Exception #1: One-to-Many (0, 1)

When an entity participates at most once in a relationship (i.e., has a **(0, 1)** constraint), the naive Rule #3 translation gives a wrong primary key.

**Problem (Incorrect):** Applying Rule #3 naively with both entity keys as primary key allows an employee to work for two different companies.

```sql
-- INCORRECT: PK is (emp_number, name) -- allows same employee at 2 companies
CREATE TABLE work_for (
    start_date  DATE NOT NULL,
    emp_number  CHAR(8),
    name        VARCHAR(32) NOT NULL,
    PRIMARY KEY (emp_number, name),   -- WRONG
    ...
);
```

**Correction:** Restrict the primary key to only the entity set with **(0, 1)** cardinality.

```sql
-- CORRECT: PK is just (emp_number) -- each employee works for at most 1 company
CREATE TABLE work_for (
    start_date  DATE NOT NULL,
    emp_number  CHAR(8),
    name        VARCHAR(32) NOT NULL,
    PRIMARY KEY (emp_number),   -- only the (0,1) side
    FOREIGN KEY (emp_number) REFERENCES employees(emp_number),
    FOREIGN KEY (name) REFERENCES companies(name)
);
```

### Exception #2: Mandatory Participation (1, 1)

When an entity has **(1, 1)** participation (must participate exactly once), keeping entity and relationship as separate tables creates a loophole.

**Problem (Incorrect):** With (1, 1) cardinality, keeping separate tables for the entity and relationship allows inserting an employee without a corresponding work_for entry, violating the minimum constraint. There's nothing in the schema that *forces* every employee to have a row in the work_for table.

**Correction:** **Merge** the entity table and relationship table into one. Now every employee row *must* contain a company reference -- the minimum constraint is enforced by the schema itself.

```sql
-- CORRECT: Merged table ensures every employee has a company
CREATE TABLE employee_work_for (
    start_date   DATE NOT NULL,
    emp_number   CHAR(8),
    email        VARCHAR(64) NOT NULL,
    name         VARCHAR(32) NOT NULL,
    PRIMARY KEY (emp_number),
    UNIQUE (email),              -- candidate key
    FOREIGN KEY (name) REFERENCES companies(name)
);
```

### Exception #3: Partial Key (Weak Entity)

When the entity has a **partial key** (weak entity), its key alone is not enough to uniquely identify it -- we need the dominant entity's key too.

**Problem (Incorrect):** Using only the partial key (`emp_number`) as the primary key when it's a partial key -- it should NOT uniquely identify the entity by itself. Two different companies could have employees with the same `emp_number`.

**Correction:** **Add the primary key of the dominant entity set** to the primary key. Still merge the tables (because weak entities always have (1,1) participation on the identifying relationship, so Exception #2 also applies).

```sql
-- CORRECT: Include dominant entity's key in PK
CREATE TABLE employee_work_for (
    start_date   DATE NOT NULL,
    emp_number   CHAR(8),
    email        VARCHAR(64) NOT NULL,
    name         VARCHAR(32) NOT NULL,
    PRIMARY KEY (emp_number, name),   -- includes dominant entity's key
    UNIQUE (email),                   -- candidate key (email is not partial)
    FOREIGN KEY (name) REFERENCES companies(name)
);
```

---

## 11. Limitations

### Minimization of NULL

The translation scheme is designed to **minimize NULL values**. Any NULL values present should be due to the meaning of the attribute, not due to the translation.

### Inability to Translate Certain Cardinalities

The scheme (3 rules + 3 exceptions) **cannot** translate all cardinalities. For example, **(1, n)** -- "must participate at least once, can participate many times" -- cannot be fully enforced by schema alone.

**Priority of enforcement** (when you cannot enforce everything, follow this order):
1. Ensure **identities** can uniquely identify the entity set (most important)
2. Ensure **maximum cardinality** is satisfied
3. Ensure **minimum cardinality** is satisfied (hardest to enforce in SQL)

Not all constraints can be enforced purely through schema -- enforce **as much as possible**. For the rest, application-level logic or triggers may be needed.

### Connection to Normalization

The translation scheme closely matches the expected **normalized form** of the database. There is a direct connection between ER-to-relational translation and the normalization procedure.

---

## Quick Reference: ER Diagram Notation Summary

| Concept | Notation |
|---|---|
| Entity set | Rectangle (box) |
| Attribute | Circle (hollow) connected by line |
| Derived attribute | Circle connected by **dashed line** |
| Identifying attribute (key) | **Filled (black) circle** |
| Composite key | **Connected black circles** |
| Relationship set | Diamond |
| Aggregation | Diamond **wrapped in a box** |
| Participation constraint | (min, max) on the line between entity and relationship |
| Weak entity partial key | Blue filled circle connected to dominant entity's participation line |
| Role names | Labels on lines from same entity set to relationship |

## Quick Reference: Translation Rules Summary

| Rule | ER Concept | SQL Mapping |
|---|---|---|
| Rule #1 | Value sets | Domains (data types), default NOT NULL |
| Rule #2 | Entity sets | CREATE TABLE, PK + UNIQUE NOT NULL for candidate keys |
| Rule #3 | Relationship sets | CREATE TABLE with FK refs, PK = participating entity keys |
| Exception #1 | One-to-many (0,1) | PK = only the (0,1) side's key |
| Exception #2 | (1,1) participation | **Merge** entity + relationship into one table |
| Exception #3 | Partial key | **Merge** + add dominant entity's PK to composite PK |
