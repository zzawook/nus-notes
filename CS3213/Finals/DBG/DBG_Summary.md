# Debugging (DBG) — Comprehensive Summary

> **What this covers:** This module covers the systematic art and science of debugging software. You will learn why debugging matters (developers spend 35–50% of their time on it), the precise terminology (mistake → defect → fault → failure), and a range of techniques from manual (printf debugging, logging, rubber ducking) to semi-automated (scientific method, tracking origins) to fully automated (statistical fault localization / Tarantula, program slicing, delta debugging, git bisect). Each technique addresses a different question in the debugging process — "where might the bug be?", "what code could have influenced this variable?", "what is the minimal input that triggers the bug?", "which commit introduced the regression?" Understanding the formal definitions, algorithms, and worked examples for each technique is essential for the exam.

---

## 1. Why Debugging Matters

Software developers spend **35–50% of their time validating and debugging software**. The estimated cost of debugging, testing, and verification accounts for **50–75% of the total budget** of software development projects. Identifying systematic and effective approaches to debugging therefore greatly enhances software engineering productivity.

**When does debugging happen?** Debugging occurs after a failure is observed — typically after testing reveals unexpected behavior, a user files a bug report, or a system crashes in production. It is the process of finding and fixing the root cause of the failure.

---

## 2. Terminology: The Chain from Mistake to Failure

Understanding the precise meaning of each term is critical. The chain is:

**Mistake → Defect (Bug) → Fault → ... → Fault → Failure**

| Term | Definition | Example (using `removeHtmlMarkup`) |
|---|---|---|
| **Mistake** | A human act that produces an incorrect result — the programmer's error in reasoning or typing | The programmer forgot that `&&` has higher precedence than `\|\|` in Python |
| **Defect (Bug)** | The incorrect code in the program — the concrete manifestation of the mistake in the source code | The line `c == '"' or c == '\'' and tag` is missing parentheses |
| **Fault** | An incorrect intermediate program state caused by executing the defect — the point during execution where the internal state first diverges from what it should be | The variable `quote` is toggled to `True` even though the character is outside a tag, because the `and tag` only binds to the second comparison |
| **Failure** | An externally visible incorrect behavior — the output differs from what is expected | `removeHtmlMarkup('"<b>foo</b>"')` returns `"foo"` instead of `'"<b>foo</b>"'` |

**Key insight about the chain:** A single defect can cause multiple faults during execution, and multiple faults can propagate through program states before finally producing a visible failure. Debugging means working backwards from the failure through the chain of faulty states to identify the defect, and ultimately understanding the mistake that caused it.

### Mental Model: Program States

Think of a program's execution as a sequence of states:

```
Input ✔ → [State 1 ✔] → [State 2 ✘] → [State 3 ✘] → Output ✘
```

The input may be correct, but at some point a defect in the code causes the state to become faulty. From that point on, the faulty state may propagate through subsequent states until it produces a visible failure at the output. The challenge is that the first fault may occur millions of steps before the failure becomes visible.

---

## 3. How NOT to Debug

Before learning the right techniques, it is useful to understand common bad practices:

- **Guessing randomly** without forming hypotheses
- **Making changes without understanding** the bug first ("let me just try changing this and see if it works")
- **Not keeping track** of what you have already tried
- **Ignoring the evidence** — changing code that is unrelated to the observed failure

The alternative is to debug **systematically**, using the scientific method.

---

## 4. Printf Debugging and Logging

### 4.1 Printf Debugging

The simplest debugging technique: insert print statements to observe the program's state at various points during execution.

**Advantages:** Simple to use, language-agnostic, requires no special tools, works in any environment.

**Disadvantages:** Output can become confusing and overwhelming; print statements are often forgotten in the code after debugging; requires recompilation after each change; can alter timing-sensitive behavior (especially in concurrent programs).

### 4.2 Logging

A more structured alternative to printf debugging. Instead of ad-hoc print statements, use a logging framework with defined **severity levels**:

| Level | Purpose |
|---|---|
| **DEBUG** | Detailed diagnostic information for developers |
| **INFO** | General information about program execution |
| **WARN** | Potentially harmful situations that do not prevent execution |
| **ERROR** | Error events that might still allow the program to continue |
| **FATAL** | Severe error events that will lead to program termination |

