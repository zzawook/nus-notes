# CS4231 Lecture 10 — Self-Stabilization

## 1. The Big Idea

A distributed system is **self-stabilizing** if, starting from **any** (possibly arbitrary, possibly corrupted) global state, it is guaranteed to reach a **legal** state in a finite number of steps, and once in a legal state, it stays in legal states forever (assuming no further faults).

Why we care: real distributed systems get hit by transient faults — a cosmic ray flips a bit, a process crashes and restarts with garbage memory, a message gets corrupted. Traditional fault-tolerance asks "tolerate up to f faults of type T." Self-stabilization is much stronger: **no assumption on the initial state**. Wherever the system finds itself, it heals.

Two properties define self-stabilization:

1. **Convergence** — From any state, the system reaches a legal state in finite time.
2. **Closure** — Once in a legal state, the system remains in legal states (in the absence of new faults).

The set of *legal* states is application-defined. The set of *illegal* states is everything else.

> Subtle point: convergence does **not** require that the system pass through "increasingly legal" states monotonically. It only requires that a legal state is eventually reached. Many self-stabilizing algorithms in fact have non-monotonic recoveries.

---

## 2. The Computation Model

We use a network of $n$ processes connected by a graph. Each process has local state. A **global state** (or *configuration*) is the tuple of all local states.

