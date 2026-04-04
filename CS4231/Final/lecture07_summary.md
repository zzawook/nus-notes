# Lecture 07: Leader Election

> **What this covers:** This lecture addresses the **leader election** problem in distributed systems (Chapter 13). You will learn why leader election is impossible on anonymous rings (symmetry argument), study the **Chang-Roberts Algorithm** on a ring with its best-case O(n), worst-case O(n²), and average-case O(n log n) message complexity (the average-case proof is particularly interesting and exam-worthy), and then see how to elect a leader on **general graphs** using spanning tree construction and node counting. Understanding the impossibility result, the algorithm, and the full average-case analysis is essential for the exam.

---

## 1. The Problem: Leader Election

**Context:** We have a network of n processes (nodes) in a distributed system. The goal is to elect **exactly one** process as the "leader." Every process must eventually output either "I am the leader" or "I am not the leader," and exactly one process should declare itself the leader.

**Why do we need a leader?** Many distributed algorithms need a single coordinator — for example, to initiate a broadcast, to serve as a central point for collecting results, or to break symmetry in a protocol. Leader election is a fundamental building block.

**Assumptions:**
- Each process has a **unique identifier** (UID/ID). This is critical — we will see that without unique IDs, the problem becomes impossible.
- The network topology may be a ring or a general graph, depending on the variant.

---

## 2. Impossibility on Anonymous Rings

**Setup:** An **anonymous ring** is a ring where all processes are identical — they have no unique IDs. Every process runs the same deterministic algorithm with the same initial state.

**Theorem:** There is **no deterministic algorithm** that can elect a leader on an anonymous ring.

**Proof (symmetry argument):**

The key insight is that if all processes start in the same state and execute the same deterministic algorithm, then at every step, all processes are in the **same state**. Here is why:

1. Initially, all processes are in the same state (they are identical and anonymous).
2. In a synchronous ring, at every round, every process sends the same message (because they are in the same state running the same code), and every process receives the same message (because its left and right neighbours both sent the same thing).
3. Therefore, after processing the received messages, every process transitions to the **same new state**.
4. By induction, at every point in time, all processes are in the same state.

**Consequence:** If any process decides "I am the leader," then *all* processes decide "I am the leader" (because they are all in the same state). But that means either 0 or n leaders — never exactly 1. Contradiction.

This is why **unique IDs** are essential for leader election. The IDs break the symmetry.

> **Exam intuition:** The impossibility rests on the fact that identical initial states + identical algorithm + symmetric topology = identical executions. Any property that one process has, all processes have. So you cannot single out exactly one.

---

## 3. Chang-Roberts Algorithm (Ring, with Unique IDs)

### 3.1 Setup

- **Topology:** Unidirectional ring — each process can only send messages **clockwise** (to its right neighbour).
- Each process has a unique ID.
- The goal: the process with the **maximum ID** becomes the leader.

### 3.2 The Algorithm

Each process Pi executes:

```
1. Send an "election" message containing your own ID clockwise.
2. When you receive an election message with ID j:
     - If j > myId:   forward the message clockwise (j is a better candidate)
     - If j < myId:   discard the message (j cannot be the leader)
     - If j == myId:  declare yourself the leader (your message went around
                      the entire ring without being stopped — you have the max ID)
```

**How it works intuitively:** Think of each node's election message as a "candidacy" that travels around the ring. Whenever a message encounters a node with a higher ID, the message is killed (that node has its own, better candidacy). Only the message from the node with the globally maximum ID survives the entire trip around the ring and returns to its sender.

### 3.3 Correctness

**Exactly one leader:** The only way a process declares itself the leader is if it receives its own election message back. For this to happen, the message must have passed through every other node without being discarded. At each node, the message is only forwarded if the message's ID is larger than the node's own ID. The only ID that is larger than every other ID in the ring is the **maximum ID**. Therefore, only the process with the maximum ID can ever receive its own message back. Exactly one leader is elected.

**Termination:** The maximum-ID message is always forwarded and eventually returns to its sender after at most n hops.

### 3.4 Message Complexity Analysis

