# CS4231 Lecture 5: Global Snapshot

**Instructor:** YU Haifeng

> Welcome to the study notes for Lecture 5. This lecture tackles an important question in distributed systems: how do you take a "picture" of the entire system when there is no shared clock? We will build up from the motivation, formalize what a consistent snapshot means, and then learn the elegant Chandy-Lamport protocol that actually captures one. These notes follow the lecture closely -- no extra content has been added.

---

## Quick Review of Last Lecture

Before we dive in, recall what we covered in "Models and Clocks":

- **Logical clock:** If event A happened before event B, then A gets a smaller clock value. (One direction only -- the converse is not guaranteed.)
- **Vector clock:** Happened-before holds if and only if the vector clock value is smaller. (Both directions -- this is stronger.)
- **Matrix clock:** Gives each process knowledge about what other processes know about the system.

These tools for reasoning about "time" in a distributed system set the stage for today's topic.

---

## Roadmap

This lecture covers **Global Snapshot** -- capturing the state of a distributed computation. We will work through three parts:

1. **What is our goal?** -- Why do we need global snapshots, and why is it hard?
2. **Formalizing consistent global snapshot** -- What does "consistent" actually mean, mathematically?
3. **The Chandy-Lamport Protocol** -- A practical protocol to capture a consistent global snapshot.

---

## 1. Motivation: Why Do We Need Global Snapshots?

### The Goal

We want to take a "picture" of the entire global computation at a single point in time. Specifically, this means:

- A snapshot of the **local states** on all *n* processes, **together with** all messages currently in transit ("on the fly"), taken **at exactly the same time**.

> **Note:** The textbook uses both "global state" and "global snapshot" -- we will stick to "global snapshot."

### Why Is This Useful?

- **Debugging** distributed systems (inspect what the system looked like at some point)
- **Backup/check-pointing** (save a recovery point so you can restart after failures)
- **Computing global predicates** (e.g., "How much total currency exists in the country?" when money constantly flows between people)

### The Problem: We Do Not Have Perfect Physical Time

If every process had access to a perfectly accurate physical clock, this would be trivial:

- All processes record their local state at, say, 1:00:00pm.
- We always get a correct total (e.g., $200 between two users, regardless of when money was transferred).

**But we do not have perfectly accurate physical time.** Processes may record states at slightly different times due to clock drift. How much inaccuracy can we tolerate? The clock inaccuracy needs to be below the **minimum message propagation delay** -- and that is very hard to achieve in practice.

