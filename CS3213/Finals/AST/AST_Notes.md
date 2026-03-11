# CS3213: Automated Software Testing (AST)

---

## AST 1 - Introduction: Automated Test-case Generation Overview

### 1. Recap: What is a Test Case?

A test case consists of two components:

| Component | Description | Example |
|-----------|-------------|---------|
| **Test Oracle** | The expected result / assertion | `assertEquals(5, ...)` |
| **Test Input** | The values fed to the system under test | `add(2, 3)` |

```java
@Test
public void testAdd() {
    assertEquals(5, add(2, 3));
//  ^^^^^^^^^^^^     ^^^^^^^^
//  Test Oracle      Test Input
}
```

Previously in the course, test cases were written manually using **example-based testing** (specification-based testing and structural testing). The challenge: this is labor-intensive and may miss bugs.

---

### 2. What is Automated Test-case Generation?

**Goal:** Automatically construct a test oracle and/or test input.

- Based on some specifications or insights about the program
- The test oracle might be **incomplete** (i.e., it may not check everything)

#### Why automate?

| Reason | Explanation |
|--------|-------------|
| **Comprehensiveness** | Manually-written, example-based tests might be incomprehensive and miss bugs |
| **Speed & Cost** | Automated approaches are fast and cost-effective |
| **Maintenance** | Low maintenance effort compared to hand-written tests |

> **Note on LLMs:** While LLMs (ChatGPT, Gemini, etc.) can generate tests, this module focuses on *principled, systematic* automated test-case generation techniques.

---

### 3. Black-box, Grey-box, and White-box Approaches

These categories describe **how much internal information** about the program is used:

| Approach | Internal Information | Description |
|----------|---------------------|-------------|
| **Black-box** | None | Treats the program as an opaque box. Only sees inputs and outputs. |
| **White-box** | Full | Has access to source code, internal structure, and execution paths. |
| **Grey-box** | Some | Uses lightweight internal information (e.g., code coverage feedback). |

---

### 4. Test Oracle vs. Test Input Generation

Automated techniques can target either (or both) components of a test case:

- **Generating the Test Oracle** (what to check) -- covered later in the course:
  - Differential Testing
  - Metamorphic Testing
  - Property-based Testing
- **Generating the Test Input** (what values to feed) -- the main focus of AST 1:
  - Fuzzing (black-box, grey-box)
  - Grammar-based fuzzing
  - Symbolic execution (white-box)

---

### 5. Automated Test Oracle Generation (Preview)

Three key techniques covered later in the AST module:

#### 5.1 Differential Testing
- Feed the **same input** `I` to **multiple implementations** (SUT1, SUT2, SUT3, ...) of the same specification
- Compare their outputs (O', O'', O''')
- **Oracle:** If results match, likely correct. If results mismatch, a bug exists in at least one implementation.

#### 5.2 Metamorphic Testing
- Run program `P` on original input `I` to get output `O`
- Derive a **follow-up input** `I'` from `I` using a known transformation
- Run `P` on `I'` to get `O'`
- **Oracle:** Check whether the relationship between `O` and `O'` matches the expected **metamorphic relation**

#### 5.3 Property-based Testing
- Define a **general property** that should hold for all inputs (e.g., "for all lists L, L.size() >= 0")
- **Automatically generate** random inputs using a generator
- Run many trials checking the property
- If a failure is found, optionally **shrink** the input to find a minimal counterexample

---

### 6. Generation vs. Mutation (Input Generation Strategies)

Two fundamental strategies for producing test inputs:

| Strategy | How it works | Example |
|----------|-------------|---------|
| **Generation-based** | Generate input cases **from scratch** | Build a random arithmetic expression from a grammar |
| **Mutation-based** | Start with **initial seeds** that are then mutated | Take `<h2>header</h2>` and mutate to `<h2>header<</h2>` |

---

### 7. Black-box Input Generation

#### 7.1 Naive (Black-box) Fuzzing

- Feed **completely random data** (e.g., `U39p5jwo3jsasdf...`) to the program
- Look for crashes (e.g., SEGFAULT)
- **Advantage:** Extremely simple and efficient
- **Disadvantage:** Unlikely to uncover deep bugs, especially with complex input formats
  - Example: A PNG parser checks a magic number field first -- random bytes almost never pass this initial check

#### 7.2 Input Generators / Program Generators

Instead of purely random bytes, use a **structured generator** that understands the input format:

```python
import random

OPERATORS = ["+", "-", "*", "/"]
NUMBERS = list(range(1, 10))

def generate_expression(depth=0, max_depth=3):
    """Generate a random arithmetic expression as a string."""
    # Base case: just return a number
    if depth >= max_depth or random.random() < 0.3:
        return str(random.choice(NUMBERS))

    # Recursive case: combine two expressions with an operator
    left = generate_expression(depth + 1, max_depth)
    right = generate_expression(depth + 1, max_depth)
    op = random.choice(OPERATORS)

    return f"({left} {op} {right})"
```

**Real-world example -- Csmith:**
- Random **program** generator for C
- Challenge: must generate valid programs free of undefined behavior
- Found **hundreds of bugs** in production C compilers (GCC, LLVM, etc.)

---

