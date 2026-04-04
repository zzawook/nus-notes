# Lecture 10: Prototyping

> **What this covers:** This lecture covers the **Prototyping** stage in the design thinking process — the bridge between ideation and user testing. You will learn about **storyboarding** (capturing task flows visually with context and narrative), the full **fidelity spectrum** from lo-fi sketches to wireframes to hi-fi interactive prototypes, the three **fidelity dimensions** (interactivity, visuals, content/navigation), **horizontal vs vertical** prototyping, **serial vs parallel** prototyping strategies, and the **Wizard of Oz** method for simulating functionality. The lecture also surveys **AI-powered prototyping tools** (Figma AI, Lovable, v0.dev, Wireframe Designer, UX Pilot, Uizard, Galileo Bot) and compares their strengths and weaknesses.

---

## 1. From Ideation to Prototyping: The Pipeline

The journey from ideation to a testable prototype follows this pipeline:

**Solution Ideas → Selected Ideas → Tasks / Flows / Screens / Interactions / Storyboards → Wireframing → Interactive Prototyping**

This means you do not jump directly from ideas to a hi-fi prototype. You first articulate what the user needs to do (tasks), how they move through the app (flows), what screens are needed, what interactions occur on each screen, and how the overall experience plays out (storyboards). Only then do you begin wireframing and building interactive prototypes.

---

## 2. Storyboarding

### 2.1 What Is a Storyboard?

A storyboard is **a sequence of sketches that captures a task description or TaskFlow**. Each sketch shows a moment in time — a single scene — represented as freeze-frame sketches or cells. Think of it like a comic strip that tells the story of a user completing a task.

Storyboards go beyond task flows by adding **user context** — where the user is, what they are feeling, why they are doing the task — which makes the experience tangible and communicable to stakeholders.

### 2.2 Example: ClosetLogue

A storyboard for a wardrobe app might show: (1) Rachel is getting ready for a formal event, (2) she opens the app, (3) she browses her digitized wardrobe, (4) she selects a dress, (5) she sees outfit suggestions, (6) she picks one and feels confident. Each frame captures both the **action** and the **emotional context**.

### 2.3 Example: Campus Food Ordering

A campus food ordering storyboard might show: (1) Student in lecture feels hungry, (2) opens the super-app, (3) browses nearby food stalls, (4) selects a stall and food item, (5) confirms order, (6) receives pickup notification. This illustrates how a task flow translates into visual scenes.

### 2.4 Other Storyboard Formats

Beyond hand-drawn sketches, storyboards can also take the form of **photo-based storyboards** (using real photos instead of drawings) and **video stories** (short video clips showing the user journey).

### 2.5 Two Parts of a Storyboard

Every storyboard has two layers:

| Part | Description |
|---|---|
| **Story part** | The narrative — provides purpose, context, background, and persona. Answers "why is the user doing this?" and "what situation are they in?" |
| **Task part** | The specific instructions — stepwise details about user interactions. Concrete and actionable. Used in prototype evaluation to verify that the prototype supports the intended task |

The story part gives meaning and motivation; the task part gives testable, concrete steps.

### 2.6 TaskFlows vs Storyboards

| Aspect | TaskFlow | Storyboard |
|---|---|---|
| **Focus** | "What user does" | Visual representation of the task flow **including** user context and emotions |
| **Format** | Descriptive / diagrammatic | Concrete / visual / narrative |
| **Purpose** | Analyzing and validating tasks | Communicating design ideas to stakeholders and team |

TaskFlows are more analytical; storyboards are more communicative. Storyboards make it easier for non-designers to understand and critique a design idea.

### 2.7 How to Use in a CS3240 Project

For the CS3240 project, you should identify **3 key tasks per service**, provide **TaskFlows** for each, and optionally create **storyboards** to communicate the design vision.

---

## 3. Prototyping: Why and What

### 3.1 Why Prototype?

The goal of prototyping is to **let users try out prototypes for the tasks you have developed**. Specifically, prototyping allows you to:

- Test ideas before building the full product
- Identify usability problems early (when they are cheap to fix)
- Compare alternative design options
- Get feedback from real users

