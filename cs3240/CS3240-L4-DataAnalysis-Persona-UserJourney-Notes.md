# CS3240 Lecture 4: User Research Data Analysis — Persona & User Journey

> **Module:** CS3240 Interaction Design | **Semester:** S2 AY2025-26 | **Instructor:** Assoc Prof Bimlesh Wadhwa

---

## Table of Contents

1. [From Raw Data to Design Decisions](#1-from-raw-data-to-design-decisions)
2. [The Sense-Making Process](#2-the-sense-making-process)
3. [Data → Insight → Design Decision](#3-data--insight--design-decision)
4. [Analysing Who We Are Designing For](#4-analysing-who-we-are-designing-for)
5. [How to Analyse Common Traits of Target Users (Step-by-Step)](#5-how-to-analyse-common-traits-of-target-users-step-by-step)
6. [Worked Example: Campus Super-App](#6-worked-example-campus-super-app)
7. [Good Clustering vs. Bad Clustering](#7-good-clustering-vs-bad-clustering)
8. [From Clusters to UI Decisions](#8-from-clusters-to-ui-decisions)
9. [Personas: Decision Tools, Not Profiles](#9-personas-decision-tools-not-profiles)
10. [Weak vs. Strong Personas](#10-weak-vs-strong-personas)
11. [Persona Self-Check](#11-persona-self-check)
12. [User Journey Maps](#12-user-journey-maps)
13. [Creating a User Journey Map (Step-by-Step)](#13-creating-a-user-journey-map-step-by-step)
14. [What Journey Maps ARE vs. ARE NOT](#14-what-journey-maps-are-vs-are-not)
15. [AI in Data Analysis](#15-ai-in-data-analysis)
16. [Summary and Key Takeaways](#16-summary-and-key-takeaways)
17. [Quick-Fire Exam Review](#17-quick-fire-exam-review)

---

## 1. From Raw Data to Design Decisions

After conducting user research (L3), you are left with **raw data** that does not speak for itself. This lecture covers how to turn that raw data into actionable design decisions.

### What Counts as Raw Data?

| Source | Examples |
|---|---|
| **Audio / Video** | Interview recordings, observation footage |
| **Self-documenting items** | Survey responses, diary entries |
| **Notes** | Handwritten or typed interview answers, observation notes |

> **Key principle:** Raw data is messy. Interviews, notes, surveys, and observations don't speak for themselves. You must actively analyse them.

### What We Expect from Raw Data

Raw user research data should yield:

1. **Better understanding of the User** — who they are, how they behave
2. **Gaps in the user's workflow** — breakdowns, pain points, unmet needs
3. **Better understanding of the User's context and tasks** — when, where, why they act
4. **Common themes / patterns** — recurring behaviors, frustrations, goals

---

## 2. The Sense-Making Process

**Data Analysis of User Research Data** is the process of:

1. **Making sense** of raw data
2. **Identifying what matters**
3. **Deciding what to design** (and what *not* to)

### The Full Pipeline

```
Raw Data ──────> Sense Making ──────> Analysis Artifacts ──────> Design Decisions

Notes and         Clustering,          Persona (Who)              Features
Recordings        Categorising,        Journeys (Where)           Interaction Flows
                  Comparing users,     Tasks (How)                Trade-offs &
                  Asking Why,          Themes (What)              Exclusions
                  Interpreting,
                  Reasoning

         Data → Insight               Insight → Design Decision
```

> **AI note:** AI can assist with suggestions and generation of artifacts, but you must **always validate** artifacts and **always work with real user data**.

---

## 3. Data → Insight → Design Decision

This is the **core analytical framework** of this lecture. Every piece of analysis should follow this chain:

### The Three-Step Chain

| Step | Question | Example |
|---|---|---|
| **Data** | What did users say or do? | "I often abandon checkout." |
| **Insight** | Why does this matter? | Users drop off when costs appear late. |
| **Design Decision** | What will you do about it? | Show full price earlier in the flow. |

### What IS vs. IS NOT Analysis

| IS Analysis | IS NOT Analysis |
|---|---|
| Explaining **why** something matters | Listing user quotes without interpretation |
| Identifying breakdowns and their causes | Making diagrams without interpretation |
| Connecting data to design trade-offs | Presenting raw data in a prettier format |

> **Exam tip:** Listing user quotes is NOT analysis. Making diagrams without interpretation is NOT analysis. Explaining **why** something matters IS analysis.

---

## 4. Analysing Who We Are Designing For

The first major analysis question: **Who are we designing for?**

This involves going from raw data to **meaningful user groups** through behavioral clustering — NOT demographic labelling.

### The 4-Step Process (+ Step 3A)

| Step | Action | Guideline |
|---|---|---|
| **Step 1** | Start from raw data | Review interview notes, observations, survey responses. Highlight repeated behaviors, needs, frustrations. |
| **Step 2** | Cluster by behavior and context | Group users who behave similarly in the same situations. Focus on **what users do and why**, not who they are. |
| **Step 3** | Identify meaningful user roles | Ask: Do these users have different goals or key tasks? Merge groups that lead to similar design decisions. |
| **Step 3A** | Say NO to non-key users | Remove groups that do not influence core design decisions. A smaller set of strong groups is better than many weak ones. |
| **Step 4** | Add details to distinct roles | Details should support a task, context, or trade-off. |

### Step 4 — What Details to Include vs. Avoid

| Include | Avoid |
|---|---|
| Context of use (when, where, under what pressure) | Hobbies, personality traits, lifestyle details |
| Key tasks (prioritised) | Demographics that don't change design decisions |
| Frustrations that affect design | Filler information for "realism" |

> **Key principle:** Don't start by inventing roles. Start by clustering behaviors. If two user groups lead to the same design decisions, they should be merged. Only after roles are clear do you add details — and only details that help you design.

---

## 5. How to Analyse Common Traits of Target Users (Step-by-Step)

```
Step 1: Review raw data        ──> Highlight repeated behaviors, needs, frustrations
        │
Step 2: Cluster by behavior    ──> Group users who behave similarly in same contexts
        │                          Focus on what users DO and WHY
        │
Step 3: Identify distinct      ──> Do groups lead to different design decisions?
        roles                      Merge groups with same design implications
        │
Step 3A: Say NO to non-key     ──> Remove groups that don't influence core decisions
         users                     Fewer strong groups > many weak groups
        │
Step 4: Add details to         ──> Context of use, key tasks, design-relevant
        distinct roles              frustrations ONLY
```

---

## 6. Worked Example: Campus Super-App

**Context:** A campus super-app combining food ordering, transport, payments, and event access.

### Step 1: Raw Observations from User Research

From interviews and diary studies:
- Some users open the app only when rushing between classes
- Some users browse food and events during free time
- Some users care most about speed and minimal steps
- Some users enjoy exploring options and promotions

### Step 2: Cluster by Behaviour

| Observed Pattern | Possible Group |
|---|---|
| Opens app under time pressure | "In-a-rush users" |
| Browses menus & events | "Explorers" |
| Uses app mainly for payments | "Utility-only users" |
| Occasionally checks events | "Casual users" |

### Step 3: Identify Distinct Roles

Ask: Do these groups lead to **different design decisions**?

| Group | Design Implication |
|---|---|
| "In-a-rush users" | Need fast access, minimal screens |
| "Explorers" | Tolerate browsing, recommendations |
| "Casual users" | Infrequent, low impact on core flows |
| "Utility-only" | Same task priorities as "In-a-rush" |

### Step 3A: Merge and Say NO

- **Merge** "Utility-only" with "In-a-rush" (same task priorities → same design decisions)
- **Say NO** to "Casual users" as a key user group (low impact on core flows)

### Step 4: Add Details (Only What Matters)

#### Primary User: The Between-Classes Sprinter

| Dimension | Details |
|---|---|
| **Context of Use** | Uses the super-app between classes. Typically has ≤ 2 minutes. Often multitasking: walking, queueing, talking to friends. One-handed phone use, poor attention bandwidth. |
| **Primary Goal** | Complete a necessary task as fast as possible with minimal thinking. |
| **Key Tasks (prioritised)** | 1. Order food for quick pickup 2. Pay (canteen, vending, transport) 3. Check transport timing. **Note:** Browsing actively works against their goal. |
| **Behaviors & Mental Model** | Expects immediate action on app open. Trusts defaults; does not want to configure. Will abandon task if it takes more than a few screens. Associates "good app" with speed, not richness. |
| **Frustrations** | Too many choices before starting. Pop-ups, confirmations, or suggestions mid-flow. Re-entering information already provided. Anything that makes them stop walking. |
| **Design Weight** | Their constraints dominate UI and task flow decisions. |

#### Primary User: The Planner & Explorer

| Dimension | Details |
|---|---|
| **Context of Use** | Uses the super-app during downtime — evenings, weekends, or free gaps. Sitting down, full attention. Often on Wi-Fi, not rushed. |
| **Primary Goal** | Make informed choices and plan ahead. |
| **Key Tasks** | Compare food options, prices, wait times. Discover events, promotions, or new services. Save favourites for later use. |
| **Behaviors & Mental Model** | Willing to explore non-linear paths. Enjoys browsing and discovery. Comfortable with more steps if they add value. Associates "good app" with clarity and control. |
| **Frustrations** | Shallow information. No way to compare or filter. Losing track of options liked earlier. Interfaces that feel rushed or stripped down. |
| **Design Weight** | Influences feature depth, but not core speed. |

---

## 7. Good Clustering vs. Bad Clustering

| Aspect | "Not So Good" Clustering | Good Clustering |
|---|---|---|
| **Basis** | Demographics & labels | Behavior & context |
| **Examples** | Students, Staff, Residents, Visitors, Year 1 students, International students, Foodies, Tech-savvy users | Users rushing between classes (≤2 min), Users planning meals during free time, Users opening app only to pay quickly |
| **Problems** | Descriptive, not behavioral. No clear context of use. Looks organized but doesn't change any design decision. | Different time pressure. Different tolerance for steps. Different feature priorities. Forces trade-offs in design. |
| **Result** | Cannot derive design trade-offs | Directly shapes what the app must optimise for |

> **Exam tip:** If your clusters don't force any design trade-offs, they are descriptive, not analytical. Good clusters are based on **what users do and the context in which they do it**, not who they are demographically.

---

## 8. From Clusters to UI Decisions

### How User Groups Impact UI Design

| For Between-Classes Sprinter | For Planner & Explorer |
|---|---|
| One-tap access to key services | Browse, compare, save |
| No browsing by default | Recommendations & deals |
| Minimal confirmations | Rich filters and categories |
| Payment & pickup surfaced first | Non-urgent notifications allowed |

### How User Groups Impact Task Navigation

| Aspect | Between-Classes Sprinter | Planner & Explorer |
|---|---|---|
| **Decision points** | Minimal | Multiple (filters & categories) |
| **Browsing** | No browsing before task | Non-linear flows encouraged |
| **Options** | Defaults over options | Rich options with comparisons |
| **Time pressure** | High — complete action fast | Low — compare and plan ahead |
| **Primary task** | Complete action fast under time pressure | Compare options & plan ahead |

---

## 9. Personas: Decision Tools, Not Profiles

> **If a persona doesn't help you make design decisions, it's decoration.**

### What is a Good Persona?

A good persona:
- Is **grounded in research data** (not invented)
- Represents a **meaningful user group** (behavioral, not demographic)
- Has clear **goals, frustrations, and key tasks**
- **Enables design trade-offs** — helps you say YES to some features and NO to others

### Good Persona Self-Check Questions

- Can I name 2–3 key tasks this persona cares about?
- Would this persona help me prioritise one feature over another?
- Is there information here that is interesting but irrelevant?

### Common Persona Mistakes

- Too many personas
- Too much demographic trivia
- Personas not connected to tasks or journeys

### Example: Persona with Description but No Decision Power

A persona that is:
- Detailed, relatable, nicely written, easy to empathize with

But **lacks**:
- No clear key task
- No specific context of use
- No implied constraints
- Difficult to reject any feature based on it

**This persona has NO decision power.** It's description, not analysis.

### Example: Persona with Decision Power

A persona tied to exam-period study behavior:

| Design we CAN consider | Design we CAN avoid |
|---|---|
| One-tap "Start Study Mode" | Social sharing during study sessions |
| No prompts or questions after session starts | Frequent check-ins or AI "suggestions" |
| Defaults preferred over customization during exams | Complex setup flows before starting |

---

## 10. Weak vs. Strong Personas

| Dimension | Weak Persona | Strong Persona |
|---|---|---|
| Focus | Describes a person | Represents a behavior |
| Details | Interesting details | Relevant constraints |
| Approach | Empathy-driven | Decision-driven |
| Applicability | Flexible to any design | Forces trade-offs |
| Feature filtering | Cannot reject features | Helps say "no" |

> **Key principle:** Good personas don't describe people. They argue for design choices.

> **Reminder:** Personas exist to **prioritise tasks** — not to describe people. This ties directly to the topic of user tasks and task analysis.

---

## 11. Persona Self-Check

Use this checklist for the CS3240 project and exam:

| Question | If "No"... |
|---|---|
| Can I name 1–2 key tasks this persona cares about most? | Persona lacks task focus |
| Does this persona prioritise one feature over another? | Persona is too generic |
| Does this persona help me say NO to certain ideas? | Persona has no decision power |
| Is this persona tied to a specific context of use? | Persona is untethered from reality |

> If you answered "no" to most of the above, your persona is **descriptive, not analytical** — it does not help with design decisions.

---

## 12. User Journey Maps

### Definition

> A **User Journey Map** is a visual representation of a user's interactions with a system. It shows the entire journey through a user's perspective and concentrates on the **experience of a persona**, typically with a **single goal**.

### Current vs. Future Journey Maps

| Type | Purpose |
|---|---|
| **Current** | Gain a deeper understanding of what your user experiences while interacting with your product or service **right now** |
| **Future** | Visualize the **ideal** journey you'd like your user to embark on |

> **Guideline:** Start with the **current** reality.

### What Data a Journey Map Captures

| Data Category | What It Covers |
|---|---|
| **Touchpoints** | Where users interact with the system |
| **Pain points & emotions** | Frustrations, confusion, positive moments |
| **Context of use** | When, where, and why users interact |
| **Actions & decisions** | Steps taken before and after using the system |

### How a Journey Map is Useful

- Empathize with the user's experience better
- Create a logical order to your user's journey
- Understand where users struggle to complete actions
- Compare user expectations against actual experience
- Gain insights about potential and existing users

---

## 13. Creating a User Journey Map (Step-by-Step)

### Step-by-Step Process

```
1. List all touchpoints ──> Both online and offline steps
         │
2. Decide entry and     ──> Where does the user's interaction begin and end?
   exit points
         │
3. Arrange touchpoints  ──> Chronological order between entry and exit
   in order
         │
4. Indicate loops       ──> Potential repetitions or back-tracking
         │
5. At each touchpoint,  ──> What are users Doing?
   ask key questions         What are users Feeling?
                             What are users Thinking?
                             What breaks here?
         │
6. Plot emotions        ──> Show how emotional experience changes
                             with each touchpoint
         │
7. Add opportunities    ──> Ideas to resolve negative experiences
   for improvement           or build upon positive ones
```

### Key Questions at Each Touchpoint

| Question | Purpose |
|---|---|
| **What are users Doing?** | Captures actions and behaviors |
| **What are users Feeling?** | Captures emotional state (frustration, satisfaction, confusion) |
| **What are users Thinking?** | Captures expectations and mental models |
| **What breaks here?** | Identifies breakdowns and pain points |

### Plotting Emotions

Plot the persona's emotions under your journey map to see how emotional experience changes with each touchpoint. Use feeling adjectives or emoji indicators.

> **Reminder:** Emotions help you **locate breakdowns**, not decorate the map. Analysis over aesthetics.

### Adding Opportunities

For each negative experience, add ideas that would:
- Help resolve the cause of a negative experience, OR
- Take a positive part of the experience and build upon it

---

## 14. What Journey Maps ARE vs. ARE NOT

| Journey Maps ARE | Journey Maps ARE NOT |
|---|---|
| Analysis of breakdowns | Feature lists |
| Evidence of pain points | Screen flows |
| Input to task analysis and design | Future UI mockups |
| Grounded in real user data | Happy stories |

> **Exam tip:** A journey map is an analytical tool to identify breakdowns and pain points — not a feature list or a happy story about ideal usage.

### Tools and Templates

- **Miro:** miro.com/miroverse/user-journey-map/
- **Mural:** mural.co/templates/user-journey
- **Figma Community:** Various user journey map templates available

---

## 15. AI in Data Analysis

### What AI Can Help With

AI can help synthesize research data, structure journey stages, and draft initial artifacts — but designers must **validate insights and make final decisions**.

> **Critical requirement:** AI needs **raw user research data** as input. Without real data, AI will generate stereotypical, generic outputs.

### What NOT to Use AI For

| Do Not | Why |
|---|---|
| Ask AI to invent personas or journeys without data | You'll get stereotypical, invented outputs |
| Present AI-generated personas as real research findings | Violates research integrity |
| Skip user research and rely only on AI outputs | AI cannot replace real user voices |
| Copy outputs without critical thinking | AI outputs require validation |

### Using AI Prompts Responsibly

A prompt like *"Help me define key user personas and their behaviors"* is:

| Acceptable Use | Bad Practice |
|---|---|
| Brainstorming exercise — compare AI output to actual research | Submitting AI-generated personas as research evidence |
| Learning persona structure (what elements to include) | Assuming AI "knows your users" — it predicts plausible answers, not real ones |
| Idea-starters when you have real data but are stuck | Using without giving AI your actual research data |

### AI as a Complementary Tool

- AI is **not a replacement** for human expertise and creativity
- It can **streamline and enhance** your UX research process
- It is **not a substitute** for critical thinking and analysis
- Always **review and validate** AI-generated outputs
- Use AI **ethically**: be transparent about use, protect participant privacy
- **Cite AI tools** when used (APA style for ChatGPT: apastyle.apa.org/blog/how-to-cite-chatgpt)

> **Key message:** AI is a co-pilot, not a replacement for real user voices.

---

## 16. Summary and Key Takeaways

### Core Points

1. **Raw data doesn't speak for itself.** Analysis is the process of turning raw user research data into design decisions through sense-making.
2. **The core chain is: Data → Insight → Design Decision.** Every analysis must follow this — listing quotes or making diagrams without interpretation is NOT analysis.
3. **Cluster by behavior and context, not demographics.** Good clusters force design trade-offs; bad clusters are descriptive labels that don't change any decision.
4. **The 4-step process** for identifying user groups: Start from raw data → Cluster by behavior → Identify distinct roles (merge similar ones, say NO to non-key groups) → Add only design-relevant details.
5. **Personas are decision tools, not profiles.** A good persona helps you say YES to some features and NO to others. If a persona can't reject a feature, it has no decision power.
6. **Strong personas** represent behaviors and relevant constraints; **weak personas** describe people with interesting but irrelevant details.
7. **User Journey Maps** are visual analyses of a user's interactions with a system — showing touchpoints, emotions, pain points, and opportunities.
8. **Journey maps capture breakdowns**, not feature lists. Start with the current reality, not the ideal future.
9. **At each touchpoint**, ask: What are users doing? Feeling? Thinking? What breaks here?
10. **AI is a co-pilot, not a replacement.** Use AI with real research data; always validate and never present AI outputs as research evidence.

### Concept Map

```
Data Analysis of User Research Data
    │
    ├── Core Framework
    │   └── Data ──> Insight ──> Design Decision
    │       (What users said/did) (Why it matters) (What to do about it)
    │
    ├── Analysing WHO (User Groups → Personas)
    │   ├── Step 1: Start from raw data (highlight patterns)
    │   ├── Step 2: Cluster by behavior & context (NOT demographics)
    │   ├── Step 3: Identify distinct roles (merge similar groups)
    │   ├── Step 3A: Say NO to non-key users
    │   ├── Step 4: Add design-relevant details only
    │   └── Persona
    │       ├── Decision tool, NOT profile
    │       ├── Must force design trade-offs
    │       ├── Strong: behavior-based, constraint-driven, helps say NO
    │       └── Weak: description-based, empathy-driven, can't reject features
    │
    ├── Analysing WHERE (User Journey Maps)
    │   ├── Visual representation of user interactions
    │   ├── Focused on one persona + one goal
    │   ├── Start with CURRENT reality
    │   ├── Captures: touchpoints, emotions, pain points, context, actions
    │   ├── At each touchpoint: Doing? Feeling? Thinking? What breaks?
    │   ├── Plot emotions to locate breakdowns
    │   ├── Add improvement opportunities
    │   ├── ARE: analysis of breakdowns, evidence of pain points
    │   └── ARE NOT: feature lists, screen flows, UI mockups, happy stories
    │
    └── AI in Data Analysis
        ├── Can help: synthesize data, structure stages, draft artifacts
        ├── Cannot replace: real user voices, critical thinking, validation
        ├── Requires: real research data as input
        └── Rule: validate all AI outputs; never present as research evidence
```

### Key Relationships Across Lectures

```
L3: User Research (Collect Data) ──> L4: Data Analysis (Make Sense of Data)
                                         │
                                         ├── WHO? → Personas (decision tools)
                                         ├── WHERE? → User Journey Maps (breakdown analysis)
                                         ├── HOW? → Tasks (covered later)
                                         └── WHAT? → Themes (covered later)
```

---

## 17. Quick-Fire Exam Review (Test Yourself)

1. What are the three types of **raw data** from user research? Give an example of each.
2. What are the **four things** we expect to learn from raw user research data?
3. Explain the **Data → Insight → Design Decision** chain with a concrete example.
4. Why is listing user quotes **not** considered analysis?
5. What are the **four steps** (plus Step 3A) for analysing who you are designing for?
6. What is the difference between clustering by **behavior** vs. clustering by **demographics**? Why does it matter?
7. Give an example of a "not so good" cluster and a "good" cluster. Explain why one is better.
8. When should you **merge** two user groups? When should you **say NO** to a user group?
9. In Step 4 (adding details), what should you **include** and what should you **avoid**?
10. What makes a persona a **decision tool** rather than a **profile**?
11. Compare **weak personas** and **strong personas** across five dimensions.
12. What are the **four self-check questions** for evaluating a persona in CS3240?
13. Define **User Journey Map**. What does it focus on?
14. Should you start with a **current** or **future** journey map? Why?
15. What **four categories of data** does a journey map capture?
16. List the **seven steps** for creating a user journey map.
17. What are the **four key questions** to ask at each touchpoint in a journey map?
18. Why do we plot **emotions** on a journey map? What is the purpose?
19. What are journey maps? What are they **NOT**? (Give four examples of each.)
20. How can **AI** help with data analysis? What should you **never** do with AI in this context?
21. What happens if you use an AI prompt to generate personas **without** giving it real research data?
22. What is the **key message** about AI in UX research?
23. Walk through the **super-app example**: from raw observations → clustering → merging → final personas. What design decisions does each persona drive?
24. A persona is detailed, relatable, and nicely written — but has no clear key task, no specific context of use, and no implied constraints. Is this a good or bad persona? Explain.
25. How does the **Data → Insight → Design Decision** framework connect to the **sense-making pipeline** (Raw Data → Sense Making → Analysis Artifacts → Design Decisions)?

---

*Notes based on CS3240 L4 lecture slides by Assoc Prof Bimlesh Wadhwa, NUS. For personal study use only.*