### 8. Grammar-based Fuzzing

A more structured form of generation-based fuzzing.

#### Key Idea
Use a **grammar** (which specifies the syntactic structure of a language) to generate valid inputs.

#### Grammar Components

| Component | Definition | Example |
|-----------|-----------|---------|
| **Terminals** | Actual symbols in the final output string | `"0"`, `"1"`, `"+"`, `"*"` |
| **Non-terminals** | Abstract placeholders to be expanded | `<expr>`, `<term>`, `<digit>` |
| **Production rules** | Rules for replacing non-terminals with terminals or other non-terminals | `<expr> ::= <term> \| <expr> "+" <term>` |

#### Example: Context-Free Grammar (CFG) for Arithmetic

```
<expr>   ::= <term> | <expr> "+" <term> | <expr> "-" <term>
<term>   ::= <factor> | <term> "*" <factor> | <term> "/" <factor>
<factor> ::= <number> | "(" <expr> ")"
<number> ::= <digit> | <digit> <number>
<digit>  ::= "0" | "1" | ... | "9"
```

#### How Grammar-based Fuzzing Works

The fuzzer **randomly expands** all production rules from the root until no more non-terminals remain.

**Walkthrough example** generating `0 * 19`:

```
Step 1: <expr>                          -- start with root non-terminal
Step 2: <term>                          -- expand <expr> -> <term>
Step 3: <term> * <factor>               -- expand <term> -> <term> "*" <factor>
Step 4: 0 * <factor>                    -- expand left <term> -> <factor> -> <number> -> <digit> -> "0"
Step 5: 0 * <factor>                    -- now expand right <factor>
Step 6: 0 * 19                          -- expand <factor> -> <number> -> <digit> <number> -> "1" "9"
```

#### ANTLR Grammars
- Real-world grammars (e.g., PostgreSQL) are available as ANTLR grammar files
- These can be used to generate syntactically valid inputs for complex languages like SQL

---

### 9. Mutation-based Fuzzing

Rather than generating inputs from scratch, **mutate existing inputs**.

- Start with a **seed corpus** (collection of valid sample inputs)
- Apply small random mutations (bit flips, byte insertions, deletions, etc.)

| Aspect | Detail |
|--------|--------|
| **Advantage** | Higher-quality inputs (assuming a diverse seed corpus), more likely to pass initial validity checks |
| **Disadvantage** | Requires seed inputs to mutate |

**Example:**
```
Original:  <h2>header</h2>
Mutated:   <h2>header<</h2>    (inserted an extra '<')
```

---

### 10. White-box Fuzzing

Uses **full internal information** (source code, execution paths) to guide input generation.

#### Symbolic Execution / Concolic Testing

Core idea:
1. **Start** with a concrete input
2. **Execute** the program and gather **path constraints** along the way
3. **Systematically negate** constraints and solve them with a **constraint solver** to explore new paths

#### Satisfiability Modulo Theories (SMT)

White-box fuzzing relies on **SMT solvers** (e.g., Z3):

| Concept | Detail |
|---------|--------|
| **What they do** | Solve formulas based on a given theory (e.g., Linear Integer Arithmetic) |
| **Input** | A logical formula, e.g., `(a > 5) AND (a != 6)` |
| **Output** | A model (assignment) satisfying the formula, e.g., `a = 7` |
| **Complexity** | NP-hard; for many theories, undecidable |

#### SAGE (Microsoft)
- White-box fuzzer employed at Microsoft
- Found **1/3 of bugs** discovered by fuzzing during Windows 7 development
- Approach: start with concrete input, execute, gather path constraints, negate & solve

#### KLEE
- Symbolic execution engine (open-source)
- Works on LLVM bitcode
- Systematically explores program paths

---

### 11. Grey-box Fuzzing

A practical middle ground: uses **some** internal information, typically **code coverage feedback**.

#### How Grey-box Fuzzing Works (Simplified Scheme)

```
                   Mutate & Execute
[Fuzzer] ---------> [Instrumented Program] ---------> [Execution Feedback]
   ^                                                         |
   |                                                    Crash? --Y--> Report
   |                                                         |
   |  Pick input                                        New coverage?
   |  for mutation                                           |
   |                                                    Y    |
[Queue of Inputs] <------------------------------------------+
```

1. **Pick** an input from the queue
2. **Mutate** it and run on the **instrumented** program
3. Collect **execution feedback** (coverage info)
4. If **crash** -- report the bug
5. If **new coverage** achieved -- add the mutated input to the queue for further mutation
6. Repeat

#### American Fuzzy Lop (AFL / AFL++)

- The most well-known grey-box fuzzer
- **Lightweight instrumentation** for coverage tracking
- **Puts more energy** into further mutating inputs that cover **new code paths**
- Impressively powerful: starting from a simple "hello" text file, AFL can generate **valid JPEG images** within ~6 hours just by mutation + coverage feedback

---

### 12. Summary: Classification of Techniques

| Dimension | Options |
|-----------|---------|
| **What to generate** | Test Oracle vs. Test Input |
| **Information used** | Black-box / Grey-box / White-box |
| **Input strategy** | Generation-based vs. Mutation-based |

#### Mapping Techniques to Categories