This is the heart of the lecture and a likely exam topic. Let's carefully go through all three cases.

#### Best Case: O(n)

**When does this happen?** When the IDs are arranged in **decreasing order clockwise** around the ring. That is, the process with the largest ID is followed clockwise by the process with the second-largest ID, and so on.

**Why O(n)?** In this arrangement:
- The maximum-ID message travels all the way around: **n messages**.
- The second-largest ID's message reaches the max-ID node (its immediate clockwise neighbour) and is killed after just **1 hop**.
- The third-largest's message also reaches a larger node after 1 hop and is killed.
- In fact, every non-max message is killed after just 1 hop.

Total: n (for the max message) + (n − 1) × 1 (each other message travels 1 hop) = **2n − 1 = O(n)**.

#### Worst Case: O(n²)

**When does this happen?** When the IDs are arranged in **increasing order clockwise**. That is, the smallest ID is followed clockwise by the second-smallest, and so on, with the largest ID at the end.

**Why O(n²)?** In this arrangement:
- The maximum-ID message travels all the way around: n messages.
- The second-largest ID's message travels n − 1 hops before being killed by the max.
- The third-largest's message travels n − 2 hops.
- And so on.

Total: n + (n − 1) + (n − 2) + … + 1 = **n(n + 1)/2 = O(n²)**.

#### Average Case: O(n log n) ⭐

This is the most interesting and subtle part. The analysis uses **linearity of expectation** and a comparison with the **geometric distribution**.

**Setup:** Assume all n! permutations of IDs around the ring are equally likely. Without loss of generality, assume the IDs are {1, 2, …, n} (what matters is relative ordering, not absolute values).

**Random variable definition:** For each node k (where k is its rank, i.e., the node with the k-th smallest ID), define:

$$X_k = \text{number of messages caused by node } k\text{'s election message}$$

More precisely, Xk is the number of hops that node k's election message travels before being killed (or, if k = n, it travels all n hops around the ring).

We want to compute:

$$E\left[\sum_{k=1}^{n} X_k\right] = \sum_{k=1}^{n} E[X_k] \quad \text{(by linearity of expectation)}$$

**Key observation about Xk:**

- Xk ≥ 1 always (the message always travels at least one hop — it is sent to the clockwise neighbour).
- Xk = i + 1 means the message traveled i + 1 hops. For this to happen, the i immediate clockwise neighbours of node k all had IDs smaller than k (so they forwarded the message), and the (i + 1)-th neighbour had an ID larger than k (and killed it). Actually, more precisely: Xk > i means node k's message survived at least i hops, meaning the next i clockwise nodes all have IDs less than k's ID.

**Computing Pr[Xk = 1]:**

Node k's message is killed immediately (after 1 hop) if and only if its clockwise neighbour has a larger ID. There are n − 1 other positions. The probability that the immediate clockwise neighbour has an ID larger than k's ID (rank k) is:

$$\Pr[X_k = 1] = \frac{n - k}{n - 1}$$

because there are n − k nodes with IDs larger than k, and any of the n − 1 other nodes is equally likely to be the clockwise neighbour.

**Conditional probabilities — Pr[Xk = i + 1 | Xk > i]:**

Given that node k's message has survived i hops (meaning the next i clockwise nodes all had IDs smaller than k), what is the probability it is killed at hop i + 1?

The (i + 1)-th clockwise neighbour is chosen uniformly from the remaining n − 1 − i nodes. Among those, n − k have IDs larger than k. So:

$$\Pr[X_k = i + 1 \mid X_k > i] = \frac{n - k}{n - 1 - i}$$

**Key inequality:** Since i ≥ 0 means n − 1 − i ≤ n − 1, we have:

$$\frac{n - k}{n - 1 - i} \geq \frac{n - k}{n - 1} = p$$

where we define p = (n − k)/(n − 1).

So the probability of being killed at each step is **at least p**, regardless of how far the message has already traveled.

**Comparison with geometric distribution (the lottery analogy):**

A geometric random variable Y with parameter p is the number of trials until the first success, where each trial succeeds independently with probability p. Its expectation is:

$$E[Y] = \frac{1}{p}$$

Think of it like a lottery: if you buy a ticket each day with success probability p, the expected number of days until you win is 1/p.

The crucial insight: Xk is **stochastically dominated** by a geometric random variable with parameter p. At each hop, the message has probability ≥ p of being killed. A geometric variable with the same p has probability *exactly* p of stopping at each step. Since Xk is "at least as likely to stop" at each step, Xk tends to be smaller (in a stochastic sense) than Y. Therefore:

$$E[X_k] \leq E[Y] = \frac{1}{p} = \frac{n - 1}{n - k}$$

**Summing over all k:**

$$E\left[\sum_{k=1}^{n} X_k\right] = \sum_{k=1}^{n} E[X_k] \leq \sum_{k=1}^{n} \frac{n - 1}{n - k}$$

Let's compute this sum. Substitute j = n − k, so when k = 1, j = n − 1; when k = n, j = 0:

$$\sum_{k=1}^{n} \frac{n-1}{n-k} = (n-1) \cdot \sum_{k=1}^{n} \frac{1}{n-k} = (n-1) \cdot \left(\frac{1}{n-1} + \frac{1}{n-2} + \cdots + \frac{1}{1} + \frac{1}{0}\right)$$

Wait — when k = n, we get 1/0, which is undefined. This is because the node with the maximum ID (k = n) is never killed — its message travels all the way around. So we handle it separately: Xn = n (the max-ID message always travels n hops). For k = 1, …, n − 1:

$$\sum_{k=1}^{n-1} \frac{n-1}{n-k} = (n-1) \cdot \left(\frac{1}{n-1} + \frac{1}{n-2} + \cdots + \frac{1}{1}\right) = (n-1) \cdot H(n-1)$$

where H(m) = 1 + 1/2 + 1/3 + … + 1/m is the **m-th harmonic number**, and H(m) = Θ(ln m).

Therefore:

$$E\left[\text{total messages}\right] \leq n + (n-1) \cdot H(n-1) = n + (n-1) \cdot \Theta(\ln n) = O(n \log n)$$

> **Exam takeaway:** The average-case analysis is a beautiful application of linearity of expectation. The key steps are: (1) decompose total messages into per-node contributions Xk, (2) bound each E[Xk] by comparing with a geometric distribution using the key inequality on conditional kill probabilities, (3) sum the resulting harmonic series.

---

## 4. Leader Election on General Graphs

### 4.1 When n Is Known

If the number of nodes n is known to all processes, leader election on a general graph is straightforward:

**Approach (Flooding):** Every node floods its ID to all other nodes. Once a node has collected all n IDs (it knows n, so it knows when to stop), it picks the maximum. The node with the maximum ID declares itself the leader.

This is correct but has high message complexity. In practice, we often want more efficient solutions.

### 4.2 When n Is Unknown

If n is **not known**, nodes cannot tell when they have heard from everyone. The solution uses a **spanning tree** to first count the number of nodes, and then uses that count to run a leader election.

**Strategy:**
1. Build a spanning tree of the network.
2. Use the spanning tree to count the number of nodes (convergecast).
3. Now n is known — use the "n known" approach.

The spanning tree is also independently useful for broadcast and aggregation, which is why this lecture covers its construction in detail.

---

## 5. Spanning Tree Construction

### 5.1 Setup

- One node is designated as the **root** (or initiator). In practice, this could be any node that starts the protocol — the tree is built from its perspective.
- The network is a general connected graph with bidirectional links.
- The goal: build a spanning tree rooted at the initiator — every node becomes part of the tree with a unique parent (except the root).

### 5.2 The Protocol

The root initiates the protocol by sending a **"child request"** message to all its neighbours.

When a node receives a child request:
- **If this is the first child request it has received:** It accepts the sender as its **parent** and replies **"I'm your child."** It then forwards child requests to all of *its own* neighbours (except the parent it just accepted).
- **If it has already accepted a parent (i.e., it has received a child request before):** It replies **"I'm NOT your child"** and does nothing further.

**When does a node know it is done?** A node has received a response ("I'm your child" or "I'm NOT your child") from every neighbour it sent a child request to. At that point, it knows exactly which of its neighbours are its children in the spanning tree.

**Why this works:** Since the graph is connected, the child requests will eventually reach every node. The first request to reach any node "claims" it for the tree. The result is a spanning tree rooted at the initiator, because:
- Every non-root node has exactly one parent (the sender of the first child request it received).
- There are no cycles (each node is claimed exactly once, and the parent relationship always points toward the root).

### 5.3 Example Walkthrough

Consider nodes connected in some general graph with node 1 as root:
1. Node 1 sends child requests to all its neighbours.
2. Each neighbour that has not been claimed replies "I'm your child" and propagates child requests to *their* neighbours.
3. If a node receives a child request but is already in the tree, it replies "I'm NOT your child."
4. The process ripples outward from the root until all nodes are in the tree.

---

## 6. Counting Nodes Using the Spanning Tree (Convergecast)

Once the spanning tree is built, we can count the total number of nodes using a **convergecast** — information flows from the leaves up to the root.

### 6.1 The Protocol

```
Leaf nodes: send value = 1 to their parent.
Internal nodes: wait until all children have sent their values.
                 Set value = 1 + sum of all children's values.
                 Send value to parent.
Root: value = 1 + sum of all children's values = n (total node count).
```

**Why it works:** Each leaf contributes 1 (itself). Each internal node adds itself (1) plus the total count of all nodes in its subtree (the sum of its children's values). By induction, the value at any node equals the number of nodes in its subtree. At the root, this equals n.

### 6.2 Applications of the Spanning Tree

Once you have a spanning tree, you get several useful operations essentially for free:

- **Broadcast:** The root sends a message down the tree. Each internal node forwards it to all its children. Reaches every node with exactly n − 1 messages.
- **Aggregation (convergecast):** Compute any aggregate function (sum, max, min, count) by having leaves send their values up and internal nodes combine children's values with their own.
- **Max/Min:** Same convergecast pattern, but each internal node computes max (or min) of its own value and all children's values instead of the sum.

> **Key insight:** The spanning tree reduces communication on a general graph to communication on a tree, which is much simpler and more efficient. Many distributed algorithms use a spanning tree as an underlying infrastructure.

---

## 7. Putting It All Together: Leader Election on a General Graph (n Unknown)

**Full protocol:**

1. **Some node initiates** spanning tree construction (it becomes the root of the tree).
2. **Build spanning tree** using the child request/response protocol (Section 5).
3. **Count nodes** using convergecast on the tree (Section 6). The root now knows n.
4. **Broadcast n** from the root down the spanning tree so every node knows n.
5. **Run "n-known" leader election:** Every node floods its ID. Each node collects all n IDs and the one with the maximum ID declares itself the leader.

**Subtlety:** In step 1, "some node initiates" — but which one? If multiple nodes initiate, you can still make this work (each builds its own tree and they can be merged or compared), but the lecture focuses on the single-initiator case.

---

## 8. Summary Table

| Topic | Key Result |
|---|---|
| Anonymous ring (deterministic) | Leader election is **impossible** — symmetry argument |
| Chang-Roberts (ring, unique IDs) | Elects max-ID as leader; **O(n) best**, **O(n²) worst**, **O(n log n) average** messages |
| General graph (n known) | Flood all IDs, pick max — straightforward |
| General graph (n unknown) | Build spanning tree → count nodes → flood IDs |
| Spanning tree | Enables broadcast, convergecast (aggregation, count, max/min) |

**Key takeaways:**
1. **Unique IDs are necessary** for deterministic leader election — anonymous rings make it impossible due to symmetry.
2. **Chang-Roberts** is simple and elegant. The average-case O(n log n) analysis is a textbook application of linearity of expectation and geometric distribution comparison.
3. **Spanning trees** are the Swiss Army knife of distributed algorithms on general graphs — they enable counting, broadcast, and aggregation, turning complex graph problems into simple tree problems.
