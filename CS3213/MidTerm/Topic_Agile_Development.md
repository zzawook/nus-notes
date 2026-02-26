# Agile Development

---

# Part 1: Software Process Models, Plan-Driven and Agile Software Engineering

---

## 1. Central Software Engineering Activities

Every software engineering process involves four fundamental activities:

| Activity | Description |
|---|---|
| **Software Specification** | The functionality of the software and constraints on its operation **must be defined** |
| **Software Development** | The software to meet the specification **must be produced** |
| **Software Validation** | The software **must be validated** to ensure that it does what the customer wants |
| **Software Evolution** | The software **must evolve** to meet changing customer needs |

---

## 2. Software Processes

- **Software process**: A set of related activities that leads to the production of a software system
- **Software process model**: A simplified representation of a software process (e.g., sequence of activities)
  - Used to illustrate different approaches to developing software
- **No silver bullet!** — There is no single best process model for all situations

---

## 3. Three Important Software Process Models

The three major software process models are:

1. **Waterfall Model**
2. **Incremental Development**
3. **Integration and Configuration (Reuse-based Development)**

---

### 3.1 Waterfall Model

The Waterfall Model is a **sequential, plan-driven** process with distinct phases flowing downward like a waterfall.

#### Phases (in order):

```
Requirements Definition
    → System and Software Design
        → Implementation and Unit Testing
            → Integration and System Testing
                → Operation and Maintenance
```

Each phase must be completed before the next one begins. There are feedback arrows allowing return to the previous phase.

#### Origin — Winston W. Royce (1970)

The Waterfall Model originates from Dr. Winston W. Royce's 1970 paper *"Managing the Development of Large Software Systems."*

**Key quote from Royce:**
> "[...] the implementation [of the waterfall model] described above is **risky and invites failure**. [...] The testing phase which occurs at the end of the development cycle is the first event for which timing, storage, input/output transfers, etc., are experienced as distinguished from analyzed."

**Important:** Royce himself recognized the pure waterfall as risky — he actually proposed iterative improvements to it. The model is often misunderstood as his recommended approach.

#### Use Cases for Waterfall

The Waterfall Model is appropriate for:
- **Embedded systems** (e.g., fax machines, traffic management, security systems, printers, televisions, audio systems, ovens, ultrasound machines)
- **Safety-critical systems** (e.g., railway systems)
- Systems where requirements are **well-understood and stable** before development begins

---

### 3.2 Incremental Development

Incremental Development interleaves specification, development, and validation activities. The system is developed as a series of versions (increments).

#### How It Works:

```
Outline Description
    → [Concurrent Activities]
        ↔ Specification
        ↔ Development
        ↔ Validation
    → Initial Version
    → Intermediate Versions
    → Final Version
```

The three activities (Specification, Development, Validation) happen **concurrently** and feed back into each other, producing successive versions of the system.

#### Pros:
- **Cost of accommodating change** and responding to changing customer requirements is **lower**
- **More rapid delivery** of software

#### Cons:
- Process is **less measurable** (for managers)
- **Degrading system structure** (refactoring required over time)

---

### 3.3 Integration and Configuration (Reuse-Based Development)

#### Key Idea:
- **Observation:** Many existing (open-source) systems, libraries, frameworks exist
- **Idea:** Focus the process on **re-using them**, rather than developing software from scratch
- This approach is called **"reuse-based development"**

#### Process Flow:

```
Requirements Specification
    → Software Discovery + Software Evaluation
        → Requirements Refinement
            → [Decision Point]
                If Application System Available:
                    → Configure Application System
                If Components Available:
                    → Adapt Components
                    → Develop New Components
            → Integrate System
```

#### Pros:
- **Reduces the amount of software** to be developed, which has advantages in terms of **cost and managing risk**
- **Faster delivery** of software

#### Cons:
- **Requirements compromises** (may need to adjust requirements to fit existing components)
- **Control over system evolution** is lost or might be difficult

---

## 4. Summary of the Three Models

| Aspect | Waterfall | Incremental | Reuse-Based |
|---|---|---|---|
| **Approach** | Sequential phases | Concurrent activities, iterative | Assemble from existing components |
| **Flexibility** | Low | High | Medium |
| **Speed** | Slow | Faster | Fastest |
| **Best for** | Stable requirements, safety-critical | Evolving requirements | Available reusable components |
| **Main risk** | Late discovery of issues | Structural degradation | Requirements compromises |

**Key takeaway:** Software process models specify how essential software engineering activities are executed. Each has distinct advantages and disadvantages.

---

## 5. Plan-Driven vs. Agile

There are **two general approaches** to developing software: **plan-driven** and **agile**.

### 5.1 Plan-Driven Development

```
Requirements Engineering → Requirements Specification (document) → Design and Implementation
        ↑                                                                    |
        └──────────── Requirements Change Requests ←────────────────────────┘
```

- Motto: **"Plan your work, work your plan."**
- Focuses on producing a **high-quality Software Requirements Specification (SRS)**, based on which the system is developed
- All activities are **planned in advance**
- Progress is measured against the plan

### 5.2 Agile Development

```
Requirements Engineering ↔ Design and Implementation
     (continuous cycle)
```

- Motto: **"Inspect and adapt."** / **"Responding to change over following a plan."**
- Requirements engineering and design/implementation happen in a **continuous, interleaved cycle**
- Focuses on producing **value (working software) quickly**
- Accounts for **changing requirements**

---

## 6. Software Requirements Document (SRS)