| Technique | Oracle/Input | Box Type | Gen/Mutation |
|-----------|-------------|----------|-------------|
| Naive Black-box Fuzzing | Input | Black-box | Generation |
| Input/Program Generators (Csmith) | Input | Black-box | Generation |
| Grammar-based Fuzzing | Input | Black-box | Generation |
| Mutation-based Fuzzing | Input | Black-box | Mutation |
| Grey-box Fuzzing (AFL) | Input | Grey-box | Mutation |
| Symbolic Execution (SAGE, KLEE) | Input | White-box | Generation |
| Differential Testing | Oracle | Black-box | -- |
| Metamorphic Testing | Oracle | Black-box | -- |
| Property-based Testing | Oracle + Input | Black-box | Generation |

---
---

## AST 2 - Differential Testing

### 1. What is Differential Testing?

**Core Idea:** Send the **same input** to **multiple systems** that implement the same specification, and check whether their results (or other properties) **agree**.

- If results **match** --> likely correct
- If results **mismatch** --> a bug exists in **at least one** of the systems

```
                    Differential Testing

                 +--------+     +----+
            +--> |  SUT1  | --> | O' |
            |    +--------+     +----+
  +---+     |                            +--------+    Results match   --> OK
  | I | ----+--> +--------+     +-----+  |        |
  +---+     |    |  SUT2  | --> | O'' |--| Oracle |
            |    +--------+     +-----+  |        |    Results mismatch --> BUG
            +--> +--------+     +------+ +--------+
                 |  SUT3  | --> | O''' |
                 +--------+     +------+

  I: Input    O: Output    SUT: Software Under Test
```

**Key characteristics:**
- Also known as **A/B testing** or **N-version testing**
- **Black-box** approach -- no internal knowledge of any system needed
- Can be applied to both **functional** and **non-functional** testing
- Provides **both** a test oracle and test input (the oracle is "outputs should agree"; inputs can be generated with any technique)

> **Motivation:** Even when "All Unit Tests Passed!", bugs may still lurk. Differential testing can find bugs that hand-written unit tests miss by exploring a much larger input space.

---

### 2. What to Compare? (Four Strategies)

| Strategy | Description | Key Example |
|----------|-------------|-------------|
| **Different versions** | Compare old (base/golden) vs. new version | Regression testing |
| **Different configurations** | Same software, different settings | Compiler `-O0` vs. `-O3` |
| **Against a reference engine** | Compare production system vs. simplified oracle | Google Spanner vs. MiniSpanner |
| **Different implementations** | Independent implementations of same spec | GCC vs. Clang, MySQL vs. SQLite |

---

#### 2.1 Comparing Different Versions

- Check whether an **updated version** has not changed the behavior of a known-working **base** (a.k.a. **golden**) version
- This is essentially a form of **regression testing**
- **Disadvantage:** A behavioral change might be **intentional** (new feature, bug fix), leading to false alarms

**Differential Testing at Google (Industry Practice):**
- Adopted by **hundreds of teams** at Google
- Survey findings:
  - Typical usage: **1,000 to 1 million** input records
  - **70%** use a version known to be good as the base (e.g., production or "golden" version)
  - **60%** use the changed version as the test version

---

#### 2.2 Comparing Different Configurations

Many software systems provide multiple configurations that should produce the same result:

| System | Configurations to Compare |
|--------|--------------------------|
| **Compilers** | Different optimization levels (`-O0` vs. `-O3`) |
| **Database systems** | Different storage engines (MyISAM vs. InnoDB) |

**Compiler example:**
```bash
# Compile the same program with different optimization levels
gcc -O0 program.c -o program_O0
gcc -O3 program.c -o program_O3

# Run both and compare outputs
./program_O0 > output_O0.txt
./program_O3 > output_O3.txt

# Difference found! output_O0.txt: 100, output_O3.txt: 101 --> BUG in optimizer!
```

**Database example:**
```sql
-- Same table structure, different engines
CREATE TABLE myisam_table (...) ENGINE=MyISAM;
CREATE TABLE innodb_table (...) ENGINE=InnoDB;

-- Insert same data, then query
SELECT count(*) FROM myisam_table;  -- 1000
SELECT count(*) FROM innodb_table;  -- 1001  --> BUG!
```

---

#### 2.3 Comparing Against a Reference Engine

Build a **simplified implementation** that acts as the test oracle.

**Example: Google's Spanner database**
- Google built **MiniSpanner**, an in-memory emulator of Cloud Spanner
- MiniSpanner implements only Spanner's **data model** (schema, tables, indexes)
- It **does not** implement concurrency, distribution, replication, or low-level storage
- Purpose: answer "if we apply this series of mutations and run this query, what should the results be?"
- A lightweight, correct-by-construction oracle to catch bugs in the complex production system

---

#### 2.4 Comparing Different Implementations

Many domains have **multiple independent implementations** of the same specification:

| Domain | Implementations |
|--------|----------------|
| **Compilers** | GCC, Clang/LLVM, MSVC |
| **Database systems** | MySQL, SQLite, PostgreSQL |
| **Operating systems** | Linux, macOS, Windows |
| **Web browsers** | Firefox, Chrome, Safari |
| **AI assistants** | ChatGPT, Gemini, Claude |
| **Common algorithms** | Merge sort, Bubble sort, Quicksort |

