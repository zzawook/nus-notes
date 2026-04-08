# CS3213 — Course Summary

This lecture is the course wrap-up. It does two things: (1) zoom out and remind us *what software engineering as a discipline is about*, with reference to a current snapshot of the field including AI/LLM-assisted development, and (2) characterize what *great* software engineers actually look like, drawing on Microsoft's well-known empirical study and on Andrej Karpathy's recent commentary on the new shape of the engineering job.

It ties the technical content of the course (testing, debugging, AST-based analysis) back to a humanistic frame: the techniques exist to support human engineers doing inherently difficult cognitive work, and that work is being reshaped by AI tooling.

---

## 1. Software Engineering — Where We Are

Software engineering is the discipline of producing software that is correct, maintainable, and useful, in teams, under uncertainty, at scale. The field is structured around a set of recurring tasks. A useful taxonomy comes from a Systematic Literature Review (SLR) of SE tasks:

- **Requirements** — eliciting, specifying, analyzing, validating.
- **Design & Architecture** — module decomposition, interfaces, patterns, trade-offs.
- **Construction (Coding)** — writing code, code review.
- **Testing** — unit, integration, system, acceptance, automated test generation, fuzzing.
- **Debugging** — fault localization, slicing, scientific method.
- **Maintenance & Evolution** — refactoring, migration, technical debt.
- **Project Management** — planning, estimation, risk, quality assurance.
- **Collaboration & Process** — version control, code review, CI/CD, agile/lean.

The course this semester covered, in depth, the *testing*, *debugging*, and *AST-based* slices of this taxonomy, plus the cross-cutting question of *what makes good code*.

### 1.1 SE and AI — The New Layer

The newest layer of the field is the use of large language models and agentic tools to *do* parts of the engineer's job. The lecturer explicitly references:

- **Andrej Karpathy** (founding member of OpenAI, former director of AI at Tesla) and his commentary on how programming is changing — "programming is the highest-leverage skill" and the rise of "vibe coding" (sketching by intent, letting an LLM produce the code).
- **LLM-assisted IDEs and agentic coding tools** — Cursor, Copilot, Claude Code, Windsurf, Aider. These let a developer state intent in natural language and have an agent edit, run, and debug code.
- The shift in *what* the engineer does: less mechanical typing, more *judgment* — choosing what to build, evaluating LLM output for correctness, debugging when the LLM is confidently wrong, and integrating generated pieces into a coherent design.

The course's testing/debugging content matters *more*, not less, in this world: when an LLM produces code, you still need testing, fault localization, and analytical thinking to be sure the code is correct.

---

## 2. What Makes a Great Software Engineer? — The 2015 Microsoft Study

The lecture's centerpiece is a 2015 empirical study by Li, Ko, and Begel ("What Makes a Great Software Engineer?"). They interviewed 59 experienced engineers across many Microsoft teams to identify the attributes that distinguish *great* engineers from merely competent ones.

The study identified **four clusters** of attributes, each with several specific traits:

### 2.1 Personal Characteristics

- **Improving** — actively seek to grow; reflect on mistakes; pick up new technologies; never satisfied with current skill level.
- **Passionate** — genuinely care about software and about the craft; enthusiasm sustains them through tedious work.
- **Open-minded** — willing to entertain ideas different from their own; will change their mind on the basis of evidence; not threatened by being shown wrong.
- **Data-driven** — make decisions based on numbers, telemetry, experiments rather than gut feel.

### 2.2 Decision Making

- **People knowledge** — understand the strengths, weaknesses, motivations, and contexts of teammates and customers; route problems to the right person; communicate in terms the audience can absorb.
- **Mental models** — maintain accurate, simplified internal representations of the system, the codebase, and how parts interact; can simulate consequences of changes mentally.
- **Sees the forest *and* the trees** — fluently switches between high-level architectural concerns and low-level details; never gets lost in either direction.
- **Handles complexity** — does not collapse under systems with many moving parts; decomposes, isolates, and tackles complexity systematically.

### 2.3 Team Mates

- **Creates shared context** — makes sure the team has a common understanding of goals, designs, and decisions; over-communicates rather than under-communicates.
- **Creates shared success** — frames wins as team wins; lifts other engineers; celebrates collective accomplishment.
- **Creates a safe haven** — psychological safety; teammates can ask "dumb" questions or admit not knowing without fear; willing to be wrong in public.
- **Honest** — direct, gives truthful feedback (both good and bad), does not posture, owns mistakes.

### 2.4 Software Product

- **Elegant** — values simplicity, clarity, and readability of code and design; prefers fewer moving parts.
- **Creative** — finds non-obvious solutions; reframes problems.
- **Anticipates needs** — thinks ahead about what users, teammates, and future maintainers will need; designs for evolution.

> Exam-relevant detail: there are exactly **four clusters**, and within them, the lecture lists exactly four traits each. You should be able to recall the cluster headings, the per-cluster traits, and explain at least one in detail with an example.