Think of it this way: if user1 records their state a little too late and user2 records a little too early, the same $50 might be counted twice (once in user1's account and once "on the wire"), giving a total of $250 instead of $200. That is clearly wrong.

---

## 2. Weakened Goal: Consistent Global Snapshot

Since taking an exact simultaneous snapshot is impractical, we **weaken the goal**:

> Instead of a snapshot taken at exactly the same time, we aim for a snapshot of local states such that the global snapshot **could have happened sometime in the past**.

Let's unpack what this means:

- **"In the past":** We do not know exactly when it occurred.
- **"Could have happened":** The user cannot tell the difference from a "real" simultaneous snapshot, but in reality it may not have actually happened at any single point in time.

Why does this matter? Perhaps surprisingly, such a weaker goal is still perfectly useful for debugging, backup, and computing global predicates. If the snapshot is one that *could have* occurred, then any global predicate computed from it is a value the system *could have* had.

---

## 3. Formalizing Consistent Global Snapshot

Now let's make the notion of "consistent" precise. We need to revisit a key concept first.

### Review: The "Happened-Before" Relation

This is a **partial order** among events (local computation, send, receive), defined by three rules:

1. **Process order:** Events on the same process are ordered by their occurrence.
2. **Send-receive order:** A send event happens before the corresponding receive event.
3. **Transitivity:** If A -> B and B -> C, then A -> C.

### Consistent vs. Inconsistent Cuts

Imagine drawing a "cut" line across all the process timelines, dividing events into "before the snapshot" and "after the snapshot."

- **Consistent cut (green):** This cut *could have happened* in the past -- if processes had simply made progress faster or slower, the system would have passed through exactly this state.
- **Inconsistent cut (red):** This cut could **never** have happened in the past. It violates causality -- for example, a receive event is included in the snapshot but the corresponding send event is not. That is like saying "I received a letter, but nobody ever sent it."

### Formal Definitions

**Global snapshot:**
- A set of events *S* such that: if event *e2* is in *S* and *e1* comes before *e2* in **process order**, then *e1* must also be in *S*.
- In other words, on each process, *S* includes a **prefix** of that process's events. You cannot skip events on a single process.

**Consistent global snapshot:**
- A global snapshot *S* such that: if *e2* is in *S* and *e1* comes before *e2* in **send-receive order**, then *e1* must also be in *S*.
- Think of it this way: you cannot have a receive event in *S* without the corresponding send event also being in *S*. No "effect without cause."

> **Important note:** We do NOT need to explicitly check transitive relations. Checking only process order and send-receive order is sufficient. Why? Because the closure under process order (taking prefixes on each process) combined with the send-receive check automatically handles transitivity.

---

## 4. Existence of Consistent Global Snapshots

It is a good habit to prove that something we have just defined actually exists. (Otherwise, we might be chasing something impossible!) This proof also leads to an important characterization.

### Theorem

For any positive integer *m*, there exists a consistent global snapshot *S* such that on any given process with ordered events e1, e2, ...:
- e_i is in *S* for i <= m
- e_i is NOT in *S* for i > m

In other words, you can draw the cut at any point on any process, and there will always be a way to extend it to a consistent global snapshot.

### Proof Intuition

Let's see why this works. Suppose on process2 we want the first *m* events (call them "blue") to be in *S* and the remaining events ("red") to not be in *S*:

1. Find **all events** across all processes that **happened before** any of the blue events.
2. Include them in *S*.
3. **Claim:** *S* must be a consistent global snapshot. (The key insight is that by construction, we have included everything that causally precedes the blue events, so no send-receive violation can occur.)

### Characterization: Three Kinds of Events

Given the blue/red boundary on one process, every event in the entire system falls into exactly one of three categories:

- **Must be in S:** Events that happened before any blue event. (Leaving them out would violate consistency.)
- **Cannot be in S:** Events that happened after any red event. (Including them would violate the prefix property on their own process or create a causality violation.)
- **May or may not be in S:** All other events -- these are concurrent with the boundary.

This gives rise to a **smallest S** (only the "must be" events) and a **largest S** (everything except the "cannot be" events). Any valid *S* lies between these two extremes.

---

## 5. The Chandy-Lamport Protocol

Now we get to the practical part: how do we actually *capture* a consistent global snapshot in a running distributed system?

### Communication Model Assumptions

The Chandy-Lamport protocol requires the following:

- **No message loss** -- every sent message is eventually received.
- Communication channels are **unidirectional** (one-way pipes).
- **FIFO delivery** on each channel -- messages arrive in the order they were sent.
  - This can be ensured by: each process maintains a message number counter per channel and stamps each message; the receiver delivers messages in order of these numbers.

### Protocol Intuition

Let's build up the key insight step by step.

Suppose process1 takes its snapshot, and then sends an application message M to process2. For the global snapshot to be consistent, process2 **must** take its snapshot **before** delivering M. Why? Because:
- The send of M happened **after** process1's snapshot (so the send is NOT in process1's snapshot).
- If process2 delivers M before taking its snapshot, the receive of M would be in process2's snapshot.
- That means the snapshot includes a receive without the corresponding send -- inconsistent!

**The key to making a consistent global snapshot is to avoid this scenario.**

**Solution:** Use a special **Marker message**. After taking its snapshot, process1 sends a Marker to process2 *before* any subsequent application messages. Since the channel is FIFO, the Marker arrives at process2 before any post-snapshot application messages from process1. When process2 receives the Marker, it knows it must take its own snapshot immediately (if it has not already done so).