In plan-driven development, the **Software Requirements Specification (SRS)** is a key artifact containing:

- **User requirements**
- **System requirements**

### SRS Document Structure:

| Chapter | Description |
|---|---|
| **Preface** | Defines the expected readership of the document and describes its version history, including a rationale for the creation of a new version and a summary of changes made in each version |
| **Introduction** | Describes the need for the system. Should briefly describe the system's functions and explain how it will work with other systems. Should also describe how the system fits into the overall business or strategic objectives of the organization commissioning the software |
| **Glossary** | Defines the technical terms used in the document. Should not make assumptions about the experience or expertise of the reader |
| **User Requirements Definition** | Describes the services provided for the user. Nonfunctional system requirements should also be described. May use natural language, diagrams, or other notations understandable to customers. Product and process standards that must be followed should be specified |
| **System Architecture** | Presents a high-level overview of the anticipated system architecture, showing the distribution of functions across system modules. Architectural components that are reused should be highlighted |
| **System Requirements Specification** | Describes the functional and nonfunctional requirements in more detail. If necessary, further detail may also be added to the nonfunctional requirements. Interfaces to other systems may be defined |
| **System Models** | Includes graphical system models showing relationships between system components and the system and its environment. Examples: object models, data-flow models, or semantic data models |
| **System Evolution** | Describes the fundamental assumptions on which the system is based, and any anticipated changes due to hardware evolution, changing user needs, etc. Useful for avoiding design decisions that constrain likely future changes |
| **Appendices** | Provide detailed, specific information related to the application (e.g., hardware and database descriptions). Hardware requirements define minimal and optimal configurations. Database requirements define logical organization of data and relationships |
| **Index** | Several indexes may be included — alphabetic index, index of diagrams, index of functions, etc. |

---

## 7. Agile Development

### 7.1 What is Agile?

Agile is both a **mindset** and **concrete management and engineering frameworks**:

- Focus on **delivering value** in increments and/or avoiding waste
- **Feedback loops** and customer involvement
- **Adaptive** to changing requirements
- **Lightweight** processes
- **Empowering teams** and people
- Inspired by **lean manufacturing principles**
- A lot of buzzwords and certifications

### 7.2 Historic Development

**1980s and early 1990s:** There was a widespread view that the best way to achieve better software was through:
- **Careful project planning**
- **Formalized quality assurance**
- Use of **analysis and design methods** supported by software tools
- **Controlled and rigorous software development processes**

**Quote from Jeff Sutherland (co-creator of Scrum):**
> "Up to that point — and even as late as 2005 — most software development projects were created using the Waterfall method, where a project was completed in distinct stages and moved step by step toward ultimate release to consumers or software users. The process **was slow, unpredictable, and often never resulted in a product that people wanted or would pay to buy**. **Delays** of months or even years were endemic to the process. The early step-by-step plans, laid out in comforting detail in Gantt charts, reassured management that we were in control of the development process — but almost without fail, we would fall quickly behind schedule and disastrously over budget."

---

## 8. The Agile Manifesto (2001)