---

### 3. Case Study: Differential Testing of XML Processors

- Multiple XML processors support the **XPath** query language (e.g., BaseX, Saxon, eXist)
- The XPath standard is **well specified**, making it ideal for differential testing
- Differential testing found close to **30 bugs**

**Example:** Given an XML document with books, the XPath query `//*[@id*(-1)<2]` produced:
| Processor | Result |
|-----------|--------|
| BaseX | `{}` (empty) -- incorrect |
| Saxon | Books 1, 2, 3 |
| eXist | Books 1, 2, 3 |

The mismatch revealed a bug in BaseX.

---

### 4. Case Study: Differential Testing of Database Engines

- Hundreds of database engines exist, SQL is the most common query language
- Core SQL features are supported by many engines, enabling differential testing

**Key challenge:** Only about **10% of SQL statements are common** across different DBMSes.

A generator using only common features would overlook **>96% of bugs** found by DBMS-specific approaches.

**Examples of legitimate differences across DBMSes (not bugs, but false alarms):**

```sql
-- Escape character handling
mysql>    SELECT '\a' LIKE '\a';  -- true
postgres> SELECT '\a' LIKE '\a';  -- false (different escape rules)

-- Integer division
mysql>    SELECT 5/2;  -- 2.5000 (float division)
postgres> SELECT 5/2;  -- 2      (integer division)
sqlite>   SELECT 5/2;  -- 2      (integer division)

-- Type comparison
mysql>    SELECT 1 = '1';  -- true  (implicit cast)
postgres> SELECT 1 = '1';  -- true  (implicit cast)
sqlite>   SELECT 1 = '1';  -- false (no implicit cast)
```

These are **intended differences**, not bugs -- but they produce false alarms in differential testing, which must be filtered out.

---

### 5. Case Study: Differential Testing of Compilers (Csmith)

**Csmith** is a random **program generator** for C, used to test compilers via differential testing.

- Manually written, rule-based generator
- Found **hundreds of bugs** in production C compilers (GCC, LLVM, etc.)

**The Undefined Behavior Challenge:**

In C, certain operations (e.g., signed integer overflow) trigger **undefined behavior**, which makes the entire program/execution meaningless. A program with undefined behavior can produce *any* output, so output differences don't indicate compiler bugs.

```c
int a = ...;
int b = ...;
return a + b;  // If this overflows, undefined behavior!
```

**Csmith's solution:** Ensure programs are **free of undefined behavior by construction**. For example, wrap integer operations in safe wrappers that check for overflow before performing the operation:

```c
int a = ...;
int b = ...;
return safe_add(a, b);  // Checks for overflow first
```

**Real-world impact:** Csmith + yarpgen (another C program generator) continue to find bugs -- e.g., comparing random programs compiled with different compilers against GCC and finding mismatches.

---

### 6. Case Study: Differential Testing of DRL Libraries

**Deep Reinforcement Learning (DRL)** libraries (e.g., TensorFlow, PyTorch-based libraries) implement the same algorithms (DQN, PPO) but may produce different results.

**Approach:**
1. Feed the **same environment + algorithm + hyperparameters** to multiple DRL libraries
2. Train agents in each library
3. Compare their **human-normalized scores**

**Handling Non-determinism:** DRL training is inherently non-deterministic, so **statistical techniques** (e.g., stratified bootstrapping) are used instead of exact output comparison.

**Findings:** Implementations assumed to be interchangeable actually differ significantly in performance, revealing implementation bugs and inconsistencies.

---

### 7. Advantages and Challenges

#### Advantages
- **Simple and effective** -- conceptually straightforward, yet powerful in practice
- Finds bugs that traditional testing misses
- Scales well with automated input generation

#### Challenges

| Challenge | Explanation |
|-----------|------------|
| **Small overlap / false alarms** | Different systems may have intentionally different behavior, producing false alarms that need manual analysis |
| **Coincidental correctness** | Multiple systems might produce the **same incorrect result** (e.g., due to shared libraries or common misunderstandings of the spec) |
| **Non-deterministic output** | Systems with randomness, concurrency, or floating-point differences make exact comparison difficult |
| **Valid input generation** | For some domains, generating valid inputs is hard (e.g., C programs free of undefined behavior) |

---

### 8. Summary

| Aspect | Detail |
|--------|--------|
| **What it does** | Sends the same input to multiple systems and checks if outputs agree |
| **Oracle** | Output disagreement = bug in at least one system |
| **Box type** | Black-box |
| **Comparison strategies** | (1) Different versions, (2) Different configurations, (3) Reference engine, (4) Different implementations |
| **Key strength** | Simple, effective, finds bugs missed by unit tests |
| **Key weakness** | False alarms from intended differences; misses bugs when all systems agree on wrong answer |

---
---

## AST 3 - Metamorphic Testing

### 1. Motivation: Limitations of Differential Testing

Differential testing requires **multiple systems** that implement the same specification. But what if:

| Problem | Description |
|---------|-------------|
| **No other systems exist** | Only one implementation available -- nothing to compare against |
| **"Untestable" systems** | Systems like machine translation or AI classifiers where no clear single correct answer exists, so even comparing outputs across systems is not meaningful |