Logging frameworks allow you to control which levels are active (e.g., only show WARN and above in production), write to different destinations (console, file, remote server), and include contextual information (timestamps, thread IDs, source locations).

### 4.3 Debuggers

Interactive debuggers (GDB, IDE debuggers, etc.) allow you to set breakpoints, step through code line by line, inspect variable values, and examine the call stack. They provide more precise control than printf debugging but require familiarity with the tool.

---

## 5. Rubber Ducking

**Rubber ducking** (or rubber duck debugging) is the practice of explaining the problem — step by step — to someone or something else (traditionally a rubber duck). The act of articulating the problem forces you to revisit your observations, re-examine your assumptions, and form new hypotheses.

Why it works: When you explain a problem aloud, you are forced to organize your thoughts sequentially and explicitly state things you might otherwise gloss over mentally. This process often reveals the faulty assumption or overlooked detail that is the key to finding the bug.

---

## 6. The Scientific Method Applied to Debugging

This is the core systematic approach to debugging taught in this module. The method has **5 steps**:

1. **Formulate a question** — Start with the observed failure and ask: "Why does this happen?"
2. **Come up with a hypothesis** — Based on the knowledge obtained while formulating the question, propose a possible explanation for the observed behavior.
3. **Formulate a prediction that can support or refute the hypothesis** — Determine the logical consequences of the hypothesis. Ideally, the prediction would distinguish the hypothesis from likely alternatives.
4. **Test the prediction** (and thus the hypothesis) in an experiment — If the prediction holds, confidence in the hypothesis increases; otherwise, it decreases.
5. **Repeat Steps 2–4** until there are no discrepancies between the hypothesis, predictions, and observations.

### 6.1 Worked Example: `removeHtmlMarkup`

This is the running example used throughout all debugging lectures. The function is supposed to strip HTML tags from a string while preserving quoted content:

```python
def removeHtmlMarkup(s):
    tag = False
    quote = False
    out = ""
    for c in s:
        if c == '<' and not quote:
            tag = True
        elif c == '>' and not quote:
            tag = False
        elif c == '"' or c == '\'' and tag:   # BUG IS HERE
            quote = not quote
        elif not tag:
            out = out + c
    return out
```

**The question:** `removeHtmlMarkup('"<b>foo</b>"')` returns `"foo"` instead of `'"<b>foo</b>"'`. Why are the double quotes missing from the output?

**Hypothesis 1:** "The bug is related to double quotes."
- **Prediction:** If we remove the double quotes from the input, the function should work correctly.
- **Test:** `removeHtmlMarkup('<b>foo</b>')` returns `"foo"` ✔ (correct).
- **Observation:** The hypothesis is supported — the bug is triggered by double quotes.

**Hypothesis 2:** "Double quotes are stripped from the output when they are outside tags."
- **Prediction:** If we add an assertion `assert not tag` inside the quote-toggling branch, it should trigger when quotes are toggled outside a tag.
- **Test:** The assertion fires — confirming that quotes are being toggled even when `tag` is `False`.

