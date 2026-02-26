# CS4231 Lecture 4: Models and Clocks

**Instructor:** YU Haifeng

> Welcome to the study notes for Lecture 4. This lecture tackles a fundamental challenge in distributed systems: **how do you define "time" when there is no shared global clock?** You will learn about three increasingly powerful software clocks -- Logical Clocks, Vector Clocks, and Matrix Clocks -- each of which captures a different level of ordering information. Understanding these clocks is essential because they underpin many distributed protocols you will encounter later.

---

## Review of Last Lecture

- Consistency: what it means and why we care about it
- Sequential consistency: equivalent to a legal sequential history preserving process order
- Linearizability: equivalent to a legal sequential history preserving external (real-time) order; a local property
- Consistency models for registers: Atomic > Regular > Safe

---

## Overview

**Goal:** Define "time" in a distributed system using software clocks, since physical clocks are impractical.

Three types of software clocks covered, each more powerful (and more expensive) than the last:

| Clock | What it captures | Data per event |
|-------|-----------------|----------------|
| **Logical Clock** (Lamport) | One-way: happened-before => smaller value | 1 integer |
| **Vector Clock** | Two-way: happened-before <=> smaller value | n integers |
| **Matrix Clock** | Knowledge about what others know | n x n integers |

---

## 1. System Assumptions

Before defining clocks, we need to be precise about our model.

### Atomic Actions/Events

A process can perform three kinds of atomic actions:
- **Local computation** (internal processing)
- **Send** a single message to a single process
- **Receive** a single message from a single process
- There is **no atomic broadcast** (you cannot atomically send to multiple processes at once)

### Communication Model

- **Point-to-point** messaging
- **Error-free** channels with **infinite buffer**
- Messages may arrive **out of order** (no FIFO guarantee between the same pair of processes)

---

## 2. Motivation for Clocks

### Why do we need ordering?

Many protocols need to impose an **ordering among events** across different processes.

Think of it this way: your mom deposits money into your bank account (Event A at one bank server), and then you use your ATM card to buy something (Event B at a different server). The bank must order these correctly -- the deposit must be recognized before the withdrawal.

### Why not just use physical clocks?

Physical clocks seem like an obvious solution, but they have serious problems:
- **Theory of relativity** makes perfect synchronization fundamentally impossible across distances.
- Even ignoring relativity, there are **efficiency problems**: the clock error must be smaller than the message propagation delay, meaning clocks need to be "quite" accurate, which is expensive to maintain.

### Software "Clocks" to the Rescue

Software clocks offer a better approach:
- **Much lower overhead** than maintaining sufficiently accurate physical clocks
- Allow a protocol to **infer ordering among events**
- Goal: capture the event orderings that are **visible to users who do not have physical clocks**

But what orderings *are* visible to users without physical clocks? This leads us to the happened-before relation.

---

## 3. The "Happened-Before" Relation

### Definition

The **happened-before** relation (denoted ->) captures the ordering that is visible to users when there is no physical clock. It is a **partial order** among events, defined by three rules:

1. **Process order:** If events A and B are on the same process and A occurs before B, then A -> B.
2. **Send-receive order:** If A is a send event and B is the corresponding receive event, then A -> B.
3. **Transitivity:** If A -> B and B -> C, then A -> C.

Think of it this way: if you can trace a chain of "within the same process" steps and "message sent then received" steps from event A to event B, then A happened before B. If you cannot trace such a chain in either direction, the events are **concurrent** -- neither happened before the other.

### Historical Significance

This relation was first introduced by **Lamport** and is considered the first fundamental result in distributed computing. Lamport won the 1st Edsger W. Dijkstra Prize in Distributed Computing for this work.

### Example

Consider three processes with events A, B (on process 1), C (on process 2), and D (on process 3), where process 1 sends a message to process 2 (B -> C), and process 3 sends a message to process 2 (D -> some event before C):

- A -> B (process order on process 1)
- B -> C (send-receive order)
- A -> C (transitivity)
- A ? D -- **concurrent** (no chain connects them)
- B ? D -- **concurrent**
- C ? D -- depends on the specific timing of messages

The goal of software clocks is to **capture this happened-before relation** using simple data structures.

---

## 4. Logical Clocks (Lamport Clocks)

### Why does this matter?

Logical clocks are the simplest software clock -- just a single integer per event. They give us a one-directional guarantee about ordering, which is often good enough for many protocols.