### 3.2 Prototype Fidelity

**Fidelity** refers to the degree of realism in a prototype. There is a spectrum:

**Storyboard → Lo-fi Prototype → Wireframe → Hi-fi Prototype**

Each level increases in detail, interactivity, and visual polish. The key principle is: **do NOT jump directly to hi-fi**. Start rough and iterate, because early-stage changes are cheap and late-stage changes are expensive.

### 3.3 Three Fidelity Dimensions

Fidelity is not a single axis — it has three independent dimensions:

| Dimension | What It Measures |
|---|---|
| **Interactivity** | How much the user can actually interact with the prototype (static screens vs clickable vs fully functional) |
| **Visuals** | How close the look-and-feel is to the final product (rough sketches vs polished UI) |
| **Content / Navigation** | How realistic the content is and how complete the navigation structure is |

A prototype can be high on one dimension but low on another. For example, a wireframe might have complete navigation (high content/navigation) but minimal visuals (low visuals) and limited click-through (medium interactivity).

---

## 4. Lo-fi Prototypes

### 4.1 What Is a Lo-fi Prototype?

A lo-fi (low-fidelity) prototype is a rough, quick representation of a design. Characteristics include:

- **Rough boxes** with minimal detail
- **Minimal colours** and text
- Focus on **flow and structure**, not aesthetics
- Made from art/craft materials (paper, sticky notes, cardboard) or simple digital sketches
- **Faster and cheaper** to iterate on than detailed prototypes
- **Not limited by technology** — you can sketch anything

### 4.2 Example: PalmPilot Wooden Model

A famous example of lo-fi prototyping is Jeff Hawkins' wooden block model of the PalmPilot. Before building any technology, Hawkins carried a block of wood cut to the intended size of the device in his shirt pocket for weeks, pretending to use it. He would pull it out, tap on it with a chopstick (simulating a stylus), and "check his calendar." This helped him validate the form factor and basic interaction concept before any engineering began.

### 4.3 Example: Mozilla's Support Website

Mozilla used a lo-fi paper prototype (hand-drawn wireframes on paper) to test the layout of their Support website before building it digitally. Users could interact with the paper prototype by pointing at elements, and the facilitator would swap out paper "screens" to simulate navigation.

---

## 5. Wireframes (Mid-Fidelity)

### 5.1 What Is a Wireframe?

A wireframe is **a rough schematic** created in the early stages of digital product design to help visualize and communicate the structure of a product or website. More precisely, a wireframe is a **visual skeletal representation** of an intended design.

### 5.2 Characteristics

- **Layout** — arrangement of content, interface elements (navigation, buttons)
- **Generally limited fonts**, minimum content
- **White / black / grey colour** palette, minimum graphics
- Focuses on structure and information hierarchy, not aesthetics

### 5.3 Why Use Wireframes?

There are three key reasons:

1. **Faster to create** because they are less detailed than hi-fi prototypes
2. **Product appearance and/or functionality are not fully defined** yet, so many details are unknown — wireframes acknowledge this
3. **Focus reviewers and collaborators on structure, layout, and navigation** instead of visual details — prevents premature bikeshedding on colours and fonts

### 5.4 Tools

Common wireframing tools include **Balsamiq** (deliberately sketch-style to keep focus on structure) and various digital tools. Wireframes can also be created in Figma, Sketch, or even PowerPoint.

---

## 6. Hi-fi Prototypes

### 6.1 What Is a Hi-fi Prototype?

A hi-fi (high-fidelity) prototype is **a detailed representation of a product**, usually created later in the design phase, that works, looks, behaves, or moves **as closely as possible to the intended final product**.

### 6.2 Characteristics

- **As close as possible to the real interface** — realistic visuals, interactions, content, navigation, and layout
- **Testable by users** for the specific tasks the prototype is designed for
- Includes real (or realistic) content, proper typography, colour schemes, and interactive elements

### 6.3 Example: Campus Food Ordering App

