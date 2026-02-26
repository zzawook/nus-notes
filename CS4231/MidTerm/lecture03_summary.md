# CS4231 Lecture 3: Consistency Conditions

**Instructor:** YU Haifeng

> Welcome to the study notes for Lecture 3. This lecture is all about **consistency** -- the rules that govern what behavior is "allowed" when multiple processes access shared data at the same time. You will learn two major consistency definitions (sequential consistency and linearizability), understand how they relate to each other, and see how registers have their own hierarchy of consistency models. These are fundamental concepts that show up repeatedly in distributed systems and parallel computing, so take your time here.

---

## 1. Review of Last Lecture

Before diving in, recall what we covered previously:

- **Why synchronization primitives?** Busy waiting wastes CPU, but synchronization primitives need OS support.
- **Semaphore:** Used to solve the dining philosophers problem; helps avoid deadlocks.
- **Monitor:** Easier to use than semaphores and more popular; two kinds of monitors; used to solve producer-consumer and reader-writer problems.

---

## 2. What is Consistency?

### Why does this matter?

When multiple processes share data, things can go wrong. Think back to the `x = x + 1` mutual exclusion problem from Lecture 1: two processes both increment a shared variable, but if their reads and writes interleave in the wrong way, the final result is incorrect. By saying that execution was "not good," we were *implicitly assuming* some consistency definition. This lecture makes that notion precise.

In other words, mutual exclusion is actually a *way to ensure consistency* -- so we have already been dealing with consistency without naming it!

### Definition (in this course)

- **Consistency** specifies what behavior is allowed when a shared object is accessed by multiple processes.
- When we say something is "consistent," we mean the behavior satisfies a given specification.

### The Specification Trade-off

A specification is not inherently "right" or "wrong" -- anything *can* be a specification. But to be *useful*, it must balance two goals:

- **Sufficiently strong** -- otherwise the shared object is useless in a real program (too many weird behaviors are allowed).
- **Efficiently implementable** -- otherwise the specification remains a nice theory that nobody can actually build.

These two goals are often in tension with each other, which is why we study multiple consistency models.

### Abstract Data Type (ADT)

An abstract data type is a piece of data together with the operations allowed on it. Examples:

| Data Type | Operations |
|-----------|-----------|
| Integer X | `read()`, `write()` |
| Integer X | `increment()` |
| Queue | `enqueue()`, `dequeue()` |
| Stack | `push()`, `pop()` |

In this course, we focus on **shared** abstract data types -- ones that can be accessed by multiple processes. We use "shared object" as shorthand.

---

## 3. Formalizing a Parallel Execution

To reason precisely about consistency, we need formal language. Let's build it up piece by piece.

### Operation

An **operation** is a single invocation/response pair of a single method on a single shared object by a single process.

Think of it this way: when process p calls `read(X)`, the call starts at some wall-clock time (the *invocation*) and finishes at some later wall-clock time (the *response*). That entire call-and-return is one operation.

For an operation *e*:
- **proc(e):** The invoking process
- **obj(e):** The object being accessed
- **inv(e):** The invocation event (start time in wall clock / physical / real time)
- **resp(e):** The reply event (finish time in wall clock / physical / real time)

Two invocation events are the same if the invoker, invokee, and parameters are the same -- e.g., `inv(p, read, X)`. Two response events are the same if the invoker, invokee, and response value are the same -- e.g., `resp(p, read, X, 1)`.

### History

A **history** H is a sequence of invocations and responses **ordered by wall clock time**.
- For any invocation in H, the corresponding response is required to be in H.
- Each execution of a parallel system corresponds to a history, and vice versa.

**Example:** `inv(p, read, X)  inv(q, write, X, 1)  resp(p, read, X, 0)  resp(q, write, X, OK)`

Notice how p's read and q's write overlap in time -- p starts reading before q finishes writing.

---

## 4. Sequential vs. Concurrent History

### Sequential History

A history H is **sequential** if every invocation is *immediately* followed by its corresponding response. In other words, there is **no interleaving** -- operations happen one at a time.

**Example (sequential):**
```
inv(p, read, X)  resp(p, read, X, 0)  inv(q, write, X, 1)  resp(q, write, X, OK)
    [---- p's read ----]                 [---- q's write ----]
```

### Concurrent History

If an invocation is NOT immediately followed by its response (i.e., operations overlap), the history is **concurrent**.

**Example (concurrent):**
```
inv(p, read, X)  inv(q, write, X, 1)  resp(p, read, X, 0)  resp(q, write, X, OK)
    [------------ p's read ------------------]
                  [------------ q's write --------------------]
```