**Hypothesis 3 (Diagnosis):** "The condition `c == '"' or c == '\'' and tag` does not work as intended because of **operator precedence**. In Python, `and` binds tighter than `or`. So the expression is parsed as `c == '"' or (c == '\'' and tag)`. This means that for double-quote characters, the quote toggle happens unconditionally — regardless of whether we are inside a tag."

- **Fix:** Add parentheses: `(c == '"' or c == '\'') and tag`

### 6.2 Diagnosis: Causality and Incorrectness

A proper **diagnosis** must explain two things:

1. **Causality** — How the failure came to be. What is the chain of cause and effect from the defect to the failure?
2. **Incorrectness** — How to correct the code. What should the code look like instead?

Simply finding the line that is wrong is not enough. You need to understand *why* it is wrong and *how* to fix it correctly. A diagnosis that only explains one of these two aspects is incomplete.

---

## 7. Tracking Origins

**Tracking origins** is a debugging strategy that works backwards from the failure:

1. Start with a single faulty state *f* — the failure (the incorrect output or behavior).
2. Determine *f*'s **origins** — the parts of earlier states that could have caused the faulty state *f*.
3. For each of these origins *e*, determine whether they are faulty or not.
4. Trace back through the chain of faulty states until you have found the **defect** — the first point where the code itself is wrong.

This strategy is formalized by **program slicing** (Section 9), which systematically identifies which parts of the code could have influenced a given variable at a given point.

---

## 8. Statistical Fault Localization (Tarantula)

### 8.1 The Idea

Statistical fault localization (SFL) uses information from **test suite execution** to estimate which lines of code are most likely to contain a bug. The core insight: lines that are executed primarily in **failing tests** (and rarely in passing tests) are more suspicious than lines executed equally in both.

This is a **spectrum-based** technique — it uses the execution spectrum (which lines are covered by which tests) to compute suspiciousness scores.

### 8.2 The Tarantula Formula

For each line of code, compute:

$$\text{Suspiciousness}(\text{line}) = \frac{\dfrac{\text{failed}(\text{line})}{\text{total\_failed}}}{\dfrac{\text{failed}(\text{line})}{\text{total\_failed}} + \dfrac{\text{passed}(\text{line})}{\text{total\_passed}}}$$

where:
- `failed(line)` = number of failing tests that execute this line
- `passed(line)` = number of passing tests that execute this line
- `total_failed` = total number of failing tests
- `total_passed` = total number of passing tests

**Special case:** If the denominator is zero (the line is never executed), the suspiciousness is defined as **0**.

### 8.3 Interpreting the Score

| Suspiciousness | Meaning | Hue Color |
|---|---|---|
| **1.0** | Line is executed **only** in failing tests — highly suspicious | **Red** |
| **0.5** | Line is executed proportionally equally in passing and failing tests | **Yellow** |
| **0.0** | Line is executed **only** in passing tests — likely not the bug | **Green** |

The **hue** visualization maps suspiciousness scores to a color spectrum from green (safe) through yellow (medium) to red (suspicious), providing an intuitive visual guide for developers.

### 8.4 Worked Example: `removeHtmlMarkup`

Consider three test cases for `removeHtmlMarkup`:

| Test | Input | Expected | Actual | Result |
|---|---|---|---|---|
| T1 | `"abc"` | `"abc"` | `"abc"` | ✔ Pass |
| T2 | `"<b>abc</b>"` | `"abc"` | `"abc"` | ✔ Pass |
| T3 | `'"abc"'` | `'"abc"'` | `'abc'` | ✘ Fail |

For Line 10 (`quote = not quote`):

- `failed(10) = 1` (executed in T3), `total_failed = 1`
- `passed(10) = 0` (not executed in T1 or T2), `total_passed = 2`

$$\text{Suspiciousness}(10) = \frac{1/1}{1/1 + 0/2} = \frac{1}{1 + 0} = 1$$

Line 10 gets the **maximum suspiciousness score of 1** — it is covered only in failing tests and never in passing tests. This correctly identifies it as the most suspicious line and the actual location of the bug.

### 8.5 Limitations and Notes

- Many other SFL coefficients exist beyond Tarantula (e.g., Ochiai, Jaccard, DStar). A survey of these can be found in the research literature.
- SFL has seen **limited adoption** in practical tools, partly because it requires a well-designed test suite with both passing and failing tests.
- SFL is particularly useful as a component in **automatic program repair** systems, where it helps identify candidate locations for patches.

---

## 9. Program Slicing

### 9.1 What Is a Slice?

A **program slice** is a subset of the program that could have **influenced** the value of a specific variable at a specific location. In debugging, slicing helps answer: "What code could have caused this variable to have this value?"

Think of it as the "chain of life choices" that led to a particular outcome — given a variable's value at some point, which earlier lines of code could possibly have affected it?

### 9.2 Two Types of Dependencies

Program slicing is built on two types of dependencies, which together form the **Program Dependence Graph (PDG)**:

**Data dependency:** A statement *S2* is data-dependent on *S1* if *S1* assigns a value to a variable that *S2* reads. In other words, data flows from *S1* to *S2*.

Example: In `x = 5; y = x + 1;`, the second statement is data-dependent on the first because it reads `x`.

**Control dependency:** A statement *S2* is control-dependent on *S1* if *S1* is a conditional (e.g., an `if` or `while`) and the execution of *S2* depends on the outcome of that conditional.

Example: In `if (x > 0) { y = 1; }`, the assignment `y = 1` is control-dependent on the `if` condition because whether it executes depends on the condition's truth value.

### 9.3 Forward vs. Backward Slicing

**Backward slicing:** Starting from a variable at a specific location, trace **backwards** to find all statements that could have influenced its value. This answers: "What chain of events led to this value?" Used in debugging to trace the origin of a faulty value.

**Forward slicing:** Starting from a variable at a specific location, trace **forwards** to find all statements that could be influenced by a change to this variable. This answers: "If I change this variable, what else could be affected?" Used in impact analysis — e.g., "If we finish the CS3213 assignment by Friday (changing `workDone` to `true`), how does that change my weekend plan?"

### 9.4 Static vs. Dynamic Slicing

**Static slicing** (Weiser, 1984): Considers **all possible executions** of the program. Does not use any specific input. The slice includes every statement that *could* influence the variable under *any* input.

- **Advantage:** Complete — guaranteed to include the relevant code.
- **Disadvantage:** Often too large — may include the entire program. For example, slicing the `isEven()` function statically could include the whole method because all branches are possible.

**Dynamic slicing** (Agrawal, 1990): Considers a **specific concrete execution** (a specific input). The slice only includes statements that *actually* influenced the variable during *that particular run*.

- **Advantage:** Typically much smaller and more useful for debugging.
- **Disadvantage:** Only valid for the specific execution being analyzed.

### 9.5 Worked Example: `decideWeekendPlan`

```java
public boolean decideWeekendPlan(boolean workDone, double savings, int friendCount) {
    boolean canAfford = savings > 30.0;          // Line 1
    boolean peerPressure = friendCount > 5;       // Line 2
    boolean willGoOut = false;                    // Line 3
    if (workDone) {                               // Line 4
        if (canAfford || peerPressure) {          // Line 5
            willGoOut = true;                     // Line 6
        }
    } else if (peerPressure) {                    // Line 7 (FOMO logic)
        willGoOut = true;                         // Line 8
    }
    return willGoOut;                             // Line 9
}
```

**Backward static slice** on `willGoOut` at Line 9: The static slice includes **all lines (1–9)** because under different inputs, `willGoOut` could be assigned at Line 3, Line 6, or Line 8, and these depend (via control dependencies) on conditions at Lines 4, 5, and 7, which in turn depend (via data dependencies) on Lines 1 and 2.

**Backward dynamic slice** on `willGoOut` at Line 9, for the input `workDone = false, savings = 0, friendCount = 10`:

With this input, the actual execution path is: Line 1, Line 2, Line 3, Line 4 (false), Line 7 (true), Line 8, Line 9. The value of `willGoOut` at Line 9 is `true`, assigned at Line 8. Tracing backwards:
- Line 9 reads `willGoOut` ← data dependency on Line 8
- Line 8 assigns `willGoOut = true` ← control dependency on Line 7 (`else if (peerPressure)`)
- Line 7 checks `peerPressure` ← data dependency on Line 2
- Line 7 is also control-dependent on Line 4 (it is in the `else` branch)
- Line 4 checks `workDone` ← from function parameter
- Line 2 depends on `friendCount` ← from function parameter

**Dynamic slice = {Line 2, Line 3, Line 4, Line 7, Line 8, Line 9}** (Line 1 is NOT included because `canAfford` was never used in the actual execution path — it was only relevant inside the `if (workDone)` branch which was not taken).

**Important:** Dynamic slicing is different from code coverage. Code coverage tells you which lines executed. Dynamic slicing tells you which lines *influenced the specific variable you care about*. Line 1 was executed (covered) in this run, but it is **not** in the dynamic slice because it had no influence on the final value of `willGoOut`.

### 9.6 The `middle()` Example

```java
public static int middle(int x, int y, int z) {
    if (y < z) {
        if (x < y) { return y; }
        else if (x < z) { return y; }   // BUG: should return x
    } else {
        if (x > y) { return y; }
        else if (x > z) { return x; }
    }
    return z;
}
```

For the call `middle(2, 1, 3)`, the expected result is 2 but the actual result is 1. A slice on the return value helps identify that the second `return y` should be `return x` — the slice traces back through the conditions to show which variables influenced the incorrect return value.

---

## 10. Test-Case Reduction and Delta Debugging

### 10.1 Motivation: The Mozilla BugAThon

In July 1999, the Mozilla bug database (Bugzilla) listed more than **370 open, unsimplified bug reports**, and the queue was growing. Eric Krock, the Netscape product manager, sent out the **Mozilla BugAThon** — a call for volunteers to help simplify bug reports. The instructions were to manually remove HTML markup, CSS rules, and lines of JavaScript from bug-triggering web pages, checking every few minutes that the page still reproduced the bug.

This manual process begs the question: can we **automate** test-case reduction?

### 10.2 Test-Case Reduction

A **test case** consists of two components:
- **Test input** — the data fed to the program
- **Test oracle** — the mechanism that determines whether the bug is triggered (e.g., does the program crash? does it produce the wrong output?)

**Test-case reduction** tools reduce the test input, apply the (user-supplied) test oracle, and check whether the bug is still being triggered. If so, the tool continues reducing. If not, it undoes the change and tries a different reduction.

### 10.3 Naïve Approach: Binary Search

The simplest reduction strategy is binary search:
1. Split the input into two halves.
2. Test each half — keep the half that still triggers the bug.
3. Repeat until you cannot reduce further.

This works in O(log n) steps when the bug is caused by a single contiguous element.

**Limitation:** Binary search fails when the bug requires elements from **both halves**. For example, if `<SELECT` triggers a bug in a browser, the input `<SELECT NAME="priority" MULTIPLE SIZE=7>` cannot be reduced by simple binary search because neither `<SELECT NAME="priori` nor `ty" MULTIPLE SIZE=7>` alone contains the triggering token `<SELECT`.

### 10.4 Delta Debugging (ddmin)

Delta debugging overcomes the limitation of binary search by systematically trying **different combinations of smaller blocks** when simple halving fails.

#### Formal Definition

**Preconditions:**
- `test(∅) = ✔` — the empty input passes (does not trigger the bug)
- `test(c_✘) = ✘` — the original failing input triggers the bug

**Goal:** Find `c'_✘ = ddmin(c_✘)` such that `c'_✘ ⊆ c_✘`, `test(c'_✘) = ✘`, and `c'_✘` is **1-minimal**.

The algorithm `ddmin(c_✘) = ddmin₂(c_✘, 2)` where:

```
                    ⎧ ddmin₂(Δᵢ, 2)                    if ∃i: test(Δᵢ) = ✘     ("reduce to subset")
ddmin₂(c'_✘, n) = ⎨ ddmin₂(∇ᵢ, max(n-1, 2))          if ∃i: test(∇ᵢ) = ✘     ("reduce to complement")
                    ⎪ ddmin₂(c'_✘, min(|c'_✘|, 2n))    if n < |c'_✘|            ("increase granularity")
                    ⎩ c'_✘                               otherwise                ("done")
```

where:
- `c'_✘ = Δ₁ ∪ Δ₂ ∪ ... ∪ Δₙ` — the current failing input is partitioned into n roughly equal-sized subsets
- `∇ᵢ = c'_✘ − Δᵢ` — the complement of subset i (everything except that subset)
- All Δᵢ are pairwise disjoint, and each `|Δᵢ| ≈ |c'_✘|/n`
- The recursion invariant is `test(c'_✘) = ✘ ∧ n ≤ |c'_✘|`

#### How ddmin Works (Intuition)

The algorithm tries four strategies in order of preference:

1. **Reduce to subset:** Can we replace the entire current input with just one of the n partitions? If any partition Δᵢ alone triggers the bug, recurse on that smaller input with n = 2.

2. **Reduce to complement:** Can we remove one partition and still trigger the bug? If any complement ∇ᵢ = c'_✘ − Δᵢ still fails, recurse on that complement with n decremented by 1 (but at least 2).

3. **Increase granularity:** If neither subsets nor complements work, double the number of partitions (n → 2n) and try again with finer-grained subsets. This allows the algorithm to isolate combinations of elements from different partitions.

4. **Done:** If n ≥ |c'_✘| (granularity equals the number of elements), no further reduction is possible. Return the current input.

#### 1-Minimality

Delta debugging produces a **1-minimal** result: no single element can be removed from the result without causing the test to pass. This is a **local minimum**, not necessarily the global minimum.

Finding the **global minimum** would require testing all 2^|c_✘| subsets — exponential complexity. Delta debugging trades off global optimality for polynomial running time by achieving only local (1-minimal) optimality.

### 10.5 Worked Example: `<SELECT>` Tag

**Failing input:** `<SELECT NAME="priority" MULTIPLE SIZE=7>` (triggers a browser bug)

**test(∅) = ✔** (empty input does not trigger the bug)

**Step-by-step execution of ddmin:**

**n = 2:** Split into Δ₁ = `<SELECT NAME="priori`, Δ₂ = `ty" MULTIPLE SIZE=7>`
- test(Δ₁) = ✔ (cannot reduce to subset)
- test(Δ₂) = ✔ (cannot reduce to subset)
- test(∇₁) = ✔ (cannot reduce to complement — complements are the same as the other partition when n=2)
- Need to increase granularity → n = 4

**n = 4:** Split into 4 parts: Δ₁ = `<SELECT NA`, Δ₂ = `ME="priori`, Δ₃ = `ty" MULTIP`, Δ₄ = `LE SIZE=7>`
- No subset alone fails the test
- Check complements: ∇₁ = `ME="priority" MULTIPLE SIZE=7>` → ✔ (passes, since `<SELECT` is missing)
- Continue checking complements...
- Eventually, through several more rounds of increasing granularity and testing, the algorithm reduces the input down to `<SELECT>` — the 1-minimal failing input.

At the character level with n = 8: Δ₁ = `<`, Δ₂ = `S`, Δ₃ = `E`, Δ₄ = `L`, Δ₅ = `E`, Δ₆ = `C`, Δ₇ = `T`, Δ₈ = `>`. No single character can be removed without making the test pass → `<SELECT>` is 1-minimal.

### 10.6 Worked Example: NUS Course List (from In-Class Session)

**Setup:** An application crashes when processing a list of NUS CS courses. The bug is caused by **duplicate course codes** (CS3213 appears at indices 4 and 7).

| Index | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|---|---|---|---|---|---|---|---|---|
| Course | CS1010 | CS2030 | CS2103T | **CS3213** | CS2100 | CS2106 | **CS3213** | CS4226 |

**Running ddmin step by step:**

**Round 1 (n=2):** c'_✘ = {1,2,3,**4**,5,6,**7**,8}
- Δ₁ = {1,2,3,4}, Δ₂ = {5,6,7,8}
- test(Δ₁) = ✔, test(Δ₂) = ✔ (neither half alone has duplicates)
- test(∇₁) = ✔, test(∇₂) = ✔ (complements = other half when n=2)
- **Increase granularity** → n = 4 (4 calls so far)

**Round 2 (n=4):** c'_✘ = {1,2,3,**4**,5,6,**7**,8}
- Δ₁ = {1,2}, Δ₂ = {3,4}, Δ₃ = {5,6}, Δ₄ = {7,8}
- No subset fails alone
- Check complements: ∇₁ = {3,**4**,5,6,**7**,8} → ✘ (still has both CS3213s!)
- **Reduce to complement** → c'_✘ = {3,4,5,6,7,8}, n = max(4-1, 2) = 3 (5 calls)

**Round 3 (n=3):** c'_✘ = {3,**4**,5,6,**7**,8}
- Δ₁ = {3,4}, Δ₂ = {5,6}, Δ₃ = {7,8}
- No subset fails alone
- ∇₁ = {5,6,7,8} → ✔ (only one CS3213 at index 7 — no duplicates)
- ∇₂ = {3,**4**,**7**,8} → ✘ (has CS3213 at both indices 4 and 7 — duplicates trigger the bug!)
- **Reduce to complement** → c'_✘ = {3,4,7,8}, n = max(3-1, 2) = 2 (5 calls)

**Round 4 (n=2):** c'_✘ = {3,**4**,**7**,8}
- Δ₁ = {3,4}, Δ₂ = {7,8}
- test(Δ₁) = ✔, test(Δ₂) = ✔
- Complements are same as other subset when n=2
- **Increase granularity** → n = 4 (4 calls)

**Round 5 (n=4):** c'_✘ = {3,**4**,**7**,8}
- Δ₁ = {3}, Δ₂ = {4}, Δ₃ = {7}, Δ₄ = {8}
- No subset alone fails
- ∇₁ = {**4**,**7**,8} → ✘ (has both CS3213s!)
- **Reduce to complement** → c'_✘ = {4,7,8}, n = 3 (5 calls)

**Round 6 (n=3):** c'_✘ = {**4**,**7**,8}
- Δ₁ = {4}, Δ₂ = {7}, Δ₃ = {8}
- No subset alone fails
- ∇₁ = {**7**,8} → ✔ (only one CS3213), ∇₂ = {**4**,8} → ✔ (only one CS3213), ∇₃ = {**4**,**7**} → ✘ (both CS3213s!)
- **Reduce to complement** → c'_✘ = {4,7}, n = 2 (6 calls)

**Round 7 (n=2):** c'_✘ = {**4**,**7**}
- Δ₁ = {4}, Δ₂ = {7}
- test(Δ₁) = ✔, test(Δ₂) = ✔
- Complements are same as other subset
- n = 2 = |c'_✘| → **Done!** (4 calls)

**Result:** `{CS3213, CS3213}` (indices 4 and 7) — the 1-minimal failing input. Neither element alone causes the crash; you need both duplicates.

**Total interestingness tests: 4 + 5 + 5 + 4 + 5 + 6 + 4 = 33**

### 10.7 C-Reduce

**C-Reduce** is a practical tool for test-case reduction, originally designed for C/C++ programs but also applicable to other formats. It takes:
- A test input (e.g., a large SQL file `test.sql`)
- An **interestingness test** (a shell script that checks if the bug is still triggered)

And produces a reduced test case. Example from the slides: reducing a large SQL test file to just 4 lines that trigger a "malformed" error in SQLite.

---

## 11. Isolating Failure-Inducing Changes

### 11.1 Regression Bugs

A **regression bug** is something (e.g., a feature) that **worked before but stopped working**. The question is: "Yesterday, my program worked. Today, it does not. Why?"

**Prerequisite:** We have older, working versions of the software — typically through a version control system like git.

### 11.2 Naïve Approach: Linear Search Through Versions

Check each version sequentially: v1 (✔), v2 (?), v3 (✔), v4 (✘), ... This identifies that the bug was introduced in v4.

**Disadvantage:** Takes a long time — hundreds of versions might need to be inspected. Between two adjacent versions, there might still be many changes. However, this is a task that can be fully automated.

### 11.3 Binary Search on Versions (Git Bisect)

Binary search is more efficient: it identifies the commit that introduced the regression bug in **O(log n)** steps, where n is the number of versions between the known-good and known-bad commits.

**Git bisect** provides built-in functionality for this:

```bash
$ git bisect start
$ git bisect good <known-good-commit>
$ git bisect bad <known-bad-commit>
```

Git checks out the midpoint version. You test it and report:
- `git bisect good` — the bug is not present in this version
- `git bisect bad` — the bug is present in this version

Git then narrows the search range and checks out the next midpoint. Repeat until the first bad commit is found.

### 11.4 Automatic Bisection

The process can be fully automated with a test script:

```bash
$ git bisect run ./test.sh
```

The test script:
1. (Re)builds the program under test for the given version
2. Tests whether the failure is present
3. Returns an exit code:
   - **0** means "good" (the failure did not occur)
   - **1** means "bad" (the failure did occur)
   - **125** means "undetermined" (cannot decide — e.g., build fails for this version)

**Example (SQLite):** Using `git bisect run` with a test script that builds SQLite and checks if `SELECT (1 IN (2 IS TRUE))` returns the correct result, git bisect automatically identified the exact commit that introduced the bug — a commit that reinstated an optimization converting `x IN (y)` into `x==y`.

### 11.5 Delta Debugging on Changes (Patches)

Once `git bisect` identifies the **commit** that introduced the bug, the commit may still contain **many changes** across multiple files. To further narrow down the defect, we can apply delta debugging to the individual **patches** (hunks) within that commit.

A **hunk** is a group of consecutive differing lines (as opposed to lines common to both the old and new versions). By treating each hunk as an element and applying the ddmin algorithm, we can identify the **minimal set of hunks** that, when applied, introduce the bug.

This provides a much finer-grained isolation of the failure-inducing change — potentially down to a single line change.

---

## 12. Summary Table

| Technique | Question It Answers | Key Details |
|---|---|---|
| **Printf / Logging** | "What is the state at this point?" | Manual; logging adds structured levels (DEBUG, INFO, WARN, ERROR, FATAL) |
| **Rubber Ducking** | "What am I overlooking?" | Explaining the problem forces structured re-examination of assumptions |
| **Scientific Method** | "Why does this failure happen?" | 5 steps: question → hypothesis → prediction → test → repeat; diagnosis requires both causality and incorrectness |
| **Tracking Origins** | "What earlier states caused this?" | Work backwards from failure through faulty states to defect |
| **Tarantula (SFL)** | "Which lines are most suspicious?" | Suspiciousness = [failed(line)/total_failed] / [failed(line)/total_failed + passed(line)/total_passed]; score 1 = only in failing tests, 0 = only in passing tests |
| **Program Slicing** | "What code influenced this variable?" | Backward (trace origin) vs. Forward (trace impact); Static (all executions, larger) vs. Dynamic (specific execution, smaller); Built on data and control dependencies |
| **Delta Debugging** | "What is the minimal input that triggers the bug?" | ddmin algorithm: try subsets → try complements → increase granularity → done; produces 1-minimal result; global minimum is exponential (2^n) |
| **Git Bisect** | "Which commit introduced the regression?" | Binary search on version history; O(log n) steps; can be automated with test scripts |
| **DD on Changes** | "Which exact lines in the commit caused the bug?" | Apply ddmin to hunks/patches within the identified commit |

---

## 13. Key Terminology Quick Reference

| Term | Definition |
|---|---|
| **Mistake** | Human act that produces an incorrect result |
| **Defect (Bug)** | Incorrect code in the program |
| **Fault** | Incorrect intermediate program state during execution |
| **Failure** | Externally visible incorrect behavior |
| **Diagnosis** | Explanation of both **causality** (how the failure came to be) and **incorrectness** (how to correct the code) |
| **Test oracle** | Mechanism that determines whether the bug is triggered |
| **1-minimal** | No single element can be removed from the failing input without making it pass |
| **Hunk** | A group of consecutive differing lines in a diff/patch |
| **Regression bug** | A feature that worked before but stopped working |
| **Spectrum-based** | Using test execution coverage to compute suspiciousness |
| **Data dependency** | Statement S2 reads a variable assigned by S1 |
| **Control dependency** | Whether S2 executes depends on the outcome of condition S1 |
| **PDG** | Program Dependence Graph — combines data and control dependencies |
| **Static slice** | Slice considering all possible executions (Weiser, 1984) |
| **Dynamic slice** | Slice for a specific concrete execution (Agrawal, 1990) |

---

## 14. Key Takeaways

1. **Debugging is expensive** — 35–50% of developer time, 50–75% of project budgets. Systematic approaches pay off enormously.

2. **The terminology chain matters:** Mistake → Defect → Fault → Failure. A defect is in the code; a fault is in the state during execution; a failure is what the user sees. Know the difference.

3. **The scientific method is the gold standard** for manual debugging: form hypotheses, make testable predictions, run experiments. A proper diagnosis must explain both causality and incorrectness.

4. **Tarantula/SFL** gives you a ranked list of suspicious lines based on test coverage data. Lines executed only in failing tests get score 1 (most suspicious); lines only in passing tests get score 0.

5. **Program slicing** identifies which code could have influenced a specific variable. Static slices can be large (whole program); dynamic slices are smaller and more useful for debugging. Slicing is built on data and control dependencies in the PDG. Dynamic slicing is different from code coverage.

6. **Delta debugging (ddmin)** finds 1-minimal failing inputs. The four cases (subset → complement → increase granularity → done) are essential to understand. The algorithm handles the case where the bug requires elements from multiple parts of the input — something binary search cannot do.

7. **Git bisect** efficiently finds regression-introducing commits via binary search (O(log n)). It can be fully automated. Delta debugging can then be applied to the individual patches within the identified commit to further isolate the exact change.