Metamorphic testing solves both problems: it needs **only a single system** and does **not** require knowing the exact correct output.

---

### 2. What is Metamorphic Testing?

**Core Idea:** Use a **source test input** (and its result) to generate a **follow-up test input** for which the relationship between the outputs is **known**, even if the exact outputs are not.

- Involves a **metamorphic relation (MR)** -- a known relationship between inputs and outputs
- **Black-box** approach -- no internal knowledge of the system needed

```
                 Metamorphic Testing

  +-----+                        +-----+
  |  I  |----+                +--|  O  |
  +-----+    |   +-------+   |  +-----+
              +-->|       |---+            Oracle
  +-----+    |   |   P   |   |  +-----+   for each
  | I'  |----+   |       |---+--| O'  |   other
  +-----+        +-------+     +-----+
  (derived
   follow-up
   input)

  I: Source input    I': Follow-up input (derived from I)
  O: P(I)            O': P(I')
  P: Software Under Test
  Oracle: Check whether O and O' satisfy the metamorphic relation
```

**Key distinction from differential testing:**
- Differential testing: same input, **multiple systems**, compare outputs directly
- Metamorphic testing: **one system**, different (related) inputs, compare outputs via a **relation**

---

### 3. Metamorphic Relation (MR)

A **metamorphic relation** is a property that relates:
- The **source input** `I` and **follow-up input** `I'`
- The **source output** `O = P(I)` and **follow-up output** `O' = P(I')`

**Formal structure:**
1. **Input transformation:** How to derive `I'` from `I`
2. **Output relation:** The expected relationship between `O` and `O'`

If the output relation is **violated**, a bug is detected.

---

### 4. Example: Neural Network Image Classifier

| Step | Detail |
|------|--------|
| **Source input** | Original image of a person |
| **Source output** | Label: PERSON, Score: 0.98 |
| **Transformation** | Rotate image, adjust brightness |
| **Follow-up input** | Transformed image (still a person) |
| **Follow-up output** | Label: PERSON, Score: 0.95 |
| **MR Check 1** | Labels should match (PERSON == PERSON) |
| **MR Check 2** | Scores should be close (0.98 ≈ 0.95) |
| **Result** | MR verified -- system PASS |

**Why this is powerful:** We don't need to know the *correct* label for every image. We only need to know that small visual transformations **should not change the classification**.

---

### 5. Example: Search Engine

| Step | Detail |
|------|--------|
| **Source input (TC1)** | Search query: `"Apples"` |
| **Source output** | ~15,000,000 results (C1) |
| **Transformation** | Add a restrictive keyword: `"Green Apples"` |
| **Follow-up input (TC2)** | Search query: `"Green Apples"` |
| **Follow-up output** | ~4,000,000 results (C2) |
| **MR (Inequality)** | `Count(TC2) <= Count(TC1)`, i.e., C2 <= C1 |
| **Check** | 4,000,000 <= 15,000,000 --> PASS |

**If violated (C2 > C1):** Adding a more specific keyword should **never** increase the result count -- a violation indicates a bug.

**Why this is powerful:** We don't need to know the *exact* correct number of results for "Green Apples" (the oracle problem). We only verify a **consistency property**.

---

### 6. Test Oracle + Test Input Generation

Metamorphic testing generates **both** components of a test case:

| Component | How it's generated |
|-----------|-------------------|
| **Test Oracle** | The metamorphic relation serves as the property to verify |
| **Test Input** | The follow-up test case is inferred from the source test case (which can come from any source, e.g., randomly generated) |

---

### 7. Real-World Usage: Cockroach Labs (CockroachDB)

Cockroach Labs uses metamorphic testing extensively for their distributed database:

> *"We employ a slew of testing methodologies and techniques. [...] One such technique, known as **metamorphic testing**, deserves more attention. We're **happy to vouch for it, given the number of obscure bugs it has helped us catch**. [...] **Many of the underlying bugs could never have been dreamt up during development**. Many are very subtle edge cases, exercised with obscure database configurations. **Many have been severe, and would have resulted in corruption or data loss** if they had made their way into a production binary."*

**Key takeaway:** Metamorphic testing finds subtle, severe bugs that developers would never anticipate through manual test writing.

---

### 8. Case Study: EMI (Equivalence Modulo Inputs) for Compiler Testing

**Equivalence Modulo Inputs (EMI)** is one of the most successful metamorphic testing approaches, having found **hundreds of bugs** in GCC and LLVM.

#### 8.1 The Challenge

- Generating diverse **fully equivalent programs** (programs that behave the same for *all* inputs) is very difficult
- **EMI's insight:** Instead of creating programs equivalent for *all* inputs, create programs that are **equivalent only for a given input** -- which is much easier

#### 8.2 EMI Approach

```
Step 1: Compile and run program P with input I, record output O = P(I)
Step 2: Mutate the UNEXECUTED part of P, creating program P'
        (P' may differ from P in general, but behaves the same for input I)
Step 3: Verify that P(I) = P'(I)
        If not equal --> compiler bug!
```