Execution model used in this lecture (Dijkstra's original): a **central daemon** picks one *enabled* (also called *privileged*) process at a time and lets it execute one atomic step. A process is enabled when its local guard evaluates to true.

A *legal state* is one in which **exactly one** process is privileged (this is the application: ensuring mutual exclusion via a single-token property).

---

## 3. Dijkstra's Self-Stabilizing Ring (Rotating Privilege)

**Setting.** Processes $P_0, P_1, \ldots, P_{n-1}$ are arranged on a ring. Each process $P_i$ has a state variable $V_i \in \{0, 1, \ldots, k-1\}$ for some $k$. Each process can read only its own state and the state of its **left neighbor** (the predecessor on the ring). $P_0$ is special.

**Rules.**

- **Bottom process $P_0$:** privileged when $V_0 = V_{n-1}$. When it executes, it sets
  $$V_0 \leftarrow (V_0 + 1) \bmod k.$$
- **Other processes $P_i$ ($i \neq 0$):** privileged when $V_i \neq V_{i-1}$. When executed, it sets
  $$V_i \leftarrow V_{i-1}.$$

**Legal state.** Exactly one process is privileged. Equivalently: there is exactly one "boundary" on the ring where the value differs from the predecessor (or, if all values are equal, only $P_0$ is privileged).

**Required.** $k \geq n$. (In fact, the lecture uses $k \geq n$; it is known that $k = n$ suffices, $k < n$ does not in general.)

### 3.1 Why "Rotating Privilege"?

In a legal state, the unique privilege moves around the ring. If all $V_i$ are equal, $P_0$ is privileged; it bumps $V_0$ so $V_0 \neq V_{n-1}$. Now $P_1$'s guard $V_1 \neq V_0$ becomes true, so $P_1$ becomes privileged and copies $V_0$ into $V_1$. Then $P_2$, then $P_3$, $\ldots$, then $P_{n-1}$ — each "absorbs" the new value. Once $P_{n-1}$ has copied, $V_{n-1} = V_0$ again and $P_0$ becomes privileged. The token has gone around once.

This is exactly mutual exclusion via a circulating token, and it is self-stabilizing.

### 3.2 Correctness Proof — Sketch with All Lemmas

Let $L$ denote the set of legal configurations (exactly one privileged process). We must show **convergence** (any execution reaches $L$) and **closure** ($L$ is closed under the rules).

**Closure** is easy. In a legal state where, say, only $P_i$ is privileged ($i \neq 0$), executing $P_i$ sets $V_i \leftarrow V_{i-1}$. After this, $V_i = V_{i-1}$ so $P_i$ is no longer privileged; on the other hand $V_{i+1}$ may now differ from $V_i$ (it didn't before because in the legal state $V_{i+1}$ was equal to the *old* $V_i$). So $P_{i+1}$ becomes privileged. Exactly one privilege exists. The case for $P_0$ is symmetric.

**Convergence.** Six lemmas. Throughout, "value $v$" means an element of $\{0, \ldots, k-1\}$. Define the **multi-set of values** $M = \{V_0, V_1, \ldots, V_{n-1}\}$.

**Lemma 1.** *In any execution, $P_0$ executes infinitely often* (under the central-daemon weak-fairness assumption: an enabled process is eventually selected).

*Proof idea.* If $P_0$ is enabled, fairness eventually fires it. If $P_0$ is not enabled, then $V_0 = V_{n-1}$. Consider the chain of guards $V_i \neq V_{i-1}$: the configuration is not all-equal only if some $i \neq 0$ is privileged; firing such $P_i$ sets $V_i = V_{i-1}$ and never creates a new boundary "above" $i$. Eventually all boundaries are pushed off the high end and we reach the all-equal configuration, which makes $P_0$ privileged.

**Lemma 2.** *There is at least one privileged process in every configuration.*

*Proof.* Suppose not. Then $V_0 \neq V_{n-1}$ (else $P_0$ is privileged) and for all $i \neq 0$, $V_i = V_{i-1}$. Chaining these equalities: $V_0 = V_1 = V_2 = \cdots = V_{n-1}$, contradicting $V_0 \neq V_{n-1}$. $\square$

**Lemma 3.** *The number of privileged processes is bounded above by $n$ and is monotonically non-increasing along an execution segment in which $P_0$ does not move.*

*Proof.* When $P_i$ ($i \neq 0$) executes, $V_i \leftarrow V_{i-1}$. The guard $V_i \neq V_{i-1}$ becomes false (so $P_i$ loses its privilege). The only guard that can newly become *true* is $V_{i+1} \neq V_i$ (because we changed $V_i$). So we lose exactly one privilege and gain at most one. The total count does not increase. $\square$

**Lemma 4.** *Let $v^* = V_0$ at some moment. As long as $P_0$ does not move again, no other process $P_i$ ever takes a value outside the values currently present in the ring; in fact only $P_0$ ever introduces "fresh" values.*

*Proof.* Non-zero processes only copy values from their predecessor; they cannot create a value not already present. Only $P_0$ produces a new value via $V_0 \leftarrow (V_0 + 1) \bmod k$. $\square$

**Lemma 5.** *There exists $v \in \{0, \ldots, k-1\}$ such that infinitely often the configuration contains no $V_i$ equal to $v$.*

*Proof.* By Lemma 1 $P_0$ executes infinitely often, cycling $V_0$ through all $k$ values. There are at most $n$ values present in any configuration and $k \geq n$, but more importantly: pick any execution suffix; consider any value $v$ that $V_0$ is about to take. Just *before* $P_0$ assigns $V_0 \leftarrow v$, $V_0 \neq v$. By tracking the propagation, one shows that there is a moment when $v$ does not appear anywhere in the ring (the lecture's argument: the value $v$ is "consumed" by being overwritten as the previous wave propagates around). $\square$

**Lemma 6.** *Eventually all $V_i$ become equal.*

*Proof sketch.* By Lemma 5 there is a moment when some value $v$ is absent. After that moment, $P_0$ will eventually set $V_0 \leftarrow v$ (since $P_0$ cycles through all values). At the moment $P_0$ assigns $V_0 = v$, this is a value that no $P_i$ holds. The wave then propagates: $P_1$ adopts $v$, then $P_2$, $\ldots$, then $P_{n-1}$. Crucially, while this wave is propagating, no other $P_j$ can introduce $v$ (only $P_0$ creates new values, and $P_0$ won't move until $V_0 = V_{n-1}$, which only happens once the wave has completed). When the wave finishes, $V_0 = V_1 = \cdots = V_{n-1} = v$. $\square$

**Theorem (Dijkstra).** *The algorithm is self-stabilizing: from any initial configuration, it reaches a legal configuration in finite time, and legal configurations are closed under the rules.*

*Proof.* By Lemma 6 the system reaches an all-equal configuration $V_0 = \cdots = V_{n-1}$. In that configuration only $P_0$ is privileged (one privilege — legal). Closure was shown earlier. $\square$

> Detail for the exam: the bound $k \geq n$ is needed exactly so that $P_0$ can produce a value not currently in the ring (Lemma 5). With $k < n$, by pigeonhole every value may be present and Lemma 5 fails.

---

## 4. Self-Stabilizing Spanning Tree

**Problem.** Given a connected undirected graph $G = (V, E)$ with a distinguished root $r$, build a BFS spanning tree rooted at $r$, and self-stabilize: from any garbage initial state of the variables, converge to a legal BFS tree.

**Variables (per process $i$).**

- $\mathit{parent}_i$: the id of one of $i$'s neighbors (or $\bot$ for the root).
- $\mathit{dist}_i$: a non-negative integer, intended to be the BFS distance from $r$.

**Root rules.** $r$ permanently sets $\mathit{parent}_r = \bot$ and $\mathit{dist}_r = 0$. (It overwrites these every step if necessary.)

**Non-root rules.** Each non-root $i$ repeatedly does:
$$\mathit{dist}_i \leftarrow \min_{j \in N(i)} (\mathit{dist}_j + 1), \qquad \mathit{parent}_i \leftarrow \arg\min_{j \in N(i)} (\mathit{dist}_j + 1).$$

(Ties broken by, e.g., smallest neighbor id — irrelevant to correctness.)

**Legal state.** For every $i$, $\mathit{dist}_i$ equals the true BFS distance from $r$ to $i$, and $\mathit{parent}_i$ is a neighbor with $\mathit{dist}_{\mathit{parent}_i} = \mathit{dist}_i - 1$.

### 4.1 Correctness Proof

Let $A_i$ denote the true BFS distance from $r$ to $i$ in $G$. Let $H = \max_i A_i$ be the *height* of the BFS tree (eccentricity of $r$).

We prove convergence in **phases**. Define phase $p$ to be a maximal segment of execution in which every process has been activated at least once since the start of the phase. (Equivalently: phase $p$ ends as soon as every process has executed in this round; the next phase starts immediately.)

**Claim 1.** *After phase 1, $\mathit{dist}_r = 0$.*

*Proof.* Phase 1 includes one activation of $r$, which sets $\mathit{dist}_r = 0$. The root never changes thereafter. $\square$

**Claim 2.** *For all $i$ with $A_i = 1$ (i.e., $i$ is a neighbor of $r$), after phase 2 we have $\mathit{dist}_i = 1$ and $\mathit{parent}_i = r$.*

*Proof.* By Claim 1, throughout phase 2, $\mathit{dist}_r = 0$ permanently. When $i$ executes during phase 2, it computes $\min_j(\mathit{dist}_j + 1)$ over neighbors. One neighbor is $r$ with $\mathit{dist}_r + 1 = 1$. No neighbor can give a value below 1 (distances are non-negative; even if some neighbor's $\mathit{dist}$ is garbage 0, then garbage $+ 1 = 1$ ties; ties don't hurt — but we must rule out garbage *negative* values, which is why we restrict $\mathit{dist}_j \geq 0$). So $\mathit{dist}_i$ becomes at most 1. It cannot become less than 1 because all neighbors have $\mathit{dist} \geq 0$, so $\min + 1 \geq 1$. Hence $\mathit{dist}_i = 1$ exactly. $\square$

**Claim 3 (induction).** *For each level $\ell = 0, 1, \ldots, H$, after phase $\ell + 1$, every process $i$ with $A_i = \ell$ has $\mathit{dist}_i = \ell$ and $\mathit{parent}_i$ is a true BFS parent.*

*Proof by induction on $\ell$.*

- *Base.* $\ell = 0$: by Claim 1.
- *Step.* Assume true for $\ell$. Consider $i$ with $A_i = \ell + 1$. By definition of BFS distance, $i$ has at least one neighbor $j$ with $A_j = \ell$, and **no** neighbor $j$ has $A_j < \ell$ (else $A_i \leq \ell < \ell + 1$).

  By the inductive hypothesis, throughout phase $\ell + 2$, every neighbor $j$ with $A_j \leq \ell$ has the correct $\mathit{dist}_j = A_j$, and these values do not change (because once correct, they remain a fixed point: a process at level $\leq \ell$ already sees a neighbor at level $\leq \ell - 1$ that gives $\min + 1 = A_j$).

  When $i$ executes during phase $\ell + 2$, it computes $\min_{j \in N(i)}(\mathit{dist}_j + 1)$. Among neighbors with $A_j \leq \ell$, the minimum of $\mathit{dist}_j + 1$ is $\ell + 1$ (achieved by some $j$ with $A_j = \ell$, and no smaller because no neighbor has $A_j < \ell$). Among neighbors with $A_j > \ell$, their $\mathit{dist}_j$ may be garbage, but it cannot be less than 0, so $\mathit{dist}_j + 1 \geq 1$. We need to ensure none of these garbage values is less than $\ell + 1$ — and this is exactly where the bound matters.

  To handle the garbage case rigorously: we use the fact that *once* every level-$\ell$ process is fixed, every level-$(\ell+1)$ process *eventually* sees the value $\ell + 1$ and locks on. Even if a "bad" neighbor (a non-tree neighbor at higher true level) currently exhibits a garbage low value, the level-$\ell$ neighbor still gives $\ell + 1$, so $\mathit{dist}_i$ becomes $\min(\text{garbage}, \ell+1) \leq \ell + 1$. The lecture handles the symmetric "garbage too low" worry by noting that the garbage neighbor itself, once activated in a later phase, will be corrected upward — so the system might temporarily have $\mathit{dist}_i$ smaller than $A_i$, but those incorrect-low values get *increased* as their false sources get corrected. After at most $H + 1$ phases, all distances equal the BFS distances. $\square$

**Theorem.** *After at most $H + 1$ phases, every process $i$ has $\mathit{dist}_i = A_i$, and $\mathit{parent}$ pointers form a BFS spanning tree rooted at $r$.*

*Proof.* By Claim 3 with $\ell = H$. Closure: a configuration with all $\mathit{dist}_i = A_i$ is a fixed point of the rules. $\square$

> Exam-relevant detail: the bound is $H + 1$ phases, where $H$ is the *eccentricity of the root*, which can be as large as the diameter of $G$. Phases here are defined w.r.t. a *round-robin* scheduler; under a fully adversarial daemon the bound becomes a function of activations, not phases.

---

## 5. Why Self-Stabilization is Powerful

- **No assumptions on the initial state.** Equivalent to tolerating any finite burst of transient faults: after the burst, the system self-heals.
- **Composability.** Self-stabilizing layers can be stacked: a self-stabilizing routing layer beneath a self-stabilizing application layer means the whole stack is self-stabilizing (with care about fairness).
- **No global reset.** Unlike "checkpoint and roll back," self-stabilization recovers in place.

**Limitations.**

- Convergence time can be large (here $\Theta(H)$ phases for the spanning tree, $\Theta(n^2)$ steps in the worst case for Dijkstra's ring).
- Requires unbounded or sufficiently large state space (the $k \geq n$ requirement).
- Pure self-stabilization tolerates only *transient* faults; permanent crashes need additional mechanisms.

---

## 6. Quick Reference Cheat-Sheet

| Concept | Definition |
| --- | --- |
| Self-stabilizing | From **any** state, reaches legal state in finite time, and stays. |
| Convergence | Any execution eventually enters $L$. |
| Closure | $L$ is closed under transitions. |
| Privileged process | A process whose local guard is enabled. |
| Legal state (Dijkstra ring) | Exactly one privileged process. |
| Dijkstra ring requirement | $k \geq n$ where $V_i \in \{0,\ldots,k-1\}$. |
| Spanning-tree convergence | $\leq H+1$ phases, $H$ = eccentricity of root. |

**Six lemmas of Dijkstra's ring.** (1) $P_0$ moves infinitely often. (2) $\geq 1$ process is always privileged. (3) Privilege count is $\leq n$ and non-increasing between $P_0$ moves. (4) Only $P_0$ creates fresh values. (5) Some value is infinitely often absent. (6) Eventually all $V_i$ are equal — then exactly one privilege exists.
