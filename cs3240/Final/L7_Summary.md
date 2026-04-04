# Lecture 07: User Research Data Analysis — Task, Thematic & Quantitative Analysis

> **What this covers:** This lecture is the bridge between raw user research data and defensible design decisions. You will learn three analysis methods: **Task Analysis** (decomposing what users do into goals, tasks, and subtasks), **Thematic Analysis** (clustering raw user notes into themes via affinity diagramming, then converting themes into design ideas), and **Quantitative Analysis** (using numbers to complement qualitative insights). The lecture also covers how AI can assist in each of these steps, including prompt templates and their limitations. Understanding how these three layers work together — and how they connect back to personas and journey maps — is essential for the exam and for CS3240 projects.

---

## 1. The Big Picture: From Data to Defensible Design

Before diving into each method, it helps to see where we are in the overall user research process. Earlier lectures covered personas (who your users are) and journey maps (where problems happen along the user's experience). This lecture adds three more analytical layers:

**The Analysis Stack:**

| Layer | Question It Answers |
|---|---|
| Persona | **Who** is the user? |
| Journey Map | **Where** do problems happen? |
| Task Analysis | **How** do users act? |
| Thematic Analysis | **What patterns** explain user behaviour? |
| Quantitative Analysis | **How much** / **how often**? |

Each layer strengthens the next. You do not need to follow this order strictly — they can be done in parallel. However, personas are typically done before journey maps and task identification.

> **Key exam insight:** In CS3240 projects, artifacts are not graded for completeness — they are graded for the **decisions they enable**. Strong projects trace every feature back to user research, show how personas shape tasks, identify decision points and breakdowns, turn themes into design ideas, and use quantitative data to validate priorities.

---

## 2. Task Analysis

### 2.1 What Is a Task?

A **task** is a set of actions a user takes to achieve a goal. Tasks have a hierarchical structure:

**Goals → Tasks → Subtasks**

Think of it this way: a goal is the "why" (e.g., "eat lunch quickly between classes"), a task is the "what" (e.g., "order food for pickup"), and subtasks are the "how" (e.g., "open the app," "select a restaurant," "choose a meal," "pay").

### 2.2 Persona-Task Mapping

Different personas approach the same goal with different tasks. This is where the connection between personas and task analysis becomes powerful.

**Example — NUS Food Ordering App:**

**Persona 1: "Between-Classes Sprinter"** — A busy student with only 15 minutes between lectures.

| Dimension | Detail |
|---|---|
| Goal | Eat quickly between classes |
| Task | Order food for pickup |
| Time pressure | High — 15-minute window |
| Primary need | Speed and reliability |
| Navigation style | Fastest option, minimal browsing |
| Cognitive load | Low tolerance — wants quick decisions |
| Task length | Short — 2-3 minutes |

**Persona 2: "Planner & Explorer"** — A student with a longer break who enjoys exploring food options.

| Dimension | Detail |
|---|---|
| Goal | Find a satisfying meal during a longer break |
| Task | Compare food options |
| Time pressure | Low — has 1-2 hours |
| Primary need | Variety and discovery |
| Navigation style | Browsing, comparing menus and reviews |
| Cognitive load | Higher tolerance — enjoys exploring |
| Task length | Longer — 5-10 minutes of browsing |

> **Why this matters for design:** The Sprinter needs a fast, streamlined flow — maybe a "reorder previous meal" button right on the home screen. The Planner needs rich filtering and comparison features. If you only design for one persona, you fail the other. The persona tells you *who*; the task tells you *how they actually behave*.

### 2.3 What Task Analysis Means in Practice

Task analysis means you:

1. **Visualize the task** — map out the sequence of steps a user takes.
2. **Understand user perception** — how does the user think about and experience each step?
3. **Evaluate current behaviour** — what are users currently doing (with or without your product)?
4. **Identify decision points** — where does the user have to make a choice?
5. **Identify pitfalls and breakdowns** — where do things go wrong, and why?

**Example — Sprinter's "Order Food" Task with Decision Points and Pitfalls:**

The Sprinter opens the app → sees the home screen → needs to pick a restaurant. **Decision point:** browse by distance or by cuisine? **Pitfall:** if the app defaults to showing all restaurants sorted alphabetically, the Sprinter wastes time scrolling through irrelevant options. **Breakdown:** the Sprinter gives up and just goes to the nearest canteen instead.

### 2.4 Why Persona + Task Analysis Together = Defensible Design Decisions

This is a critical conceptual point:

- **Persona alone** = a story (interesting but not actionable).
- **Task analysis alone** = mechanics (detailed but lacks the "for whom").
- **Persona + Task analysis together** = **defensible design decisions**. You can justify every feature by pointing to a specific user (persona) performing a specific action (task) with a specific pain point (pitfall).

### 2.5 Key Task Criteria

A well-defined task should be:

- **Multi-step** — not just a single click, but a sequence of actions.
- **Observable** — you can watch or describe the user performing it.
- **Grounded in research** — derived from actual user data (interviews, observations), not assumptions.
- **Influences UI** — the task decomposition should directly inform your interface design.

### 2.6 Crafting Effective Task Titles

Task titles matter because they frame the design challenge. Good titles should:

- Reflect an **action or decision** the user makes, not just a static description.
- Be written from the **user's perspective**, reflecting their experience, concern, or objective.
- Be **concise** (usually under 10 words).
- If the task is a **problem**, the title should reflect urgency or difficulty (e.g., "Navigating a Tight Travel Schedule: Taré's Meal Dilemma").
- If the task is a **solution**, the title should emphasize ease or benefit (e.g., "Seamless Travel: Pre-Ordering a Meal for a Tight Connection").

---

## 3. Thematic Analysis (Affinity Diagramming)

### 3.1 What Is Thematic Analysis?

Thematic analysis is a method for finding **patterns (themes)** in qualitative user research data. The primary technique taught in this lecture is **affinity diagramming** — a bottom-up process of clustering raw user notes into groups, labelling those groups, and building a hierarchy of insights.

The core idea: you start with messy, unstructured data (interview quotes, observation notes, survey responses) and systematically organize it into structured insights that directly guide design decisions.

### 3.2 The Affinity Diagram Process (5 Steps)

**Step 1: Label User Notes**

Start with raw user data — quotes, observations, or paraphrased insights from individual users. Each note should be labelled with a user identifier and note number.

**Labelling convention:** U{user number}-{note number}. For example, U01-1 is the first note from User 1, U02-3 is the third note from User 2.

**Example — Facebook Privacy Study:**
- U01-1: "I have few close friends on Facebook. That's all I really care about."
- U02-2: "I only use Facebook to keep up with my mom."
- U03-1: "I wish I could completely hide my profile so no one else can find me."
- U04-1: "I am not available to make new friends."

**Step 2: Make Clusters**

Group (create **affinities** of) user notes based on common patterns or themes. Notes that express similar intents, problems, or issues go together.

In the Facebook example, you might cluster U02-2 ("keep up with my mom") and U01-1 ("few close friends… that's all I care about") together — both are about using Facebook for a small circle. Similarly, U03-1 ("hide my profile") and U04-1 ("not available to make new friends") cluster together — both are about avoiding contact with strangers.

**Step 3: Make a Header Note for Each Cluster**

Attach a summarized label to each cluster. The label should be on a **different colour** than the user notes, and it does **not** need to be in user voice — it is a researcher-written summary.

**Example labels:**
- Cluster 1 label: *"I use Facebook to keep in touch with few people close to me."*
- Cluster 2 label: *"I don't want to be bothered by people who are not close to me."*

**Step 4: Further Cluster (Build Bottom-Up Hierarchy)**

Continue building the hierarchy bottom-up — group clusters into higher-level themes. Typically you end up with **1 to 3 levels** of labels.

**Example higher-level label:** *"Some people want to share their social life with only few select and not with the whole world."* — This label sits above both cluster 1 and cluster 2.

**Step 5: Review, Discuss, and Generate Design Ideas**

Review the completed affinity diagram with your team. For each identified theme, generate Design Ideas (DIs).

### 3.3 From Themes to Design Ideas

Each identified theme corresponds to a **user need**. Each identified theme leads to one or more **Design Ideas (DIs)**.

**Example:**

| Theme | Design Idea |
|---|---|
| "I use Facebook to keep in touch with few people close to me." | Provide means to set only a few people/groups who can see a post |
| "I don't want to be bothered by people who are not close to me." | Provide means to hide a post; Provide means to hide profile |

**Another example:**

| Theme | Design Idea |
|---|---|
| "Users find it hard to stay motivated when using productivity apps." | Provide motivational feedback, gamification, or personalized reminders |
| "Users forget tasks frequently." | A context-aware reminder feature that adjusts notifications based on user routines |

### 3.4 The Affinity Diagram Template (Visual Hierarchy)

The final affinity diagram has a clear top-down visual structure:

```
                    [Top Level Label]
                   /                 \
        [Second Group Label]    [Second Group Label]
        /            \              /            \
  [Group Label]  [Group Label]  [Group Label]  [Group Label]
    |     |        |     |        |     |        |
  [User] [User]  [User] [User]  [User] [User]  [User]
  [Note] [Note]  [Note] [Note]  [Note] [Note]
```

Key reminders:
- Cluster user notes to make "affinities" — notes that focus on similar intents, problems, and issues.
- Affinity diagrams turn **messy qualitative data into structured insights** that directly guide design decisions.

---

## 4. Using AI in Task and Thematic Analysis

The lecture covers how AI tools (like Gemini, ChatGPT/GPT) can assist with each step of the analysis pipeline. The five AI-assisted steps are: Identifying Key User Tasks → Extract User Notes → Cluster and Label User Notes → Turn Themes into Design Ideas → Create Affinity Diagram.

**Critical caveat about using AI for data analysis:** When using AI, you must **keep it tied to the raw data**. Do not ask it to "make up" findings or let it fill in gaps. AI reasoning is based on patterns learned from its training data, which may not match your specific context or the reality of your study. Use it to speed things up, but **always double-check**: go back to the quotes, notes, or numbers, and consider the real context.

### 4.1 AI for Identifying Key User Tasks

**Input needed:** Raw user research data — interview transcripts, survey answers, observation notes, user goals/scenarios, pain points/frustrations, user context.

**Prompt template:**
- **ROLE:** Act as a senior UX researcher.
- **DATA:** <interview quotes / notes / observation logs>
- **CONCEPT:** <1 sentence product concept>
- **USER & GOAL:** <target user and research goal>
- **TASK:** Identify the key user tasks and sub-tasks based on the data.
- **OUTPUT:** Bullet list with headings, including assumptions and supporting evidence.

**Improving results:** The lecture notes that the first prompt may produce results that are too abstract or use too much academic language. A better approach is to instruct the AI to: avoid abstract or academic language, describe what the user actually does step by step using simple verbs, imagine explaining the behaviour to a friend who has never studied UX, and ensure each sub-task describes a real action or decision the user makes.

### 4.2 AI for Extracting User Notes

For the affinity diagram, the first step is to have the AI generate different users' viewpoints in a fixed, consistent format.

**Prompt template:**
- **TASK:** Based on the interview document, extract and rewrite the interview data into short, atomic user notes, suitable for placing on sticky notes.
- **RULES:** Each note must represent a clear idea, feeling, behaviour, or need. Use first-person voice. Keep each note to 1–2 short sentences. Do NOT summarize across users. Do NOT merge ideas. Stay close to what the user actually said; avoid researcher language.
- **LABELING FORMAT:** Label each note as U{user number}-{note number}. Example: U02-3 = third note from User 2. Number notes sequentially per user.

### 4.3 AI for Clustering and Labelling

**Prompt:** "Group these notes into clusters. For each cluster: (1) Give a short, clear label written as a first-person insight sentence. (2) List the user quotes that belong to that cluster."

### 4.4 AI for Building Bottom-Up Hierarchy

**Prompt:** "Continue building a bottom-up affinity hierarchy. Group these existing clusters into higher-level themes based on shared underlying needs or goals."

The lecture notes that different user viewpoints belong to different categories. When you want to progressively synthesize them into shared characteristics, it is necessary to give the AI **clear guidance and explicit requirements**.

### 4.5 AI for Themes to Design Ideas

**Prompt template:** "For each identified theme mentioned above, generate 1–2 Design Ideas. Each Design Idea should: directly respond to the user's need or problem expressed in the theme, be actionable and designable (not abstract principles), describe what the product could do, not how it is technically implemented."

### 4.6 AI for Creating the Affinity Diagram (Visualization)

**Prompt for visualization:**
- **TASK:** Visualize the following affinity hierarchy as a clean, top-down diagram.
- **VISUAL RULES:** Top-Level Theme at the top center. Second-Level Themes beneath it. Group Labels below each theme. User Notes at the bottom. No crossing lines. Clear vertical hierarchy. Do not add or change any content. Only focus on layout clarity and hierarchy.

**Limitations of AI-generated diagrams:** The lecture explicitly notes that AI-generated affinity diagrams often have issues — spelling errors, insufficient content richness, basic layout, limited text clarity. Even with detailed prompts, issues such as spelling errors and occasional ambiguity remain. As a result, the visualization **should be treated as a reference only** — the final diagram should be manually created.

**Tip:** When the output generated by one AI is unsatisfactory, you can ask another AI (e.g., ChatGPT) to review the result and suggest how the prompt should be revised to achieve the desired outcome.

---

## 5. Quantitative Data Analysis

### 5.1 What Quantitative Data Tells Us

Quantitative data complements qualitative findings. While qualitative data gives you **stories** (the "why"), quantitative data gives you **scale and evidence** (the "how many," "how often," "what and when").

Quantitative data answers questions like:
- How many users begin and finish a process?
- When is a certain option used?
- How often is an option used?
- Which different types of options do users select?
- What do users click on when they are on the main page?

**Important limitation:** Quantitative data **cannot tell us the "why."** It tells you *what* happened, but only users can tell you *why* it happened.

### 5.2 Basic Quantitative Measures

**Averages:**
- **Mean:** Add up values and divide by number of data points.
- **Median:** Middle value of data when ranked.
- **Mode:** Figure that appears most often in the data.

Also: percentages, frequencies, etc.

**Descriptors and Charts:** Visualizations of findings (e.g., bar charts, pie charts, dashboards showing usage statistics, percentages, counts).

### 5.3 How Quantitative Analysis Supports Personas, Journey Maps, and Task Flows

**Supporting Personas:** Quantitative data validates who your primary users really are. For example, survey results showing 70% of users are students aged 18–25 means your persona should reflect this dominant group. Metrics like frequency of feature use or time spent help flesh out persona behaviours.

**Supporting Journey Maps:** Quantitative data highlights where bottlenecks occur. For example, analytics showing drop-offs at checkout (45%) means the journey map should emphasize frustration at the Payment stage. Time metrics, clickstream data, or heatmap data reveals which steps take longest.

**Supporting Task Flows / Task Analysis:** Quantitative data measures efficiency and errors. For example, a task completion rate of only 60% succeeding on first try indicates usability issues. Average time-on-task shows where workflows can be streamlined.

### 5.4 Reflecting on Quantitative Data

**Strengths:**

| Strength | Example | Implication |
|---|---|---|
| Large Sample Sizes | Survey with 2,000+ respondents across demographics | Findings are more reliable and generalizable |
| Clear Patterns & Trends | Analytics show 80% of users drop off at checkout stage | Helps identify where to focus design improvements |
| Objective Evidence | Task completion times compared across two prototypes | Supports evidence-based design decisions |

**Limitations:**

| Limitation | Example | Implication |
|---|---|---|
| Missing Data | Survey has 200 responses but age field is empty | Cannot analyze age-based patterns |
| Limited Time Range | App usage tracked only during exam period | Findings may not generalize to normal usage |
| Data Accuracy Issues | Analytics inflated by bots / duplicate entries | Leads to false conclusions |
| Not User-Validated | Checkout takes 5 mins — unclear if intentional | Misinterpreting intent may cause wrong design decisions |

> **Key principle:** Always interpret charts with caution. Pair quantitative findings with qualitative insights to see the full picture. Quantitative data tells us *what happened*, but only users can tell us *why it happened*.

### 5.5 Reflecting on Qualitative Data

For completeness, the lecture also provides a reflection on qualitative data:

**Strengths:**

| Strength | Example | Implication |
|---|---|---|
| Rich, detailed insights | Interview reveals *why* users abandon checkout | Provides context behind user behaviour |
| Captures emotions & motivations | Observation shows frustration in facial expressions | Helps design for emotional experience |
| Uncovers unexpected findings | Open-ended survey response reveals unanticipated needs | Can inspire new features or product directions |
| Strong user empathy | Shadowing a user shows their real-life context | Keeps design grounded in real contexts |

**Limitations:**

| Limitation | Example | Implication |
|---|---|---|
| Small sample sizes | Only 6–10 users interviewed | Findings may not be generalizable |
| Subjective interpretation | Researcher interprets a user's comment differently | Risk of bias in design decisions |
| Time & resource intensive | Conducting 1-hour interviews with 20 users | Slower design cycles |
| Harder to quantify | User says "checkout is confusing" but no metric | Need quantitative data to confirm scale |

---

## 6. Summary: From Data to Defensible Design

### 6.1 The Analysis Stack (Revisited)

| Layer | Question | Method |
|---|---|---|
| Persona | Who | User interviews, surveys → persona creation |
| Journey | Where problems happen | Journey mapping |
| Task | How users act | Task analysis (goals → tasks → subtasks) |
| Theme | What patterns explain behaviour | Thematic analysis / affinity diagramming |
| Quantitative | How much / how often | Analytics, surveys, metrics |

Each layer strengthens the next. They can be done in parallel, but typically Persona is done before Journey and Task Identification.

### 6.2 What Strong vs. Weak Projects Do

**What Strong Projects Do:**
- Trace every feature back to user research
- Show how persona shapes task structure
- Identify decision points & breakdowns
- Turn themes into design ideas
- Use quantitative data to validate priorities
- Justify trade-offs clearly

**What Weak Projects Do:**
- Produce artifacts without linkage
- Write personas that do not influence tasks
- List task steps without analysis
- Cluster themes that never affect design
- Present numbers without interpretation

> **Bottom line for CS3240 projects:** Artifacts are not graded for completeness. They are graded for the **decisions they enable**.

---

## 7. Summary Table

| Topic | Key Concept |
|---|---|
| Task Analysis | Decompose goals → tasks → subtasks; identify decision points and pitfalls |
| Persona + Task | Together they produce defensible design decisions (persona alone = story, task alone = mechanics) |
| Key Task Criteria | Multi-step, observable, grounded in research, influences UI |
| Thematic Analysis | Bottom-up clustering: raw notes → clusters → labels → themes → design ideas |
| Affinity Diagram | 5-step process: label notes → cluster → header labels → hierarchy (1-3 levels) → review & generate DIs |
| Theme → Design Idea | Each theme = a user need; each need → actionable, designable feature ideas |
| AI in Analysis | Can assist all 5 steps, but must stay tied to raw data; always double-check; treat AI diagrams as reference only |
| Quantitative Analysis | Complements qualitative; answers "how many/often" not "why"; supports personas, journey maps, task flows |
| Quant Strengths | Large samples, clear patterns, objective evidence |
| Quant Limitations | Missing data, limited time range, accuracy issues, not user-validated |
| Qual Strengths | Rich insights, emotions, unexpected findings, empathy |
| Qual Limitations | Small samples, subjective, time-intensive, hard to quantify |
| Defensible Design | Requires linking all layers: persona → journey → task → theme → quant validation |
