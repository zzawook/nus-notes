# Software Testing — Comprehensive Summary

> **What this covers:** This summary consolidates all three ST lectures plus the in-class session. You will learn the fundamentals of software testing (why we test, test cases, testing levels, testing processes), **black-box (specification-based) testing** with the complete 7-step workflow, **white-box (structural) testing** with all coverage criteria from line coverage through MC/DC, and additional topics on **mutation testing**, **test suite quality**, and **static analysis**. Being able to apply the partition/boundary workflow, compute coverage, derive MC/DC test sets, and reason about mutation testing is essential for the exam.

---

## 1. Why Testing Matters

### 1.1 Two Goals of Testing

Testing serves two complementary purposes. **Finding bugs** means discovering defects before they reach users — this is what most people think of when they hear "testing." **Validation testing** means confirming that the software meets its requirements and behaves as intended. Both goals are critical: finding bugs ensures quality, while validation ensures the right product was built.

### 1.2 Dijkstra's Insight

Edsger Dijkstra famously observed: *"Program testing can be used to show the presence of bugs, but never to show their absence."* This means that passing all tests does not prove correctness — it only means the specific tested scenarios worked. There could always be untested inputs that trigger bugs. This is a fundamental limitation of testing, which is why we complement it with techniques like formal verification and static analysis.

### 1.3 Shifting Left

The **Shift Left** principle says: find and fix defects **as early as possible** in the software development lifecycle. The cost of fixing a defect increases exponentially the later it is discovered. A bug caught during coding might take minutes to fix; the same bug caught in production might require hotfixes, rollbacks, and customer support. This motivates early testing practices like TDD and unit testing.

---

## 2. Test Cases: The Building Blocks

A **test case** consists of two components:

**Test Input:** The data and conditions you feed to the system under test. This includes function arguments, system/environment state, action sequences, command-line arguments, and GUI interactions (button clicks, form entries). A test input sets up the scenario.

**Test Oracle:** The mechanism for determining whether the test passed or failed. The oracle defines the **expected behavior**. For example, `assertEquals(5, add(2, 3))` — the oracle is the assertion that the result should be 5. Without an oracle, running a program is just an execution, not a test.

### 2.1 Types of Properties Tested

**Functional properties** (the focus of this module): Does the system produce the correct output for a given input? These are typically specified in requirements and tested via example-based testing with user-provided oracles.

**Non-functional properties:** Performance (response time, throughput), usability, accessibility, security. These are harder to test with simple assertions and often require specialized tools and benchmarks.

---

## 3. Testing Levels

Testing happens at different granularities, each with distinct trade-offs.

### 3.1 Unit Testing

Tests **individual components** (functions, methods, classes) in isolation. External dependencies (databases, web services, file systems) are replaced with **mocks** or **stubs** — fake implementations that simulate the real dependencies.

**Advantages:** Fast execution, easy to control inputs and environment, easy to write and maintain, good for pinpointing the location of bugs.

**Disadvantages:** Lacks realism because mocks may not perfectly replicate real dependencies. Misses integration bugs — the units may work perfectly individually but fail when combined. Requires writing and maintaining mock objects.

### 3.2 Integration Testing

Tests **multiple components together** to verify they interact correctly. Unlike unit tests, integration tests use real external components (real database, real web services) rather than mocks. The focus is on testing "our" component's interaction with its dependencies.

### 3.3 System / End-to-End (E2E) Testing

Tests the **entire system** as a whole, simulating real user scenarios from start to finish. These tests are the most realistic but also the slowest, hardest to write, and most prone to **flakiness** (non-deterministic pass/fail due to concurrency, async operations, timeouts, test order dependencies, or resource leaks).

### 3.4 Google's Test Pyramid

Google recommends a distribution of tests shaped like a pyramid:

| Level | Proportion | Characteristics |
|---|---|---|
| **Unit** | ~80% | Many small, fast tests forming the base |
| **Integration** | ~15% | Moderate number of medium-speed tests |
| **E2E** | ~5% | Few slow, comprehensive tests at the top |

The **antipattern** is the "Ice Cream Cone" — too many manual/high-level tests and too few unit tests, which leads to slow feedback, high flakiness, and expensive test maintenance.

### 3.5 Test Sizes at Google

Google also classifies tests by resource constraints:

| Size | Processes | Network | Description |
|---|---|---|---|
| **Small** | Single process | No I/O | Fast, deterministic |
| **Medium** | Multiple processes | Localhost only | Can use local services |
| **Large** | No restriction | No restriction | Full integration |

---

## 4. Testing in Development Processes

### 4.1 Testing in Agile (XP)

**Extreme Programming** emphasizes tight feedback loops. **Unit tests** run on every commit (seconds to minutes), and **acceptance tests** validate user stories.

**Test-Driven Development (TDD)** follows a strict cycle:
1. **Write a failing test** that defines the desired behavior
2. Write the **simplest code** that makes the test pass
3. **Refactor** the code while keeping tests green

TDD benefits: produces inherently testable code, results in high-quality test suites (every line of code exists because a test demanded it), prevents feature creep (you only build what tests require), and gives confidence for refactoring.

**Acceptance Criteria** in user stories define when a story is "done" and serve as the basis for acceptance tests.

### 4.2 Testing in Plan-Driven (V-Model)

The V-Model pairs each development phase with a corresponding testing phase:

| Development Phase | Testing Phase |
|---|---|
| Requirement Gathering | Acceptance Testing |
| System Analysis | System Testing |
| Software Design | Integration Testing |
| Module Design | Unit Testing |

Testing is planned **alongside** development, not as an afterthought.

### 4.3 Verification vs. Validation

**Verification** — "Are we building the product **right**?" Does the software conform to its specification?

**Validation** — "Are we building the **right** product?" Does the software meet the user's actual needs?

**User Acceptance Testing (UAT)** focuses on validation — it involves the customer and checks whether the delivered software satisfies their real-world requirements.

---

## 5. Black-Box vs. White-Box Testing

| Aspect | Black-Box | White-Box |
|---|---|---|
| Also called | Specification-based, opaque-box | Structural testing |
| Information used | Requirements, documentation | Source code, implementation |
| Focus | What the program should do | How the program does it |
| Strength | Tests against spec; independent of implementation | Finds gaps in test coverage; exercises code paths |

These are **complementary**, not competing. The recommended workflow is: first apply specification-based testing (black-box), then use structural testing (white-box) to identify gaps.

---

## 6. Black-Box Testing: The 7-Step Workflow

This is a systematic, repeatable process for deriving test cases from specifications.

### Step 1: Understand the Requirements

Read the documentation, specifications, or Javadoc carefully. Understand the inputs (types, ranges, constraints), outputs (return types, possible values), and expected behavior (what does the function do for each scenario?).

### Step 2: Explore the Program

Run the program with a few example inputs to build a mental model of its behavior. This **exploratory testing** phase helps you discover edge cases and unexpected behaviors that the specification might not mention.

### Step 3: Identify the Partitions (Equivalence Classes)

The central idea: inputs within the same partition should trigger the **same behavior**, so testing one representative from each partition is sufficient. Testing all partitions ensures broad coverage.

**How to identify partitions — three perspectives:**

**(a) Each input parameter individually.** For each input, ask: what are the meaningfully different categories of values? For example, for a string parameter: null, empty string (""), single character, multiple characters. For a numeric parameter: negative, zero, positive; or specific ranges.

**(b) Interactions between inputs.** Consider combinations of input values that produce different behaviors. For example, if a function takes `str`, `open`, and `close` tags: str contains neither tag, only open, only close, both once, both multiple times.

**(c) Output partitions.** Work backwards from possible outputs. If the function can return null, an empty array, a single-element array, or a multi-element array, ensure you have test cases that exercise each output category.

### Step 4: Analyze the Boundaries

Bugs cluster at the **boundaries** between partitions. For each boundary, identify:

**On point:** A value that sits exactly **on** the boundary (where behavior changes).

**Off point:** The closest value in the **other** partition (the nearest neighbor across the boundary).

**Example:** For partitions `x < 10000`, `10000 ≤ x ≤ 99999`, `x > 99999`:
- Boundary between first and second partition: on point = 10000, off point = 9999
- Boundary between second and third partition: on point = 99999, off point = 100000

For string lengths: if the boundary is between "empty" and "non-empty," the on point is "" (length 0) and the off point is a single-character string (length 1).

### Step 5: Devise Test Cases