The trait that the lecture emphasizes most is **mental models**: great engineers maintain a more accurate, more nuanced internal model of the system than average engineers, and this is what lets them debug faster, design better interfaces, and predict the consequences of changes. Building accurate mental models is a teachable skill — through reading code, drawing diagrams, asking "what if" questions, and writing out invariants.

---

## 3. Karpathy's Four Skills for the New Era

Building on the Microsoft study, the lecture references Andrej Karpathy's recent talks on what skills matter for software engineers in an LLM-powered world. He highlights four:

1. **Problem decomposition** — breaking a vague, large problem into smaller, well-defined subproblems that can be solved (or asked of an LLM) one at a time. This is the most important skill because LLMs are bad at large amorphous problems but very good at small well-specified ones.
2. **System thinking** — keeping the whole system in mind: data flow, control flow, dependencies, edge cases, failure modes. The opposite of reading code line-by-line; you must hold the architecture in your head simultaneously.
3. **Taste / judgment** — the ability to look at a piece of generated code (or any code) and recognize whether it is good or bad. Taste is not principles, it is *trained pattern recognition* from seeing many systems and knowing which choices age well. Without taste, an engineer cannot evaluate LLM output and will accept code that "looks right" but is actually subtly wrong.
4. **Debug & verification** — being able to take a piece of code or a system, understand why it does *not* work as intended, isolate the cause, and know how to verify that it now does. This is the entire content of our debugging unit (scientific method, fault localization, slicing) and it becomes *more* important when LLMs introduce confidently-wrong code.

> Exam-relevant: be able to map each of Karpathy's four skills onto something we covered in the course. Problem decomposition ↔ design and modular testing; system thinking ↔ AST-based analysis and architecture; taste ↔ code review and "elegant" trait from the Microsoft study; debug & verification ↔ the entire debugging unit.

---

## 4. How the Course's Topics Fit Together

The course was structured around three pillars:

| Pillar | Lectures | Connection to "Great Engineer" |
| --- | --- | --- |
| **Software Testing (ST)** | Black-box, white-box, in-class | Anticipating needs, data-driven, handles complexity |
| **Debugging (DBG)** | Scientific method, fault localization, slicing, test-case reduction | Mental models, sees forest and trees, debug & verification |
| **AST-based Techniques (AST)** | Differential testing, metamorphic testing, property-based testing | System thinking, creative, elegant |

Across all three pillars, the engineer's job is to:

1. **Generate evidence** about software (tests, traces, slices).
2. **Reason** under uncertainty about that evidence (fault localization, scientific method).
3. **Update their mental model** of the system based on the evidence.
4. **Make decisions** (what to fix, what to test, what to refactor).

This is the loop. Every technique we learned is a tool to make some part of the loop tighter, more rigorous, or cheaper.

---

## 5. The Role of AI in the Course's Topics

The lecture explicitly walks through how each course topic interacts with current LLM/AI tooling.

**Testing.** LLMs can generate test cases (sometimes good, sometimes superficial). Property-based testing pairs especially well with LLMs because the LLM can suggest properties and a Hypothesis-style framework can refute or confirm them. **But**: an LLM-generated test suite that always passes is *not* evidence the code is correct; it might just be evidence the LLM trained on similar code.

**Debugging.** LLMs can suggest fault localization and even propose fixes, but they hallucinate. The scientific method (hypothesis, prediction, experiment, conclusion) is the discipline that protects you from being misled. Slicing and fault-localization metrics give *grounded* signals that an LLM cannot fake.

**AST-based techniques.** Differential testing and metamorphic testing become *easier* with LLMs as test-input generators, but the mathematical correctness of the metamorphic relations and the soundness of the differential oracles remain a human responsibility.

> The recurring theme: **AI helps you do more, but it does not relieve you of judgment.** The engineer who understands testing, debugging, and program analysis is *more* valuable in an LLM-assisted world, not less.

---

## 6. Closing Themes the Lecturer Emphasizes

- **Engineering is fundamentally a human, judgmental, social activity.** Tools change, the human core does not.
- **Mental models are the central engineering asset.** Cultivate them deliberately.
- **Taste compounds.** Read good code. Compare alternatives. Develop opinions and revise them.
- **Debug & verify is your most durable skill.** It will outlast every framework, language, and AI tool.
- **The Microsoft study's traits are *learnable*.** None of them is innate. Improving, open-minded, data-driven, honest, elegant — all of these are habits you can choose.

---

## 7. Cheat-Sheet

**Microsoft "Great Engineer" Clusters & Traits.**

- *Personal Characteristics*: Improving, Passionate, Open-minded, Data-driven.
- *Decision Making*: People knowledge, Mental models, Forest & trees, Handles complexity.
- *Team Mates*: Shared context, Shared success, Safe haven, Honest.
- *Software Product*: Elegant, Creative, Anticipates needs.

**Karpathy's Four Skills.** Problem decomposition · System thinking · Taste/judgment · Debug & verification.

**Course pillars.** Testing · Debugging · AST-based techniques.

**Big claim.** The course's content matters *more* in the AI era, because LLMs accelerate *production* but not *judgment*, and judgment is what testing, debugging, and program analysis develop.
