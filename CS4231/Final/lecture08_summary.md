# Lecture 08: Distributed Consensus

> **What this covers:** This lecture introduces the **distributed consensus** (agreement) problem — one of the most fundamental problems in distributed computing. You will study 5 different versions of consensus under different failure and timing models. This lecture covers **Version 0** (no failures — trivial), **Version 1** (node crash failures, synchronous — the (f+1)-round protocol with its correctness proof), and **Version 2** (link failures / the coordinated attack problem — impossibility result and a randomized algorithm that circumvents it). Versions 3 and 4 are covered in the next lecture. The impossibility proofs using **indistinguishability arguments** are a key technique to understand for the exam.

---

## 1. The Problem: Distributed Consensus

**Setup:** A set of n nodes in a distributed system. Each node has an **input value**. The nodes want to **agree on a common decision value**.

**Motivating example:** Think of booking a multi-segment flight. Each segment (node) needs to decide whether to purchase the ticket. All segments should either all purchase or all not purchase — you don't want a partial booking.

### Version 0: No Failures (Trivial)

If there are no node or link failures, consensus is easy:
1. Everyone sends its input to everyone else.
2. Each node now has all n values. Take the **majority** (with tie-breaking favouring 0).
3. Decide. Done.

This works because every node sees the exact same set of n values, so they all compute the same majority. But what happens when things fail?

---

## 2. The 5 Versions of Distributed Consensus

The lecture systematically explores consensus under increasingly challenging failure models:

| Version | Failure Model and Timing Model | Result |
|---|---|---|
| **Ver 0** | No node or link failures | Trivial — all-to-all broadcast |
| **Ver 1** | Node crash failures; Channels reliable; **Synchronous** | (f+1)-round protocol can tolerate f crash failures |
| **Ver 2** | No node failures; Channels may drop messages (arbitrarily many); **Synchronous** | **Impossible** without error; Randomized algorithm with 1/r error prob |
| **Ver 3** | Node crash failures; Channels reliable; **Asynchronous** | Next lecture |
| **Ver 4** | Node **Byzantine** failures; Channels reliable; **Synchronous** | Next lecture |

---

## 3. Version 1: Consensus with Node Crash Failures (Synchronous)

### 3.1 Model

**Failure model:**
- Nodes may experience **crash failures** — a crashed node stops executing and sending messages forever. Importantly, a node might crash *mid-round*, having sent messages to only some of the other nodes (not all).
- Communication channels are **reliable** — messages are never lost or corrupted.

**Timing model: Synchronous.** This means:
- Message delay has a **known** upper bound x.
- Node processing delay has a **known** upper bound y.
- This allows **accurate failure detection** — if you don't hear from a node within the expected time, you know it crashed.

### 3.2 Synchronous Systems and Rounds

Under a synchronous timing model, we can structure execution into **inter-locked rounds**. In each round:
1. Every process does some local computation.
2. Every process sends one message to every other process.
3. Every process receives one message from every other (non-crashed) process.

**Why rounds are useful:**
- Conceptually very clear — all processes make progress together, no one is left behind.
- Messages sent during a round are guaranteed to be received **by the end of that round**.
- Enables **accurate failure detection** — if you expect a message from node X and it doesn't arrive by the end of the round, X must have crashed.

**How to implement rounds in practice:** If each process has a physical clock with bounded clock error ε (e.g., 1 second), message propagation delay is at most d (e.g., 3 seconds), and local processing delay is at most p (e.g., 4 seconds), then set the round duration to ε + d + p (e.g., 1 + 3 + 4 = 8 seconds). Each process starts a new round every 8 seconds according to its local clock. Each message carries a round number, so late messages from previous rounds can be identified.

> **Key point:** Only a synchronous system enables us to define rounds this way. This is a very powerful abstraction, but remember it requires the underlying timing bounds to hold.

### 3.3 Three Required Properties

Any correct consensus protocol must satisfy:

| Property | Definition |
|---|---|
| **Termination** | All nodes that have not failed eventually decide |
| **Agreement** | All nodes that decide must decide on the **same value** |
| **Validity** | If all nodes have the same initial input v, then the decision must be v. Otherwise, nodes may decide on anything (but they must still agree). |

Think of Validity as a "non-triviality" condition — it prevents the degenerate solution of always deciding 0 regardless of inputs.

### 3.4 The Problem with Failures

Without failures, each process sends its input to all others, everyone picks the min (or max), done. But with crash failures, a process may **crash mid-round**, sending its input to only a subset of the other processes. Different non-crashed processes may then have different information, leading to different decisions.

**Solution: Keep forwarding values.** Don't just send your own input — in each subsequent round, forward *everything* you've learned. This way, even if the original sender crashed after telling only a few nodes, those nodes will relay the information to everyone else in the next round.

