# CS4231 Lecture 6: Message Ordering

**Instructor:** YU Haifeng

> Welcome to the study notes for Lecture 6. This lecture is all about **message ordering** -- making sure messages in a distributed system are delivered in a sensible order. We start with causal ordering (messages that are causally related should be delivered in the right order), and then move to total ordering (all processes see all messages in the exact same order). Along the way, we cover the matrix-based causal ordering protocol and Skeen's algorithm for total ordering. These notes follow the lecture slides closely.

---

## Overview

This lecture covers three types of message ordering, from weakest to strongest:

1. **FIFO ordering** -- already discussed in the previous lecture (Lecture 5, as part of the Chandy-Lamport communication model)
2. **Causal ordering** -- and its protocol using an n x n matrix
3. **Total ordering** -- and two protocols (coordinator-based and Skeen's algorithm)

---

## 1. Causal Ordering

### Motivation: The Internet Chat Room Example

Why does causal ordering matter? Consider a simple scenario with three people: Alice, Bob, and Cary.

- Alice sends a message to Cary: "Introducing Bob to Cary"
- Alice sends a message to Bob: "Hi Bob, I just introduced you to Cary"
- Bob then sends a message to Cary: "Hi Cary, this is Bob"

**The problem:** If Cary receives Bob's message *before* Alice's introduction, Cary has no idea who Bob is. This is confusing and wrong from a user's perspective.

Think of it this way: Bob's message to Cary was *caused* by Alice's introduction. So the introduction must be delivered to Cary before Bob's greeting. This is the essence of **causal order**.

### Formalizing Causal Order

Let's make this precise. If a "send" event s1 **caused** a "send" event s2, then causal order requires the corresponding receive events to be delivered in the right order: r1 must come before r2 (when r1 and r2 are on the same process).

But how do we know whether s1 actually caused s2? In a distributed system, we cannot inspect program logic to determine true causality. Instead, we use the **happened-before** relation as a safe approximation:

- If s1 **happened before** s2, then s1 *may* have caused s2.
- We take the pessimistic (safe) approach: assume s1 indeed caused s2.
- Can we avoid being pessimistic? In general, no -- without knowing the application semantics, happened-before is our best tool.

**Causal Order Definition:** If s1 happened before s2, and their corresponding receive events r1 and r2 are on the same process, then r1 must be delivered before r2.

### How to Ensure Causal Ordering -- Naive Approach (Intuition)

A first idea: each message carries a "postscript" (PS) -- a log of all previously sent messages that the sender knows about. When a receiver gets a message, it checks the log and delays delivery until all causally prior messages have been delivered.

**The issue:** This message log grows unboundedly. Every message would carry the entire history of all messages the sender has ever seen. This is not practical.

**The solution:** Replace the unbounded log with a compact summary -- an n x n matrix.

### How to Ensure Causal Ordering -- The Matrix-Based Protocol

Each process maintains an **n x n matrix M**:

> **Important:** This is NOT the matrix clock from Lecture 4! It has a different meaning.

- **M[i, j]** = the number of messages sent from process *i* to process *j*, **as known by the current process**.

**On sending:** When process *i* sends a message to process *j*:
1. Increment `M[i, j]++` (one more message from *i* to *j*)
2. Piggyback the entire matrix *M* on the message

**On receiving:** When process *j* receives a message from process *i*, with piggybacked matrix *T*:
- Let *M* be the local matrix on process *j*
- **Deliver the message** (and set `M = pairwise-max(M, T)`) **if and only if** both conditions hold:
  - `T[k, j] <= M[k, j]` for all `k != i` -- all causally prior messages from other senders to *j* have already been delivered
  - `T[i, j] = M[i, j] + 1` -- this is the **next expected** message from sender *i* to *j*
- **Otherwise:** delay (buffer) the message and check again later

In other words, `M[i, j]` on process *j* takes consecutive values 0, 1, 2, 3, ... as messages from *i* arrive and are delivered. The matrix compactly summarizes everything the naive message-log approach would track.

### Example Run

Three processes: Alice (p1), Bob (p2), Cary (p3).

**Step 1:** Alice sends to Cary. Alice's matrix after incrementing M[1,3]:
```
(0, 0, 1)
(0, 0, 0)
(0, 0, 0)
```

**Step 2:** Alice sends to Bob. Alice's matrix after incrementing M[1,2]:
```
(0, 1, 1)
(0, 0, 0)
(0, 0, 0)
```

**Step 3:** Bob receives Alice's message. Check conditions:
- T[k, 2] <= M[k, 2] for all k != 1? Yes (all zeros).
- T[1, 2] = M[1, 2] + 1? Yes (1 = 0 + 1).
- **Can deliver!** Bob updates his matrix via pairwise-max.

**Step 4:** Bob sends to Cary (carrying his updated matrix). Cary receives Bob's message *before* Alice's message.
- Check: T[1, 3] <= M[1, 3]? T[1,3] = 1 but M[1,3] = 0. **Condition fails!**
- **Cannot deliver!** Cary must wait until Alice's message arrives first.

This is exactly the behavior we want -- Cary will not see Bob's "Hi Cary, this is Bob" until after Alice's introduction has been delivered.

### Correctness Proof

The correctness proof has two parts. Let's walk through each one.

**Part 1: If s1 happened before s2, then r2 will NOT be delivered before r1.**

Setup: s1 on process1, s2 on process2, r1 and r2 both on process3. (The case where s1 and s2 are on the same process is easier and left as an exercise.) We focus on the 3rd column (the "to process3" column) of the matrices.

Let's trace what happens:

1. At s1 (process1 sends Blue message to process3): the 3rd column of the matrix is `(x1, y1, 0)`. The 0 is because process3 never sends a message to itself.

2. Since s1 happened before s2, by the time s2 occurs on process2, the matrix values can only have grown. At s2 (process2 sends Red message to process3): the 3rd column is `(x4, y4, 0)` where `x4 >= x1` and `y4 > y1` (strictly greater for the y-component because process2 incremented M[2,3]).

3. **Proof by contradiction:** Assume Red (r2) is delivered before Blue (r1) on process3.
   - After delivering Red, the 3rd column of M on process3 will be at least `(>= x4, >= y4, 0)`, so `M[1, 3] >= x4 >= x1`.
   - M never decreases (it only gets updated via pairwise-max).
   - To deliver Blue, we need `T[1, 3] = M[1, 3] + 1`, meaning we need `M[1, 3] = x1 - 1`.
   - But we already have `M[1, 3] >= x1`. This condition can **never** be met. Contradiction!

**Part 2: All messages will eventually be delivered.**

This ensures no message gets stuck in the buffer forever. Here is the argument:

1. Consider the non-empty set of all undelivered messages destined for some process *j*.
2. Look at the j-th column of M on process *j*: it reads `(x_1, x_2, ..., x_n)`.
3. For each sender *i* that has undelivered messages, there exists a **successor message** -- the undelivered message whose piggybacked matrix *T* has `T[i, j] = x_i + 1` (the next expected message from *i*).
4. The set of successor messages is non-empty, has at most one message per sender, and at most *n* messages total.
5. Among the corresponding send events of the successor messages, there must be a **top successor message** -- one whose send event has no other successor's send event happened before it. (This exists because happened-before is a partial order on a finite set.)
6. **Claim: Any top successor message can be delivered.** Let's see why. Suppose the top successor is from process1, with T column `(x_1 + 1, y_2, ..., y_n)`. We need to show `x_i >= y_i` for all `i >= 2` (i.e., the delivery conditions are met). Proof by contradiction: if `x_2 < y_2`, then process2 has sent `y_2` messages to *j* (as known by the sender of the top successor), but only `x_2` have been delivered. So there exists an undelivered message from process2. Its successor message's send event must have happened before the top successor's send event (since the sender of the top successor knows about `y_2` messages from process2). But that contradicts the "top" property! So `x_i >= y_i` for all *i*, and the message can be delivered.
7. By induction (each delivery may enable more deliveries), all messages are eventually delivered.

**Summary of correctness:**
- All messages will eventually be delivered (no starvation).
- The delivery order satisfies causal order (no causality violations).

---

## 2. Causal Ordering of Broadcast Messages

What if instead of point-to-point messaging, every message is **broadcast** to all participants (including the sender itself)?

- A broadcast is modeled as *n* point-to-point messages (one to each process).
- **Application:** Internet chat room (everyone sees every message).
- For the same reasons as point-to-point, we may need to ensure causal order among broadcast messages.
- **The same matrix-based protocol works** -- each process simply uses the protocol from above for each of the *n* point-to-point messages that make up the broadcast.

---

## 3. Total Ordering of Broadcast Messages

### Definition

Total ordering is a stronger requirement than causal ordering:

- **All messages are delivered to all processes in exactly the same order.**
- Also called **atomic broadcast**.
- Total ordering **only** applies to broadcast messages (it does not make sense for point-to-point, since different processes receive different messages).

**Relationship to causal order:** Total order does NOT necessarily imply causal order, and causal order does NOT necessarily imply total order. They are independent properties. (The homework explores this further.)

### Why Does This Matter?

**Application 1: Internet Chat Room**
- Assign sequence numbers to messages, consistent across all users.
- Example: `[0] Alice: Welcome!`, `[1] Bob: Hello, Alice`, `[2] Alice: Let's try to prove P=NP`, ...
- Users can reference messages by number (e.g., "Please refer to message [2]"), and everyone knows which message is being referenced.

**Application 2: Distributed Ledger / Blockchain**
- Each node maintains a ledger of transactions (or a list of blocks, where each block contains a list of transactions).
- Each new transaction is broadcast to all nodes.
- All nodes must have the **same ordering** for all transactions -- this ensures consistency across the ledgers on all nodes.
- Note: Our protocols assume no failures.

### Protocol 1: Coordinator-Based Total Order Broadcast

The simplest approach:

1. A special process is assigned as the **coordinator**.
2. To broadcast a message:
   - Send the message to the coordinator.
   - The coordinator assigns a **sequence number** to the message.
   - The coordinator forwards the message (with its sequence number) to all processes.
   - Processes deliver messages in sequence number order.

**Problem:** The coordinator has too much control -- it is a single point of failure and a potential bottleneck. Can we do better?

### Protocol 2: Skeen's Algorithm for Total Order Broadcast

Skeen's algorithm distributes the work of assigning sequence numbers across all processes, removing the single coordinator.

**Setup:** Each process maintains:
- A **logical clock** (Lamport's logical clock)
- A **message buffer** for messages that have not yet been delivered

**Algorithm (step by step):**

1. **Sender** broadcasts the message to all processes (including itself).
2. **Each receiver** places the message in its buffer and replies to the sender with its **current logical clock value**.
3. **Sender** collects replies from all processes, picks the **maximum clock value** as the message's **sequence number**.
4. **Sender** notifies all processes of the assigned sequence number.

**Delivery condition:** A message in the buffer is delivered (and removed from the buffer) when:
- **All** messages currently in the buffer have been assigned their sequence numbers, AND
- This message has the **smallest** sequence number among all buffered messages.

**Tie-breaking:** If two messages have the same sequence number, use the **process ID** of the sender to break ties. This ensures a consistent total order across all processes (since everyone uses the same tie-breaking rule).

### Correctness Proof for Skeen's Algorithm

There are three claims to establish:

1. **All messages will be assigned sequence numbers.** (Straightforward -- the sender waits for all replies, which will eventually arrive since there is no message loss.)
2. **All messages will be delivered.** (Since all messages get numbers, and the buffer eventually has all messages numbered, the smallest-numbered one can be delivered, and so on.)
3. **If message A has a smaller number than B, then B is delivered after A on every process.**

Let's walk through the proof of Claim 3, which is the heart of correctness.

**Proof of Claim 3 (by contradiction):**

Suppose message A has a smaller sequence number than message B, but on some process (say process 3), A is delivered *after* B.

1. For A to be delivered after B, A must have been placed in process 3's buffer **after** B was already delivered.
2. At the time B was delivered, B's sequence number was already known and B was removed from the buffer.
3. When A arrives at process 3 (after B's delivery), process 3's logical clock must be **larger than B's sequence number**. (Why? Because delivering B -- which involved receiving the notification of B's number -- updates the logical clock to be at least as large as B's number. The clock only increases.)
4. Process 3 then replies to A's sender with its current logical clock value, which is **larger than B's number**.
5. A's final sequence number is the **maximum** of all replies, so A's number must be **larger than B's number**.
6. This contradicts our assumption that A has a smaller number than B!

Therefore, if A has a smaller number, A must be delivered before B on every process.

---

## Summary Table

| Topic | Scope | Key Mechanism |
|-------|-------|---------------|
| FIFO ordering | Point-to-point | Sequence numbers per channel |
| Causal ordering | Point-to-point | n x n matrix M piggybacked on messages |
| Causal ordering | Broadcast | Same matrix protocol (broadcast = n point-to-point messages) |
| Total ordering | Broadcast only | Coordinator-based OR Skeen's algorithm |

---

## Key Takeaways for the Exam

1. **Causal order** ensures that if s1 happened-before s2, and their receives r1, r2 are on the same process, then r1 is delivered before r2. It uses the happened-before relation as a pessimistic approximation of true causality.
2. **The matrix M[i,j]** counts messages from process *i* to process *j*. It is NOT the matrix clock. The delivery conditions check that all causally prior messages have arrived.
3. **Correctness of causal ordering** has two parts: (a) causal violations cannot happen, and (b) no message is stuck forever (all messages eventually get delivered).
4. **Total order** means all processes deliver all broadcast messages in the exact same order. It does NOT automatically imply causal order (and vice versa).
5. **Coordinator-based** total order is simple but has a single point of failure.
6. **Skeen's algorithm** uses logical clocks: receivers propose timestamps, the sender picks the max as the sequence number. The key insight in the correctness proof is that the logical clock on any process must exceed B's number before replying for A (if A arrives after B is delivered), forcing A's number to be larger than B's.

---

## Homework Questions

**Problem:** Show the relationship between conditions (C1), (C2), and (C3) on message delivery:

- **(C1):** `s1 -> s2` implies `not(r2 -> r1)` -- if s1 happened-before s2, then r2 does not happen-before r1
- **(C2):** `s1 < s2` implies `not(r2 -> r1)` -- if s1 is before s2 on the **same process**, then r2 does not happen-before r1
- **(C3):** `s1 -> s2` implies `not(r2 < r1)` -- if s1 happened-before s2, then r2 is not before r1 on the **same process**

Where `s1 -> s2` means s1 **happened-before** s2, and `s1 < s2` means s1 and s2 are on the **same process** and s1 occurs before s2.

**Task:** For each pair (C1 & C2, C1 & C3, C2 & C3), determine if one implies the other, both imply each other, or neither implies the other. Provide proofs or counterexamples.

**Additional question:** For the coordinator-based protocol (Slide 28), does the resulting total order always satisfy causal order? Why or why not?