Combine partitions and boundary values into concrete test cases. Each test case should have a clear purpose (which partition or boundary it targets). Use a systematic approach:

- Ensure every partition has at least one representative test case
- Ensure every boundary has on-point and off-point tests
- Cover interesting combinations of input partitions

**The "each choice" strategy** picks at least one value from each partition for each input. More thorough strategies (pairwise, all-combinations) cover more interaction scenarios but generate more tests.

### Step 6: Automate Test Cases

Translate your test cases into executable automated tests (e.g., JUnit, pytest). Automated tests can be run repeatedly, quickly, and consistently — manual testing does not scale.

### Step 7: Augment with Creativity and Experience

After systematic testing, add tests based on your intuition, domain knowledge, and experience. Think about: common programming errors (off-by-one, null pointer, integer overflow), unusual inputs (Unicode, very long strings, concurrent access), and real-world usage patterns.

---

## 7. White-Box (Structural) Testing

### 7.1 Why Structural Testing?

Specification-based testing alone may miss code that the specification does not explicitly mention. Structural testing uses knowledge of the source code to ensure all parts of the implementation are exercised.

**The recommended workflow:**
1. Apply specification-based testing first (derive tests from requirements)
2. Run a code coverage tool to measure how much code was exercised
3. Understand **why** uncovered code was not covered (missing test case? dead code? defensive code?)
4. Add missing tests (or address the underlying issue)

### 7.2 Control Flow Graph (CFG)

Structural testing reasons about the code's **control flow graph**, where nodes represent statements or blocks and edges represent possible execution transitions (branches). The CFG makes it visual: you can see which paths through the code your tests exercise.

### 7.3 Line (Statement) Coverage

**Definition:** The percentage of **executable lines** that are executed by the test suite.

$$\text{Line coverage} = \frac{\text{Lines executed}}{\text{Total executable lines}} \times 100$$

This is the simplest and weakest coverage criterion. Achieving 100% line coverage means every statement was executed at least once, but it does not guarantee all branches were tested.

**Limitation:** Line coverage can miss bugs in branch logic. If a condition has two branches but one is never taken, 100% line coverage may still be achieved if the untested branch is on a line that gets counted as "covered" through the other branch.

### 7.4 Branch (Decision) Coverage

**Definition:** The percentage of **branches** (edges in the CFG) exercised by the test suite. For every decision point (if, while, for, switch), both the true and false outcomes must be tested.

$$\text{Branch coverage} = \frac{\text{Branches exercised}}{\text{Total branches}} \times 100$$

**Key relationship:** Branch coverage **subsumes** line coverage — if you achieve 100% branch coverage, you automatically have 100% line coverage (because reaching all branches means executing all reachable statements). But the reverse is not true.

### 7.5 Condition Coverage

**Definition:** Each individual **condition** (atomic boolean sub-expression) within a decision must evaluate to both true and false across the test suite.

For the decision `if (A && B)`, condition coverage requires tests where A is true, A is false, B is true, and B is false. However, condition coverage alone does **not** guarantee branch coverage — you could satisfy condition coverage without the overall decision ever evaluating to true.

### 7.6 Branch + Condition Coverage

**Definition:** Combines branch coverage and condition coverage — every branch must be taken, AND every individual condition must be both true and false.

This is stronger than either branch or condition coverage alone, but still has a gap: it does not ensure that each condition **independently** affects the decision outcome.

### 7.7 Path Coverage

**Definition:** Every possible **path** through the control flow graph must be exercised. A path is a complete sequence of edges from the method entry to exit.

**Problem:** In the presence of loops, the number of paths can be **infinite** (or exponentially large). Path coverage is usually impractical for real programs. It is the strongest criterion but rarely achievable.

### 7.8 MC/DC Coverage (Modified Condition/Decision Coverage)

MC/DC is a practical compromise between the weakness of branch+condition coverage and the impracticality of path coverage. It was developed for safety-critical systems (aviation, DO-178B/C standard) and provides strong assurance that conditions are tested thoroughly.

**Three requirements:**
1. Each **decision** takes every possible outcome (true and false) — this is branch coverage
2. Each **condition** in a decision takes every possible outcome (true and false) — this is condition coverage
3. Each condition is shown to **independently affect** the outcome of the decision