### Part 1: Taking Local Snapshots

Each process is in one of two states:
- **White:** Has NOT yet taken its local snapshot.
- **Red:** HAS taken its local snapshot.

**Protocol steps:**

1. A single process **initiates** the protocol by turning itself from White to Red (it takes its local snapshot).
2. As soon as a process turns Red, it immediately sends **Marker messages** to all other processes.
3. When a White process receives a Marker, it turns Red (takes its own local snapshot) and sends Markers to all other processes.
4. If a Red process receives a Marker, it simply ignores the snapshot trigger (it has already taken its snapshot).

**Message complexity:** Every process sends a Marker to every other process, so there are **n * (n - 1)** Marker messages in total.

### Part 2: Capturing Messages "On the Fly"

A complete global snapshot must also capture messages that are **in transit** at the time of the snapshot. Let's see why this matters and how it works.

**Four cases for an application message M:**

| Sent (relative to sender's snapshot) | Received (relative to receiver's snapshot) | Status |
|---|---|---|
| Before | Before | Not on-the-fly (already processed by both sides) |
| After | After | Not on-the-fly (neither side has recorded it) |
| After | Before | **Impossible!** (The Marker arrives before any post-snapshot message due to FIFO, forcing the receiver to snapshot first.) |
| Before | After | **On-the-fly** -- must be captured |

Think of it this way: the "sent after, received before" case is exactly the dangerous inconsistency we discussed earlier, and the Marker + FIFO mechanism makes it impossible. The only remaining on-the-fly case is when M was sent *before* the sender's snapshot but received *after* the receiver's snapshot.

**How to capture on-the-fly messages:**

Each process records all application messages received in the window between:
1. Taking its own local snapshot (turning Red), and
2. Receiving the Marker from the sender of that message.

These are exactly the messages that are "on the fly."

**Why this works:** Due to FIFO ordering, the Marker from process1 arrives at process2 after all messages that process1 sent before its snapshot. So any message from process1 that process2 receives between its own snapshot and the Marker from process1 must have been sent before process1's snapshot -- exactly the on-the-fly messages we need to capture.

---

## Summary Table

| Concept | Description |
|---|---|
| Global Snapshot Goal | Capture a picture of the distributed computation that *could have happened* |
| Consistent Global Snapshot | No receive event in the snapshot without its corresponding send event |
| Chandy-Lamport Protocol | Uses Marker messages + FIFO channels to capture consistent snapshots |
| Communication Model | No message loss, unidirectional channels, FIFO delivery |
| Message Complexity | n * (n - 1) Marker messages |

---

## Key Takeaways for the Exam

1. **Consistent global snapshot** = the snapshot "could have happened" in the past. The user cannot tell the difference from a real simultaneous snapshot.
2. **Formal condition:** A global snapshot *S* is consistent if: (a) it includes a prefix of events on each process (process order), and (b) if a receive event is in *S*, the corresponding send must also be in *S* (send-receive order).
3. **Existence:** For any cut point on any process, a consistent global snapshot exists. Events split into "must be in S," "cannot be in S," and "may or may not be in S."
4. **Chandy-Lamport requires FIFO channels** -- this is what prevents the "sent after, received before" case that would break consistency.
5. **Marker messages** serve as a barrier: they force a process to take its local snapshot before processing any post-snapshot messages from the sender.
6. **On-the-fly messages** are captured by recording all messages received between the local snapshot and the Marker arrival from each sender.

---

## Homework Questions

1. **Using Lamport's logical clock for consistent global snapshot:** Given the set of ALL events, how do you use Lamport's logical clock to compute a consistent global snapshot? (You should assume you are given the set of all events. Prove that the global snapshot you get is consistent.)

2. **Intersection and union of consistent global snapshots:** Prove that if G and H are both consistent global snapshots, then G intersect H and G union H are also consistent global snapshots.