### 3.5 The Protocol

**Parameter:** f = maximum number of crash failures to tolerate. This must be provided as input.

Each node executes:

```
Set S = {my input}
for (int i = 1; i <= f+1; i++) {
    // do this for f+1 rounds
    send S to all other nodes
    receive n-1 sets (empty set from crashed nodes)
    for each set T received: S = S ∪ T
}
Decide on min(S)
```

**Intuition:** Each round, every node broadcasts its entire knowledge set S. After receiving sets from others, it unions everything together. After f+1 rounds, all surviving nodes decide on the minimum value in their set S.

**Why f+1 rounds?** Each failure can "corrupt" at most one round (a crashing node may send inconsistent partial information). With f failures spread across f+1 rounds, at least one round must be **failure-free** (a "good round"). As we'll prove, a single good round is enough to synchronize all nodes' knowledge.

### 3.6 Correctness Proof

#### Termination
Obvious — the protocol runs for exactly f+1 rounds with no waiting. Every non-crashed node reaches the decision step.

#### Validity
If all nodes start with the same input v, then every set S will only ever contain v (since that's the only input that exists). So min(S) = v.

#### Agreement (the interesting part)

We need to show that all non-faulty processes have the **same set S** at the end of round f+1.

**Key definitions:**
- A node is **nonfaulty during round r** if it has not crashed by the end of round r.
- A round is **good** if there is no node failure during that round (no node crashes during it).
- With f+1 rounds and at most f failures, **there must be at least one good round** (by pigeonhole principle — each failure "uses up" at most one round).

**Claim 1:** At the end of any good round r, all nonfaulty nodes during round r have the **same S**.

**Proof:** In a good round, no node crashes. So every nonfaulty node successfully sends its S to every other nonfaulty node, and every nonfaulty node receives every other's S. After taking the union, they all have the same combined set — specifically, the union of all their sets at the start of the round.

**Claim 2:** After any good round r, in any subsequent round t > r, the value of S on any nonfaulty node **does not change**.

**Proof:** After the good round r, all nonfaulty nodes have the same S. In subsequent rounds, they only exchange information that is already in everyone's S (since S can only grow via union, and they all start with the same S after round r). So no new values are added. Meanwhile, crashed nodes stop sending, and they cannot introduce new values that weren't already in S (because a crashed node's S is a subset of what it had, which was already shared during the good round).

**Claim 3:** All nonfaulty processes at round f+1 will have the same S.

**Proof:** By Claims 1 and 2. There exists at least one good round r ≤ f+1. After round r, all nonfaulty nodes have the same S, and this S doesn't change in subsequent rounds. Therefore, at round f+1, all nonfaulty nodes have the same S, and thus decide on the same min(S).

### 3.7 Lower Bound on the Number of Rounds

**Theorem:** With f crash failures, any consensus protocol requires at least **f+1 rounds**.

In other words, our (f+1)-round protocol is **optimal** in the number of rounds. The proof of this lower bound is beyond the scope of this module, but it tells us we can't do better.

> **Why f rounds are not enough:** The homework asks to show by example that if the Version 1 protocol decided after only f rounds (instead of f+1), it could violate agreement. The intuition is that with only f rounds and f failures, every round could have a failure, so there might be no good round, and nodes might end up with different sets S.

---

## 4. Version 2: Consensus with Link Failures (The Coordinated Attack Problem)

### 4.1 Model

**Failure model:**
- **Nodes do not fail** — all nodes are reliable.
- **Communication channels may fail** — they may drop an **arbitrary (unbounded) number** of messages. A message is either delivered intact or lost entirely.

**Timing model: Synchronous** — same as before, with known upper bounds on delays.

This is also known as the **coordinated attack problem**: two armies on opposite sides of a valley need to coordinate an attack, but their messengers may be captured (messages lost).

### 4.2 Goal (Same Three Properties)

| Property | Definition |
|---|---|
| **Termination** | All nodes eventually decide |
| **Agreement** | All nodes decide on the same value |
| **Validity** | If all nodes have the same input, they should decide on that value. Otherwise, decide anything (but agree). |

### 4.3 Impossibility Result (Deterministic)

**Theorem:** It is **impossible** to achieve consensus with the above three properties using a **deterministic** algorithm when channels can drop all messages.

**Why?** If the channel can drop *all* messages, a node has no way to distinguish between "the other node has input 1 but the message was lost" and "the other node has input 0 and sent nothing relevant."

**Proof technique: Indistinguishability.** Two executions are **indistinguishable** to a node if that node sees the same inputs and the same messages in both executions. A deterministic node must make the same decision in indistinguishable executions.

**The proof (for 2 nodes, A and B, with binary inputs {0, 1}):**

**Step 1:** Consider execution α₀ where both A and B have input 0, and all messages are dropped. By **validity**, both must decide 0.

**Step 2:** Consider execution α₁ where both A and B have input 1, and no messages are lost. By **validity**, both must decide 1.

**Step 3:** Now consider execution β where A has input 1, B has input 1, but all messages from B to A are dropped (A→B messages are delivered). From **A's perspective**, β is **indistinguishable** from an execution where A has input 1 and B has input 0 with all messages dropped (because A never hears from B in both cases). But wait — let's build the chain more carefully:

The proof constructs a chain of indistinguishable executions:

1. **Execution E₁:** A has input=0, B has input=0, all messages dropped. By validity: both decide **0**.

2. **Execution E₂:** A has input=1, B has input=0, all messages dropped. **B cannot distinguish E₂ from E₁** (B has input 0 and receives no messages in both). So B decides 0 in E₂. By **agreement**, A must also decide **0** in E₂.

3. **Execution E₃:** A has input=1, B has input=1, all messages dropped. **A cannot distinguish E₃ from E₂** (A has input 1 and receives no messages in both). So A decides 0 in E₃. By **agreement**, B must also decide **0** in E₃.

4. But in E₃, both inputs are 1 and we could have no message loss. Actually, E₃ has all messages dropped. Consider **Execution E₄:** A has input=1, B has input=1, no messages lost. We need to connect E₃ to E₄.

The actual chain works by progressively removing the "last message" in a finite execution. In any finite-round protocol, there is a last message. Consider the execution with that last message removed — the sender cannot tell the difference (it sent the message regardless), so this new execution is indistinguishable to the sender. By repeating this argument, we strip away all messages one by one, eventually reaching an execution with no messages at all — which we showed must decide 0. But the original execution with all messages and all-1 inputs must decide 1. Contradiction.

> **Exam intuition:** The indistinguishability argument is the key technique. The idea is: you can always remove the "last" message in any execution, and the sender can't tell. Repeating this strips all messages, connecting any execution to the all-messages-dropped case. This contradicts validity.

### 4.4 Even the Weakened Version Is Impossible

What if we weaken Validity?

**Weakened Validity (for binary inputs 0/1):**
- If all nodes start with **0**: decision should be **0**.
- If all nodes start with **1** AND **no message is lost** throughout the execution: decision should be **1**.
- Otherwise: nodes are allowed to decide on anything (but must still agree).

Note the asymmetry — the "all 1s" case requires no messages to be lost. This is much weaker.

**Theorem:** Even this weakened goal is **impossible** with a deterministic algorithm.

**Proof sketch (same indistinguishability technique):** Start from the execution where all inputs are 1 and no messages are lost (must decide 1). Now remove the last message sent in the protocol — the sender can't tell the difference, so it still decides 1, and by agreement the other node must too. Keep removing messages one at a time. Eventually, you reach an execution with all inputs = 1 but all messages lost. This is indistinguishable from all-0-inputs-all-messages-lost to both nodes (since neither node received anything). By validity (all 0s → decide 0), this is a contradiction.

### 4.5 Circumventing Impossibility: Randomization

Since deterministic consensus is impossible with link failures, we turn to **randomization**. The key insight: if processes can flip coins, the adversary (who controls which messages are lost) can no longer perfectly predict the processes' behaviour.

**Relaxed goal (with randomization):**
- **Termination:** All nodes eventually decide.
- **Agreement:** All nodes decide on the same value with probability **(1 − error_prob)**.
- **Validity (weakened):** Same as above (all 0 → decide 0; all 1 with no losses → decide 1; otherwise anything).

**Adversary model:** The adversary can set inputs and choose which messages to drop, but the adversary decides its strategy **before seeing the random coin flips**. This is the key — randomness is the weapon against the adversary.

### 4.6 The Randomized Algorithm (2 Processes)

**Setup:** Two processes P1 and P2, running for a predetermined number of rounds r.

**Mechanism — Level Variables:**

Each process maintains a **level** variable (L1 for P1, L2 for P2), both initialized to 0. In each round, both P1 and P2 send messages to each other. Each message carries: the sender's `bar` value (explained below), input, and current level.

**Level update rule:**
- When P2 receives a message from P1 with L1 attached: P2 sets L2 = L1 + 1.
- L1 is maintained similarly: when P1 receives a message from P2 with L2 attached: P1 sets L1 = L2 + 1.
- If a message is lost, the level stays the same.

**Lemma:** L1 and L2 **never decrease**, and at the end of any round, L1 and L2 differ by **at most 1**.

**Proof (by induction on rounds):**

*Base case:* Initially both L1 = L2 = 0. Difference is 0 ≤ 1. ✓

*Inductive step:* Suppose at the end of round k, |L1 − L2| ≤ 1. Without loss of generality, say L1 = L₀ and L2 = L₀ − 1 (the worst case where they differ by 1, with L1 ahead). In round k+1:
- If both messages are received: P1 receives L2 = L₀ − 1, so P1 sets L1 = L₀ (stays the same, since L₀ − 1 + 1 = L₀ = current L1). P2 receives L1 = L₀, so P2 sets L2 = L₀ + 1. New values: L1 = L₀, L2 = L₀ + 1. Difference = 1. ✓
- If P1→P2 is lost but P2→P1 is received: P1 sets L1 = L₀, P2 stays at L₀ − 1. Difference = 1. ✓
- If P2→P1 is lost but P1→P2 is received: P2 sets L2 = L₀ + 1, P1 stays at L₀. Difference = 1. ✓
- If both lost: nothing changes. Difference = 1. ✓

All cases maintain |L1 − L2| ≤ 1.

**The random "bar":** P1 picks a random integer **bar** ∈ {1, 2, …, r} at the beginning and sends it to P2 in every round. The value of `bar` is the secret random threshold that the adversary doesn't know in advance.

**Decision rule:**

At the end of r rounds:
- **P1 decides 1** if and only if: P1 knows both inputs are 1, **AND** L1 ≥ bar.
- **P2 decides 1** if and only if: P2 knows both inputs are 1, P2 knows bar, **AND** L2 ≥ bar.
- Otherwise, decide **0**.

(If P1 never sees P2's input, P1 defaults to 0. If P2 never learns bar, P2 defaults to 0.)

### 4.7 Error Analysis

**When can disagreement occur?** For P1 and P2 to disagree, one must decide 1 and the other 0. For anyone to decide 1, both inputs must be 1. So we only analyze the case where both inputs are 1.

**Case 1:** P1 sees P2's input but P2 doesn't see P1's input (or doesn't see bar). Then L1 = 1 and L2 = 0. P1 decides 1 iff bar ≤ 1, i.e., bar = 1. P2 decides 0 (since it doesn't have full info). Error only when **bar = 1**.

**Case 2:** P2 sees P1's input and bar, but P1 doesn't see P2's input. Then L1 = 0 and L2 = 1. P1 decides 0 (doesn't see P2's input). P2 decides 1 iff bar ≤ 1, i.e., bar = 1. Error only when **bar = 1**.

**Case 3:** Both see each other's input, and P2 sees bar. Let Lmax = max(L1, L2). Disagreement occurs when one has level ≥ bar and the other has level < bar. Since |L1 − L2| ≤ 1, this only happens when **bar = Lmax** (the boundary case where the higher level passes the threshold but the lower one doesn't).

**In all cases:** Error occurs only when bar hits a specific value (out of r possible values). Since bar is chosen uniformly at random from {1, …, r}:

$$\Pr[\text{error}] \leq \frac{1}{r}$$

### 4.8 Correctness Summary

- **Termination:** Obvious — runs for exactly r rounds.
- **Validity (all 0s):** If all inputs are 0, no one can decide 1 (the decision rule requires both inputs to be 1). So decision is 0. ✓
- **Validity (all 1s, no losses):** If all inputs are 1 and no messages are lost, then L1 = L2 = r after r rounds. Since bar ≤ r, both L1 ≥ bar and L2 ≥ bar. Both know each other's inputs and bar. Both decide 1. ✓
- **Agreement:** Holds with probability at least **(1 − 1/r)**.

By choosing r large, the error probability can be made arbitrarily small (but never zero — this is consistent with the impossibility result for deterministic algorithms).

---

## 5. Summary Table

| Version | Model | Result | Key Technique |
|---|---|---|---|
| **Ver 0** | No failures | Trivial (all-to-all, pick majority) | — |
| **Ver 1** | Crash failures, synchronous | **(f+1)-round protocol** — optimal | Good round argument (pigeonhole) |
| **Ver 2** | Link failures, synchronous | **Impossible** deterministically | Indistinguishability argument |
| **Ver 2** (randomized) | Link failures, synchronous | Randomized protocol, error ≤ 1/r | Random threshold `bar` hides info from adversary |

**Key takeaways:**

1. **Rounds are powerful:** The synchronous model lets us define rounds, which provide clean failure detection and structured progress. This is what makes Version 1 solvable.
2. **The (f+1)-round protocol** works because with f failures and f+1 rounds, at least one round is good (no failure), which synchronizes all nodes' knowledge. This is tight — f rounds are not enough.
3. **Link failures are devastating:** Even with only 2 nodes and no node failures, if the channel can drop messages, deterministic consensus is impossible. The indistinguishability proof technique (stripping the last message) is elegant and important.
4. **Randomization circumvents impossibility:** By introducing a random threshold unknown to the adversary, we can achieve consensus with arbitrarily small (but non-zero) error probability.
