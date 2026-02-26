# Architecture: Modeling, UML, C4, Introduction, Strategies, and Patterns

---

# Part 1: Systems Modeling, UML, and C4

---

## 1. Systems Modeling

### 1.1 What is System Modeling?

- Goal: develop **abstract models** of a system
- Typically means a graphical notation based on diagram types in the **Unified Modeling Language (UML)**
- Emphasized in plan-driven development, but also used in agile development in a lightweight manner

### 1.2 System Models and SE Activities

- System models are developed as part of **requirements engineering** and **system design** processes
  - RE: to help derive the **detailed requirements** for a system
  - System design: to **describe the system** to engineers implementing the system
- After implementation to **document** the system's structure and operation

### 1.3 Abstraction vs Representation

- **Modeling** is about abstractions
- **Abstraction**: leaves out details to make the system easier to understand
- **Representation**: maintains all information about the system being represented

---

## 2. Modeling Perspectives

Systems can be modeled from four different perspectives:

| Perspective | Description |
|---|---|
| **Structural perspective** | Model the organization of a system or the structure of the data that is processed by the system |
| **External perspective** | Context or environment of the system |
| **Behavioral perspective** | Model the dynamic behavior of the system and how it responds to events |
| **Interaction perspective** | Interactions between a system and its environment, or between the components of a system |

---

## 3. Modeling Languages

Examples of modeling languages include:
- **Unified Modelling Language (UML)**
- **C4**
- Business Process Model and Notation (BPMN)
- Systems modeling language (SysML)
- Entity Relationship Diagram

---

## 4. UML (Unified Modeling Language)

### 4.1 Why UML?

UML provides a comprehensive set of diagram types organized into two main categories:

#### Behaviour Diagrams:
- **Activity Diagram**
- **State Machine Diagram**
- *Interaction Diagram*:
  - Communication Diagram
  - Interaction Overview Diagram
  - **Sequence Diagram**
  - Timing Diagram
- **Use Case Diagram**

#### Structure Diagrams:
- **Class Diagram**
- Component Diagram
- Object Diagram
- Composite Structure Diagram
- Deployment Diagram
- Package Diagram
- Profile Diagram

### 4.2 UML in Practice (Marian Petre Study)

A study ("UML in Practice") interviewed **50 professional software engineers** in 50 companies and identified 5 patterns of UML use:

- **35/50** practitioners **do not use UML**
  - They have considered it, tried it for a project or two, and found it did not offer advantages over their current practices
  - Some felt UML **came with too much 'philosophy' or 'ideology'**
  - Quote: "What was good about UML was not new, and what was new about UML was not good."
- **11/50** use it **selectively**
  - UML as a **thought tool**
  - For **communication with stakeholders** (mainly sequence diagrams, also activity diagrams)
  - For **collaborative dialogues** in teams — "UML helps a lot when talking to [international] speakers -- it provides a language bridge"
  - Adapted version: "I adapt UML to the task."
- **4/50** use it for **automatic code generation** or to retrofit it (e.g., due to customer requirements)

### 4.3 Which UML Diagrams Are Used?

| UML Diagram | Number of Users | Reported to be used for... |
|---|---|---|
| **Class diagrams** | 7 | Structure, conceptual models, concept analysis of domain, architecture, interfaces |
| **Sequence diagrams** | 6 | Requirements elicitation, eliciting behaviors, instantiation history |
| **Activity diagrams** | 6 | Modeling concurrency, eliciting useful behaviors, ordering processes |
| **State machine diagrams** | 3 | — |
| **Use case diagrams** | 1 | Represent requirements |

---

## 5. Key UML Diagrams

### 5.1 Class Diagrams (Structure Diagram)

Class diagrams describe the **structure (but not the behavior) of an OOP solution**. They are the most often used diagrams in industry.

#### Important Concepts:
- **Classes**
- **Attributes**
- **Associations**
- **Generalization**
- **Constraints**

#### Class Structure:
A class is represented as a box with three sections:

| Section | Content |
|---|---|
| **Class name** | Name of the class (top section) |
| **Attributes** | Properties/data of the class (middle section) |
| **Operations** | Methods/behaviors of the class (bottom section) |

#### Example (Consultation Class):
```
┌─────────────────────────┐
│      Consultation        │
├─────────────────────────┤
│ Doctors                  │
│ Date                     │
│ Time                     │
│ Clinic                   │
│ Reason                   │
│ Medication prescribed    │
│ Treatment prescribed     │
│ Voice notes              │
│ Transcript               │
├─────────────────────────┤
│ New()                    │
│ Prescribe()              │
│ RecordNotes()            │
│ Transcribe()             │
└─────────────────────────┘
```

#### Attribute Notation:
```
- reason: String [1] = "Untitled" {readOnly}
```

| Part | Meaning |
|---|---|
| `-` | **Visibility**: public (+) or private (-) |
| `reason` | **Attribute name** |
| `String` | **Attribute type** |
| `[1]` | **Multiplicity** |
| `= "Untitled"` | **Default value** |
| `{readOnly}` | **Constraint** |

#### Operation Notation:
```
Prescribe (med: Medication) : boolean
```

| Part | Meaning |
|---|---|
| `Prescribe` | **Operation name** |
| `(med: Medication)` | **Operation parameters** |
| `: boolean` | **Return type** |

#### Associations and Multiplicities:
- **Associations**: solid line between two classes
- **Multiplicities**: how many objects may fill the property

| Notation | Meaning |
|---|---|
| `1` | Exactly one |
| `*` | Many (zero or more) |
| `0..1` | Optional (zero or one) |
| `m..n` | Numerically specified |

#### Properties: Attributes vs. Associations
- Properties can often be shown both as **attributes** or **associations**
- Use attributes for things **less important** for the respective diagram

#### Generalization:
- Represents inheritance hierarchies (e.g., Doctor → Hospital doctor / General practitioner → Consultant / Team doctor → Trainee doctor / Qualified doctor)
- Shown with a **hollow triangle arrowhead** pointing to the superclass

---

### 5.2 Sequence Diagrams (Interaction/Behaviour Diagram)

Sequence diagrams model the **interaction between objects** over time. They show how objects communicate by sending messages in a specific order.

#### Key Elements:

| Element | Description |
|---|---|
| **Objects or actors** | Shown at the top as boxes (objects) or stick figures (actors) |
| **Lifelines** | Dashed vertical lines extending downward from each object |
| **Activation bars** | Thin rectangles on lifelines showing when an object is active |
| **Messages** | Horizontal arrows between lifelines (solid = synchronous call) |
| **Return messages** | Dashed arrows going back to the caller |
| **Alternatives (alt)** | Combined fragments showing conditional branching (e.g., [authorization OK] vs [authorization fail]) |

#### Example (Medical System — View Patient Info):
- **Actor**: Medical Receptionist
- **Objects**: P: PatientInfo, D: Mentcare-DB, AS: Authorization
- **Flow**: Receptionist calls ViewInfo(PID) → PatientInfo sends report(Info, PID, UID) to DB → DB sends authorize(Info, UID) to Authorization → Authorization returns → alt fragment: [authorization OK] returns Patient info, [authorization fail] returns Error (no access)

#### Sequence Diagrams in Practice:
- Used at **Instagram** (Scaling Infrastructure — Memcache Lease)
- Used at **Uber** (Payments Platform — Google Pay Auth Flow)
- Used at **Shopify** (Architecture to Handle Flash Sales)

---

### 5.3 Activity Diagrams (Behaviour Diagram)

Activity diagrams show the **flow of activities and actions** in a system, similar to flowcharts but with support for parallelism.

#### Key Elements:

| Element | Description |
|---|---|
| **Initial node** | Filled black circle — starting point |
| **Activity final (end node)** | Filled circle with ring — ending point |
| **Action** | Rounded rectangle — a single step/activity |
| **Object** | Rectangle — data produced or consumed |
| **Flow/edge** | Arrow connecting elements |
| **Fork** | Thick horizontal bar splitting into parallel flows |
| **Join** | Thick horizontal bar merging parallel flows back together |
| **Decision** | Diamond — branching based on conditions (e.g., [priority order] / [else]) |
| **Merge** | Diamond — combining alternative flows back together |

#### Partitioning / Swim Lanes:
- Activity diagrams can be divided into **swim lanes** (vertical partitions) to show which actor or department is responsible for each activity
- Example: Fulfillment | Customer Service | Finance

---

### 5.4 State Machine Diagrams (Behaviour Diagram)

State machine diagrams model the **states of a system** and the **transitions between states** triggered by events (stimuli).

#### Key Elements:

| Element | Description |
|---|---|
| **State** | Rounded rectangle with name and `do:` activity (e.g., `Waiting / do: display time`) |
| **Initial state** | Filled black circle |
| **Final state** | Filled circle with ring (bull's eye) |
| **Transition** | Arrow labeled with the triggering **stimulus/event** |

#### Example (Microwave Oven):

**States:**

| State | Description |
|---|---|
| Waiting | The oven is waiting for input. The display shows the current time. |
| Half power | The oven power is set to 300 watts. The display shows 'Half power'. |
| Full power | The oven power is set to 600 watts. The display shows 'Full power'. |
| Set time | The cooking time is set to the user's input value. The display shows the cooking time selected and is updated as the time is set. |
| Disabled | Oven operation is disabled for safety. Interior oven light is on. Display shows 'Not ready'. |
| Enabled | Oven operation is enabled. Interior oven light is off. Display shows 'Ready to cook'. |
| Operation | Oven in operation. Interior oven light is on. Display shows the timer countdown. On completion of cooking, the buzzer is sounded for five seconds. |

**Stimuli:**

| Stimulus | Description |
|---|---|
| Half power | The user has pressed the half-power button. |
| Full power | The user has pressed the full-power button. |
| Timer | The user has pressed one of the timer buttons. |
| Number | The user has pressed a numeric key. |
| Door open | The oven door switch is not closed. |
| Door closed | The oven door switch is closed. |
| Start | The user has pressed the Start button. |
| Cancel | The user has pressed the Cancel button. |

#### Superstate (Composite State):
- States can contain **substates** (e.g., the Operation superstate in the microwave contains: Checking, Cook, Alarm, Done sub-states)

---

### 5.5 Use Case Diagrams (Behaviour Diagram)

- Already covered in Requirements Engineering (RE3 Specification)
- Shows **actors** (stick figures), **use cases** (ovals), and their relationships within a **system boundary**
- Example: Medical system with actors (Medical receptionist, Manager, Nurse, Doctor) and use cases (Register patient, View personal info, Export statistics, etc.)

---

## 6. C4 Model

### 6.1 Motivation: Agile Architectural Modeling

> "In the age of agile, too many software designers are afraid to over-design their applications upfront. As a result, **many software teams have abandoned architectural thinking**, up front design, documentation, diagramming, and modelling. [...] **the goal is 'to do enough upfront design, enough upfront thinking, to put a starting point in place, and set a general direction.'**" -- Simon Brown

### 6.2 C4 Hierarchy

A **software system** is made up of one or more **containers** (applications and data stores), each of which contains one or more **components**, which in turn are implemented by one or more **code** elements (classes, interfaces, objects, functions, etc).

```
Software System
    └── Container (e.g., web app, mobile app, database, file system)
            └── Component
                    └── Code (classes, interfaces, functions)
```

### 6.3 C4 Notation

| Element | Visual |
|---|---|
| **Container** | Green rectangle |
| **Container, Database** | Green cylinder |
| **Container, Mobile App** | Green mobile shape |
| **Container, Web Browser** | Green browser shape |
| **Person, Customer** | Dark green person figure |
| **Software System, Existing System** | Grey rectangle |
| **Relationship** | Dashed arrow |

### 6.4 The Four C4 Diagram Levels

#### Level 1: System Context Diagram
- Shows the **software system** in its environment
- Shows **people** (users) and **other software systems** that interact with it
- Highest level of abstraction — shows the big picture

#### Level 2: Container Diagram
- Zooms into the software system to show its **containers** (applications, databases, etc.)
- Shows how containers communicate with each other and with external systems
- Example: Web Application, Single-Page Application, Mobile App, API Application, Database

#### Level 3: Component Diagram
- Zooms into a specific container to show its **internal components**
- Shows how components interact within the container
- Example: Sign In Controller, Reset Password Controller, Accounts Summary Controller, Security Component, E-mail Component

#### Level 4: Code Diagram
- Zooms into a specific component to show the **code-level structure** (classes, interfaces)
- Essentially a UML class diagram for that component

### 6.5 C4 and UML Mapping

| C4 | UML |
|---|---|
| System Context Diagram | Use case diagrams, component diagrams |
| Container Diagram | Component diagrams (high-level), deployment diagrams |
| Component Diagram | Component diagrams (detailed), class diagrams |
| Code Diagram | Class diagram |

---

# Part 2: Architecture Introduction, Strategies, and Patterns

---

## 7. What is Software Architecture?

### 7.1 Definitions

| Source | Definition |
|---|---|
| **Sommerville** | The design process for identifying the sub-systems making up a system and the framework for sub-system control and communication |
| **Bass, Clements, Kazman** | The set of **structures** needed to reason about the system. These structures comprise software elements, **relations** among them, and **properties** of both |
| **Grady Booch** | Architecture represents the **significant design decisions** that shape a system, where significant is measured by **cost of change** |
| **Ralph Johnson** | Architecture is about the important stuff. **Whatever that is.** |

### 7.2 Architecture vs. Design

- **Software Architecture** is a **subset** of Software Design
- Software Architecture deals with the high-level, significant decisions
- Software Design also includes lower-level design details (e.g., design patterns from the "Gang of Four" book)

### 7.3 Big Ball of Mud (Anti-Pattern)

- A haphazardly structured, sprawling, sloppy, duct-tape-and-baling-wire, spaghetti-code jungle
- Represents what happens when architecture is neglected

---

## 8. Architecture in Software Processes

### 8.1 Architecture in the Waterfall Model

- Architecture is part of the **"System and Software Design"** phase
- Occurs after Requirements Definition and before Implementation

### 8.2 Architecture in Agile Methods

- Agile mindset: architectural design in **early stages**
- Common perspective: **mitigate highest priority risks**
- Quote (Dave Thomas): "Big design up front is dumb. Doing no design up front is even dumber."

---

## 9. Why Architecture?

### 9.1 Quality Attributes

- Whether or not a system meets its quality attributes (i.e., non-functional requirements) is **significantly influenced by its architecture**
- Example 1: **High performance** requires managing time-based behavior of components and their access to shared resources
- Example 2: **Modifiability** requires assigning responsibilities to components and limiting their interaction, so that a change ideally affects only a single component
- Example 3: **Safe and secure** systems require safeguards and recovery mechanisms

### 9.2 Modifiability and Reasoning about Change

- **Modifiability** is one of the **most common and important** quality attributes
- Three categories of changes:

| Category | Description | Example |
|---|---|---|
| **Local change** | Only a single component is affected | Adding a new rule to a pricing module |
| **Non-local change** | Multiple elements are affected | Besides the rule, database and UI need changes |
| **Architectural change** | Affects how elements interact, might require changes over the whole system | Changing from single-threaded to multi-threaded |

### 9.3 Communication Among Stakeholders

- Architecture serves as a **common abstraction** of the system
- Can be used as a basis for **mutual understanding**, negotiating, forming consensus, and communicating
- Non-technical people are also likely to understand the architecture to the extent they need to

### 9.4 Architecturally Significant Requirements (ASRs)

- Not all requirements are architecturally significant
- Example of **not** architecturally significant: "Temperature should be displayed in Celsius not Fahrenheit on this webpage"
- Example of architecturally significant: "The system should provide five nines (99.999 percent) availability"

---

## 10. Architectural Structures

### 10.1 Three Kinds of Structures

| Structure Type | Description | Examples |
|---|---|---|
| **Component-and-Connector (C&C)** | Focus on runtime behavior — how elements interact at runtime | Client-Server, Concurrency, Shared Data, Process |
| **Module** | Focus on how code is structured and organized | Decomposition, Uses, Generalization/Class, Layered |
| **Allocation** | Focus on how software maps to non-software structures | Deployment, Work Assignment, Implementation |

### 10.2 Module Structures

| Structure | Description |
|---|---|
| **Decomposition** | Units are modules related by "is a submodule of" — shows how modules are decomposed into smaller ones |
| **Uses** | Units are modules related by "uses" — one module depends on the correct functioning of another |
| **Generalization** | Units are modules related by "inherits from" or "is an instance of" — inheritance hierarchies |

### 10.3 Allocation Structures

| Structure | Description |
|---|---|
| **Deployment** | How software elements are mapped to hardware (processing and communication elements) |

### 10.4 Which Structures to Model?

- Not all structures need to be modeled for every system
- The choice depends on what aspects of the system need to be reasoned about

---

## 11. Conway's Law

> "Any organization that designs a system will produce a design whose structure is a copy of the organization's communication structure." — Melvin Conway

- The architecture of a system tends to mirror the communication structure of the organization that built it

---

## 12. Approaching Architecture

### 12.1 Overview

The architectural design process involves three main elements:

```
Architectural Drivers → The Architect → Structures
                          ↑                ↑
                     Design Concepts
                    (selects and instantiates)
```

### 12.2 Architectural Drivers (Inputs)

| Driver | Description |
|---|---|
| **Design Purpose** | What the system is meant to do |
| **Primary Functionality** | Core functional requirements |
| **Quality Attributes** | Non-functional requirements (performance, availability, etc.) |
| **Constraints** | Limitations on the design |
| **Architectural Concerns** | Cross-cutting concerns |

### 12.3 Design Concepts (Tools)

| Concept | Description |
|---|---|
| **Reference Architectures** | Existing architectural blueprints (e.g., AOSA — Architecture of Open Source Applications) |
| **Externally Developed Components** | Reusable libraries, frameworks, systems |
| **Tactics** | Design decisions that influence the response of a quality attribute |
| **Patterns** | Proven architectural solutions that typically comprise multiple tactics |

### 12.4 Output: Structures

The architect produces structures in three categories:
- **Module**: Uses, Decomposition, Class/Generalization, Layered
- **Component-and-Connector**: Client-Server, Concurrency, Shared Data, Process
- **Allocation**: Work Assignment, Deployment, Implementation

---

## 13. Attribute-Driven Design (ADD)

ADD is an iterative, 7-step process for designing software architecture based on quality attribute requirements.

### 13.1 Inputs

- Design purpose
- Primary functional requirements
- Quality attribute scenarios
- Constraints
- Concerns

### 13.2 The 7 Steps

| Step | Description |
|---|---|
| **Step 1** | Review inputs |
| **Step 2** | Establish iteration goal by selecting drivers |
| **Step 3** | Choose one or more elements of the system to refine |
| **Step 4** | Choose one or more design concepts that satisfy the selected drivers |
| **Step 5** | Instantiate architectural elements, allocate responsibilities, and define interfaces |
| **Step 6** | Sketch views and record design decisions |
| **Step 7** | Perform analysis of current design and review iteration goal and achievement of design purpose |

- The process is **iterative**: after Step 7, loop back to Step 2 if necessary
- Can also iterate from a previous round of iterations or from an existing system (brownfield development)
- Output: (Refined) Software Architecture Design

---

## 14. Architectural Tactics

### 14.1 Tactics vs. Patterns

- An **architectural tactic** is a design decision that influences the response of a **quality attribute**
- An **architectural pattern** typically **comprises multiple architectural tactics**
- Tactics and patterns are design concepts used in Step 4 of ADD

### 14.2 Quality Attributes

| External Quality Attributes | Internal Quality Attributes |
|---|---|
| Availability | Efficiency |
| Installability | Modifiability |
| Integrity | Portability |
| Interoperability | Reusability |
| Performance | Scalability |
| Reliability | Verifiability |
| Robustness | |
| Safety | |
| Security | |
| Usability | |

### 14.3 Fault Terminology

```
Mistake → Defect (Bug) → Fault → Failure
```

| Term | Description |
|---|---|
| **Mistake** | A human action that produces an incorrect result |
| **Defect (Bug)** | An error in the code |
| **Fault** | An incorrect internal state |
| **Failure** | An externally visible deviation from the expected behavior |

### 14.4 Quality Attribute Scenarios

A quality attribute scenario has six parts:

| Part | Description |
|---|---|
| **Source** | Entity that generates the stimulus |
| **Stimulus** | A condition that arrives at the system |
| **Artifact** | The part of the system that is stimulated |
| **Environment** | The conditions under which the stimulus occurs |
| **Response** | The activity that occurs as a result of the stimulus |
| **Response Measure** | How the response is measured |

---

## 15. Availability Tactics

```
Availability Tactics
├── Detect Faults
├── Recover from Faults
│   ├── Preparation and Repair
│   └── Reintroduction
└── Prevent Faults
```

### 15.1 Detect Faults

| Tactic | Description |
|---|---|
| **Monitor** | Monitor various components of the system (e.g., using a system monitor) |
| **Ping/Echo** | Asynchronous request/response to check component health |
| **Heartbeat** | Periodic message exchange between a system monitor and a process being monitored |
| **Timestamp** | Used to detect incorrect sequences of events |
| **Condition Monitoring** | Checking conditions of a process or device |
| **Sanity Checking** | Checks the validity of specific operations or outputs |
| **Voting** | Compare the results of multiple sources that should produce the same result |
| **Exception Detection** | Detect conditions that alter the normal flow of execution |
| **Self-Test** | A component runs tests on itself |

### 15.2 Recover from Faults — Preparation and Repair

| Tactic | Description |
|---|---|
| **Redundant Spare** | One or more duplicate components can step in if a component fails |
| **Rollback** | Revert to a previous, known good state |
| **Exception Handling** | Deal with faults by handling exceptions |
| **Software Upgrade** | Upgrade software in-service |
| **Retry** | Retry a failed operation |
| **Ignore Faulty Behavior** | Ignore messages from a source deemed faulty |
| **Graceful Degradation** | Maintain most critical system functions |
| **Reconfiguration** | Reassign responsibilities to remaining resources |

### 15.3 Recover from Faults — Reintroduction

| Tactic | Description |
|---|---|
| **Shadow** | Operate a component in a "shadow mode" while being monitored before reverting it back to active mode |
| **State Resynchronization** | Restore the state of a component after repair |
| **Escalating Restart** | Automatic restart at different granularities (e.g., lowest level might clear caches, while highest one restarts the whole system) |
| **Nonstop Forwarding** | Continue forwarding traffic while recovering |

### 15.4 Prevent Faults

| Tactic | Description |
|---|---|
| **Removal from Service** | Temporarily placing a system component in an out-of-service state to mitigate potential faults (e.g., suspected memory leak) |
| **Transactions** | Provide ACID properties (atomic, consistent, isolated, and durable) |
| **Predictive Model** | Use models to predict and prevent faults |
| **Exception Prevention** | Prevent exceptions from occurring in the first place |
| **Increase Competence Set** | Set of states in which the program can "competently" operate (e.g., SQLite handles out-of-memory, I/O errors, crashes) |

---

## 16. Performance Tactics

```
Performance Tactics
├── Control Resource Demand
└── Manage Resources
```

### 16.1 Control Resource Demand

| Tactic | Description |
|---|---|
| **Manage Work Requests** | Reduce work by reducing the number of requests coming into the system or component |
| **Limit Event Response** | Policy for handling events too rapidly to be processed |
| **Prioritize Events** | Process higher-priority events first |
| **Reduce Computational Overhead** | Reduce the overhead of processing |
| **Bound Execution Times** | Set limits on execution time |
| **Increase Efficiency** | Improve algorithms and data structures |

### 16.2 Manage Resources

| Tactic | Description |
|---|---|
| **Increase Resources** | Add more hardware/resources |
| **Introduce Concurrency** | Process requests in parallel |
| **Maintain Multiple Copies of Computations** | Keep separate copies of data to reduce contention from multiple simultaneous accesses (e.g., RAID 1) |
| **Maintain Multiple Copies of Data** | Cache or replicate data |
| **Bound Queue Sizes** | Limit the size of queues to prevent overload |
| **Schedule Resources** | Schedule resources in the presence of contentions (FIFO, Fixed-priority scheduling, Round-robin scheduling) |

---

## 17. Modifiability Tactics

```
Modifiability Tactics
├── Increase Cohesion
├── Reduce Coupling
└── Defer Binding
```

### 17.1 Increase Cohesion

| Tactic | Description |
|---|---|
| **Split Module** | Break a module into smaller, more cohesive parts |
| **Redistribute Responsibilities** | Move responsibilities to achieve better cohesion |

### 17.2 Reduce Coupling

| Tactic | Description |
|---|---|
| **Encapsulate** | Hide internal details behind an interface |
| **Use an Intermediary** | Add an intermediary to break direct dependencies |
| **Abstract Common Services** | Factor out common functionality |
| **Restrict Dependencies** | Limit which modules can depend on which |

### 17.3 Defer Binding

| Tactic | Description |
|---|---|
| **Component Replacement** | Allow components to be replaced at runtime |
| **Couple-Time Parameterization** | Set parameters at compile time |
| **Aspects** | Use aspect-oriented programming |
| **Configuration-Time Binding** | Set parameters at configuration/deployment time |
| **Resource Files** | Externalize configuration into resource files |
| **Discovery** | Discover services at runtime |
| **Interpret Parameters** | Interpret parameters at runtime |
| **Shared Repositories** | Use shared data stores |
| **Polymorphism** | Use polymorphism for runtime flexibility |

---

## 18. Architectural Patterns

### 18.1 Layered Architecture

- System is organized into **layers**, where each layer provides services to the layer above it
- Typical layers:
  1. **User interface**
  2. **User interface management / Authentication and authorization**
  3. **Core business logic / Application functionality / System utilities**
  4. **System support (OS, database, etc.)**

**Benefits:**
- **Portability**: allows layers to be general or specific to an environment
- **Reusability**: lower-level layers could be reused across different applications (e.g., network communications layer in an OS)
- **Modifiability**: lower layers can easily be changed without affecting upper layers

**Example:** SQLAlchemy (ORM → Core → DBAPI → Database)

### 18.2 Pipe-and-Filter Architecture

- Processing steps (filters) are connected by data streams (pipes)
- Data flows through a series of filters sequentially: `Input → Filter 1 → Pipe → Filter 2 → Pipe → Filter n → Output`

**Benefits:**
- **Modifiability**: filters are independent from each other and can be modified independently
- **Reconfigurability**: filters can be combined in different ways

**Examples:**
- GNU Mailman (pipeline queue → mime delete → cleanse headers → add headers → calculate recipients → to digest/archive/outgoing)
- Unix pipes: `ps aux | grep root | wc -l`

### 18.3 Model-Centered Architecture (Repository)

- A central **repository** (data store) is surrounded by components that interact with it
- Components communicate through the shared data store rather than directly with each other
- Example: Project repository with UML editors, Code generators, Java/Python editors, Design translator, Design analyzer, Report generator

**Benefits:**
- **Modifiability/maintainability**:
  - Components can be independent; they do not know of the existence of other components
  - All data can be managed consistently, as it is managed in one place

### 18.4 Microkernel (Plugin-In) Architecture

- A **core system** with **plug-in components** that extend its functionality
- Supports **modifiability**, **extensibility**, and **testability**:
  - Plug-ins provide a controlled mechanism to extend a core product
  - Plug-ins can be developed (and tested) by different teams
  - Plug-ins can evolve independently from the microkernel, as long as the interfaces do not change

**Examples:** Eclipse SDK, Web browsers (Chrome extensions)

### 18.5 Client-Server Architecture

- Multiple **clients** communicate with a central **server**
- Clients request services; server provides them

**Benefits:**
- **Scalability**: clients can be independently scaled
- **Interoperability**: clients can be written in different languages
- **Modifiability/maintainability**: clients and servers can evolve independently

**Example:** X Window System (X Server with X clients)

### 18.6 Monoliths vs. Microservices

#### Monolithic Architecture
- All components (UI, business logic, data interface, database) are packaged and deployed as a **single unit**
- **Modular Monolith**: a monolith organized into well-defined modules internally (better than a distributed monolith with tightly coupled services)

#### Microservice Architecture
- System is decomposed into **small, independently deployable services**
- Each service has its own database and communicates via API gateway
- Related to **Amazon's Two-Pizza Rule** (teams small enough to be fed with two pizzas)

**Benefits:**
- **Deployability** and a quick time to market
- **Independence**: each team can make its own technology choices
- **Scalability**: service instances can be dynamically added

**Examples:** Netflix, Amazon

### 18.7 Other Architectural Patterns

- Service-oriented Architecture (SOA)
- Event-driven Architecture
- Peer-to-peer

---

## 19. Summary and Key Points

### Part 1 — Modeling, UML, and C4:
- **Modeling** is about creating **abstractions** using different **perspectives** (structural, external, behavioral, interaction)
- Useful for **requirements engineering** as well as when **designing** the software
- **UML**: comprehensive modeling standard with many diagram types
  - Key diagrams: **Class diagrams**, **Sequence diagrams**, **Activity diagrams**, **State machine diagrams**, **Use case diagrams**
  - In practice, most practitioners use UML **selectively** or not at all (Petre study: 35/50 don't use it)
- **C4**: architectural modeling language designed for an **agile context**
  - Four levels of zoom: System Context → Container → Component → Code

### Part 2 — Architecture Intro and Overview:
- **What is architecture?** — Significant design decisions measured by cost of change
- **Why architecture?** — Quality attributes, modifiability, communication among stakeholders
- **Different structures** — Module, C&C, Allocation
- **Approaching architecture** — Architectural Drivers → Design Concepts → Structures
- **Attribute-Driven Design (ADD)** — 7-step iterative process
- **Conway's Law** — System structure mirrors organization structure

### Part 2 — Strategies and Patterns:
- **Tactics and patterns** as design constructs in ADD
- **Architectural tactics** for:
  - **Availability**: Detect Faults, Recover from Faults, Prevent Faults
  - **Performance**: Control Resource Demand, Manage Resources
  - **Modifiability**: Increase Cohesion, Reduce Coupling, Defer Binding
- **Architectural patterns**:
  - Layered architecture
  - Pipe-and-filter architecture
  - Model-centered architecture
  - Microkernel architecture
  - Client-server architecture
  - (Modular) Monolith
  - Microservice architecture

### Main Sources and References:
- *Software Engineering* (10th Edition) by Ian Sommerville (Pearson)
- *UML Distilled* (3rd Edition) by Martin Fowler
- *Software Architecture in Practice* (4th Edition) by Len Bass, Paul Clements, Rick Kazman
- *Fundamentals of Software Architecture* by Mark Richards & Neal Ford
