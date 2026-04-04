# Lecture 09: Distributed Consensus (Continued) — FLP Impossibility & Byzantine Consensus

> **What this covers:** This lecture completes the study of distributed consensus by covering the final two versions: **Version 3** (crash failures, asynchronous — the famous **FLP impossibility theorem**, arguably the most fundamental result in distributed computing) and **Version 4** (Byzantine failures, synchronous — the **Byzantine Generals problem**, with the impossibility threshold n ≤ 3f and a protocol for n ≥ 4f+1). The FLP proof is intricate and exam-worthy — understanding the bivalency argument and the structure of Lemmas 1–4 is essential. For Byzantine consensus, understanding the rotating coordinator protocol and its two key lemmas is critical.

---

## 1. Version 3: Consensus with Crash Failures / Asynchronous

### 1.1 Model

**Failure model:**
- Nodes may experience **crash failures**.
- Communication channels are **reliable** — no messages are lost.

**Timing model: Asynchronous.**
- Process delay and message delay are **finite but unbounded**. Each message will eventually arrive, but there is no known upper bound on how long it takes.
- In practice, messages can be delayed for an arbitrarily long time.

**Critical consequence:** We can **no longer define rounds**. In a synchronous system, if you don't hear from a node within the known time bound, you know it crashed. In an asynchronous system, if you don't receive a message for a long time, you **cannot tell** whether the sender has crashed or whether the message is just very delayed. This inability to detect failures is the fundamental reason consensus becomes impossible.

### 1.2 Why the Round-Based Protocol Fails

Consider the (f+1)-round protocol from Version 1 applied in an asynchronous setting. With 3 nodes (inputs 2, 1, 3) and f = 1:

**Scenario 1:** The middle node (input=1) crashes in round 1. The left node receives from the middle and right, getting S = {1, 2, 3}. The right node only receives from the left (the crashed node's message to the right was lost), getting S = {2, 3}. After round 2, the left has {1, 2, 3} and the right has {1, 2, 3} — they agree. So 2 rounds work here.

**Scenario 2 (the problem):** The middle node crashes in round 1 but its messages are merely *delayed* (not lost — they arrive very late in round 2). In this case, the left and right nodes don't know whether the middle has crashed or is just slow. They can't wait forever (they'd violate termination), but they also can't decide early (they might disagree).

Adding more rounds doesn't help — the same issue persists. No matter how many rounds you use, the adversary can always delay messages to create ambiguity.

### 1.3 The FLP Impossibility Theorem

**FLP Theorem** [Fischer, Lynch, Paterson, 1985]:

> The distributed consensus problem under the asynchronous communication model is **impossible to solve** (with a deterministic algorithm) even with a **single** node crash failure.

This is arguably **the most fundamental result in distributed computing**. The fundamental reason is that the protocol is unable to accurately detect node failure in an asynchronous system.

### 1.4 Formalisms for the FLP Proof

The proof abstracts any possible deterministic protocol into a formal model:

**Process state:** Each process has local state plus two special variables:
- `input` ∈ {0, 1}
- `decision` ∈ {null, 0, 1} — initially null, can be written exactly once.

**Message system:** Captures the state of all communication channels. It is the set {(p, m) | message m is in transit to process p}. All messages are distinct.
- **Send** = add (dest, content) to the message system.
- **Receive** (invoked by process p) = either remove some (p, content) and return content, OR leave the message system unchanged and return null. (This models non-deterministic message delays.)

**Global state:** All process states + message system state. This is a deterministic state machine.

**Step:** A step of the protocol takes the system from one global state to another, by executing the following on some process p:
1. Receive a message m (m can be null).
2. Based on p's local state and m, send an arbitrary but finite number of messages.
3. Based on p's local state and m, change p's local state.

**Event:** An event e = (p, m) fully describes a step — process p receives message m. An event e **can be applied** to global state G if either m is null or (p, m) is in the message system.

**Schedule:** A sequence of events σ that captures an execution. σ **can be applied** to G if the events can be applied in order. We write G' = σ(G) to mean the global state after applying σ to G.

**Reachability:** G2 is **reachable** from G1 if there exists a schedule σ such that G2 = σ(G1).

**Consensus requirements (restated formally):**
- **Agreement:** No reachable global state from any initial state has more than one decision.
- **Validity:** If all nodes have the same initial input, they should all decide on that.
- **Termination:** Eventually all processes decide.

**Formalisms for asynchronous system and failures:**
- **Nonfaulty process:** takes an infinite number of steps (never stops).
- **Faulty process:** takes only a finite number of steps (eventually stops).
- If we only see a finite prefix of execution, we cannot distinguish faulty from nonfaulty (the nonfaulty one might just be slow).
- **Messages have unbounded but finite delay:** Every message is eventually delivered. Formally, if (p, m) is in the message system and p invokes receive() infinitely many times, then the system can only return null a finite number of times.
- **At most one faulty process.**

### 1.5 Proof Strategy

**Goal:** Show that for any deterministic consensus protocol, there exists an execution where no process ever decides — contradicting the termination requirement.

**Technique:** We (the adversary/scheduler) control which messages to deliver and which process takes the next step. Our strategy is to keep the system in a **bivalent state** forever, so that a decision is never forced.

**Classification of global states:**
- **0-valent:** 0 is the only possible decision reachable from G. (Processes may not have decided yet, but if they continue, they will decide 0.)
- **1-valent:** 1 is the only possible decision reachable from G.
- **Univalent:** Either 0-valent or 1-valent.
- **Bivalent:** NOT univalent — both 0 and 1 are reachable as decisions from G. The system hasn't "committed" to either outcome yet.

### 1.6 The Proof: Four Lemmas

#### Lemma 1: For any protocol A, there exists a bivalent initial state.

**Proof:** By contradiction. Assume all initial states are univalent. Consider n+1 initial states formed by progressively flipping inputs from 0 to 1: (0,0,…,0), (1,0,…,0), (1,1,0,…,0), …, (1,1,…,1).

By validity: (0,0,…,0) must be 0-valent and (1,1,…,1) must be 1-valent.

Since we assumed no bivalent states, somewhere in this sequence there must be two adjacent initial states S0 (0-valent) and S1 (1-valent) that differ in the input of a single process p.

Now consider an execution starting from S0 where p crashes at the very beginning (before doing anything). The remaining processes cannot distinguish S0 from S1 (since p's input is the only difference and p is silent). If they decide 0, this contradicts S1 being 1-valent (the same execution from S1 would also decide 0). If they decide 1, this contradicts S0 being 0-valent. Contradiction.

#### Lemma 2: (Commutativity of disjoint schedules) Let σ1 and σ2 be two schedules such that the set of processes executing steps in σ1 is **disjoint** from the set in σ2. Then for any G where both can be applied: σ1(σ2(G)) = σ2(σ1(G)).

**Proof:** By induction on k = max(|σ1|, |σ2|).

*Base case (k = 1):* Let e1 = (p1, m1) and e2 = (p2, m2) with p1 ≠ p2. Since they operate on different processes, the events don't interfere with each other. e1 can be applied to e2(G) and vice versa. The resulting global states are identical: e1(e2(G)) = e2(e1(G)).

*Inductive step:* Split one schedule into its first event plus the remainder, apply the inductive hypothesis.

> **Intuition:** If two sets of processes don't interact, the order in which they execute doesn't matter — the system reaches the same state.

#### Lemma 3: Let G be a global state and e = (p, m) an event applicable to G. Let W be the set of all global states reachable from G without applying e. Then e can be applied to any state in W.

**Proof:** Trivial. Since e = (p, m) was applicable to G (meaning m was in the message system or m = null), and no step in W involves p receiving m (since we avoided applying e), message m is still in the message system (or m is null). So e remains applicable.

#### Lemma 4: (The key lemma) Let G be a **bivalent** state, and e = (p, m) an event applicable to G. Let W be the set of states reachable from G without applying e, and V = e(W) = {e(G') | G' ∈ W}. Then **V contains a bivalent state**.

This is the critical lemma — it says that no matter what event e the scheduler is "forced" to deliver (to ensure fairness), there is always a way to execute other steps first so that applying e afterwards still leaves the system bivalent.

**Proof:** By contradiction. Assume V contains no bivalent states (all states in V are univalent).

**Claim 1:** There exists a schedule σ that contains e and σ(G) is 0-valent.

*Proof:* Since G is bivalent, there exists a 0-valent state G0 reachable from G, via some schedule σ1. If σ1 contains e, we're done (let σ = σ1). If σ1 doesn't contain e, let σ = e|σ1 (prepend e). Then σ(G) = σ1(e(G)). Since G0 = σ1(G) is 0-valent, e(G0) is also 0-valent (any state reachable from a 0-valent state is 0-valent). So σ(G) is 0-valent.

**Claim 2:** V contains a 0-valent state G0.

*Proof:* Take the σ from Claim 1. Consider the prefix σ' of σ whose last event is e. Then σ'(G) ∈ V. Since σ(G) is 0-valent and is reachable from σ'(G), σ'(G) must be 0-valent (since V has no bivalent states, it must be univalent, and since a 0-valent state is reachable from it, it's 0-valent).

**Claim 3:** V also contains a 1-valent state G1. (Symmetric argument.)

**Claim 4:** There exist F0 and F1 in W such that e(F0) = G0 is 0-valent, e(F1) = G1 is 1-valent, and **F1 = d(F0)** (i.e., F1 is reached from F0 by a single event d) or F0 = d(F1).

*Proof:* Since G0 and G1 are both in V (= e applied to states in W), there are paths in W from G to the pre-images of G0 and G1. Walk along these paths; since one ends at a 0-valent state and the other at a 1-valent state (after applying e), there must be two adjacent states F0, F1 in W where e(F0) is 0-valent and e(F1) is 1-valent.

**Deriving the contradiction:** Let d = (p', m') be the event such that F1 = d(F0). We now consider two sub-cases:

**Sub-case 1: p ≠ p'** (e and d involve different processes). By Lemma 2 (commutativity): e(d(F0)) = d(e(F0)), i.e., e(F1) = d(G0). But e(F1) = G1 is 1-valent, while d(G0) is reachable from G0 which is 0-valent, so d(G0) must also be 0-valent. Contradiction (a state can't be both 0-valent and 1-valent).

**Sub-case 2: p = p'** (both e and d involve the same process p). Whether the system decides 0 or 1 depends entirely on the ordering of e and d on process p. Consider all possible executions from F0 where **p does not execute any steps**. By the termination requirement (and since at most one process can fail — and p is that one), some other process must eventually decide. Let T = σ(F0) be the state when some process first decides, where σ involves no steps by p.

Now: e(T) = e(σ(F0)) = σ(e(F0)) = σ(G0) (by Lemma 2, since σ doesn't involve p). This is 0-valent.

Also: e(d(T)) = e(d(σ(F0))). Since d involves p and σ doesn't, d(σ(F0)) = σ(d(F0)) = σ(F1) by Lemma 2. So e(d(T)) = e(σ(F1)) = σ(e(F1)) = σ(G1), which is 1-valent.

But some process has already decided in state T. If it decided 0, then e(d(T)) (which is 1-valent) would also have that 0 decision — contradicting that it's 1-valent. If it decided 1, then e(T) (which is 0-valent) would also have that 1 decision — contradicting that it's 0-valent. Either way: **contradiction**.

### 1.7 Putting the Proof Together

**Main proof of FLP Theorem:**

1. Start from a bivalent initial state (exists by Lemma 1).
2. We act as the scheduler. Processes take steps in **round-robin** fashion.
3. When it's process p's turn: if the message system has no messages for p, let e = (p, null). Otherwise, let e = (p, m) where m is the oldest message for p.
4. If e(G) is bivalent (where G is the current state), apply e. Done — still bivalent.
5. If e(G) is univalent, use **Lemma 4** to find a schedule σ (not containing e) such that e(σ(G)) is bivalent. Apply σ then e.
6. The system stays bivalent forever. No process ever decides. ∎

**The scheduler plays by the rules:**
- All nonfaulty processes take an infinite number of steps (round-robin ensures this).
- All messages are eventually delivered (the oldest-message rule ensures this).
- But process delays and message delays may not be bounded — and this is OK in an asynchronous system.

> **Key insight:** If delays were bounded (synchronous), we could define rounds and solve consensus (Version 1). The FLP theorem shows that the moment delays become unbounded, consensus becomes impossible — even with just one crash failure.

### 1.8 Practical Implications

Complete correctness is not possible in asynchronous systems. In practice, we cope by:
- Living with a very low probability of **disagreement** (relaxing agreement).
- Living with a very low probability of **blocking/non-termination** (relaxing termination). Two-phase commit and three-phase commit can block forever.
- Using **randomization** to circumvent the impossibility.

---

## 2. Version 4: Consensus with Byzantine Failures (Synchronous)

### 2.1 Model

**Failure model:**
- Nodes may experience **Byzantine failures** — a faulty node can behave **arbitrarily**. It can send different messages to different nodes, lie about its input, or act maliciously to disrupt the protocol.
- Communication channels are **reliable**.

**Timing model: Synchronous** (rounds are well-defined).

**Goal (same three properties, adapted):**

| Property | Definition |
|---|---|
| **Termination** | All **non-faulty** nodes eventually decide |
| **Agreement** | All **non-faulty** nodes decide on the same value |
| **Validity** | If all **non-faulty** nodes have the same input, that value should be the decision. Otherwise, nodes may decide on anything (but must agree). |

Note: the properties only apply to non-faulty nodes. We don't care what faulty (Byzantine) nodes decide.

### 2.2 Why Byzantine Failures Are Hard

**Simplified example:** 3 processes (A, B, C), at most 1 Byzantine failure. Don't know which one is faulty.

**First (unsuccessful) attempt:** Each process broadcasts its input to all others.

If A is Byzantine (with the other two having inputs 1 and 0): A sends 1 to B and 0 to C. Then B sees {1, 1, 0} and C sees {0, 1, 0}. B might decide 1, C might decide 0 — agreement violated!

The problem is B and C can't tell whether A or each other is the liar.

**Second (unsuccessful) attempt:** Add a second round where each process relays what it heard.

In round 1, A sends 1 to B and 0 to C. In round 2, B tells C "A said 1" and C tells B "A said 0." Now B sees a contradiction: A said 1 directly, but C relays "A said 0." B knows *someone* is lying — but it can't tell if it's A (who lied to C) or C (who is lying about what A said).

### 2.3 Byzantine Consensus Threshold

**Theorem:** If **n ≤ 3f**, then Byzantine consensus is **impossible**.

This is a non-trivial proof (the simple examples above do NOT constitute a proof, even for f = 1). The key insight is that with n ≤ 3f, the faulty nodes can always "simulate" a partition where two groups of honest nodes see contradictory information.

### 2.4 The Protocol (for n ≥ 4f + 1)

**Structure:** n processes, at most f Byzantine failures, running for **f+1 phases**. Each phase has **two rounds**: an all-to-all broadcast round and a coordinator round. Process k is the coordinator for phase k. The total number of rounds is 2(f+1).

**Code for Process i:**

```
V[1..n] = 0; V[i] = my input;

for (k = 1; k ≤ f+1; k++) {    // f+1 phases
    // Round 1: All-to-all broadcast
    send V[i] to all processes;
    set V[1..n] to be the n values received;
    if (value x occurs > n/2 times in V) proposal = x;
    else proposal = 0;

    // Round 2: Coordinator round
    if (k == i) send proposal to all;   // I am coordinator
    receive coordinatorProposal from the coordinator;

    // Decision: listen to coordinator or not?
    if (value y occurs > n/2 + f times in V) V[i] = y;
    else V[i] = coordinatorProposal;
}

decide on V[i];
```

### 2.5 Key Ideas Behind the Protocol

**Rotating coordinator paradigm:** In each phase, a different process acts as coordinator. With at most f failures and f+1 phases, **at least one phase has a nonfaulty coordinator** (pigeonhole). This is the "deciding phase."

**Two thresholds — the heart of the protocol:**

1. **Majority threshold (> n/2):** Used to compute the proposal. If a value x appears more than n/2 times in what you received, it must be "popular" — adopt it as your proposal.

2. **Super-majority threshold (> n/2 + f):** Used to decide whether to **ignore the coordinator**. If a value y appears more than n/2 + f times in what you received, it means enough non-faulty processes support y that it's "safe" to stick with y regardless of what the coordinator says. This prevents a faulty coordinator from overriding the consensus of a deciding phase.

**Why ignore the coordinator sometimes?** After a deciding phase (where a nonfaulty coordinator made everyone agree), a later phase might have a *faulty* coordinator who tries to override the agreement. The super-majority check detects this: if you see overwhelming support for a value, you trust the crowd over the coordinator.

### 2.6 Correctness Proof

#### Lemma 1: If all nonfaulty processes have V[i] = y at the beginning of phase k, then this remains true at the end of phase k.

**Proof:** If all nonfaulty processes hold value y, then in the all-to-all broadcast round, at least n − f processes send y. So every nonfaulty process sees y at least n − f > n/2 + f times (since n ≥ 4f + 1 implies n − f ≥ 3f + 1 > n/2 + f). So every nonfaulty process hits the super-majority threshold and sets V[i] = y, **ignoring the coordinator**. The coordinator (even if Byzantine) cannot override this.

#### Lemma 2: If the coordinator in phase k is nonfaulty, then all nonfaulty processes have the same V[i] at the end of phase k.

**Proof:** Two cases based on what the nonfaulty coordinator's proposal is:

**Case 1: Coordinator has proposal = x** (some specific value x appeared > n/2 times in the coordinator's V). Since the coordinator is nonfaulty, it honestly received n values. If x appears > n/2 times on the coordinator, then at least > n/2 − f of those must come from nonfaulty processes. On any other nonfaulty process, x also appears ≥ n/2 − f times (at least the same nonfaulty processes sent x). So it's impossible for any *other* value y ≠ x to appear > n/2 + f times on any nonfaulty process (there aren't enough processes). Therefore, no nonfaulty process hits the super-majority threshold for a value different from x. All nonfaulty processes accept the coordinator's proposal x.

**Case 2: Coordinator has proposal = 0** (no value appeared > n/2 times on the coordinator). This means no value has overwhelming support. Similarly, on any other nonfaulty process, no value can appear > n/2 + f times. So no nonfaulty process hits the super-majority threshold, and all adopt the coordinator's proposal (which is 0). All nonfaulty processes end up with V[i] = 0.

#### Correctness Summary

- **Termination:** Obvious — f+1 phases, each with 2 rounds.
- **Validity:** Follows from Lemma 1. If all nonfaulty processes start with the same input y, then by Lemma 1, y is preserved through every phase.
- **Agreement:** With f+1 phases and at most f failures, at least one phase has a nonfaulty coordinator (the "deciding phase"). By Lemma 2, all nonfaulty processes have the same V[i] after the deciding phase. By Lemma 1, this value is preserved through all subsequent phases. So all nonfaulty processes decide the same value.

### 2.7 Open Question: 3f + 1 ≤ n ≤ 4f

The protocol above requires n ≥ 4f + 1. The impossibility says n ≤ 3f is impossible. What about the gap 3f + 1 ≤ n ≤ 4f? Byzantine consensus IS solvable in this range, but the protocols are more complex (beyond the scope of this module).

---

## 3. Complete Summary Table

| Version | Model | Result | Key Technique/Insight |
|---|---|---|---|
| **Ver 0** | No failures | Trivial (all-to-all, pick majority) | — |
| **Ver 1** | Crash, synchronous | (f+1)-round protocol | Good round argument |
| **Ver 2** | Link failures, synchronous | Impossible deterministically; randomized with 1/r error | Indistinguishability; random threshold |
| **Ver 3** | Crash, **asynchronous** | **Impossible** (FLP theorem) | Bivalency argument; can't detect failures |
| **Ver 4** | **Byzantine**, synchronous | Impossible if n ≤ 3f; protocol for n ≥ 4f+1 with 2(f+1) rounds | Rotating coordinator + two thresholds |

**Key takeaways:**

1. **FLP is the crown jewel:** Asynchrony + even one crash failure = impossible. The proof uses bivalent states as a tool — the adversary keeps the system bivalent by carefully scheduling events. The fundamental issue is that you cannot distinguish a crashed process from a slow one.

2. **Byzantine failures are the hardest:** A Byzantine node can lie and send contradictory information. You need n > 3f nodes to tolerate f Byzantine failures. The rotating coordinator protocol uses two rounds per phase: all-to-all broadcast (to detect consensus) and coordinator proposal (to push towards agreement), with the super-majority threshold as a "lock" to prevent faulty coordinators from overriding previous agreement.

3. **The hierarchy of difficulty:** No failures (trivial) < crash + synchronous (solvable) < link failures (impossible deterministically) < crash + asynchronous (impossible) and Byzantine + synchronous (impossible if n ≤ 3f, solvable otherwise).