**Why mutate only the unexecuted part?**
- The unexecuted code does not affect the output for input `I`
- So `P` and `P'` must produce the same output for `I`
- But the compiler must still handle the unexecuted code correctly during optimization

#### 8.3 EMI Illustration (with CFG diagrams)

Think of programs as **control-flow graphs**. Given input `I`:
- **Green nodes** = executed path
- **Red nodes** = unexecuted path

EMI mutates the red (unexecuted) nodes to create variant programs. All variants must produce output `O` for input `I`.

#### 8.4 Detailed EMI Example: Common Subexpression Elimination (CSE)

**Original program:**
```c
int func(int a, int b, int c) {
    if (c) {
        return a + b + 5;    // `a + b` appears here
    } else {
        return a + b + 3;    // `a + b` appears here too
    }
}
```

**Compiler optimization (CSE):** The compiler notices `a + b` is a **common subexpression** in both branches, and may optimize it to:
```c
int func(int a, int b, int c) {
    int temp = a + b;     // Compute once
    if (c) {
        return temp + 5;  // Reuse
    } else {
        return temp + 3;  // Reuse
    }
}
```
Every optimization could possibly introduce bugs.

**EMI step -- identify unexecuted code:**
- Assume `a = 1, b = 2, c = 3`
- Since `c = 3` is truthy, the `else` branch is **not executed**

```c
int func(int a, int b, int c) {
    if (c) {
        return a + b + 5;    // EXECUTED
    } else {
        return a + b + 3;    // NOT EXECUTED -- safe to mutate
    }
}
```

**EMI step -- mutate the unexecuted part:**
```c
int func(int a, int b, int c) {
    if (c) {
        return a + b + 5;    // Unchanged
    } else {
        return a + c + 3;    // Mutated: changed `b` to `c`
    }
}
```

**Why this finds bugs:**
- After mutation, `a + b` is no longer a common subexpression across both branches
- CSE optimization **might no longer be applicable** (or might be applied incorrectly)
- If the compiler's CSE optimization has a bug, it might still wrongly apply CSE, producing incorrect code
- The mutated program must still output `8` (= 1 + 2 + 5) for input `(1, 2, 3)` -- if it doesn't, the compiler has a bug

#### 8.5 Why EMI Works

| Reason | Explanation |
|--------|-------------|
| **Compiler must handle all code** | The compiler generates code that produces the correct result for *any* input, and optimizes both executed and unexecuted parts |
| **Even simple mutations work** | Already by only *deleting* unexecuted code, EMI can find bugs in various compiler optimizations |
| **Diverse source tests help** | The more diverse the input programs, the more optimization paths are exercised |

---

### 9. Strengths and Weaknesses

#### Strengths

| Strength | Explanation |
|----------|-------------|
| **Only needs one system** | Avoids the main weakness of differential testing (requiring multiple systems) |
| **Works for "untestable" systems** | Applicable to AI/ML, search engines, and other systems with no clear expected output |
| **Simple and effective** | Easy to implement, assuming the metamorphic relation is straightforward |
| **Generates both oracle and input** | The MR is the oracle; the follow-up input is derived automatically |

#### Weaknesses

| Weakness | Explanation |
|----------|-------------|
| **Finding good MRs is hard** | Identifying a metamorphic relation that is both valid and bug-revealing requires domain knowledge and creativity |
| **Consistent incorrectness** | Overlooks bugs where the system acts **consistently incorrect** (e.g., if `P(I)` and `P(I')` are both wrong but satisfy the MR) |

---

### 10. Metamorphic Testing vs. Differential Testing

| Aspect | Differential Testing | Metamorphic Testing |
|--------|---------------------|-------------------|
| **Systems needed** | Multiple (2+) | Single |
| **Oracle** | Output agreement across systems | Metamorphic relation between outputs |
| **Input** | Same input to all systems | Source input + derived follow-up input |
| **Exact output needed?** | No (just agreement) | No (just relation) |
| **Main challenge** | Finding/building comparable systems | Finding bug-revealing MRs |
| **Blind spot** | All systems agree on wrong answer | System consistently wrong in a way that satisfies MR |

---

### 11. Summary

| Aspect | Detail |
|--------|--------|
| **What it does** | Derives follow-up inputs from source inputs and checks whether outputs satisfy a known metamorphic relation |
| **Oracle** | The metamorphic relation (MR) |
| **Box type** | Black-box |
| **Key technique** | EMI (Equivalence Modulo Inputs) -- mutate unexecuted code, verify same output |
| **Key strength** | Works with a single system; applicable to "untestable" systems |
| **Key weakness** | Finding good MRs is difficult; misses consistently incorrect behavior |

---
---

## AST 4 - Property-based Testing (PBT)

### 1. Motivation: Generalizing Differential & Metamorphic Testing

Differential testing checks for **consensus** (same input, multiple systems, outputs should agree). Metamorphic testing checks **relational rules** (one system, related inputs, outputs satisfy a relation). Property-based testing **unifies and generalizes** both approaches under a single framework:

| Approach | What it checks | PBT Perspective |
|----------|---------------|-----------------|
| **Unary Property** | A property that holds for any single input (e.g., `list.size() >= 0`) | A property over one input |
| **Metamorphic Relation** | A relation between outputs of related inputs (e.g., image + noise -> same label) | A property over two related inputs/outputs |
| **Differential Equality** | Agreement across multiple systems (e.g., app consensus) | A property comparing outputs of multiple systems |