Published in 2001 at [agilemanifesto.org](https://agilemanifesto.org/)

### The Four Core Values:

> "We are uncovering better ways of developing software by doing it and helping others do it. Through this work we have come to value:"

| **Valued More (Left)** | | **Valued Less (Right)** |
|---|---|---|
| **Individuals and interactions** | over | processes and tools |
| **Working software** | over | comprehensive documentation |
| **Customer collaboration** | over | contract negotiation |
| **Responding to change** | over | following a plan |

> *"That is, while there is value in the items on the right, we value the items on the left more."*

**Important nuance:** The Agile Manifesto does **NOT** say documentation, processes, contracts, or plans are worthless. It says the items on the left are valued **more**. Agile software development **can also involve** architectural planning and modeling (e.g., the C4 model).

---

## 9. The 12 Agile Principles

### Principles (1/3):
1. Our highest priority is to **satisfy the customer** through **early and continuous delivery of valuable software**
2. **Welcome changing requirements**, even late in development. Agile processes harness change for the customer's competitive advantage
3. **Deliver working software frequently**, from a couple of weeks to a couple of months, with a preference to the shorter timescale
4. **Business people and developers must work together daily** throughout the project

### Principles (2/3):
5. **Build projects around motivated individuals**. Give them the environment and support they need, and trust them to get the job done
6. The most efficient and effective method of conveying information to and within a development team is **face-to-face conversation**
7. **Working software** is the primary measure of progress
8. Agile processes promote **sustainable development**. The sponsors, developers, and users should be able to maintain a constant pace indefinitely

### Principles (3/3):
9. Continuous attention to **technical excellence and good design** enhances agility
10. **Simplicity** — the art of maximizing the amount of work not done — is essential
11. The best architectures, requirements, and designs emerge from **self-organizing teams**
12. At regular intervals, **the team reflects on how to become more effective**, then tunes and adjusts its behavior accordingly

---

## 10. Agile in Practice — Survey Data

### 10.1 State of Agile Report (17th Edition, 2023)

- Yearly survey studying the adoption of agile methods
- Survey conducted in 2023 based on **788 survey respondents**
- Top industry: Technology (26%), followed by Financial Services (11%)

### 10.2 Organizations' Software Engineering Priorities

| Priority | Percentage |
|---|---|
| **End-customer satisfaction** | **43%** |
| **Time to delivery** | **39%** |
| **Competitive advantage** | **34%** |
| Product revenue increase | 27% |
| Market expansion goals | 27% |
| New customer acquisition | 26% |
| Budget | 26% |
| Total company revenue increase | 25% |
| Product adoption increase | 21% |
| Company adoption increase | 11% |

### 10.3 Agile Usage

| Methodology | Usage |
|---|---|
| **Agile** (ongoing release cycles, small incremental changes) | **71%** |
| **Hybrid** (combination of any) | **42%** |
| **DevOps** (Dev and Ops teams work together) | **36%** |
| Iterative | — |
| Spiral | 3% |
| Custom process | 10% |
| None of these | 1% |

> "Just **over 70% of survey takers report using Agile** [...], while 42% said their organizations use a **hybrid model** that includes Agile, DevOps, or other choices."

### 10.4 Perceived Advantages of Agile

| Advantage | Percentage |
|---|---|
| **Increased collaboration** | **59%** |
| **Better alignment to business needs** | **57%** |
| **Better work environment** | **36%** |
| **Better quality software delivered** | **25%** |
| Increased visibility across the SDLC | 21% |
| Faster response to competitive threats | 18% |
| Better user experience | 14% |
| Better customer service | 13% |
| Increased time for innovation | 13% |
| Increased product adoption | 9% |
| Increased revenue | 8% |
| Something else | 3% |

### 10.5 Agile at Microsoft (Research Study)

A study titled *"Have Agile Techniques been the Silver Bullet for Software Development at Microsoft?"* surveyed developers on perceived benefits of Agile.

**Agile Devs: Perceived Benefits** (majority said "Yes"):
- Improved communication
- Aware of others' work
- Team coordination
- Short iterations
- Flexibility
- Better code quality
- Predictability
- Faster
- Increased customer focus
- Less process overhead

---

## 11. Summary and Key Points

### On Software Process Models:
- **Software process models** specify how essential software engineering activities are executed
- Three important and common models with distinct advantages and disadvantages:
  - **Waterfall Model** — sequential, plan-driven, best for stable requirements
  - **Incremental Development** — iterative, flexible, rapid delivery
  - **Integration and Configuration** — reuse-focused, fast but may compromise requirements

### On Plan-Driven vs. Agile:
- At a high level, you can choose between a **plan-driven** and **agile** approach to software engineering
  - **Plan-driven models** focus on producing a high-quality SRS, based on which the system is developed
  - **Agile approaches** account for changing requirements by focusing on producing value (e.g., working software) quickly
- The **Agile Manifesto** specifies the key agile principles

### Main Sources and References:
- *Software Engineering* (10th Edition) by Ian Sommerville (Pearson)
- *Scrum: The Art of Doing Twice the Work in Half the Time* by Jeff Sutherland

---
---

# Part 2: Agile Origins

---

## 1. Origins of Agile and Lean Methods

Agile and lean methods in software engineering trace their roots to two key sources:

1. **"The New New Product Development Game" (1986)** — by Hirotaka Takeuchi and Ikujiro Nonaka (Harvard Business Review)
2. **Toyota Production System (TPS)** — by Taiichi Ohno

---

## 2. The New New Product Development Game (1986)

### 2.1 Background

- Published by **Hirotaka Takeuchi** and **Ikujiro Nonaka** in the Harvard Business Review
- Subtitle: *"Stop running the relay race and take up rugby"*
- Argued that the traditional **sequential ("relay race")** approach to product development was too slow and inflexible

### 2.2 Three Types of Development Processes

The paper identified three types of product development approaches:

| Type | Description |
|---|---|
| **Type A** | Sequential phases — each phase is completed before the next begins (like a relay race). Phases are discrete and non-overlapping |
| **Type B** | Overlapping phases — phases overlap at the boundaries, with some concurrency between adjacent phases |
| **Type C** | Highly integrated and overlapping phases — all phases overlap extensively, with the team working as a unit across all phases simultaneously (like a rugby "Scrum") |

- **Type A** = traditional sequential/waterfall approach
- **Type C** = the new "rugby" approach — fast, flexible, and **integrated** — this inspired the name **"Scrum"**

### 2.3 Key Characteristics of the New Approach

- **Built-in instability**: Management only signals a broad goal, providing the team freedom to realize it
- **Subtle control**: Checkpoints exist, but rigid control is avoided
- **Self-organizing project teams**
- **Focus on learning** and transfer of learning within the company

### 2.4 Commonalities with Agile Methods

- The **"rhythm" in stages** is reflected in **Scrum Sprints** or **feedback loops in Extreme Programming**
- This paper **inspired and gave the name to "Scrum"**
- Focus on **empowering people and teams** over following processes
- **Cross-functional teams**
- **Feedback loops** and focus on learning

---

## 3. Toyota Production System (TPS)

### 3.1 Overview

- Developed by **Taiichi Ohno** at Toyota
- Its success is rooted in a special system that began what is now known as **"Lean Manufacturing"**
- Known as a **"lean manufacturing system"** or **"Just In Time" (JIT) system**
  - **Lean**: Focus on **creating value** and **avoiding waste**

### 3.2 Key Concepts

| Concept | Description |
|---|---|
| **Just-in-Time (JIT)** | A pull-based production system where production depends on customer demand |
| **Jidoka** | Automation with a human touch |
| **Kaizen** | Continuous improvement of the process |

### 3.3 Push vs. Pull Systems

**Traditional (Push-based) system:**
- Cars were produced in **large batches irrespective** of whether they would be sold
- Production is driven by **forecasts**, not actual demand

```
Push System:
  Information Flow → (forecast-driven)
  Manufacturing → Material Flow → Demand
```

**TPS (Pull-based) system:**
- Production **depends on customer demand**
- Each process produces **only what is needed** for the next process in a **continuous flow**

```
Pull System:
  ← Information Flow (demand-driven)
  Manufacturing → Material Flow → Demand
```

---

## 4. TPS: Just-in-Time and Kanban

### 4.1 Kanban Board

- **Kanban** = "signal board" or "billboard"
- If a process needs another input, the process transfers a **Kanban card** to the preceding process, asking it to produce the input
- Visualized in modern software development as a **Kanban board** with columns (e.g., Backlog, Up Next, In Progress, On Hold, Done)

### 4.2 Kanban in Software Context

- Work items are represented as **cards** on a board
- Cards flow through columns representing stages of work
- **Work-in-progress (WIP) limits** control the flow
- Enables visual management and continuous delivery

---

## 5. TPS: Jidoka

- Can be loosely translated as **"automation with a human touch"**
- Key mechanisms:
  - **Andon Cord/Button**: A mechanism that allows any worker to **stop the production line** when a problem is detected
  - **Kaizen**: **Continuous improvement** of the process

### Jidoka Cycle (Daily Improvements):

```
A situation deviates from the normal workflow
    → A machine detects a problem and communicates it
        → The line is stopped
            → Manager/supervisor removes cause of the problem
                → Improvements incorporated into the standard workflow
                    → Good products can be produced
```

---

## 6. TPS and Agile

The connection between TPS and agile software development:

- **Lean manufacturing** inspired **"lean software development"**
- **Kanban** has been adopted as an **agile method** for software development
- **Introspection and continuous improvement** (Kaizen) is part of agile processes as well as **DevOps**

---

## 7. "Waste" in Software Engineering

An alternative, more modern interpretation of waste: **hardship in our daily work**

| Type of Waste | Description |
|---|---|
| **Partially done work** | Work that is started but not completed |
| **Extra processes** | Processes that do not add value to the customer |
| **Extra features** | Features not needed by the organization or the customer |
| **Task switching** | People working on multiple projects simultaneously |
| **Waiting** | Waiting for any input required to finish the current work |
| **Motion** | Handoffs that require communication |
| **Defects** | Software defects or unclear information |

---

## 8. Summary and Key Points

- Many modern management frameworks for software engineering (e.g., **Scrum** and **Kanban**) stem from **product development** and **car manufacturing**
- Includes principles such as **empowering people**, **cross-functional teams**, **continuous improvement**, and having **feedback loops**
- Concepts like **"Waste"**, **"Lean"**, and **"Scrum"** were adopted to a software engineering context

---
---

# Part 3: Agile Frameworks — Scrum

---

## 1. What is Scrum?

- A **general, lightweight framework** for project management
- **63%** of team-level Agile users follow the Scrum methodology (State of Agile Report, 17th Edition, 2024) — the most popular Agile methodology since the first survey in 2006
- **Prescriptive**: proposes specific rules that need to be followed
- **Focus**: delivering value via **increments** in **sprints**
- Founded on **empiricism** and **lean thinking**:
  - **Empiricism**: knowledge comes from experience and decision making based on observations
  - **Lean thinking**: reduce waste and focus on the essentials

---

## 2. Scrum Pillars and Values

### 2.1 Three Pillars (Foundation of Trust)

| Pillar | Description |
|---|---|
| **Transparency** | The process and work must be visible to those performing the work and those receiving the work |
| **Inspection** | The Scrum artifacts and progress toward agreed goals must be inspected frequently to detect problems |
| **Adaptation** | If any aspects of a process deviate outside acceptable limits, the process must be adjusted |

### 2.2 Five Scrum Values

| Value | Description |
|---|---|
| **Courage** | Scrum Team members have courage to do the right thing and work on tough problems |
| **Focus** | Everyone focuses on the work of the Sprint and the goals of the Scrum Team |
| **Commitment** | People personally commit to achieving the goals of the Scrum Team |
| **Respect** | Scrum Team members respect each other to be capable, independent people |
| **Openness** | The Scrum Team and its stakeholders agree to be open about all the work and the challenges with performing the work |

---

## 3. Scrum Overview (Framework Flow)

```
Product Backlog Refinement → Product Backlog (with Product Goal)
    → Sprint Planning → Sprint Goal + Sprint Backlog
        → Scrum Team (Dev + SM + PO) works during Sprint
            → Daily Scrum (daily)
        → Increment (with Definition of Done)
            → Sprint Review
                → Sprint Retrospective (feeds back into next Sprint)
```

---

## 4. Scrum Team

The Scrum Team consists of three roles with **no hierarchy** — it is **self-sufficient and self-managing**:

| Role | Accountability |
|---|---|
| **Scrum Master** | Accountable for establishing and facilitating the Scrum process |
| **Product Owner** | Accountable for maximizing the value of the product |
| **Developers** | Focusing on creating an *Increment* each *Sprint* |

---

## 5. Scrum Events

Scrum defines five events:

### 5.1 Sprint

- The **heartbeat of Scrum**
- Goal: create a useful **Increment**
- A Sprint includes Sprint Planning, Daily Scrums, Sprint Review, Sprint Retrospective, and Product Backlog redefinition as needed

### 5.2 Sprint Planning

- **Goal**: lay out the work for the sprint in a **Sprint Goal**
- Product Owner ensures that the most important Product Backlog items are discussed
- Key questions:
  - *"If we ran out of time and this would be our last Sprint, what is the one thing we still need to do to ensure we deliver value?"*
  - *"What is the one crucial thing the team would swarm on so it can be achieved within the Sprint?"*

### 5.3 Daily Scrum

- **Short standup meeting** at the same time every day
- Focus on **Sprint Goal** and an actionable plan for the next day of work
- Adapt the **Sprint Backlog** and adjust the plan if necessary

### 5.4 Sprint Review

- Scrum Team **presents their results** to key stakeholders and progress towards the Product Goal is discussed
- **Collaborate** on what to do next and adjust Product Backlog if necessary

### 5.5 Sprint Retrospective

- **Plan ways to increase quality and effectiveness**
- **Inspect** how well the Sprint went and discuss potential changes

---

## 6. Scrum Artifacts

Scrum defines three artifacts:

### 6.1 Product Backlog

- **Ordered list** of what is needed to improve the product
- **Product Goal**: long-term objective for the Scrum Team
- For Sprint Planning, items can be directly selected if they can be done within one sprint

### 6.2 Sprint Backlog

- A plan **by and for the developers** to achieve the Sprint Goal
- Contains the Sprint Goal, selected Product Backlog items, and the plan for delivering them

### 6.3 Increment

- A **concrete stepping point** towards the Product Goal
- Must be **usable and verifiable**
- **Definition of Done**: created by the organization or Scrum Team — defines when an Increment is considered complete

---

## 7. Timeboxing

All Scrum events are **timeboxed** — they have maximum durations:

| Phase | Duration |
|---|---|
| **Sprint** | 1 month or less |
| **Sprint Planning** | Up to 8 hours |
| **Daily Scrum** | 15 minutes |
| **Sprint Review** | Up to 4 hours |
| **Sprint Retrospective** | Up to 3 hours |

---

## 8. Summary and Key Points

- Scrum is a **general, lightweight project management framework**
- Focus: **delivering value**
- Structured in **Sprints**
- **Self-sufficient and self-managing teams** with no hierarchy
- **Prescriptive framework** with defined:
  - **Roles**: Scrum Master, Product Owner, and Developers
  - **Events**: Sprint, Sprint Planning, Daily Scrum, Sprint Review, Sprint Retrospective
  - **Artifacts**: Product Backlog, Sprint Backlog, Increment

### Main Sources and References:
- *The Scrum Guide* (November 2020) by Ken Schwaber & Jeff Sutherland
- State of Agile Report (17th Edition)

---
---

# Part 4: Agile Frameworks — Kanban

---

## 1. What is Kanban?

- A **visual system** for managing work as it moves through a process
- Originated from the **Toyota Production System** (TPS) — adapted to software engineering
- Focuses on **optimizing flow** of work items through the system
- Less prescriptive than Scrum — does not define specific roles, artifacts, or timeboxing
- Works well for **small teams** (even individual contributors)

---

## 2. Kanban Board

### 2.1 Structure

- **Columns** represent each **stage** (state) of the process (e.g., Backlog, Up Next, In Progress, On Hold, Done)
- **Cards** represent **work items** — they move across columns as work progresses

### 2.2 Types of Kanban Boards

| Type | Description |
|---|---|
| **Digital Kanban Board** | Software tools like Trello, GitHub Projects, Jira — cards with metadata, labels, assignments |
| **Physical Kanban Board** | Sticky notes on a wall or whiteboard — visible to the whole team in a shared space |

### 2.3 Example Columns (GitHub Projects — CockroachDB)

```
Triage → Active → 25.1 Release → 25.2 Release → Bugs to Fix → Backlog → Cold Storage → Done
```

---

## 3. Flow

- **Flow**: the movement of potential value through a system
- Kanban **optimizes flow**
- Enforces a **Work in Progress (WIP) limit** — the maximum number of items allowed in a given stage at any time

### Example WIP Limits:

```
Backlog → Creating (WIP: 3) → Review (WIP: 2) → Publishing (WIP: 1) → Done
```

- WIP limits prevent overloading any stage and reduce **task switching**
- If a stage is at its WIP limit, no new items can enter until an existing item moves forward

---

## 4. Definition of Workflow (DoW)

The **Definition of Workflow (DoW)** is an explicit shared understanding of flow, which requires a definition of:

- One or more **defined states** (visually represented as stages) that the work items flow through from started to finished
  - Including policy on how work items can flow through each state
- **Units of value**, which are referred to as **work items** (or items)
- When work items are **started** and **finished** within the workflow
- A definition of how **WIP will be controlled** from started to finished

---

## 5. Kanban and Scrum

| Aspect | Scrum | Kanban |
|---|---|---|
| **Work structure** | Development done in **Sprints** (timeboxed iterations) | **Constant flow** — no iterations required |
| **Prescriptiveness** | More prescriptive — defines roles, artifacts, timeboxing | Less prescriptive — focuses on flow and WIP limits |
| **Team size** | Typically small teams (3-9) | Works well for small teams (even >0 persons / individuals) |
| **Roles** | Scrum Master, Product Owner, Developers | No prescribed roles |
| **Key mechanism** | Sprint cycle with events | WIP limits and flow optimization |

---

## 6. Summary and Key Points

- **Visual system** with columns ("state") and cards ("work item")
- Key concept of **flow**: work items "flow" through the different states
- **Definition of Workflow** defines the flow of the system as well as a **work in progress limit (WIP limit)**
- Less prescriptive than Scrum — no defined roles, timeboxes, or specific artifacts
- Can be combined with Scrum ("Scrumban") or used standalone

### Main Sources and References:
- The Kanban Guide (kanbanguides.org)
- *Agile Project Management with Kanban* — Eric Brechner (Talks at Google)

---
---

# Part 5: Extreme Programming (XP)

---

## 1. What is Extreme Programming (XP)?

- A very **influential agile method**, developed in the **late 1990s**, that introduced a range of agile development techniques
- **Software-development focused**, in contrast to Scrum and Kanban (which are more management-focused)
- Takes an **"extreme" approach to iterative development and best practices**
- Key quote: *"There are only two hard things in Computer Science: cache invalidation and naming things."* — Phil Karlton

---

## 2. XP Planning/Feedback Loops

XP is structured around nested **planning and feedback loops** at different timescales:

```
Release plan ────────── Months
    → Iteration plan ──── Weeks
        → Acceptance test ── Days
            → Stand-up meeting ── One day
                → Pair negotiation ── Hours
                    → Unit test ── Minutes
                        → Pair programming ── Seconds
                            → Code
```

Each loop provides feedback that flows back up to inform the larger loops.

---

## 3. Key XP Practices

### 3.1 Pair Programming

- All code is produced by **two developers working together** at the same computer
- Uses a **Driver/Navigator** model:
  - **Driver**: the person typing / controlling the keyboard
  - **Navigator**: the person reviewing, thinking ahead, and guiding strategy
- Analogous to a driver and co-pilot in a car

#### Pair Programming at Microsoft (2008 Study)

**22%** of engineers used pair programming.

**Top Benefits** (number of mentions):

| Rank | Benefit | Mentions |
|---|---|---|
| 1 | Fewer Bugs | 66 |
| 2 | Higher Quality Code | 48 |
| 3 | Spreads Code Understanding | 42 |
| 4 | Can Learn from Partner | 42 |
| 5 | Better Design | 30 |
| 6 | Constant Code Reviews | 22 |
| 6 | Two Heads are Better than One | 22 |
| 8 | Creativity and Brainstorming | 17 |
| 9 | Better Testing and Debugging | 14 |
| 10 | Improved Morale | 13 |

**Top Problems** (number of mentions):

| Rank | Problem | Mentions |
|---|---|---|
| 1 | Cost efficiency | 79 |
| 2 | Scheduling | 31 |
| 3 | Personality clash | 25 |
| 4 | Disagreements | 24 |
| 5 | Skill differences | 22 |
| 6 | Programming style differences | 13 |
| 7 | Hard to find a partner | 12 |
| 8 | Personal style differences | 11 |
| 9 | Distractions | 10 |
| 10 | Misanthropy | 9 |

### 3.2 Collective Code Ownership

- **Any programmer can change any code** in the project
- **Everyone is responsible** for all the code

### 3.3 Unit Tests / Test-Driven Development (TDD)

- XP proposed **test-driven development (TDD)**
- XP suggests to test for **all potential cases** where the code could fail
- **TDD Cycle**:
  1. **Write a failing test**
  2. **Make the test pass** (write minimal code)
  3. **Refactor** (improve code while keeping tests green)
- Advantages:
  - Helps development by addressing **a test case at a time**
  - Facilitates other practices like Continuous Integration, Collective Code Ownership, Refactoring

### 3.4 Acceptance Tests

- Derived from the **user requirements**
- Written together with the customer (as **user stories**)

### 3.5 Continuous Integration

- Development team should always work on the **latest version** of the software
- Code should be integrated into the **main branch** of the project **frequently**
- Facilitated by **unit tests**
- Today, often automatically run in frameworks such as **GitHub Actions** or **Jenkins**

### 3.6 Simple Design & Refactoring

- XP mandates a **"simple is best"** mentality
- Applies both to **system design** and **code** that is written
- **Refactoring** as part of the lifecycle of a project to keep the project **maintainable and easier to extend**

### 3.7 Other XP Practices

- **On-site customer**: A representative of the end-user of the system (the customer) should be available **full time** for the use of the XP team
- **Sustainable pace**: Large amounts of overtime are **not considered acceptable** as the net effect is often to reduce code quality and medium term productivity

---

## 4. XP and Scrum

| Aspect | Scrum | XP |
|---|---|---|
| **Focus** | **Agile management mechanisms** (roles, events, artifacts) | **Software engineering practices** (coding, testing, design) |
| **Approach** | Incremental and feedback-driven | Incremental and feedback-driven |
| **Adoption** | Practices are Scrum-specific | Various XP practices have been adopted in other agile and plan-driven approaches |

- Both XP and Scrum are **incremental and feedback-driven**
- Key difference: Scrum focuses on **agile management mechanisms**, while XP focuses on **software engineering practices**
- Various practices of XP have been adopted in other agile, as well as plan-driven approaches (e.g., continuous integration, pair programming, test-driven development)

---

## 5. Summary and Key Points

- XP, unlike Scrum or Kanban, established **agile software development practices**
- Key practices include:
  - **Pair programming**
  - **Collective code ownership**
  - **Continuous integration**
  - **Test-driven development (TDD)**
  - **Simple design and refactoring**
  - **On-site customer**
  - **Sustainable pace**

### Main Sources and References:
- *Extreme Programming Explained* by Kent Beck
- *"Pair programming: what's in it for me?"* (ACM, 2008 — Microsoft study)
- *"Tidy First? A Daily Exercise in Empirical Design"* — Kent Beck (GOTO 2024)

---
---

# Part 6: DevOps

---

## 1. The Problem: Silos

- Modern software process models often develop software in **increments**
- The **Development/Scrum team** creates increments, but the **Operations team** manages and maintains IT infrastructure
- Developers and Operations were traditionally **separate ("siloed")**
  - **Developers**: responsible for creating increments/value
  - **Operations**: managing and maintaining IT infrastructure
- Their **interests can be conflicting!**

---

## 2. What is DevOps?

- **DevOps**: combines the terms *Development* and *Operations*
- Typically associated with a **mindset, practices, and tools**
- Idea: the **whole organization** (including development and operation teams) **work together** to deliver value
- Make production **deployments routine and predictable**

### DevOps Infinity Loop:

```
Dev side: Plan → Code → Build → Test →
                                          → Release →
Ops side:                                              → Deploy → Operate → Monitor →
                                                                                      → (back to Plan)
```

---

## 3. DevOps vs. Agile

- Many **parallels** with agile and lean software engineering
- Often seen as a **logical continuation** of agile software development
- Also parallels to **Lean and TPS**

---

## 4. Three Key DevOps Principles

### 4.1 Flow

- DevOps emphasizes the concept of **flow**, similar to its notion in the TPS
- **Reduce batch sizes**: small batch sizes can be delivered more quickly
- **Reduce the number of handoffs**: passing work between departments and teams increases time to deployment
- **Continually identify and eliminate constraints and waste**

### 4.2 Feedback Loops

- Insight: failure and issues **cannot be prevented** for complex systems
- **Feedback and feedforward loops** to detect problems and address them while they are smaller, cheaper, and easier to fix
- **Fast and constant feedback** from all stages of the value stream
- **Automatic build, integration, and test processes** to obtain immediate feedback
- **Propagate new knowledge** across the team/company
- **Find and fix problems where the work is performed**

### 4.3 Continuous Learning and Experimentation

- Require and actively **promote learning**
- Instead of work being rigidly defined, it is **dynamic**
- Performing **experiments** to generate new improvements, enabled by standardization of work procedures and documentation of the results
- Mechanism so that **new local learnings are propagated** to the rest of the organization

---

## 5. DORA Metrics

### 5.1 DevOps Research and Assessment (DORA) Key Metrics

| Metric | Description |
|---|---|
| **Change lead time** | The time it takes for a code commit or change to be successfully deployed to production |
| **Deployment frequency** | How often application changes are deployed to production |
| **Change fail rate** | The percentage of deployments that cause failures in production, requiring hotfixes or rollbacks |
| **Failed deployment recovery time** | The time it takes to recover from a failed deployment |

### 5.2 DORA Performance Levels

| Performance Level | Change Lead Time | Deployment Frequency | Change Fail Rate | Failed Deployment Recovery Time | % of Respondents |
|---|---|---|---|---|---|
| **Elite** | Less than one day | On demand (multiple deploys per day) | 5% | Less than one hour | 19% (18-20%) |
| **High** | Between one day and one week | Between once per day and once per week | 20% | Less than one day | 22% (21-23%) |
| **Medium** | Between one week and one month | Between once per week and once per month | 10% | Less than one day | 35% (33-36%) |
| **Low** | Between one month and six months | Between once per month and once every six months | 40% | Between one week and one month | 25% (23-26%) |

---

## 6. Key DevOps Practices

### 6.1 Observability and Monitoring

- To measure the DORA metrics or other metrics, **monitoring of run code and infrastructure** is needed
- Use **metrics and insights** to improve the value stream

### 6.2 Value Stream Mapping

- **Value Stream Management**: sequence of activities to deliver value; makes the flow of actions to deliver value visible
- **Value Stream Mapping**: identify and analyze value streams aiming to **reduce or eliminate inefficiencies**
- A **Development Value Stream Map (DVSM)** shows stages like: Develop unit tests → Code solution → Pull request and code review → QA feature testing → Regression testing → Meeting definition of done → Production user acceptance testing → Smoke testing → Deploy to production → Performance testing
  - Each stage has: **LT** (Lead Time), **PT** (Process Time), **%CA** (Percent complete and accurate)

### 6.3 Continuous Integration and Deployment (CI/CD)

- **CI/CD**: frequently integrate changes, test them, and **automatically deploy** them
- Requires **automated tests**
- **Automated way** to push changes to production

### 6.4 Infrastructure as Code

- Configuration of the server infrastructure should be **treated similar to code**
- **Configuration files or scripts** (e.g., Terraform, AWS CloudFormation, Ansible, Pulumi)
- Applying best practices such as **version control** and **CI/CD**

### 6.5 Learning from Incidents: Blameless Postmortems

- After incidents, conduct **blameless postmortem** sessions
- Purpose: to **review and learn** from the incidents — not to assign blame
- Focus on **what happened** and **how to prevent it**, not **who caused it**

---

## 7. Summary and Key Points

- **Challenge**: how to put increments into production? The challenge of silos
- **DevOps**: the whole organization works together to deliver value, in particular, development and operation teams
- Key principles: **flow**, **feedback**, and **continuous learning and improvement**
- Making DevOps measurable through **DORA** (change lead time, deployment frequency, change fail rate, failed deployment recovery time)
- Key practices: **monitoring**, **infrastructure as code**, **CI/CD**, **value stream mapping**, and **learning from incidents**

### Main Sources and References:
- *The DevOps Handbook* by Gene Kim, Jez Humble, Patrick Debois & John Willis
- *"10+ Deploys per Day: Dev and Ops Cooperation at Flickr"* (Velocity 2009) — John Allspaw and Paul Hammond
- DORA Report (2024)

---
---

# Part 7: Agile In-Class Session — Exercises, Challenges, and Planning

---

## 1. Mapping Agile Principles to Practices

How the 12 Agile Principles map to concrete practices in Scrum, Kanban, XP, and DevOps:

### Principle: "Business people and developers must work together daily throughout the project."

| Framework | Mapping |
|---|---|
| **Scrum** | Product Owner role |
| **Extreme Programming** | On-site customer |
| **DevOps** | Shared responsibility and breaking down silos |

### Principle: "Agile processes promote sustainable development. The sponsors, developers, and users should be able to maintain a constant pace indefinitely."

| Framework | Mapping |
|---|---|
| **Scrum** | Sprints (timeboxed, predictable pace) |
| **Kanban** | Flow (WIP limits prevent overload) |
| **DevOps** | Continuous deployment, Value Stream Mapping |

### Principle: "At regular intervals, the team reflects on how to become more effective, then tunes and adjusts its behavior accordingly."

| Framework | Mapping |
|---|---|
| **Scrum** | Sprint Retrospective |
| **Kanban** | Continuous improvement (Kaizen) |
| **DevOps** | Blameless postmortems, monitoring and continuous improvement |

---

## 2. Agile: Challenges with Adoption and Drawbacks

### 2.1 Is Agile a Silver Bullet?

**Key quote from Microsoft study** (*"Have Agile Techniques been the Silver Bullet for Software Development at Microsoft?"*):

> "[...] some proponents of agile (both at Microsoft and elsewhere) appear to become **almost religious** about its use. These proponents **emphasize the potential benefits of using agile** while often **downplaying the cost or the learning curve**. We observed in some the attitude of 'if agile doesn't work for you, then you're doing it wrong.' [...] In either case, **teams have attempted to adopt an agile practice and have run into problems**. Portraying agile as a nearly universal solution, downplaying its difficulties, or blaming the team when they do not reap the expected benefits, all serve to drive potential adopters away from agile practices."

### 2.2 Problems with Adoption (State of Agile Report, 17th Edition)

| Barrier to Adoption | Percentage |
|---|---|
| General organizational resistance to change/culture clash | **47%** |
| Not enough leadership participation | **41%** |
| Inadequate management support and/or sponsorship | **38%** |
| Business teams not understanding what agile does and/or can do | **37%** |

- Almost **half** of survey takers perceive a general resistance to adopting agile practices
- Only **6%** said they have no barriers to Agile adoption

### 2.3 Problems After Adoption (State of Agile Report, 17th Edition)

| Problem After Adoption | Percentage |
|---|---|
| Company still has many legacy systems requiring mixed approach | **46%** |
| Siloed teams causing delays on deliverables | **37%** |
| Clashes with company culture | **34%** |
| Not used consistently across teams | **30%** |
| Cannot measure the value to the business | **28%** |
| Low adoption | **25%** |

- "Too many mixed systems" forcing practitioners to adopt **hybrid approaches**
- **Siloed teams** causing delays

### 2.4 Perceived Problems at Microsoft (Agile vs. Non-Agile Devs)

Both agile and non-agile developers perceive problems with agile:

**Common perceived problems:**
- **Incorrect practice of agile** — top concern for agile devs
- **Distributed development** — Microsoft has many geographically distributed teams
- **Scalability** — products like Windows or Office have thousands of developers; multiple Product Owners cause problems
- **Lack of documentation** — agile developers might have "felt the pain" of not having documentation when they needed it
- **Managing dependencies**
- **Lack of upfront planning**
- **Too many meetings**
- **Process overhead**
- **Sprints are too short**
- **Management buy-in** — for agile practitioners buy-in already exists; non-agile developers might have seen resistance
- **Team buy-in**

**Key insight:** Some organizations might claim to use an agile approach, but **actually use a hybrid or traditional model** (i.e., "I know it's waterfall, but we feel better if we call it Scrum").

### 2.5 Scaling Scrum

Various approaches have been proposed to scale Scrum for large organizations:
- **Scaled Agile Framework (SAFe)**
- **Scrum@Scale (S@S)**
- **Nexus**
- **Large Scale Scrum (LeSS)**
- **Disciplined Agile Delivery (DAD)**
- **The "Spotify model"**

---

## 3. Planning in Agile: Story Points and Planning Poker

### 3.1 Story Points

- Use **relative estimation** rather than absolute time
- Common scale uses **Fibonacci-like numbers**: 0, 1, 2, 3, 5, 8, 13
- Examples:
  - **1 point**: Changing the background color of the page to blue
  - **3 points**: Add an export button that downloads data from database
  - **5 points**: Adding an admin dashboard
  - **13 points**: Train and integrate an ML-based classifier to distinguish different kinds of animals

### 3.2 Planning Poker

- **Improves planning accuracy**
- Estimating tasks **relative to completed ones**, rather than in absolute time
- **Avoids bias** and gives **equal voice** to everyone

---

## 4. Kanban: Definition of Workflow (Exercise Walkthrough)

When defining a Kanban Definition of Workflow, you need to specify:

1. **What are the work items?** — Use cases, user stories, features, bug fixes, experiments, etc.
2. **What are the stages?** — e.g., Use Case → Before Next TA Meeting → In Progress → Completed
3. **What are the policies?**
   - Start and end points
   - When an item can move from one stage to another (entry/exit criteria)
   - **WIP limit** — how is it enforced?
   - **Sorting/prioritization**