Think of it this way: in a sequential history, you can draw non-overlapping boxes around each operation. In a concurrent history, the boxes overlap.

---

## 5. Legal History and Subhistory

### Legal Sequential History

A sequential history H is **legal** if all responses satisfy the **sequential semantics** of the data type.

*Sequential semantics* means: the behavior you would get if there were only one process accessing the data type. For example, if you write 1 to X and then read X, you should get 1 back.

**Important:** It is possible for a sequential history to NOT be legal. For instance, `write(X, 1)` followed by `read(X) -> 0` is sequential (no interleaving) but illegal (the read should return 1).

### Subhistories

- **Process subhistory** H|p: The subsequence of all events involving process p. A process subhistory is always sequential (a single process does one thing at a time).
- **Object subhistory** H|o: The subsequence of all events involving object o. An object subhistory may or may not be sequential.

---

## 6. Equivalency and Process Order

### Equivalency

Two histories are **equivalent** if they contain the exactly same set of events.

- Same events imply **all responses are the same** (since response events are part of the set).
- The ordering of events may differ between the two histories.
- This makes sense because users only care about what values they get back -- not the internal order.

### Process Order

- **Process order** is a **partial order** among all operations.
- For any two operations of the **same process**, process order = the order in which that process executed them (program order).
- There is **no ordering** between operations of different processes.

Think of it this way: process order says "within my own program, things happen in the order I wrote them," but it says nothing about how operations from different processes interleave.

---

## 7. Sequential Consistency

### Why does this matter?

Sequential consistency is arguably the **most widely used consistency definition**. It is the standard for almost all commercial databases (e.g., Oracle), multi-processors, and multi-core CPUs. If you understand one consistency model well, make it this one.

### Lamport's Original Definition

> "...the results ... is the same as if the operations of all the processors were executed in some sequential order, and the operations of each individual processor appear in this sequence in the order specified by the program."

### Formal Definition

A history H is **sequentially consistent** if it is equivalent to some **legal sequential history S** that **preserves process order**.

Let's unpack the three key ideas:
1. **Use sequential history as a comparison point:** We ask "could these results have come from *some* sequential execution?"
2. **Focus on results only:** Users care about what values they see, not the internal timing. Two histories are "the same" if they have the same events (same responses).
3. **Preserve program (process) order:** Within each process, the operations must appear in the same order as in the original program.

### Worked Examples (initial value of x is 0)

**Example 1: Overlapping write and read**
```
P:  [--- write(x,1) ---]
Q:       [--- read(x) -> 0 ---]
```
Is this sequentially consistent? **Yes.** We can reorder to: Q reads x (gets 0), then P writes x with 1. This is a legal sequential history that preserves process order (each process only has one operation here).

**Example 2: Non-overlapping write then read**
```
P:  [--- write(x,1) ---]
Q:                          [--- read(x) -> 0 ---]
```
Is this sequentially consistent? **Yes.** Even though the write finishes before the read starts in real time, sequential consistency only requires preserving *process* order, not real-time order. We can place Q's read before P's write in the sequential history.

**Example 3: Two reads by same process**
```
P:  [-- write(x,1) --]  [-- read(x) -> 0 --]
Q:       [--- read(x) -> 0 ---]
```
Is this sequentially consistent? Ask yourself: can P write 1, then later read 0? That would violate process order for P if x was only written once. (This kind of scenario needs careful checking.)

**Example 4: Impossible value**
```
P:  [--- write(x,1) ---]
Q:                          [--- read(x) -> 2 ---]
```
Is this sequentially consistent? **No.** The value 2 was never written, so no legal sequential history can produce it.

---

## 8. Linearizability

### Motivation

Sequential consistency is sometimes **not strong enough**. Here is the key issue:

```
P:  [--- write(x,1) ---]
Q:                          [--- read(x) -> 0 ---]
```

P's write *finishes* before Q's read *starts* in real time. Intuitively, the write "already happened" when Q reads, so Q should see the value 1. But sequential consistency allows reordering Q's read before P's write (since they are on different processes), so it considers this execution valid. That feels wrong.

In other words, sequential consistency can reorder **non-overlapping** operations from different processes, which may be undesirable when there is a clear real-time ordering.

### Definition #1 (Linearization Point)

The execution is equivalent to some execution such that **each operation happens instantaneously** at some point (called the **linearization point**) between its invocation and its response.