> **Key Insight:** All three are just **logical invariants (properties)** that should hold for all inputs. PBT defines the general logical property and provides the test data via generators.

---

### 2. What is Property-based Testing?

**Definition:** Specify **properties** (invariants) for the component we can test, and let the test framework try to find a **counterexample** that causes the property to break (i.e., falsify it).

PBT generates **both** components of a test case:

| Component | How PBT handles it |
|-----------|-------------------|
| **Test Oracle** | The *property* specified by the developer |
| **Test Input** | Generated by a *framework* (based on input data types or by a custom *generator* specified by the developer) |

---

### 3. How PBT Works (4 Steps)

```
1. DEFINE GENERAL PROPERTY
   Property: for all List L, L.size() >= 0

2. AUTOMATIC INPUT GENERATION
   Random Number Generator --> various linked lists:
     A: Empty List
     B: 1-Node List
     C: 4-Node List
     D: Very Large List

3. EXECUTE TEST LOOP
   Trial #1: Empty list   --> size() = 0  --> Assert >= 0 --> PASS
   Trial #2: 1-node list  --> size() = 1  --> Assert >= 0 --> PASS
   Trial #3: 4-node list  --> size() = 4  --> Assert >= 0 --> PASS
   ...
   Trial #N: large list   --> size() = 1000+ --> Assert >= 0 --> PASS
   TRIALS COMPLETED: 1000+

4. RESULTS AND OPTIONAL SHRINKING
   If all pass: ALL TESTS PASS
   If a failure occurs: SHRINK the failing input to find a
                         minimal counterexample --> BUG FOUND
```

---

### 4. History and Practice

#### Origins: QuickCheck for Haskell
- Pioneered by **Koen Claessen** and **John Hughes** (Chalmers University)
- Implemented in **~300 lines of code**
- Lightweight tool for random testing of Haskell programs
- Properties are described as Haskell functions

#### Industry Adoption (Jane Street Study)
- **30 interviews** with developers at **Jane Street** (financial tech firm)
- Key insights:
  - PBT is used primarily for testing **components of complex systems**
  - Especially effective in **"high-leverage" scenarios** where properties are easy to identify and test
  - Provides **greater confidence** than conventional example-based unit tests
  - Also useful to **communicate the specification** to other developers

#### Frameworks
- **jqwik** -- Property-based testing framework for **Java**
  - One of many PBT frameworks available
  - Interfaces may differ significantly between frameworks (unlike unit testing frameworks)
- Other frameworks: Hypothesis (Python), QuickCheck (Haskell/Erlang), fast-check (JavaScript), etc.

---

### 5. Example: Testing `Math.abs(x)`

#### Setting up the property

- **Function under test:** `Math.abs(x)`
- **Inputs:** any integer
- **Property:** `|x| >= 0` (absolute value is always non-negative)

Mathematical definition of absolute value:
```
|x| = x,   if x >= 0
|x| = -x,  if x < 0
```

#### jqwik Code

```java
@Property
void positiveAbs(@ForAll int val) {
    assertTrue(Math.abs(val) >= 0);
}
```

- `@Property` -- marks this as a property-based test
- `@ForAll` -- tells jqwik to generate random values for this parameter

#### The Surprise: A Counterexample!

jqwik finds a **failure** with input `val = -2147483648` (`Integer.MIN_VALUE`):

```
timestamp = 2024-03-17T15:08:01, TestAbs:positiveAbs =
  java.lang.AssertionError

tries = 149         | # of calls to property
checks = 149        | # of not rejected calls
generation = RANDOMIZED
edge-cases#mode = MIXIN    | edge cases are mixed in
edge-cases#total = 9       | # of all combined edge cases
edge-cases#tried = 3       | # of edge cases tried in current run

Sample
------
  arg0: -2147483648
```

**Why does this fail?** In Java, `Math.abs(Integer.MIN_VALUE)` returns `Integer.MIN_VALUE` (a negative number!) due to **two's complement integer overflow**. There is no positive 32-bit integer representation of 2147483648.

#### Edge Cases Are Key

jqwik automatically mixes in **generic boundary values** (edge cases):
```
-2, -1, 0, 2, 1, -2147483648, -2147483647, 2147483647, 2147483646
```

This is why PBT is powerful -- it systematically tests edge cases that developers might not think of.

#### Fix: Constrain the Input Domain

Use `@IntRange` to exclude the problematic value:

```java
@Property
void positiveAbs(@ForAll @IntRange(min = Integer.MIN_VALUE + 1,
                                    max = Integer.MAX_VALUE) int val) {
    assertTrue(Math.abs(val) >= 0);
}
```

> **Takeaway:** Frameworks like jqwik provide various ways to **adapt existing generators** or **create new ones** to constrain the input domain.

---

### 6. Example: Testing `CountWords.count()`

#### The Property

`CountWords.count("Surprising storms stress restless riders.")` returns `4` (words starting with 's' or 'r').

**Property idea:** The sum of `count()` applied to each individual word should equal `count()` applied to the complete string.