A hi-fi prototype for a food ordering app would include realistic screens showing: Home screen with stall listings and images → Food category filtering → Stall detail page with menu → Cart with item quantities and prices → Payment screen with methods (PayNow, Apple Pay, Credit/Debit Card). All screens are linked with clickable interactions that simulate the real user flow.

---

## 7. Summarising: From Task to Prototype

The full progression from task to testable prototype:

| Stage | What It Produces | Focus |
|---|---|---|
| **Storyboard** | Concept, user task flow in context | Narrative, motivation, user context |
| **Lo-fi Prototype** | Test task flow with rough screens | Flow correctness, basic structure |
| **Wireframe** | Layout and content structure | Information hierarchy, navigation |
| **Hi-fi Prototype** | Interactions that feel real | Full user experience, usability testing |

---

## 8. Prototype Functionality: Horizontal vs Vertical

### 8.1 Horizontal Prototypes

A horizontal prototype provides a **surface-level view of the entire user interface or set of tasks** with **not much underlying functionality**. It shows the breadth of the system — many screens and features — but none of them work deeply. Think of it as a wide but shallow slice of the product.

### 8.2 Vertical Prototypes

A vertical prototype includes **in-depth functionality for a few selected tasks**. With a task-centred design, users can test a few tasks **in depth**, experiencing the full flow from start to finish. Think of it as a narrow but deep slice of the product.

### 8.3 CS3240 Project Guidance

For the CS3240 super-app service:

- **DO:** Prototype 2–3 key tasks end-to-end (vertical prototyping)
- **DON'T:** Prototype the entire app superficially (horizontal prototyping)
- **Focus:** Vertical prototyping for selected tasks

The principle is: *"The more of the prototype that is completed, the more you can test."*

---

## 9. Serial vs Parallel Prototyping

### 9.1 Serial Prototyping

Develop **one prototype at a time**, refining it step by step. Each iteration takes feedback and produces a revised version.

**Pros:** Deep focus on a single idea per iteration.
**Cons:** Risk of **tunnel vision** and missing alternatives — you become attached to one design direction.

### 9.2 Parallel Prototyping

Develop **multiple variations of a design simultaneously**, then compare and combine the best elements from each.

**Pros:** Encourages **diversity of ideas**; leads to **better design decisions** (supported by research from Stanford — Dow et al.).
**Cons:** Requires **more effort in coordination**.

### 9.3 Recommendation

**Parallel prototyping is recommended** for the CS3240 project to explore multiple design ideas and merge the best elements into a final prototype. This approach is commonly used in industry to avoid premature commitment to a single design.

---

## 10. Wizard of Oz Method

### 10.1 What Is the Wizard of Oz Method?

The Wizard of Oz (WOZ) method uses prototypes with **faked functions**. The interactivity is **simulated by a hidden human ("wizard")**, while the user believes the system is real. The term was coined by John F. ("Jeff") Kelley during his dissertation work at Johns Hopkins University, inspired by the children's novel "The Wonderful Wizard of Oz" by Frank Baum — where the powerful wizard turns out to be an ordinary man pulling levers behind a curtain.

### 10.2 How It Works

- The **user** uses the system as expected — they interact with what appears to be a working interface
- A **human wizard**, generally hidden from the user, simulates the system's intelligence and interacts with the user through a real-like interface
- The wizard **interprets the user's input** according to an algorithm and updates the interactive screen display accordingly

### 10.3 When to Use Wizard of Oz

WOZ is especially useful when **developing and testing the viability of interfaces that use new complex technologies** where testing with static content is difficult and full deployment of the technology is expensive. Specific use cases include:

- **Robotic, AI, and AR applications** — where building the actual intelligence is expensive
- **Interfaces that take real-time information and present results** — where simulating the backend is easier than building it
- **Conversational UIs** — where natural language understanding can be faked by a human

WOZ is fundamentally a technique for **testing futuristic ideas** before the technology is ready.

### 10.4 Three Types of Wizard of Oz

| Type | How the Wizard Responds |
|---|---|
| **Closed WOZ** | Wizard chooses from **pre-existing responses** — a fixed script or response set |
| **Open WOZ** | Wizard comes up with responses **on the fly** — requires the wizard to have product knowledge to compose responses in real time |
| **Hybrid WOZ** | Wizard chooses from an existing set of responses **OR** creates a new response as needed — combines the structure of closed with the flexibility of open |