Think of it this way: even though an operation takes time in reality (from invocation to response), we pretend it happens at one precise moment *within* that time interval. If we can find such a moment for every operation, and the resulting "instant" execution is legal, then the history is linearizable.

### Definition #2 (Formal)

History H is **linearizable** if:
1. It is equivalent to some **legal sequential history S**, and
2. S **preserves the external order** in H -- namely, the partial order induced by H is a subset of the partial order induced by S.

Whether the two definitions are the same is a homework exercise.

### External Order

The **external order** is a partial order on operations defined by real time:
- o1 "<" o2 iff the **response of o1** appears in H **before** the **invocation of o2**.
- In other words, o1 completely finishes before o2 starts.
- For overlapping operations, there is no external ordering between them.
- For a sequential history, external order is a total order on all operations (since nothing overlaps).

### Key Relationship

- **Linearizability implies sequential consistency** (linearizability is strictly stronger).
- Linearizability is arguably the strongest form of consistency people have ever defined.
- A history can be sequentially consistent but NOT linearizable.

**Example showing the gap:** (initial value of x is 0)
```
P:  [--- write(x,1) ---]
Q:                          [--- read(x) -> 0 ---]
```
This is **sequentially consistent** (we can reorder to: Q reads 0, then P writes 1). But it is **NOT linearizable** because P's write finishes before Q's read starts (external order: write < read), so any linearization must place the write before the read, and the read should then return 1.

### Another Interesting Example

```
P:  [---------- write(x,1) ----------]
Q:     [- read(x) -> 1 -]
R:                           [- read(x) -> 0 -]
```

Q's read overlaps with the write and returns 1 -- that is fine. R's read starts after Q's read finishes, but R's read overlaps with P's write, so R *could* read either the old or new value. This requires careful analysis of whether a valid linearization point assignment exists.

---

## 9. Linearizability is a Local Property

### Theorem

H is linearizable **if and only if** for every object x, H|x is linearizable.

**Why does this matter?** This is a powerful result for system designers: you can verify (or ensure) linearizability **one object at a time**, independently. You do not need to reason about all objects together.

**Sequential consistency is NOT a local property.** The lecture gives a counterexample using two queues x and y:

**Counterexample (x, y are initially empty queues):**
```
P:  [enque(x,1)] [enque(y,1)]                  [deque(x)->2]
Q:        [enque(y,2)]    [enque(x,2)]  [deque(y)->1]
```

- H|x is sequentially consistent: we can order it as enque(x,2), enque(x,1), deque(x)->2 (preserving process order within each process for x operations).
- H|y is sequentially consistent: similarly works out.
- But H itself is NOT sequentially consistent. The process orders from both objects together create contradictions: P does enque(x,1) before enque(y,1), and Q does enque(y,2) before enque(x,2). For deque(x)->2, x must have Q's enqueue first. For deque(y)->1, y must have P's enqueue first. This forces contradictory orderings of P and Q.

### Proof Sketch (using Definition #2)

Let's walk through how we prove the locality theorem step by step.

**Step 1: Construct a directed graph.** Create a node for every operation. Add directed edges in two cases:
   - **Edge "due to obj":** o1 and o2 are on the same object x, and o1 comes before o2 in the linearization of H|x.
   - **Edge "due to H":** o1 < o2 in the external order of H (response of o1 before invocation of o2 in real time).

**Step 2: Prove the graph is acyclic (Lemma).** This is the hard part -- proved by contradiction (see below).

**Step 3: Topological sort.** Since the graph is acyclic (a DAG), we can topologically sort it. Any topological sort gives a legal sequential history S, and by construction, every edge in the graph is respected by S. Since we included all external-order edges, S preserves the external order of H. Since we included per-object ordering edges, S is legal for each object. Since operations on different objects do not interfere with each other's legality ("Legal + Legal = Legal"), S is legal overall.

**Why "Legal + Legal = Legal"?** Suppose H|x linearizes to O1, O2 and H|y linearizes to O3, O4. If the topological sort gives O1, O3, O2, O4, is this legal? Yes -- inserting O3 (which is on object y) between O1 and O2 (which are on object x) does not change the behavior of x-operations or y-operations, because all the invocation events and their parameters come from the original history H. We do not re-run the program code; we just check that the events already recorded are consistent.

### Acyclicity Proof Intuition

Let's see why the directed graph cannot have a cycle.