**What "independently affect" means:** For each condition C, there must exist a pair of test cases where:
- C changes value (true → false or vice versa)
- All other conditions stay the same
- The overall decision outcome changes

This pair demonstrates that C matters — it is not "masked" by other conditions.

**Decision vs. Branch:** A decision is a boolean expression consisting of conditions and operators. Unlike branches, decisions also cover assignments to boolean variables (e.g., `boolean flag = A && B`).

#### MC/DC Systematic Method

For each condition in the decision, find **independence pairs** — pairs of rows in the truth table where only that condition differs and the decision outcome flips:

1. Write out the full truth table for the decision
2. For each condition, find all pairs of rows where only that condition changes and the decision flips
3. Select a minimal set of test cases that covers at least one independence pair per condition, plus decision coverage

#### MC/DC Worked Example

For `isLetter && (== 's' || == 'r')` with conditions A = `isLetter`, B = `== 's'`, C = `== 'r'`:

| Test | A | B | C | Decision |
|---|---|---|---|---|
| T1 | T | T | T | T |
| T2 | T | T | F | T |
| T3 | T | F | T | T |
| T4 | T | F | F | F |
| T5 | F | T | T | F |
| T6 | F | T | F | F |
| T7 | F | F | T | F |
| T8 | F | F | F | F |

**Independence pairs:**
- **A:** {T1,T5}, {T2,T6}, {T3,T7} — toggling A flips the decision (other conditions fixed)
- **B:** {T2,T4} — toggling B flips the decision (A=T, C=F fixed)
- **C:** {T3,T4} — toggling C flips the decision (A=T, B=F fixed)

**Selecting tests:** T2, T3, T4 are required (only options for B and C). For A, we need a pair — {T2,T6} or {T3,T7} work. Since T2 and T3 are already included, adding T6 or T7 suffices. **Final set: {T2, T3, T4, T6} or {T2, T3, T4, T7}** — only 4 tests instead of 8 for full path coverage.

#### Baseline + Toggle Shortcut Method

A heuristic (does not always work due to masking/short-circuit evaluation):
1. Choose a **baseline** test case where the decision is True
2. **Toggle one condition** at a time while keeping others fixed
3. If the decision flips → you have an independence pair
4. If the decision does **not** flip → adjust the baseline so the condition is not masked

#### MC/DC for XOR

For `A XOR B XOR C`, every condition flip always changes the decision (XOR is symmetric). Independence pairs: A: {T1,T5}, {T2,T6}, {T3,T7}, {T4,T8}; B: similarly; C: similarly. The baseline+toggle from T8 (T,T,T → T): flip A → T4 (F,T,T → F), flip B → T6 (T,F,T → F), flip C → T7 (T,T,F → F). Final: {T4, T6, T7, T8}.

### 7.9 Coverage Criteria Subsumption Hierarchy

```
Path Coverage
     ↓
   MC/DC
     ↓
Branch + Condition Coverage
   ↙         ↘
Branch       Condition
Coverage     Coverage
   ↓
Statement/Line Coverage
```

An arrow from A → B means "A subsumes B" — achieving 100% of A guarantees 100% of B. Path coverage is the strongest; statement coverage is the weakest.

### 7.10 Code Coverage: Criticism and Proper Use

**100% coverage can be achieved without testing anything** — if you run code with no assertions, you get coverage but verify nothing. Coverage measures which code was *executed*, not which code was *correctly verified*.

**Coverage is not a silver bullet.** SQLite achieves 100% MC/DC coverage, yet researchers have found more than 200 bugs in it.

**Proper use:** Code coverage is a tool for identifying **uncovered code** — code your tests never exercise. It should not be implemented blindly as a quality metric. Use it to find gaps, not as proof of quality.

---

## 8. Mutation Testing

### 8.1 The Idea

Mutation testing evaluates the **quality of a test suite** by asking: "If I introduce small bugs into the program, will my tests catch them?"

**How it works:**
1. **Select** a statement in the source code
2. **Apply a mutation** (create a "mutant") — a small syntactic change like replacing `>` with `>=`, or changing a variable name
3. **Execute** the test suite against the mutant
4. **If any test fails:** the mutant is **killed** (good — the test suite detected the change)
5. **If all tests pass:** the mutant **survives** (bad — the test suite missed this bug)
6. Repeat for many mutations
7. Compute the **mutation score**