---

## 11. Applying Prototyping Concepts in CS3240

For the CS3240 project and design assignment:

- Your project requires **both lo-fidelity and hi-fidelity** prototyping
- Prototype **at least 3 key user tasks** for each service (you are NOT required to design the entire service)
- Each task maps to a prototype flow: Task 1 → Prototype flow, Task 2 → Prototype flow, Task 3 → Prototype flow
- You will later **test your service** for these three key tasks
- Overall, you should apply horizontal/vertical prototyping, improve iteration and collaboration skills, and experience real-world product development workflows

---

## 12. Wireframing and Prototyping with AI ("Vibe Coding")

### 12.1 Context

The lecture explores how effectively AI tools can translate a design idea into visual concepts and working prototypes. The approach uses **the same core prompt across a few prototyping tools**, adjusting the focus and format as the tool requires it.

### 12.2 Figma AI (Figma Maker)

**What it does:** Generates UI screens and basic prototypes within Figma.

**Prompt used:** Design a mobile and web dashboard for a conference food waste management system, specifying 5 screens (organizer dashboard, vendor reporting, real-time alerts, donation coordination, sustainability report) with focus on clear information hierarchy, simple user flow, data visualization, and clean modern interface.

**Results:**
- **Time taken:** ~5 minutes to generate initial screens
- **Strengths:** Good starting point; quickly generates basic dashboard structure and layout; covers obvious features (analytics, reports, data inputs); consistent visual style across screens; can iterate quickly if prompts clearly describe changes
- **Weaknesses:** Low novelty (predictable layouts); fragmented flows (screens not well integrated); needs precise prompts (vague requests produce generic output); no system explanation (AI does not explain the logic or structure it created); prototype instability (interactive links often break); requires human refinement; paid version for manual editing

**Best use case:** Rapid UI layout exploration.

### 12.3 Lovable AI

**What it does:** Creates working web app prototypes with interactive features.

**Prompt used:** Create an interactive prototype for a conference food waste management platform, with user flow steps (login → dashboard → vendor logging → alerts → donation assignment), focusing on simple dashboard layout, clear navigation, basic interactions, and mobile-friendly interface.

**Results:**
- **Time taken:** ~10 minutes to generate the prototype
- **Strengths:** More interactive setup (confirms features and requirements before building); clear system overview (structured explanation of what was generated); adds realistic product structure (login flows, landing pages, event summaries); more product-like output (feels closer to a real application); logical product flow with login and landing; stable prototype (navigation and interactions work consistently); handles edits well for specific iterative prompts
- **Weaknesses:** Basic interpretation of prompt (stays literal and simple); misses some requested details (e.g., sustainability metrics may be overlooked); limited creativity (conventional outputs, no novel interaction ideas); no real data visualization (placeholders without meaningful charts); component editing restricted (paid version needed for direct UI editing)

**Best use case:** Quick functional prototype.

### 12.4 v0.dev (VibeCoding for Generating UI)

**What it does:** Generates coded UI interfaces (functional frontend code).

**Approach:** Iterative prompting in steps — (1) initial build with features, (2) feature expansion (e.g., vendor leaderboard), (3) UX improvement (navigation, icons, colour coding), (4) data visualization (charts for waste trends), (5) mobile optimization and motion design.

**Results:**
- **Time taken:** ~15–20 minutes to generate the interface
- **Strengths:** Stronger for generating functional coded interfaces; stable functional prototype (generally works reliably across screens); supports motion elements (basic micro-interactions when requested); improves with edits (iterative prompts can improve output but require very specific instructions)
- **Weaknesses:** Weak visual quality (below basic design standards); poor data visualization (badly designed charts and graphs); bug-prone builds (code sometimes fails to load or run); slow generation time; poor visual decisions (default colour choices look unrefined); mobile limitations (struggles even when specifically prompted); prompt-heavy workflow (many detailed prompts needed); no creative exploration (focuses on functional implementation, not new design ideas)