1. Any cycle must alternate between "edges due to obj" and "edges due to H."
   - Two consecutive "due to obj" edges on the same object can be collapsed into one (because the linearization of H|x is a total order, so if A < B and B < C in that linearization, then A < C -- one edge).
   - Two consecutive "due to obj" edges on *different* objects x and y cannot be adjacent without an "edge due to H" in between (because an edge due to x goes from an x-operation to an x-operation, and an edge due to y starts at a y-operation).
   - Two consecutive "due to H" edges can be collapsed into one (because external order is transitive: if A finishes before B starts, and B finishes before C starts, then A finishes before C starts).

2. So any cycle reduces to alternating single edges: edge due to obj_x, edge due to H, edge due to obj_y, edge due to H, ...

3. Consider the simplest case -- a 4-node cycle: A -> B (due to x), B -> C (due to H), C -> D (due to y), D -> A (due to H).
   - D -> A (due to H) means D finishes before A starts in real time.
   - A -> B (due to x) means A comes before B in the linearization of H|x, which means A's linearization point is before B's, which (since linearization points are between invocation and response) means A starts no later than B finishes.
   - B -> C (due to H) means B finishes before C starts.
   - C -> D (due to y) similarly constrains C and D in real time.
   - Following the chain: D finishes before A starts, A's interval overlaps or precedes B's, B finishes before C starts, C's interval overlaps or precedes D's. This creates a contradiction in real time -- D cannot finish before A starts and also start after C, which starts after B, which finishes after A begins.

---

## 10. Consistency Models for Registers

A **register** is a specific kind of ADT: a single value that can be read and written. Registers are simple enough that we can define additional consistency models specific to them.

### Atomic Register

An implementation of a register is called **atomic** if it always ensures **linearizability** of the history.

### Regular Register

An implementation of a register is called **regular** if:
- When a read does **not overlap** with any write: the read returns the value written by one of the **most recent writes** (the write whose response time is the latest among all completed writes).
- When a read **overlaps** with one or more writes: the read returns the value written by one of the **most recent writes** OR the value written by one of the **overlapping writes**.

**An atomic register must be regular.** (Linearizability is stronger.)

### Safe Register

An implementation of a register is called **safe** if:
- When a read does **not overlap** with any write: the read returns the value written by one of the **most recent writes**.
- When a read **overlaps** with one or more writes: it can return **anything** (any value in the register's domain, even one that was never written).

**A regular register must be safe.** (Regular has the same non-overlapping guarantee, and is more restrictive during overlaps.)

### Relationships: Regular vs. Sequential Consistency

These two are **incomparable** -- neither implies the other!

**Regular but NOT sequentially consistent** (initial value 0):
```
P:          [--- write(1) ---]
Q:  [- read -> 1 -]  [- read -> 0 -]
```
Why regular? Both of Q's reads overlap with P's write, so each can return either the old value (0) or the new value (1). Returning 1 then 0 is fine under the regular definition.

Why not sequentially consistent? Q reads 1 first, then 0. In any sequential ordering, if Q sees 1 (meaning the write happened before that read), then Q's second read (which comes later by process order) should also see 1 or a later write. There is no legal sequential ordering.

**Sequentially consistent but NOT regular** (initial value 0):
```
P:  [--- write(1) ---]
Q:                        [--- read -> 0 ---]
```
Why sequentially consistent? We can order it as: Q reads 0, then P writes 1. Legal and preserves process order.

Why not regular? The read does not overlap with any write (write finishes before read starts), so a regular register must return the most recently written value, which is 1. Returning 0 violates regularity.

### Additional Relationships
- Safe does NOT imply Sequential Consistency (No)
- Sequential Consistency does NOT imply Safe (No)

### Hierarchy Summary

```
Strongest ---------------------------------> Weakest

Linearizability (Atomic) ===> Regular ===> Safe
         |
         v
Sequential Consistency

(Regular and Sequential Consistency are INCOMPARABLE)
```

---

## 11. Summary

Here is what you need to remember from this lecture:

1. **What is consistency?** It specifies the allowed behavior when shared objects are accessed by multiple processes. Different consistency definitions represent different trade-offs between strength and implementability.

2. **Sequential consistency:** A history is sequentially consistent if it is equivalent to some legal sequential history that preserves process order. Widely used in practice (databases, multi-core CPUs).

3. **Linearizability:** A history is linearizable if it is equivalent to some legal sequential history that preserves external (real-time) order. Strictly stronger than sequential consistency. Is a **local property** (can be verified per object independently). Sequential consistency is NOT a local property.

4. **Register consistency models:** Atomic (= linearizable) => Regular => Safe, in terms of strength. Regular and sequential consistency are incomparable -- neither implies the other.