### 8.2 Mutation Score

$$\text{Mutation score} = \frac{\text{# killed mutants}}{\text{# total mutants}} \times 100$$

A higher mutation score means the test suite is better at detecting small faults. A mutation score of 100% means every introduced mutation was caught by at least one test.

### 8.3 Example Mutation Operators

Common mutations for conditionals (from Pitest, a Java mutation testing tool):

| Original | Mutated |
|---|---|
| `<` | `<=` |
| `<=` | `<` |
| `>` | `>=` |
| `>=` | `>` |

Other mutations: negate conditionals, change increment to decrement (1 → -1), replace return values, negate local variables, swap variable references.

### 8.4 Goals of Mutation Testing

**Evaluate the quality of existing tests:** A low mutation score reveals that your tests are not thorough enough.

**Derive new tests:** Surviving mutants tell you exactly which code changes your tests cannot detect, guiding you to write targeted new tests.

### 8.5 Advantages and Disadvantages

**Advantage:** Very effective at assessing test suite quality — mutation testing is considered one of the strongest test adequacy criteria.

**Disadvantages:**
- **Computationally expensive** — for a non-trivial program, thousands or millions of mutants may be generated, and the full test suite must run for each
- **Equivalent mutants** — some mutations produce programs that are semantically identical to the original (they behave the same for all inputs), so they can never be killed, inflating the denominator and deflating the score unfairly

Mutation testing is a **white-box technique** (it requires access to the source code to create mutants).

---

## 9. Test Suite Quality Metrics

How can we evaluate the quality of a test suite? Multiple complementary metrics:

| Metric | What It Measures |
|---|---|
| **Tests for each requirement** | Traceability — every requirement has corresponding tests |
| **Test granularity rate** | Distribution across unit, integration, E2E (aim for the pyramid) |
| **Code coverage** | How much code is exercised (line, branch, MC/DC, etc.) |
| **Execution time** | Fast tests enable frequent runs (XP: tests should be fast) |
| **Flakiness rate** | Proportion of tests with non-deterministic results |
| **Mutation score** | How effectively tests detect small injected faults |

---

## 10. The Pesticide Paradox

Every testing method has a "residue" — a class of subtler bugs that it systematically misses. Running the same tests repeatedly will not find new bugs. The solution: **combine multiple testing approaches** (specification-based + structural + mutation + exploratory) to catch different kinds of defects.

---

## 11. Quality Assurance Beyond Testing

Testing is one tool in the quality assurance toolbox. Other important techniques:

### 11.1 Peer Reviews

Code reviews (e.g., on pull requests, design documents) catch bugs that automated testing misses — logic errors, design flaws, maintainability issues. Human reviewers bring domain knowledge and design judgment.

### 11.2 Coding Standards and Best Practices

Consistent coding standards prevent entire categories of bugs and make code easier to review and test.

### 11.3 Continuous Integration (CI)

Automated build and test pipelines that run on every commit, providing fast feedback and preventing integration problems from accumulating.

### 11.4 Static Analysis

Static analysis tools reason about code **without executing it**. They range from simple linters and style checkers (formatting, naming conventions) to deep analysis tools that reason about data flow and control flow to find bugs.

**Types of static analysis:**
- **Linters and style checkers:** Superficial checks on formatting and practices
- **Data-flow analysis:** Reasons about the potential set of values at different program points
- **Control-flow analysis:** Reasons about potential execution orders

**Example tools:** SpotBugs (Java, finds bugs like coercing `Math.random()` to int), PMD (cross-language, finds patterns like deeply nested if-statements).

### 11.5 Static Analysis: Challenges ("Bad Reputation")

Static analysis tools face several practical challenges:
- **Slow** on large codebases
- **False positives** (false alarms) — reporting bugs that are not real
- **False negatives** — missing real bugs
- **Not actionable** — warnings that developers do not know how to fix
- **Hard to integrate** into developer workflow
- **Inapplicable** for complex features not modeled by the tool

### 11.6 Lessons from Industry: Deployment Matters

**Facebook's Infer tool:**
- First deployed running **nightly** over the entire Android codebase, assigning 20-30 issues to developers → **near-zero fix rate** despite ~90% true positive rate
- Then switched to running at **diff time** (when code is submitted for review) → **fix rate rocketed to over 70%**
- Same tool, same false positive rate — the difference was **when** in the workflow it was deployed

**Google's FindBugs (later SpotBugs) journey:**
- **Attempt 1 — Bug dashboard:** Nightly runs, results in a dashboard → **little use** because the dashboard was outside the developer workflow and distinguishing new vs. old issues was distracting
- **Attempt 2 — Filing bugs:** Manual triage of nightly results, filing bug reports → 42% reviewed, **only 16% actually fixed** — not sustainable at scale
- **Attempt 3 — Code review integration:** Ran automatically when code was sent for review, posting comments on the code review thread → discontinued due to false positives causing developers to **lose confidence**, and inconsistent results from customization

**Google's solution — Tricorder:** A unified static analysis platform that delivers **only valuable results**. Key principles for successful static analysis:
- Focus on **developer happiness**
- Results must be **understandable** (developers can understand the output)
- Must be **actionable and easy to fix** (guide the developer to fix, or auto-fix)
- **Low false positive rate** (< 10%)
- Have potential for **significant impact** on code quality
- **Integrate with developer workflow** (show at the right time, in the right place)
- Reports must be **timely** (at diff time, not nightly)

### 11.7 Problems with "Old" Bugs

Running static analysis on an entire codebase produces many issues in old code, which are problematic:
- **Context switching** is costly for developers
- Issues may **not be relevant** to their current work
- It is **hard to assign** the right person (they may have left the company)

The lesson: static analysis on **diffs** (only changed code) is far more effective than full-codebase scans.

---

## 12. Black-Box vs. White-Box: When to Use Which

| Situation | Approach |
|---|---|
| Starting test design | Black-box first (derive from requirements) |
| After initial black-box tests | White-box to find coverage gaps |
| Safety-critical code | MC/DC (mandated by DO-178B/C for aviation) |
| Evaluating test suite quality | Mutation testing |
| Complementing all testing | Static analysis, code reviews, CI |

---

## 13. Summary Table

| Concept | Key Point |
|---|---|
| Test case | Test input + test oracle |
| Dijkstra's insight | Testing shows presence of bugs, not their absence |
| Shift Left | Find defects early — exponentially cheaper |
| Test Pyramid | 80% unit, 15% integration, 5% E2E |
| Test Flakiness | Non-deterministic tests; caused by concurrency, async, dependencies |
| Pesticide Paradox | Every method leaves residual bugs — combine approaches |
| Black-box workflow | 7 steps: requirements → explore → partitions → boundaries → devise → automate → augment |
| Partitions | Equivalence classes: same behavior within, different between |
| Boundaries | On point (on boundary) and off point (closest in other partition) |
| Line coverage | % executable lines hit (weakest structural criterion) |
| Branch coverage | % branches (true/false) taken; subsumes line coverage |
| Condition coverage | Each atomic condition is both true and false |
| Branch + Condition | Both branch and condition coverage combined |
| Path coverage | All paths through CFG (strongest, usually impractical) |
| MC/DC | Each condition independently affects the decision; practical for safety-critical |
| Subsumption | Path ⊃ MC/DC ⊃ Branch+Condition ⊃ Branch ⊃ Statement |
| Mutation testing | Inject small faults; mutation score = killed/total × 100 |
| Equivalent mutants | Mutations that are semantically identical — cannot be killed |
| Static analysis | Reasons about code without executing; linters, data-flow, control-flow |
| Deployment lesson | Same tool at diff time >> nightly scan (Facebook Infer, Google FindBugs) |
| Tricorder principles | Understandable, actionable, low FP rate, integrated, timely |

**Key takeaways:**
1. **Black-box and white-box testing are complementary** — start with specification-based testing, then use structural coverage to find gaps.
2. **MC/DC is the practical sweet spot** for thorough testing — stronger than branch+condition coverage but feasible unlike full path coverage. It requires demonstrating that each condition independently affects the decision.
3. **Mutation testing** is the gold standard for evaluating test suite quality, but is computationally expensive. Surviving mutants guide you to write better tests.
4. **Code coverage is a means, not an end** — use it to find uncovered code, not as proof of quality. 100% coverage with no assertions proves nothing.
5. **Static analysis works best when integrated into the developer workflow at diff time**, with low false positive rates and actionable output. Nightly batch scans on entire codebases consistently fail in practice.