### Protocol

Each process maintains a local integer counter `C`:

- **On local computation or send event:** Increment `C` by 1. (The event's logical clock value is this new `C`.)
- **On send:** Attach the current logical clock value `V` to the message.
- **On receive:** Set `C = max(C, V) + 1`, where `V` is the value attached to the received message.

Think of it this way: the counter always moves forward, and when you receive a message, you "fast-forward" your counter to be ahead of both your current count and the sender's count. This ensures that a receive event always has a larger clock value than the corresponding send event.

### Worked Example (3 processes)

```
process1:  1 ----- 2 ----- 3 -------------------- 4
                    |  (send)                       ^
                    v                               | (recv)
process2:  1 ----- 3 ---------- 4 ------- 5        |
                   ^                       |        |
                   | (recv)                | (send) |
                   | 3 = max(1,2)+1        v        |
process3:  1 ----- 2 ----------------------------- 3
```

Let's trace process 2's receive event step by step:
- Process 2's local counter was 1.
- The message from process 3 carried value V = 2 (process 3's send event had clock 2).
- C = max(1, 2) + 1 = 3.

And process 1's receive (from process 2):
- Process 1's local counter was 3.
- The message from process 2 carried value V = 3 (wait -- actually process 2 sends at clock 5, but let's check the slide: process 1 receives from process 2, with value 3 attached).
- Actually from the diagram: process 1's 4th event receives from process 2's event at clock 3. So C = max(3, 3) + 1 = 4.

### Key Property (Theorem)

> **If s happened before t, then logical_clock(s) < logical_clock(t).**

The **forward direction holds:** happened-before implies a smaller clock value.

The **reverse does NOT hold:** a smaller clock value does NOT imply happened-before.

**Why not?** Look at the example above: process 1's last event has clock value 4, and process 3's last event has clock value 3. So clock(process3's last) < clock(process1's last). But these two events are **concurrent** -- neither happened before the other. The clock values just happen to be different.

In other words, logical clocks can give "false positives" for ordering: two concurrent events may have different clock values, making it *look like* one came before the other when really we cannot tell.

---

## 5. Vector Clocks

### Motivation

Logical clocks only give us one direction of the implication. Sometimes we need the full picture: we want to determine not just "if A happened before B then clocks agree," but also "if the clocks say A is before B, then A really did happen before B."

Vector clocks give us **both directions** (if and only if).

### Definition

Each event has a **vector of n integers** (where n is the number of processes in the system).

**Comparison operators:**
- `v1 = v2` if all n fields are the same: `(4,1,7) = (4,1,7)`
- `v1 <= v2` if every field in v1 is <= the corresponding field in v2: `(3,1,5) <= (4,1,7)`
- `v1 < v2` if `v1 <= v2` **and** `v1 != v2`: `(3,1,5) < (4,1,7)`

**Important:** The `<` relation is **NOT a total order**. Some vectors are incomparable. For example, `(1,0)` and `(0,1)` -- neither is <= the other. This is actually a feature, not a bug: incomparable vectors correspond to concurrent events!

### Protocol

Each process `i` maintains a local vector `C` (initialized to all zeros):

- **On local computation or send event:** Increment `C[i]` by 1 (only your own component).
- **On send:** Attach the entire vector clock `V` to the message.
- **On receive:** Set `C = pairwise-max(C, V)` (take the max of each component), then increment `C[i]` by 1.

Think of it this way: your i-th component counts how many events *you* have done. When you receive a message, you absorb everything the sender knew about (via pairwise-max), then record your new receive event (via incrementing your own component).

### Worked Example (3 processes)

```
process1:  (1,0,0) ------- (2,0,0) ------------------- (3,0,0)
                            |  (send)
                            v
process2:  (0,1,0) ------- (0,2,2) --------- (2,3,2)
                            ^
                            | (recv)
process3:  (0,0,1) ------- (0,0,2) ------------------- (0,0,3)
```

Let's trace process 2's receive event in detail:
- Process 2's local vector was `C = (0,1,0)`.
- The received message (from process 3) carried `V = (0,0,2)`.
- Pairwise-max: `(max(0,0), max(1,0), max(0,2)) = (0,1,2)`.
- Then increment C[2] (process 2's own component): `(0, 1+1, 2) = (0,2,2)`.

And process 2's third event (receiving from process 1):
- Process 2's local vector was `C = (0,2,2)` (after some events; actually looking at the diagram, the send from process 1 at (2,0,0) is received at process 2).
- The received message carried `V = (2,0,0)`.
- Pairwise-max: `(max(0,2), max(2,0), max(2,0)) = (2,2,2)`.
- Then increment C[2]: `(2, 2+1, 2) = (2,3,2)`.

### Key Property (Theorem)

> **s happened before t  <=>  vector_clock(s) < vector_clock(t)**

This is the critical upgrade over logical clocks: the implication goes **both ways**.

**Proof sketch (forward direction: happened-before => smaller vector clock):**

We prove this by checking all three ways that happened-before can arise:
- **Process order:** If s and t are on the same process i, then between s and t, C[i] has been incremented at least once, and no component ever decreases. So every component of s's vector is <= the corresponding component of t's vector, and component i is strictly less. Hence vector(s) < vector(t).
- **Send-receive order:** If s is a send and t is the corresponding receive, the receiver takes pairwise-max with the sender's vector and then increments its own component. So vector(t) >= vector(s) component-wise, with at least one strict inequality.
- **Transitivity:** If vector(s) < vector(x) and vector(x) < vector(t), then vector(s) < vector(t), because the `<` relation on vectors is itself transitive.

**Proof sketch (reverse direction: smaller vector clock => happened-before):**

Consider two cases:
- **Same process:** If s and t are on the same process, and vector(s) < vector(t), then s must have occurred before t on that process (since the process's own component only increases). So s -> t by process order.
- **Different processes:** Suppose s is on process p and t is on process q. Since vector(s) < vector(t), we know vector(s)[p] <= vector(t)[p]. But vector(t)[p] can only increase at process q when q receives a message (directly or transitively) that carries information from p after p's counter was at least vector(s)[p]. This means there must be a chain of messages from p to q connecting s to t. Therefore s -> t.

### Application: Blockchain Transactions

In systems like Ethereum/Bitcoin, each process creates transactions, and each transaction gets a vector clock value.

- Process 2 with vector clock (0,2,2) has seen all transactions whose vector clock is < (0,2,2).
- This means process 2 has seen B2 (0,1,0) and B3 (0,0,1), but has NOT seen B1 (1,0,0) -- because (1,0,0) is not < (0,2,2) (the first component 1 > 0).
- Based on the vector clock values, other processes can determine which transactions process 2 has and has not seen.

---

## 6. Matrix Clocks

### Motivation

A vector clock tells you **what you know** -- specifically, which events you are aware of. But sometimes you need to know **what other processes know**. For example: "Has every process seen transaction B1? If so, we can safely garbage-collect it."

Think of it this way:
- A **vector clock** is like knowing your own reading list.
- A **matrix clock** is like knowing everyone's reading list.

### Structure

- Each event has **n vector clocks** (one for each process), forming an **n x n matrix**.
- Row `i` on process `i` is called the **principal vector** -- it behaves exactly like a regular vector clock.
- Row `j` (for j != i) on process `i` represents "what process i knows about what process j knows."

### Protocol

**For the principal vector (row i) on process i:**
- Increment `C[i]` at each "local computation" and "send" event (just like a regular vector clock).
- When sending a message, **all n vectors** (the entire matrix) are attached to the message.
- At each "receive" event, let `V` be the **principal vector of the sender** from the received message: `C = pairwise-max(C, V); C[i]++`.

**For non-principal vector (row j, where j != i) on process i:**
- At each "receive" event, let `V` be the **vector corresponding to process j** in the received message: `C = pairwise-max(C, V)`.
- No changes on local computation or send events (these rows only update when new information arrives via messages).

In other words, the principal vector is updated exactly like a vector clock. The non-principal vectors are "piggybacked" on messages to propagate knowledge about what other processes know.

### Worked Example (3 processes)

Each event stores a 3x3 matrix (shown as 3 rows):

```
process1:  [(1,0,0)    [(2,0,0)    [(3,0,0)
            (0,0,0)     (0,0,0)     (0,0,0)
            (0,0,0)]    (0,0,0)]    (0,0,0)]

process2:  [(0,0,0)    [(0,0,0)    [(2,0,0)    [(2,0,0)
            (0,1,0)     (0,2,2)     (2,3,2)     (2,4,2)
            (0,0,0)]    (0,0,2)]    (0,0,2)]    (0,0,2)]

process3:  [(0,0,0)    [(0,0,0)    [(0,0,0)    [(2,0,0)
            (0,0,0)     (0,0,0)     (0,0,0)     (2,4,2)
            (0,0,1)]    (0,0,2)]    (0,0,3)]    (2,4,4)]
```

Let's trace process 2's receive from process 3:
- Process 2's matrix before: rows = `[(0,0,0), (0,1,0), (0,0,0)]`
- Received message from process 3 with matrix: rows = `[(0,0,0), (0,0,0), (0,0,2)]`
- Principal vector update (row 2): `pairwise-max((0,1,0), (0,0,2)) = (0,1,2)`, then C[2]++ gives `(0,2,2)`.
- Non-principal row 1 update: `pairwise-max((0,0,0), (0,0,0)) = (0,0,0)` (no change).
- Non-principal row 3 update: `pairwise-max((0,0,0), (0,0,2)) = (0,0,2)`.
- Result: `[(0,0,0), (0,2,2), (0,0,2)]`.

Now let's look at process 3's last event (receiving from process 2):
- Process 3's matrix before: rows = `[(0,0,0), (0,0,0), (0,0,3)]`
- Received message from process 2 with matrix: rows = `[(2,0,0), (2,4,2), (0,0,2)]`
- Principal vector update (row 3): `pairwise-max((0,0,3), (2,4,2)) = (2,4,3)`, then C[3]++ gives `(2,4,4)`.
- Non-principal row 1 update: `pairwise-max((0,0,0), (2,0,0)) = (2,0,0)`.
- Non-principal row 2 update: `pairwise-max((0,0,0), (2,4,2)) = (2,4,2)`.
- Result: `[(2,0,0), (2,4,2), (2,4,4)]`.

### Application: Truncated Blockchains

With matrix clocks, a process can determine when **all** processes have seen a particular transaction, enabling safe garbage collection.

**Example:** Suppose transaction B1 has vector clock (1,0,0).

At process 3's last event, its matrix is:
```
Row 1 (what process 3 knows about process 1's knowledge): (2,0,0)
Row 2 (what process 3 knows about process 2's knowledge): (2,4,2)
Row 3 (process 3's own knowledge):                        (2,4,4)
```

Process 3 checks: does every row's principal-vector-equivalent have first component >= 1? Yes:
- Row 1: first component = 2 >= 1
- Row 2: first component = 2 >= 1
- Row 3: first component = 2 >= 1

Since all rows indicate knowledge past B1's vector clock, process 3 can conclude: **all 3 processes have seen B1**. It is now safe to garbage-collect (truncate) B1 from the blockchain.

---

## 7. Knowledge Hierarchy (A Snack for the Mind)

There is an elegant pattern here that connects to deeper theory:

| Clock Type | What it tells me | Dimensionality |
|------------|-----------------|----------------|
| Vector clock | What **I** know | 1D (one vector) |
| Matrix clock | What I know about what **others** know | 2D (matrix) |
| ??? | What I know about what others know about what others know | 3D (tensor)? |
| ... | ... | ... maybe even infinity-D? |

This hierarchy connects to the theory of **knowledge and common knowledge** in distributed systems -- a deep and fascinating area of research.

---

## Summary Table

| Clock Type | Guarantee | Data per Event | Key Insight |
|------------|-----------|----------------|-------------|
| **Logical Clock** | happened-before => smaller clock value (one way only) | 1 integer | Cheap but gives false positives |
| **Vector Clock** | happened-before <=> smaller clock value (both ways) | n integers | Can perfectly detect concurrency |
| **Matrix Clock** | Knowledge about what others know | n x n integers | Enables distributed garbage collection |

---

## Homework Questions

These are directly from the lecture slides -- great practice for the exam.

1. **Show that "concurrent with" is not a transitive relation.**
   *Hint:* Find three events A, B, C such that A is concurrent with B, and B is concurrent with C, but A is NOT concurrent with C (i.e., A -> C or C -> A).

2. **Fair total ordering:** We discussed breaking ties (when two events have the same logical clock value) using process identifiers, which always favors smaller IDs. Suggest a scheme without this disadvantage.
   *Hint:* Use the logical clock value in determining priority. Think about rotating which process gets priority based on the clock value.

3. **Convert function:** Given a vector clock implementation, write a function `convert` that takes a vector timestamp as input and outputs a logical clock timestamp.
   *Hint:* Think about the relationship between the vector clock and what information a logical clock would have had. What single number summarizes "how far along" an event is?
