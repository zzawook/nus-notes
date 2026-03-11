# CS3240 Lecture 2: User-Centered Design (UCD)

> **Module:** CS3240 Interaction Design | **Semester:** S2 AY2025-26 | **Instructor:** Assoc Prof Bimlesh Wadhwa

---

## Table of Contents

1. [What is User-Centered Design?](#1-what-is-user-centered-design)
2. [The UCD Process (4 Stages)](#2-the-ucd-process-4-stages)
3. [UCD Process Models](#3-ucd-process-models)
4. [Design Thinking — Deep Dive (5 Phases)](#4-design-thinking--deep-dive-5-phases)
5. [Real-World Example: PickMe (Sri Lanka)](#5-real-world-example-pickme-sri-lanka)
6. [UCD for Super-Apps — What Changes?](#6-ucd-for-super-apps--what-changes)
7. [UCD for AI Features — What Changes?](#7-ucd-for-ai-features--what-changes)
8. [Summary and Key Takeaways](#8-summary-and-key-takeaways)
9. [Quick-Fire Exam Review](#9-quick-fire-exam-review)

---

## 1. What is User-Centered Design?

> **User-Centered Design (UCD)** is **a design philosophy** and **process** in which the **needs, preferences, and limitations of end users are given extensive attention** at every stage of the design process.

### Four Core Principles of UCD

| Principle | Meaning |
|---|---|
| **Focus on real users and their tasks** | Design starts with understanding who the users are and what they actually need to accomplish |
| **Iterative design** | Continuous cycles of feedback and refinement — you don't get it right the first time |
| **Empirical measurement** | Observe and test with real users — decisions are based on evidence, not assumptions |
| **Design for usability** | The end goal is **effectiveness** (can users achieve their goals?), **efficiency** (minimal effort), and **satisfaction** (users feel good about the experience) |

> **Key insight:** UCD is not a one-shot waterfall process. It is inherently **iterative** — you cycle through the stages multiple times, refining the design each time based on user feedback and testing.

---

## 2. The UCD Process (4 Stages)

The UCD process is **an iterative and collaborative process** with cycles of **critical thinking** (analytical, evaluative) and **lateral thinking** (creative, divergent).

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  1. Research &    │     │  2. Define Scope  │     │  3. Ideate &     │     │  4. Evaluate      │
│  Understand       │────>│  & Specify        │────>│  Prototype       │────>│  against          │
│  Context of Use   │     │  Requirements     │     │  Design Solutions │     │  Requirements     │
└──────────────────┘     └──────────────────┘     └──────────────────┘     └──────────────────┘
        ^                                                                            │
        └────────────────────────── ITERATE ─────────────────────────────────────────┘
```

| Stage | What You Do | Key Output |
|---|---|---|
| **1. Research & Understand** | Understand users, their tasks, and the context of use | User insights, task maps, friction patterns |
| **2. Define Scope & Requirements** | Synthesize findings, identify opportunities, prioritize | Problem statements, prioritized requirements |
| **3. Ideate & Prototype** | Brainstorm solutions, sketch, build prototypes | Lo-fi to hi-fi prototypes |
| **4. Evaluate** | Test prototypes against requirements with real users | Validated or invalidated design decisions |

---

## 3. UCD Process Models

Several established process models implement the UCD framework:

| Process Model | Description |
|---|---|
| **Design Thinking** | 5-phase human-centered approach (Empathize, Define, Ideate, Prototype, Test) — popularized by IDEO and Stanford d.school |
| **Contextual Design** | Focuses on deep field research — observing users in their actual work environment |
| **Google Design Sprint** | A 5-day structured process for rapidly answering critical business questions through design, prototyping, and testing |
| **Double Diamond of Design** | Two phases of divergent/convergent thinking — first diamond discovers the right problem, second diamond discovers the right solution |

> All of these models are instantiations of the same core UCD philosophy — they just differ in structure, timeframe, and emphasis.

---

## 4. Design Thinking — Deep Dive (5 Phases)

> *"Design thinking is a user-centered approach to innovation that draws from the designer's toolkit to integrate the **needs of people**, the **possibilities of technology**, and the **requirements for business success**."*
> — **Tim Brown, CEO of IDEO**

### Overview of the 5 Phases

```
EMPATHIZE ──> DEFINE ──> IDEATE ──> PROTOTYPE ──> TEST
                                                    │
            <──────────── ITERATE ─────────────────┘
```

---

### Phase 1: EMPATHIZE — Research and Understand Context of Use

**Goal:** Start with people. Understand how work actually happens today.

| What to Do | Details |
|---|---|
| **Dig into current workflows** | Where does effort pile up? What slows users down? What does success look like from the user's perspective? |
| **Map Tasks-To-Be-Done** | Surface the tasks your users need to accomplish |
| **Identify friction patterns** | Look for: information overload, manual work, personalization gaps, adoption barriers, cross-system fragmentation |

> **Key principle:** You must empathize *before* you can design. Without understanding users' real context, you are designing based on assumptions.

---

### Phase 2: DEFINE — Define Scope & Specify Requirements

**Goal:** Synthesize research findings into actionable problem statements and prioritized requirements.

| What to Do | Details |
|---|---|
| **Synthesize findings** | Make it visual (e.g., use FigJam or Miro boards). The team should see patterns emerging. |
| **Identify opportunities** | What moves the needle on **efficiency, effectiveness, satisfaction**? What can be built realistically with current **resources** (skills, time, tools)? Where does AI intervention feel **natural** vs. **intrusive**? |
| **Prioritize** | Not everything can be done — rank by impact and feasibility |

> **Key deliverable:** A clear, scoped problem definition and a prioritized list of requirements that the team agrees on.

---

### Phase 3: IDEATE — Brainstorm for Solutions

**Goal:** Once you understand and scope the problem, start designing. This is where ideas take form.

| What to Do | Details |
|---|---|
| **Define user behaviour** | How should the user act, respond, and communicate? |
| **Brainstorm, sketch, storyboard** | Generate many ideas; don't filter prematurely |
| **Mimic real user behavior** | Design interactions that reflect how users actually behave |
| **Refine through iterative feedback** | Each round helps you understand not just **what works**, but **why users would like it (or don't)** |

---

### Phase 4: PROTOTYPE — Build Prototype(s)

**Goal:** Turn ideas into tangible artifacts that can be tested.

Prototypes range from **low-fidelity** to **high-fidelity**:

| Fidelity | Name | Characteristics |
|---|---|---|
| **Low** | **Wireframe** | Basic layout, structural guidelines, no color/images, quick to produce |
| **High** | **Prototype** | Advanced visual design, realistic interactions, closer to final product |

> **Why start lo-fi?** Low-fidelity prototypes are cheap and fast to create. They let you test fundamental structure and flow before investing in visual polish. Users also feel more comfortable criticizing a rough sketch than a polished design.

---

### Phase 5: TEST — Evaluate Prototypes against Scope

**Goal:** Test your prototypes with real users against the defined requirements and scope.

- Does the prototype solve the problem identified in the Define phase?
- Can users accomplish their tasks effectively?
- What breaks, confuses, or frustrates users?
- What works well and should be kept?

> After testing, **iterate** — go back to whichever phase needs attention (Empathize, Define, Ideate, or Prototype) based on what you learned.

---

## 5. Real-World Example: PickMe (Sri Lanka)

**PickMe** is a Sri Lankan ride-hailing app that exemplifies **Design Thinking** in practice.

### What They Did Right

Instead of copying foreign apps like Uber, PickMe **studied the local market** and **talked to both drivers and passengers**. They learned:

| User Research Finding | Design Implication |
|---|---|
| Many tuk-tuk drivers weren't used to using maps | Simpler navigation interface needed |
| Customers preferred to pay with **cash**, not credit cards | Cash payment option was essential |
| Internet coverage was **weak** in some areas | App needed to work with low bandwidth |
| Users preferred apps in **Sinhala or Tamil** | Local language support required |
| People wanted options for **bikes, cars, or three-wheelers** based on budget and need | Multiple vehicle categories needed |

### The Outcome

Instead of building a fancy app first, PickMe **listened to users, built simple prototypes, and tested features before launching**. That's how they became one of Sri Lanka's most popular ride apps.

> **Exam takeaway:** PickMe is a textbook example of why the Empathize phase matters — understanding the **local context** led to design decisions that a generic "copy Uber" approach would have missed entirely.

---

## 6. UCD for Super-Apps — What Changes?

### The Core Principle

> The UCD **process does not change**, but **how you apply it does** when designing a super-app.

You still: Understand users / Define user needs & problems / Design solutions / Evaluate with users / Iterate

### What a Super-App Introduces

A super-app introduces **scale, complexity, and diversity**. Here is how each phase adapts:

---

### EMPATHIZE — User Research Becomes Multi-Layered

| Traditional App | Super-App |
|---|---|
| One user group, few main tasks | **Multiple user groups**, multiple goals (often unrelated), multiple services |
| Single usage pattern | **Different usage frequencies** (daily vs. occasional) |
| Linear user journey | **Cross-journey behavior** (how users move between services) |

**Grab super-app example — diverse user groups:**
- Daily commuters (transport)
- Food delivery users
- Small merchants
- Users with limited payment options

---

### DEFINE — Scope Shifts from Tasks to Services & Ecosystem

Additional concerns for super-apps (not limited to):

| Concern | Key Questions |
|---|---|
| **Scope shifts to services & ecosystem** | The problem is no longer "booking a ride" but coordinating rides, food, and payments without repetition |
| **Service discoverability** | Which services should appear on the home screen? Which should be promoted vs. hidden? How to surface services contextually (e.g., food at meal times)? |
| **Navigation clarity** | Users struggle to mentally map where services live within the app. Need clear service categories and consistent entry points |
| **Perceived value** | Users must clearly see the value of using multiple services in one app vs. separate apps. Show savings through bundled promos; highlight seamless transitions |

**Grab example:**
- Users want **one payment system** (GrabPay) that works across rides, food, and shopping
- Users expect **shared data** (location, address, payment method) across services

---

### IDEATE — Additional Ideation Areas

| Area | Details |
|---|---|
| **Navigation** | Single home hub vs. separate apps; bottom navigation vs. hub-and-spoke models; persistent access to core utilities (wallet, notifications) |
| **Design system** | Shared components across multiple services — universal button styles for "Book", "Order", "Pay"; shared interaction patterns for address selection, payment confirmation, order tracking |
| **Logical grouping of services** | By user intent (Transport, Food, Finance), by frequency of use, or by context (Now, Later) |
| **Progressive disclosure** | Show most-used services first; hide less-used under "More"; contextual surfacing (food at lunch/dinner, transport during commute hours) — **not showing everything at once** |

---

### PROTOTYPE — Multiple Levels of Prototyping

Instead of prototyping a few user tasks, prototyping happens at **multiple levels**:

| Level | What to Prototype |
|---|---|
| **Ecosystem-level** | Full app shell — home, navigation, wallet — before any single service flow is finalized |
| **Home screen** | Grid vs. card-based services; priority placement; personalized sections like "Recommended for you" |
| **Global navigation** | Bottom navigation tabs (Home, Activity, Payments); persistent access to GrabPay; consistent back/exit behavior |
| **Entry points to services** | Multiple ways to reach a service (home screen icon, promotional banners, contextual prompts) |
| **Service-level** | Individual service flows (booking a ride, ordering food) |
| **Cross-service transitions** | Flows that span services: Order food -> pay with GrabPay -> book a ride |
| **Shared actions** | Single login for all services; unified wallet; unified notifications |

---

### TEST — Additional Evaluation Questions

| Question | What to Observe |
|---|---|
| **Can users find the right service?** | Can users locate GrabMart or GrabExpress? How long does it take? Do users default to only GrabRide or GrabFood? |
| **Do transitions between services feel coherent?** | Scenario-based testing (e.g., order food -> pay -> get ride suggestion). Do transitions feel smooth or abrupt? Do users feel disoriented? Are UI patterns consistent? |
| **Do users understand they're still in the same app?** | Do users say things like "I went to another app"? Do they expect to re-login or re-enter payment details? Is branding, wallet, and navigation consistent? |

---

## 7. UCD for AI Features — What Changes?

### The Core Principle

The UCD process structure remains, but **additional considerations** must be factored into each phase when designing AI features.

---

### Empathize through Ideate — Additional Factors

| Factor | Key Questions |
|---|---|
| **AI task identification** | What specific task(s) could AI help with? How does AI fit into real task flows? |
| **AI appropriateness** | Where can AI assist, automate, or amplify human judgment — and where it **shouldn't interfere**? |
| **Task suitability** | Which moments are **repetitive, data-heavy, or delay-prone** (ideal for AI)? Which require **context, empathy, or expertise** (best left human-led)? |
| **Autonomy boundaries** | How much control should the AI have? (See spectrum below) |
| **AI interaction moments** | Where AI adds clarity or speed — when it listens, recommends, summarizes, or acts — and how these connect to user intent |
| **Learning signals** | How will the AI learn from users? What signals does it collect (edits, confirmations, rejections)? |

### AI Autonomy Spectrum

```
High AI autonomy          AI suggests,           Low AI autonomy,
with light human    <-->   human approves   <-->   high human control
oversight
```

> **Key design decision:** Where on this spectrum should your AI feature sit? This depends on the stakes, user trust, and task complexity.

---

### Prototype through Test — Additional Factors

| Factor | Details |
|---|---|
| **Mimic AI behavior** | Simulate AI through task flows (Wizard of Oz technique) — study real reactions without full engineering effort |
| **Mimic AI reasoning, tone, and confidence** | How does the AI communicate its suggestions? How certain does it sound? |
| **Test incorrect/low-confidence responses** | What happens when AI gets it wrong? Understand user recovery expectations |
| **Transparency testing** | Do users grasp **why** AI suggested something? Do they feel confident acting on it? |
| **Autonomy testing** | Test the balance between AI initiative and human oversight |
| **User response testing** | What do users correct, reject, or confirm? |
| **AI behavior capture** | Document how the AI reasons, hesitates, or learns from user feedback |

### Additional Phase: Measure & Iterate

With AI features, there is an ongoing need to:
- Observe how the AI **performs, learns, and adapts**
- Tune the experience to stay **accurate, dependable, and human-aligned**
- Track metrics around **fairness, trust, accuracy, and usability**

---

### Modified UCD Process for AI Features (Full Pipeline)

```
Traditional:   Empathize ──> Define ──> Ideate ──> Prototype ──> Test

AI-adapted:

  Empathize/       Capability        Define where       Ideate/          Mimic AI           Track Metrics
  Core         --> Sense-Making/ --> AI opportunity --> Shaping AI   --> behavior,       --> & Continuous
  Problem          Data avail.,      / Define role      Patterns/        reasoning,         Improvement/
                   Risks, Success                       Behaviour        tone; Test         Fairness, Trust,
                                                        Mapping          confidence,        Accuracy,
                                                                         autonomy           Usability
```

> **Note from lecture:** For the CS3240 integrated design project, you are **NOT** expected to follow the changed UCD process for introducing AI features in the super-app/service.

---

## 8. Summary and Key Takeaways

### Core Points

1. **UCD** is a well-established industry practice for interaction and experience design of products **as well as services**.
2. There are **many specific process models** (Design Thinking, Contextual Design, Google Design Sprint, Double Diamond) that follow the UCD process framework.
3. The process is infused with cycles of **critical and lateral thinking**, and is typically **iterative** and **collaborative** in nature with **well-defined outcomes at every stage**.
4. For **super-apps**, the process stays the same but is applied at greater scale — multi-layered research, ecosystem-level prototyping, cross-service testing.
5. For **AI features**, additional phases are needed — capability sense-making, AI role definition, behaviour mapping, simulated testing, and continuous metrics tracking.

### Concept Map

```
User-Centered Design (UCD)
    |-- Philosophy: needs, preferences, limitations of users at EVERY stage
    |-- Principles: real users, iterative, empirical, usable
    |
    |-- UCD Process (4 stages, iterative):
    |   |-- 1. Research & Understand (context of use)
    |   |-- 2. Define Scope & Specify Requirements
    |   |-- 3. Ideate & Prototype (lo-fi to hi-fi)
    |   |-- 4. Evaluate against Requirements
    |
    |-- Design Thinking (5 phases):
    |   |-- Empathize -> Define -> Ideate -> Prototype -> Test -> (iterate)
    |
    |-- Super-App Adaptations:
    |   |-- Empathize: multi-layered, multiple user groups
    |   |-- Define: services + ecosystem scope, discoverability, navigation, perceived value
    |   |-- Ideate: navigation, design system, service grouping, progressive disclosure
    |   |-- Prototype: ecosystem-level + service-level + cross-service transitions
    |   |-- Test: service findability, transition coherence, unified app perception
    |
    |-- AI Feature Adaptations:
        |-- Added: capability sense-making, AI role definition, autonomy boundaries
        |-- Prototype: mimic AI behavior/reasoning/tone/confidence
        |-- Test: transparency, autonomy balance, error recovery
        |-- Added: continuous metrics (fairness, trust, accuracy, usability)
```

---

## 9. Quick-Fire Exam Review (Test Yourself)

1. Define **User-Centered Design** in one sentence. What are its four core principles?
2. Name and describe the **four stages** of the UCD process.
3. Why is the UCD process described as **iterative** and **collaborative**?
4. What are the **two types of thinking** that cycle through the UCD process?
5. Name **four UCD process models** and briefly describe one.
6. List the **five phases of Design Thinking** and explain what happens in each.
7. What does the **Empathize** phase involve? Why must it come first?
8. What is a **friction pattern**? Give three examples.
9. What is the difference between a **wireframe** (lo-fi) and a **prototype** (hi-fi)?
10. How did **PickMe** apply Design Thinking to succeed in Sri Lanka? What specific user insights drove their design?
11. When designing a **super-app**, the UCD process doesn't change — but what **does** change? Name three areas.
12. What is **service discoverability** and why does it matter for super-apps?
13. What is **progressive disclosure**? Give an example using Grab.
14. What additional questions must you test for in a super-app? Name all three.
15. When designing **AI features**, what must the Empathize-Ideate phases additionally factor in?
16. What is the **AI autonomy spectrum**? Describe its three levels.
17. What does "mimic AI behavior" mean in the Prototype-Test phase?
18. Why is there an additional **Measure & Iterate** phase for AI features?
19. What four metrics should be tracked for AI features post-launch?
20. For the CS3240 project, are you expected to follow the changed AI UCD process? (Trick question!)

---

*Notes based on CS3240 L2 lecture slides by Assoc Prof Bimlesh Wadhwa, NUS. For personal study use only.*
