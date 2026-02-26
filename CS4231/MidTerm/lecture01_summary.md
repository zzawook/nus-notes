# Lecture 01: Mutual Exclusion Problem

> **What this covers:** This lecture introduces the fundamental problem of mutual exclusion in shared-memory systems. You will learn why concurrent access to shared variables is dangerous, see three failed attempts that each teach an important lesson, and then study two correct algorithms (Peterson's for 2 processes, Bakery for n processes) along with their correctness proofs. The lecture wraps up with hardware-level solutions. Understanding the three required properties (mutual exclusion, progress, no starvation) and being able to prove them is essential for the exam.

---

## 1. The Problem: Why Concurrent Access Is Dangerous

**Context:** We are working in **shared memory systems** -- think multi-processor computers or multi-threaded programs where multiple processes can read and write the same variables.

**Motivating example:** Suppose we have a shared variable `x` with initial value 0, and two processes both execute `x = x + 1`.

The key insight is that `x = x + 1` is **not atomic** -- it actually consists of 3 separate steps:

1. Read `x` into a local register
2. Increment the register
3. Write the register value back to `x`

**Correct interleaving (sequential):** P0 does all 3 steps, then P1 does all 3 steps. Result: `x = 2`. This is what we want.

**Incorrect interleaving (concurrent):** Both processes read `x = 0` before either writes back. Both increment to 1. Both write 1. Result: `x = 1` -- a lost update!

Think of it this way: the problem arises because the "read-modify-write" sequence can be interrupted. If P1 sneaks in between P0's read and P0's write, P1 is working with stale data.

---

## 2. Critical Section: The Solution Framework

The solution is to protect the shared operations with a **Critical Section** (also called **Critical Region**). The idea is to wrap the dangerous code with an entry protocol and an exit protocol:

```
RequestCS(int processId)    // entry protocol -- ask permission to enter
    // critical section code (e.g., read-increment-write)
ReleaseCS(int processId)    // exit protocol -- let others know you are done
```

In other words, we want to ensure that only one process at a time executes the critical section code. The challenge is: how do we implement `RequestCS` and `ReleaseCS` correctly?

---

## 3. Three Required Properties

Any correct solution must satisfy these three properties:

| Property | Definition | Intuition |
|---|---|---|
| **Mutual Exclusion** | No more than one process in the critical section at any time | The core safety requirement -- two processes must never be inside at the same time |
| **Progress** | If one or more processes want to enter and no one is currently in the CS, then one of them can eventually enter | The resource should be fully utilized -- if it is free and someone wants it, they should get it |
| **No Starvation** | If a process wants to enter, it eventually will enter | Every process gets a fair chance; no process waits forever |

**Important relationship:** No starvation implies progress (if every process eventually gets in, then certainly at least one will get in when the CS is free). But progress does NOT imply no starvation.

**Key assumptions to keep in mind:**
- We must always consider the **worst-case schedule** -- if there exists any interleaving that breaks a property, the algorithm is incorrect.
- We assume that if a process enters the critical section, it will **eventually exit**. (We do not worry about a process crashing inside the CS.)

---

## 4. Unsuccessful Attempts (Software Solutions)

These three failed attempts are very instructive. Each one fixes something but breaks something else. Understanding *why* each fails helps build intuition for what a correct solution needs.

### Attempt 1: Shared boolean `openDoor`

**Idea:** Use a single boolean flag like a door. Check if it is open, then close it behind you.

```
Shared boolean openDoor; // initially true

RequestCS(int processId) {
    while (openDoor == false) {};   // wait until door is open
    openDoor = false;               // close door behind me
}

ReleaseCS(int processId) {
    openDoor = true;                // open door to let others in
}
```

**What goes wrong:** Violates **mutual exclusion**. Both processes can read `openDoor == true` before either sets it to false. Then both "walk through the door" and enter the CS simultaneously.

Why does this matter? This attempt shows that checking a condition and then acting on it is not safe unless those two steps happen atomically. The gap between "read true" and "set false" is where the race condition lives.

### Attempt 2: Shared boolean array `wantCS[]`

**Idea:** Instead of a shared door, each process announces its intent first, then checks if the other wants in.

```
Shared boolean wantCS[0] = false, wantCS[1] = false;

// Process 0                        // Process 1
RequestCS(0) {                      RequestCS(1) {
    wantCS[0] = true;                   wantCS[1] = true;
    while (wantCS[1] == true) {};       while (wantCS[0] == true) {};
}                                   }

ReleaseCS(0) {                      ReleaseCS(1) {
    wantCS[0] = false;                  wantCS[1] = false;
}                                   }
```

**What goes wrong:** Violates **progress** (deadlock). If both processes set their flags to `true` before either checks the other's flag, both see the other wants in and spin forever. No one can enter even though the CS is free.

Why does this matter? This attempt shows that having each process "raise its hand" first can lead to a standoff. We need some way to break the symmetry.

### Attempt 3: Shared `turn` variable

**Idea:** Use a turn variable to strictly alternate access. If it is your turn, enter. When you leave, give the turn to the other.

```
Shared int turn = 0;

// Process 0                        // Process 1
RequestCS(0) {                      RequestCS(1) {
    while (turn == 1) {};               while (turn == 0) {};
}                                   }

ReleaseCS(0) {                      ReleaseCS(1) {
    turn = 1;                           turn = 0;
}                                   }
```

**What goes wrong:** Violates **no starvation** (and actually also violates progress in some scenarios). This requires strict alternation -- if P1 never wants to enter, P0 can never enter a second time because it is stuck waiting for P1 to set `turn = 0`. The slide also notes "there are other kinds of starvation" with this approach.

Why does this matter? Strict alternation is too rigid. A correct solution should allow a process to enter the CS multiple times in a row if the other process is not interested.

---

## 5. Peterson's Algorithm (Correct for 2 Processes)

Peterson's algorithm cleverly combines the ideas from Attempts 2 and 3: announce your intent (like Attempt 2) AND defer to the other process (like Attempt 3). The turn variable breaks the symmetry that caused deadlock in Attempt 2, while the intent flags prevent the starvation problem from Attempt 3.

```
Shared bool wantCS[0] = false, wantCS[1] = false;
Shared int turn = 0;

// Process 0                            // Process 1
RequestCS(0) {                          RequestCS(1) {
    wantCS[0] = true;                       wantCS[1] = true;
    turn = 1;                               turn = 0;
    while (wantCS[1] == true                while (wantCS[0] == true
           && turn == 1) {};                       && turn == 0) {};
}                                       }

ReleaseCS(0) {                          ReleaseCS(1) {
    wantCS[0] = false;                      wantCS[1] = false;
}                                       }
```

**Key idea:** Each process says "I want in" (`wantCS[i] = true`) and then **politely defers to the other** (`turn = other`). A process only stays blocked if the other process wants in AND it is the other's turn. If the other does not want in, or if it is your turn, you proceed.

Think of it this way: it is like two people approaching a door at the same time. Each says "after you!" (sets `turn` to the other). Whoever said "after you!" last is the one who actually has to wait -- the other gets to go first.

### Correctness Proofs

These proofs all use **proof by contradiction**. Let's walk through each one.

#### Mutual Exclusion

**Goal:** Show that P0 and P1 cannot both be in the CS at the same time.

**Proof:** Suppose, for the sake of contradiction, that both P0 and P1 are in the CS. The variable `turn` can only be 0 or 1, so let's consider both cases.

- **Case 1: `turn == 0`** -- Since `turn` is currently 0, P0 must have executed `turn = 1` *before* P1 executed `turn = 0` (because P1's write of `turn = 0` came last). This means by the time P1 checked its while-loop condition, P0 had already set `wantCS[0] = true` (because `wantCS[0] = true` comes before `turn = 1` in P0's code). So P1 sees `wantCS[0] == true` AND `turn == 0`. But that means P1's while-loop condition is true, so P1 should still be spinning -- contradiction with P1 being in the CS.
- **Case 2: `turn == 1`** -- Symmetric argument. P0's while-loop condition would be true, so P0 should be spinning -- contradiction.

#### Progress

**Goal:** Show that if both processes want to enter and no one is in the CS, at least one will get in.

**Proof:** Suppose both P0 and P1 are waiting (spinning in their while loops). Consider the value of `turn`:

- **Case 1: `turn == 0`** -- P0's while condition is `wantCS[1] == true && turn == 1`. Since `turn == 0`, the second part of the AND is false. So P0's whole condition is false, meaning P0 exits the loop and enters the CS.
- **Case 2: `turn == 1`** -- By symmetric reasoning, P1 enters the CS.

Either way, at least one process can enter. Both cannot wait forever -- contradiction.

#### No Starvation

**Goal:** Show that if P0 wants to enter, it will eventually get in (and symmetrically for P1).

**Proof:** Suppose P0 is waiting forever. For P0 to be stuck, its while condition must remain true, meaning `wantCS[1] == true` and `turn == 1`. Consider what P1 is doing:

- **Case 1: P1 is in the CS.** We assumed P1 will eventually exit, at which point it sets `wantCS[1] = false`. If P1 does not want to re-enter, P0's condition becomes false and P0 enters. If P1 does want to re-enter, it will execute `turn = 0` in its RequestCS, which makes P0's condition false. Either way, P0 gets in.
- **Case 2: P1 is also waiting.** This contradicts progress (which we just proved above).

So P0 cannot wait forever.

---

## 6. Lamport's Bakery Algorithm (Correct for n Processes)

**Why does this matter?** Peterson's algorithm only works for 2 processes. In practice, we often need to handle n processes. Lamport's Bakery Algorithm solves mutual exclusion for any number of processes.

**Analogy:** Think of a bakery with a take-a-number system. When you arrive, you take a number (ticket). You wait until everyone with a lower number has been served. If two people accidentally get the same number, the one with the lower ID goes first.

### Shared Variables

```
boolean choosing[i] = false;  // true while process i is picking a number
int number[i] = 0;            // ticket number for process i (0 = not interested)
```

### Utility Function

```
boolean Smaller(int number1, int id1, int number2, int id2) {
    if (number1 < number2) return true;
    if (number1 == number2) {
        if (id1 < id2) return true;
        else return false;
    }
    if (number1 > number2) return false;
}
```

This compares (number, id) pairs **lexicographically** -- first compare ticket numbers; if they are tied, break the tie by process ID. This guarantees a total ordering so there is never ambiguity about who goes first.

### Algorithm

```
RequestCS(int myid) {
    // Phase 1: Get a ticket number
    choosing[myid] = true;
    for (int j = 0; j < n; j++) {
        tmp = number[j];
        if (tmp > number[myid]) number[myid] = tmp;
    }
    number[myid]++;
    choosing[myid] = false;

    // Phase 2: Wait for everyone ahead of me
    for (int j = 0; j < n; j++) {
        while (choosing[j] == true);            // wait while j is choosing
        while (number[j] != 0 &&                // j wants to enter AND
               Smaller(number[j], j,            // j has a smaller ticket
                       number[myid], myid));
    }
}

ReleaseCS(int myid) {
    number[myid] = 0;
}
```

Let's walk through how this works:

1. **Phase 1 (get a ticket):** The process sets `choosing[myid] = true` to signal it is picking a number. It scans all other processes' numbers, takes the maximum, and adds 1. Then it sets `choosing[myid] = false`.

2. **Phase 2 (wait your turn):** The process checks every other process j. First, it waits for j to finish choosing (if j is currently picking a number). Then, it waits if j has a valid ticket (not 0) and j's ticket is smaller (meaning j should go first).

### Why the `choosing` flag matters

This is a subtle but critical detail. Without the `choosing` flag, a process could read another's number while it is being updated (specifically, between finding the max and incrementing). This could lead to two processes getting the same number in a way that breaks the ordering check, violating mutual exclusion.

In other words, the `choosing` flag ensures that when you read someone's number, you are reading a *finished* number, not one that is still being computed.

### Correctness Sketch

**Mutual Exclusion:** Suppose processes *i* and *k* are both in the CS, with `Smaller(number[i], i, number[k], k)` (i.e., *i* has the smaller ticket).

- Process *k* must have seen `number[i] == 0` when it checked process *i* in Phase 2 (otherwise *k* would have waited for *i*).
- Let T1 be the time when process *k* is at the `while (number[j] != 0 && ...)` check for j = *i*, and *k* sees `number[i] == 0`.
- Since `choosing[i]` was false when *k* passed the choosing check (at some time T2 < T1), process *i* either had not started choosing or had finished choosing.
    - **Case 1: *i* finished choosing before T2.** Then `number[i]` would already be set (non-zero). But *k* sees `number[i] == 0` at T1 > T2 -- contradiction.
    - **Case 2: *i* had not started choosing before T2.** Then *i* will pick a number after seeing *k*'s number, so `number[i]` will be larger than `number[k]`. This contradicts our assumption that `Smaller(number[i], i, number[k], k)`.

**Progress:** Consider any set of processes that want to enter the CS but none can make progress. Each process is guaranteed to eventually get a queue number (Phase 1 always terminates). Let process *i* be the one with the smallest (number, id) pair among those waiting. Process *i* cannot be blocked:
- At the `choosing` check: process *j* will eventually finish choosing.
- At the `number` check: impossible, since *i* has the smallest queue number.

So *i* enters the CS -- contradiction.

**No starvation:** Once a process picks a number, only finitely many processes have smaller numbers. Each of those will eventually finish. So the process will eventually enter.

---

## 7. Hardware Solutions

The software solutions above all rely on **busy waiting** (spinning in a loop), which wastes CPU cycles. Hardware provides some alternatives.

### Disabling Interrupts

**Idea:** Prevent context switches during the critical section by disabling interrupts. If no context switch can happen, no other process on the same processor can interleave.

**Limitation:** This **only works on uniprocessors**. On a multi-processor system, another processor can still access shared memory even if your processor's interrupts are disabled.

### Special Machine-Level Instructions: TestAndSet

**Idea:** Provide an instruction that reads and writes a variable **atomically** -- as one indivisible operation. This eliminates the race condition from Attempt 1.

```
boolean TestAndSet(Boolean openDoor, boolean newValue) {
    boolean tmp = openDoor.getValue();
    openDoor.setValue(newValue);
    return tmp;
}   // entire function executes ATOMICALLY
```

**Usage for mutual exclusion:**

```
Shared Boolean openDoor = true;

RequestCS(process_id) {
    while (TestAndSet(openDoor, false) == false) {};
    // loops until it successfully reads true and sets to false atomically
}

ReleaseCS(process_id) {
    openDoor.setValue(true);
}
```

Let's see why this works: the atomicity of TestAndSet means that reading the old value and setting the new value happen as one indivisible step. So unlike Attempt 1, it is impossible for two processes to both read `true` -- the first one to execute TestAndSet gets `true` and sets it to `false`; the second one sees `false` and spins.

**Caveat:** TestAndSet provides mutual exclusion and progress, but it does **not** guarantee no starvation. A process could be unlucky and keep losing the race to other processes indefinitely.

---

## 8. Summary Table

| Algorithm | Mutual Exclusion | Progress | No Starvation | # Processes |
|---|---|---|---|---|
| Attempt 1 (openDoor) | No | - | - | 2 |
| Attempt 2 (wantCS[]) | Yes | No (deadlock) | - | 2 |
| Attempt 3 (turn) | Yes | Yes | No | 2 |
| Peterson's | Yes | Yes | Yes | 2 |
| Bakery | Yes | Yes | Yes | n |
| TestAndSet | Yes | Yes | No (possible starvation) | n |

**Key takeaway:** Getting mutual exclusion right in software is hard. Each failed attempt teaches us something: Attempt 1 shows that checking and acting must be atomic; Attempt 2 shows that symmetric intent-signaling can deadlock; Attempt 3 shows that strict alternation starves processes. Peterson's combines the best of Attempts 2 and 3, and the Bakery algorithm generalizes to n processes using a ticket-based scheme.