```
CountWords.count("Surprising storms stress restless riders.")  = 4

CountWords.count("Surprising")  = 0
CountWords.count("storms")      = 1
CountWords.count("stress")      = 1
CountWords.count("restless")    = 1
CountWords.count("riders.")     = 1
                                  ---
                                   4  (matches!)
```

#### Naive Approach (Problem with Default Generator)

```java
@Property
void countWordsNaive(@ForAll String val) {
    assertEquals(CountWords.count(val),
        Stream.of(val.split(" ")).mapToInt(s ->
            CountWords.count(s)).sum());
}
```

**Problem:** Default random string generator produces meaningless strings (random Unicode characters). For random strings, it is **unlikely** that the method returns a non-zero value -- the test passes trivially without exercising interesting behavior.

#### Better Approach: Custom Generator

```java
@Property
void countWordsBetter(@ForAll("countWordStrings") String val) {
    assertEquals(CountWords.count(val),
        Stream.of(val.split(" ")).mapToInt(s ->
            CountWords.count(s)).sum());
}

@Provide
Arbitrary<String> countWordStrings() {
    return Arbitraries.strings().withCharRange('a', 'z').whitespace()
            .ofMinLength(0).ofMaxLength(100);
}
```

- `@Provide` defines a custom generator named `"countWordStrings"`
- Generates strings with only **lowercase letters** and **whitespace** (length 0-100)
- This makes the generated inputs **much more meaningful** for testing word counting

> **Lesson:** Generators often need **careful consideration**. A poorly chosen generator may cause tests to pass trivially without exercising the property meaningfully.

---

### 7. Example: Testing Google Translate (Properties for "Untestable" Systems)

PBT can be applied even to systems where the exact correct output is unknown (the "oracle problem").

#### Property 1: Round-trip Translation Similarity

```
"I love learning about software engineering"
        |
        v  [English to Chinese]
"我喜欢学习软件工程"
        |
        v  [Chinese to English]
"I like studying software engineering"

Property: round-trip translation results in a
          SEMANTICALLY SIMILAR sentence
```

The original and round-tripped sentences should be semantically similar (not necessarily identical).

#### Property 2: Idempotency

```
"I love learning about software engineering"   "I love learning about software engineering"
        |                                               |
        v  [English to Chinese]                         v  [English to Chinese]
   "我喜欢学习软件工程"                              "我喜欢学习软件工程"

Property: translating the SAME text multiple times
          should result in IDENTICAL translations
```

Translating the same input multiple times should always produce the same output.

> **Note:** These are examples of using PBT properties (which are essentially metamorphic relations / consistency properties) to test systems where you cannot verify exact correctness.

---

### 8. PBT as a Generalization

PBT is a **generalization** of both differential testing and metamorphic testing:

| Testing Technique | PBT Property Equivalent |
|-------------------|------------------------|
| **Differential Testing** | Property: `f1(x) == f2(x)` for all x (consensus across systems) |
| **Metamorphic Testing** | Property: `relation(f(x), f(transform(x)))` holds for all x |
| **Unary Property** | Property: `invariant(f(x))` holds for all x (e.g., `abs(x) >= 0`) |

All three can be expressed as properties in a PBT framework, with appropriate generators.

---

### 9. Strengths and Weaknesses

#### Strengths

| Strength | Explanation |
|----------|-------------|
| **Generalizes other techniques** | Unifies differential testing, metamorphic testing, and direct invariant checking |
| **Automates both oracle and input** | The property is the oracle; the generator produces inputs |
| **Edge case discovery** | Frameworks automatically test boundary values (e.g., `MIN_VALUE`, `0`, `MAX_VALUE`) |
| **Shrinking** | When a failure is found, the framework reduces the input to a **minimal counterexample** |
| **Greater confidence** | Tests many more cases than hand-written example-based tests |
| **Communicates specification** | Properties serve as executable documentation of expected behavior |

#### Weaknesses

| Weakness | Explanation |
|----------|-------------|
| **Writing good properties is hard** | Requires creativity and domain knowledge (harder than writing example-based tests) |
| **Generator design is critical** | Poor generators lead to trivial tests that don't exercise meaningful behavior |
| **Not a replacement for example-based tests** | Example-based techniques are simpler and require less creativity to automate |
| **Incomplete oracle** | Properties may not capture all aspects of correctness |

---

### 10. Summary

| Aspect | Detail |
|--------|--------|
| **What it does** | Checks invariants (called properties) by generating many random inputs and verifying the property holds for all of them |
| **Oracle** | The property specified by the developer |
| **Input generation** | Automatic via generators (built-in or custom) with edge case injection |
| **Box type** | Black-box |
| **Key mechanism** | Define property + generator --> run many trials --> if failure found, shrink to minimal counterexample |
| **Relationship to others** | Generalization of differential testing and metamorphic testing |
| **Key strength** | Finds edge-case bugs missed by example-based tests; provides high confidence |
| **Key weakness** | Writing good properties and generators requires creativity and domain expertise |
| **When to use** | Complex functionality where example-based tests may be insufficient; "high-leverage" scenarios |
| **Frameworks** | QuickCheck (Haskell), jqwik (Java), Hypothesis (Python), fast-check (JS) |
