# Lecture 02: Synchronization Primitives

> **What this covers:** This lecture moves beyond the busy-waiting solutions from Lecture 01 and introduces OS-level synchronization primitives -- semaphores and monitors -- that let processes block instead of spinning. You will learn the precise semantics of each, see how they are used to solve classic synchronization problems (Dining Philosophers, Producer-Consumer, Reader-Writer), and understand the critical difference between Hoare-style and Java-style monitors. Pay special attention to the `notify()` vs `V()` distinction and the `if` vs `while` question for monitors -- these are common exam topics.

---

## 1. The Busy Wait Problem

**Recap from Lecture 01:** All the software solutions (Peterson's, Bakery) and the hardware solution (TestAndSet) use **busy waiting** -- the process sits in a `while` loop, spinning until the condition becomes true.

**Why is this a problem?** Busy waiting wastes CPU cycles. The process is doing nothing useful, but it is consuming processor time that could be given to other processes that actually have work to do.

**What we want instead:** When a process cannot enter the critical section, it should **release the CPU** and go to sleep. When the CS becomes available, it should be woken up. This requires **OS support**.

**The solution:** **Synchronization primitives** -- OS-level APIs that the program can call. The two main primitives covered in this course are:

- **Semaphores** -- lower level, more flexible, easier to misuse
- **Monitors** -- higher level, easier to use correctly, what Java provides

---

## 2. Semaphores

### Internal Structure

Think of a semaphore as a small object with two parts:

- A boolean **value** (initially `true`) -- indicates whether the resource is available
- A **queue** of blocked processes (initially empty) -- processes waiting for the resource

### Operations

A semaphore provides two atomic operations, traditionally called **P()** and **V()**:

**P() -- "wait" / "acquire":**
```
P():
    if (value == false) {
        add myself to queue and block;
    }
    value = false;
```
- The entire operation executes **atomically** (e.g., implemented via disabled interrupts internally)
- If the process blocks, the CPU context switches to another process -- no busy waiting!

**V() -- "signal" / "release":**
```
V():
    value = true;
    if (queue is not empty) {
        wake up one arbitrary process on the queue;
    }
```
- Also executed **atomically**
- **V() always sets value to true**, even if no one is waiting -- this is an important detail (we will contrast this with `notify()` later)

### Key Semantics

There are a few details worth memorizing:

1. Exactly **one** process is woken up by V()
2. The process chosen to be woken up is **arbitrary** (some implementations use FIFO -- always check the API semantics for the system you are using)
3. V() always sets `value = true`, regardless of whether anyone was waiting

### Using Semaphore for Mutual Exclusion

With semaphores, implementing mutual exclusion becomes trivially simple:

```
RequestCS() { P(); }
ReleaseCS() { V(); }
```

The big advantage over Peterson's, Bakery, etc.: **no busy waiting**. Blocked processes yield the CPU and go to sleep.

---

## 3. Dining Philosophers Problem (Dijkstra '65)

**Why does this matter?** This is a classic problem that illustrates how deadlock can arise even with correct mutual exclusion. It shows that having individual locks on resources is not always enough -- you also need to think about the order in which resources are acquired.

### Setup

- 5 philosophers sit around a circular table, with 5 chopsticks (one between each pair of neighbors)
- Each philosopher alternates between thinking and eating
- To eat, a philosopher needs **both** their left and right chopstick
- Each chopstick is modeled as a semaphore: `chopstick[1..5]`

### Naive Solution

```
Philosopher i:
while (true) {
    // think for a while
    chopstick[i].P();           // pick up left chopstick
    chopstick[(i+1) % 5].P();  // pick up right chopstick
        // eat (critical section)
    chopstick[i].V();           // put down left chopstick
    chopstick[(i+1) % 5].V();  // put down right chopstick
}
```

### The Deadlock Problem

This solution has a fatal flaw. If all 5 philosophers simultaneously pick up their **left** chopstick, every philosopher is holding one chopstick and waiting for the one to their right -- which is held by their neighbor. This creates a **circular wait**, which means **deadlock**.

Here is the worst-case scenario in detail:

| Philosopher 1 | Philosopher 2 | Philosopher 3 | Philosopher 4 | Philosopher 5 |
|---|---|---|---|---|
| cstick[1].P() | cstick[2].P() | cstick[3].P() | cstick[4].P() | cstick[5].P() |
| cstick[2].P() | cstick[3].P() | cstick[4].P() | cstick[5].P() | cstick[1].P() |

All 5 successfully pick up their left chopstick (row 1). Then all 5 try to pick up their right chopstick (row 2) and block -- everyone is waiting for everyone else. Deadlock!

### Avoiding Deadlock: Break the Cycle

**Solution:** Impose a **total ordering** on the chopsticks, and require that each philosopher always picks up the lower-numbered chopstick first. In practice, this means we make one philosopher (e.g., philosopher 5) pick up chopsticks in reverse order:

| Philosopher 1 | Philosopher 2 | Philosopher 3 | Philosopher 4 | Philosopher 5 |
|---|---|---|---|---|
| cstick[1].P() | cstick[2].P() | cstick[3].P() | cstick[4].P() | **cstick[1].P()** |
| cstick[2].P() | cstick[3].P() | cstick[4].P() | cstick[5].P() | **cstick[5].P()** |

Think of it this way: the circular dependency had the shape 1 -> 2 -> 3 -> 4 -> 5 -> 1. By making philosopher 5 grab chopstick 1 first (instead of 5 first), we break the cycle. Now philosopher 5 and philosopher 1 both compete for chopstick 1 first -- one of them will get it and eventually finish eating, freeing resources for others. No more circular wait, no more deadlock.

---

## 4. Monitors

### Why Monitors?

- Semaphores are **low-level** and error-prone -- it is easy to forget a V(), put P() and V() in the wrong order, etc.
- Monitors are **higher-level** and easier to use correctly
- Semaphores and monitors have **equivalent power** -- you can implement one using the other
- Java only provides monitors (via `synchronized`)

### Monitor Semantics

A **monitor object M** has three components:

1. A **monitor lock** -- at most one process can be inside the monitor at a time
2. A **monitor-queue** -- processes waiting to enter the monitor
3. A **wait-queue** -- processes that voluntarily gave up the monitor by calling `wait()`

In Java, **every object is a monitor**. You access the monitor using the `synchronized` keyword.

### Basic Usage (Java `synchronized`)

```java
synchronized (object) {
    // critical section -- only one thread can be in here at a time
    ....
}
```

What happens behind the scenes:

- **enterMonitor():** (at the opening `{`) If no one is in the monitor, enter. Otherwise, add myself to the **monitor-queue** and block.
- **exitMonitor():** (at the closing `}`) If the monitor-queue is not empty, pick one arbitrary process and unblock it.

### Three Special Methods (usable inside `synchronized`)

These methods let processes coordinate beyond just mutual exclusion:

| Method | What it does |
|---|---|
| `object.wait()` | Add myself to the **wait-queue**, **release the monitor lock**, and block -- all done **atomically** |
| `object.notify()` | If the wait-queue is not empty, pick **one arbitrary** process from the wait-queue and unblock it |
| `object.notifyAll()` | If the wait-queue is not empty, unblock **all** processes in the wait-queue |

Think of it this way: `wait()` is for when you are inside the monitor but realize that some condition you need is not yet true -- you voluntarily step aside and let others in. `notify()` is for when you have changed something that another process might be waiting for -- you tap one waiting process on the shoulder and say "check again."

### Important: `notify()` is NOT `V()`

This is a critical semantic difference that you must remember:

- **`V()`** always sets `value = true`, even if no process is waiting. The signal is "remembered."
- **`notify()`** is **lost** if no process is currently waiting. If you call `notify()` and the wait-queue is empty, nothing happens -- the notification just disappears.

In other words, V() has "memory" while notify() does not. This matters a lot when designing solutions -- you must be careful about when you call notify() and ensure that a process is actually waiting.

---

## 5. Two Kinds of Monitors

After `notify()` is called, there is a question: which process should continue running? The notifier is inside the monitor, and the waiter also wants to be inside the monitor. But only one process can be inside at a time. Different monitor implementations handle this differently.

### Hoare-style Monitor

- The **notified process (waiter) takes over immediately**
- The notifier is suspended and must wait until the waiter exits the monitor
- **Advantage:** The waiter can trust that the condition is still true right after waking up, because no other process ran between the notify and the waiter resuming
- You can use **`if`** to check the condition before `wait()`

```java
// Hoare-style: if is sufficient
synchronized (object) {
    if (x != 1) object.wait();
    assert(x == 1);  // guaranteed to be true
    x = 2;
}
```

Let's see why: Process 1 sets `x = 1` and calls `notify()`. In Hoare-style, Process 0 immediately takes over. No other process could have changed `x` between the notify and Process 0 resuming, so `x` is guaranteed to still be 1.

### Java-style Monitor (Mesa-style)

- The **notifier continues execution** -- it is not suspended
- The waiter is merely moved from the wait-queue to the **monitor-queue** (it still needs to re-acquire the lock)
- **Problem:** By the time the waiter finally gets the monitor lock and resumes, the condition may no longer be true -- another process might have changed things in the meantime
- **You MUST use a `while` loop** to re-check the condition after waking up

```java
// Java-style: while is REQUIRED!
synchronized (object) {
    while (x != 1)       // re-check after every wake-up
        object.wait();
    assert(x == 1);      // safe now -- we just checked
    x = 2;
}
```

Let's see why `while` is needed: Process 0 calls `wait()` because `x != 1`. Process 1 sets `x = 1` and calls `notify()`. But in Java-style, Process 1 continues running and might do more work. Meanwhile, Process 2 might enter the monitor and change `x` to something else. When Process 0 finally gets the monitor lock back, `x` might no longer be 1. The `while` loop catches this by re-checking.

### Key Comparison

| | Hoare-style | Java-style (Mesa) |
|---|---|---|
| After `notify()` | Waiter runs immediately | Notifier continues |
| Condition check | `if` is sufficient | Must use `while` |
| More popular? | No | Yes (Java uses this) |

**Practical advice from the lecture:** Synchronization code is extremely difficult (if not impossible) to debug. There are bugs that people fail to find after many years. You need to get it right the first time. Always check the semantics of the monitor in the language you are using.

---

## 6. Nested Monitors in Java

```java
synchronized (ObjA) {
    synchronized (ObjB) {
        ObjB.wait();   // releases lock on ObjB only, NOT ObjA
    }
}
```

**The trap:** In Java, `wait()` only releases the monitor lock on the **object being waited on** (`ObjB`). It does **not** release the outer monitor lock (`ObjA`).

**Why this causes deadlock:** Suppose another process wants to call `ObjB.notify()` to wake up the first process. But that process needs the lock on `ObjA` first (because `ObjB.notify()` is inside a `synchronized(ObjA)` block). The lock on `ObjA` is still held by the first process, which is blocked on `ObjB.wait()`. Neither process can make progress -- deadlock!

**Takeaway:** Be very careful with nested monitors. Different monitor implementations may handle nested monitors differently -- always check the specification.

---

## 7. Classic Synchronization Problems with Monitors

### Producer-Consumer Problem

**Setup:**
- A circular buffer of size n
- A **single producer** and a **single consumer** (the lecture emphasizes this is single-producer, single-consumer)
- Producer adds items to the end of the buffer if it is not full
- Consumer removes items from the head of the buffer if it is not empty
- Example: hard drive as producer and printer as consumer

**Solution using Java-style monitor:**

```java
object sharedBuffer;

void produce() {
    synchronized (sharedBuffer) {
        if (sharedBuffer is full)
            sharedBuffer.wait();
        add an item to sharedBuffer;
        if (sharedBuffer *was* empty)
            sharedBuffer.notify();
    }
}

void consume() {
    synchronized (sharedBuffer) {
        if (sharedBuffer is empty)
            sharedBuffer.wait();
        remove item from sharedBuffer;
        if (sharedBuffer *was* full)
            sharedBuffer.notify();
    }
}
```

**Why `if` works here (even though Java-style usually requires `while`):** This is a special case with only a single producer and a single consumer. Since there is only one process that could be waiting at any time, the notification cannot be "stolen" by another process. With multiple producers or consumers, you would need `while`.

**Important caveat about `notify()`:** Notice that we only call `notify()` when the state changes in a way the other side cares about (buffer transitions from full to not-full, or from empty to not-empty). This is because `notify()` is lost if no one is waiting -- very different from V(). If you called `notify()` every time, it would still be correct, but the conditional check makes the intent clearer and is the pattern shown in lecture.

### Reader-Writer Problem

**Setup:**
- Multiple readers and writers access a shared file
- **Writers need exclusive access** -- no other readers or writers can be present
- **Readers can access simultaneously** -- as long as no writer is active

In other words, reading is a shared activity (many can do it at once), but writing is exclusive (only one writer, and no readers).

**Solution using Java-style monitor:**

```java
int numReader, numWriter;
Object object;

void writeFile() {
    synchronized (object) {
        while (numReader > 0 || numWriter > 0)
            object.wait();
        numWriter = 1;
    }
    // write to file (outside the monitor -- so readers are not needlessly blocked from checking)
    synchronized (object) {
        numWriter = 0;
        object.notifyAll();   // wake ALL waiters (readers and writers)
    }
}

void readFile() {
    synchronized (object) {
        while (numWriter > 0)
            object.wait();
        numReader++;
    }
    // read from file
    synchronized (object) {
        numReader--;
        object.notify();      // only a writer could be waiting here
    }
}
```

**Why `notifyAll()` in the writer but `notify()` in the reader?**
- When a writer finishes, both readers and writers might be waiting. We use `notifyAll()` so that all waiting readers can proceed simultaneously (since readers do not block each other), and any waiting writer will re-check and wait if needed.
- When a reader finishes, the only process that could be blocked is a writer (waiting for `numReader == 0`). Other readers are never blocked by readers. So `notify()` suffices, and the lecture notes you can prove that it must be a writer who gets notified.

**Why `while` is used:** There can be multiple readers and writers, so after being woken up, the condition may no longer hold. For example, a writer wakes up but another writer got in first -- it must go back to waiting.

**Starvation problem:** Writers may starve if there is a continuous stream of readers. Since readers do not block each other, new readers can keep arriving and entering, and the writer never gets a chance. A starvation-free solution requires additional mechanisms (this was mentioned as a homework problem in the lecture).

---

## 8. Summary Table

| Topic | Key Points |
|---|---|
| **Busy waiting** | Wastes CPU; synchronization primitives (OS-level) solve this by blocking instead of spinning |
| **Semaphore** | Has a boolean value and a queue; P() blocks if value is false; V() wakes one process; both are atomic operations |
| **Dining Philosophers** | Naive semaphore solution can deadlock due to circular wait; fix by imposing a total ordering on resources |
| **Monitor** | Higher-level than semaphore; has a monitor lock, monitor-queue, and wait-queue; uses `wait()`, `notify()`, `notifyAll()` |
| **Hoare vs Java-style** | Hoare: waiter runs immediately after notify, so use `if`; Java: notifier continues, so you MUST use `while` |
| **`notify()` vs `V()`** | `notify()` is lost if no one is waiting; `V()` always sets value to true -- the signal has "memory" |
| **Nested monitors** | `wait()` only releases the innermost lock in Java -- can cause deadlock if outer lock is needed by another process |
| **Producer-Consumer** | Solved with monitor; notify only when state transitions matter; `if` works for single-producer single-consumer |
| **Reader-Writer** | Readers share, writers are exclusive; use `notifyAll()` when writer finishes, `notify()` when reader finishes; writers may starve with continuous readers |