**Best use case:** AI-assisted frontend generation.

### 12.5 Comparison Table: Figma AI vs Lovable vs v0.dev

| Metric | Figma AI | Lovable | v0.dev |
|---|---|---|---|
| Time to generate | ~5 mins | ~10 mins | ~15–20 mins |
| Type of output | UI screens + basic prototype | Working web app prototype | Coded UI interface |
| Visual design quality | Clean but always same | Decent but basic | Poor visuals |
| Data visualization | Basic dashboard charts | Mostly placeholders | Section clear, charts poor |
| UX flow quality | Fragmented, screens not well integrated | Logical product flow with login & landing | Not well structured |
| Creativity / novelty | Low — predictable layouts | Low — literal interpretation | Very low — purely functional |
| Realism of product | Feels like design mockups | Feels closest to a real product | Feels like a raw coded interface |
| Best use case | Rapid UI layout exploration | Quick functional prototype | AI-assisted frontend generation |

### 12.6 Other AI Tools Mentioned

- **Wireframe Designer** — AI-powered wireframe generator within Figma. Generates wireframes from text prompts. Limited: elements are locked and cannot be individually moved/edited.
- **UX Pilot** — Generates both wireframes and hi-fi designs; supports multiple screens per generation; can generate in both desktop and mobile; syncs to Figma. Provides structured prompt input (layout, style, context).
- **Uizard** (Miro Labs) — Text-to-UI generator; supports mobile, tablet, and desktop; generates multiple screens from a single prompt. Can produce both wireframe and hi-fi style outputs.
- **Galileo Bot** — Chat-based UI generation; generates multiple screen variations; supports export to Figma and code; good visual quality for themed designs.
- **Other tools not yet tried:** Creatie, UXPilot AI, Framer AI, Musho AI, Relume AI

---

## 13. Summary

| Concept | Key Point |
|---|---|
| Storyboarding | Sequence of sketches capturing a task flow with user context; has two parts — story (narrative/motivation) and task (concrete steps) |
| Fidelity spectrum | Storyboard → Lo-fi → Wireframe → Hi-fi; **do not jump to hi-fi directly** |
| Three fidelity dimensions | Interactivity, Visuals, Content/Navigation — independent axes |
| Lo-fi prototypes | Rough, fast, cheap; focus on flow not aesthetics; paper, sticky notes, craft materials |
| Wireframes | Mid-fidelity skeletal representations; black/white/grey; focus on layout, hierarchy, navigation |
| Hi-fi prototypes | As close to real product as possible; testable for specific user tasks |
| Horizontal vs Vertical | Horizontal = broad but shallow; Vertical = narrow but deep; CS3240 recommends vertical for 2–3 key tasks |
| Serial vs Parallel | Serial = one at a time (risk of tunnel vision); Parallel = multiple simultaneously (better design decisions); parallel recommended |
| Wizard of Oz | Faked functionality with hidden human wizard; three types: Closed (scripted), Open (improvised), Hybrid (mixed); useful for testing futuristic/complex-tech ideas |
| AI prototyping tools | Figma AI (fast layout), Lovable (functional prototype), v0.dev (coded UI); all require human refinement and lack creative novelty |

**Key takeaways:**
1. **Prototyping is about testing ideas, not building products.** The goal is to put something testable in front of users as early and cheaply as possible.
2. **Start low, go high.** Begin with lo-fi prototypes to validate flow and structure, then progressively increase fidelity. Jumping to hi-fi too early wastes time on visual details before the structure is validated.
3. **Vertical over horizontal** for task-centred design — prototype a few tasks deeply rather than many tasks superficially.
4. **Parallel prototyping leads to better designs** by encouraging exploration of alternatives and preventing premature commitment.
5. **Wizard of Oz** is a powerful technique for testing ideas that rely on complex technology (AI, AR, conversational UI) without building the actual technology.
6. **AI tools accelerate prototyping** but are not replacements for design thinking — they produce good starting points that require significant human refinement, lack creative novelty, and struggle with integrated UX flows.
