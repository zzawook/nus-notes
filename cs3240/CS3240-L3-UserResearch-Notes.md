# CS3240 Lecture 3: User Research

> **Module:** CS3240 Interaction Design | **Semester:** S2 AY2025-26 | **Instructor:** Assoc Prof Bimlesh Wadhwa

---

## Table of Contents

1. [What is User Research?](#1-what-is-user-research)
2. [Planning and Conducting User Research (8 Steps)](#2-planning-and-conducting-user-research-8-steps)
3. [Step 1: Who Are You Designing For?](#3-step-1-who-are-you-designing-for)
4. [Step 2: Why Do They Need What They Say They Need?](#4-step-2-why-do-they-need-what-they-say-they-need)
5. [Step 3: Where, When, How Often?](#5-step-3-where-when-how-often)
6. [Step 4: User Segmentation](#6-step-4-user-segmentation)
7. [Step 5: Recruitment](#7-step-5-recruitment)
8. [Step 6: Choose a UR Method](#8-step-6-choose-a-ur-method)
9. [Step 7: Make a Plan](#9-step-7-make-a-plan)
10. [Step 8: Execute and Collect Data](#10-step-8-execute-and-collect-data)
11. [User Research Methods — Deep Dive](#11-user-research-methods--deep-dive)
12. [Interviews](#12-interviews)
13. [Surveys](#13-surveys)
14. [Observations](#14-observations)
15. [Supporting Techniques](#15-supporting-techniques)
16. [AI in User Research](#16-ai-in-user-research)
17. [Summary and Key Takeaways](#17-summary-and-key-takeaways)
18. [Quick-Fire Exam Review](#18-quick-fire-exam-review)

---

## 1. What is User Research?

> **User Research** is the process of **discovering and understanding** users' existing problems/pain points, workflows, familiarity with technology, preferences, and new needs.

User research sits within the **Empathize** phase of Design Thinking (from L2).

### Two Types of User Research

| Type | Purpose | When to Use |
|---|---|---|
| **Discovery Research** | Understand users and their needs from scratch | Designing a **new** product |
| **Evaluative Research** | Assess how well an existing product meets user needs | **Redesigning** an existing product |

> **CS3240 project note:** For the super-app project, you will likely do **discovery + early evaluative checks**.

---

## 2. Planning and Conducting User Research (8 Steps)

The user research process is organized into **three phases** containing **8 steps**:

```
Phase A: Identify & Recruit Users          Phase B: Select Method     Phase C: Plan & Execute
┌─────────────────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  Steps 1-5                  │     │  Step 6           │     │  Steps 7 & 8     │
│  1. Who are the users?      │     │  Choose a UR      │     │  7. Make a plan  │
│  2. What are motivations?   │────>│  method            │────>│  8. Execute &    │
│  3. Context (where/when)    │     │  (Interviews,      │     │     collect data  │
│  4. User segmentation       │     │   Surveys,         │     │                  │
│  5. Recruitment              │     │   Observations)    │     │                  │
└─────────────────────────────┘     └──────────────────┘     └──────────────────┘
```

---

## 3. Step 1: Who Are You Designing For?

**Goal:** Identify the different types of users for your product.

### Example: Spotify Users

| User Group | Description |
|---|---|
| **Listeners** | Users who enjoy background music while performing daily activities |
| **Artists** | Use dedicated tools to manage profiles, track performance metrics, and engage with their audience |

> **Key principle:** Different user groups have different needs, behaviors, and goals. Start by mapping out all potential user types.

---

## 4. Step 2: Why Do They Need What They Say They Need?

**Goal:** Understand user **motivations** — the deeper "why" behind what users ask for.

Express motivations as **user stories** (the "As a..., I want to..., so that..." format):

### Example: Spotify Listener Motivations

| Motivation | User Story |
|---|---|
| **Discover & Enjoy Music Efficiently** | As a Spotify listener, I want to navigate the app easily, so that I can **quickly access my favorite playlists and discover new music** without any hassle. |
| **Personalized Recommendations** | As a Spotify listener, I want to receive tailored music recommendations, so that I can **effortlessly explore songs and artists that match my taste**. |
| **Seamless Entertainment Experience** | As a Spotify listener, I want to switch seamlessly between music and podcasts, so that I can **enjoy a smooth, uninterrupted listening experience** no matter what I'm in the mood for. |

---

## 5. Step 3: Where, When, How Often?

**Goal:** Understand the **context** in which users experience their needs or problems.

### Context Checklist

Use this 5-point checklist to capture the full picture:

| Context Factor | What to Capture | Spotify Example |
|---|---|---|
| **Physical Location** | Where the user is when using the product | Commuting on a crowded metro train during rush hour |
| **Trigger Event(s)** | What causes the user to engage with the product | Disconnects from WiFi when boarding the train; decides to play a personalized playlist; avoids mobile data use |
| **Platform Constraints** | Device, OS, accessories | Spotify mobile app on Android with Bluetooth earbuds |
| **Infrastructure** | Technical limitations and frustrations | Frustration when playback is interrupted or offline playlists aren't accessible |
| **Social Context** | Who else is involved; social behaviors | Prefers private listening but occasionally shares songs with friends via WhatsApp; not on other social media |

---

## 6. Step 4: User Segmentation

**Goal:** Classify users into **primary** and **secondary** groups.

| User Type | Definition | Spotify Example |
|---|---|---|
| **Primary** | The **main target** of your design. Someone who interacts directly with the system. | Listener who creates & shares a playlist |
| **Secondary** | May **not directly interact** with the system but is still affected/benefited by it, or has input that affects the design. | Friend who receives the shared playlist (their reactions/feedback shape what gets added, even though they didn't create it) |

> **Exam tip:** Always clearly distinguish who the primary vs. secondary users are and explain **why**.

---

## 7. Step 5: Recruitment

Recruitment has three sub-steps:

### 5a: Identify Population and Sample

| Concept | Definition |
|---|---|
| **Population** | The **entire group** of potential users who could be relevant to your research — everyone who could potentially use the product or service |
| **Sample** | A **subset** of the population that you select for study. Must be **representative** — reflecting the diversity and characteristics of the entire population |

### 5b: Choose a Sampling Method

| Method | Description | CS3240 Relevance |
|---|---|---|
| **Random** | Recruit participants randomly from the population | Ideal but **rare** in class projects |
| **Convenience** | Recruit participants based on **ease of access** | Common in class projects but **must explain the bias risk** |
| **Purposive** | Recruit for **specific behavior/role** | **Recommended** for class projects |
| **Saturated** | Recruit until no new insights emerge / select everyone from the population | **Not everyone** — impractical for class projects |

### 5c: Make Selection Criteria

| Criteria Type | Definition |
|---|---|
| **Inclusion** | Characteristics that participants **must have** to be eligible for the study |
| **Exclusion** | Characteristics that **disqualify** participants from being included in the study |

### Example: Online Food Delivery App

**Population:** People who order meals online.
**Target Population:** Urban residents aged 18-40 who regularly use delivery apps.
**Sample:** ~20-30 frequent users who place at least one order per week.

| Inclusion | Exclusion |
|---|---|
| People who order food online at least once a week | People without internet access or smartphones |
| People who own a smartphone and are comfortable using apps | People who never or rarely order online (once every few months) |
| People who live in delivery service areas | Restaurant owners or delivery drivers (different perspective; better suited for separate research) |

### How Inclusion/Exclusion Criteria Tie to Research Goals

The criteria **change depending on the research goal**:

| Research Goal | Inclusion | Exclusion |
|---|---|---|
| **Usability** of ordering process | Frequent users (at least once/week) — most exposed to friction | Rare users (once every few months) — struggles may stem from unfamiliarity, not real usability flaws |
| **Satisfaction & trust** | Users in active delivery zones who have experience with multiple restaurants — can compare and evaluate trust signals | People outside delivery areas (irrelevant experience); Restaurant staff/drivers (conflict of interest) |
| **Feature exploration** (e.g., smart recommendations) | Busy professionals or students — order often during lunch/dinner peaks | Users who always cook at home or only use the app during rare special occasions — won't provide meaningful insights for time-pressure contexts |

---

## 8. Step 6: Choose a UR Method

The four main categories of UR methods:

| Category | Description |
|---|---|
| **Interviews** | In-depth conversations with individual users |
| **Surveys** | Standardized questions distributed to many users |
| **Observations** | Watching users interact with products/environments |
| **Other Supporting Techniques** | Dogfooding, diary studies, etc. |

> There are many other UR methods, but **Interviews, Surveys, and Observations** are foundational. Often a **mixed-method approach** is used.

---

## 9. Step 7: Make a Plan

**Goal:** Create a structured plan based on the selected UR method(s).

| Planning Dimension | Key Questions |
|---|---|
| **What** | What needs to be prepared? |
| **When** | When does it happen? |
| **Where** | Where does it happen? |
| **Who** | Who is involved and who does what? |
| **Data** | What data (quantitative or qualitative) needs to be collected? |

---

## 10. Step 8: Execute and Collect Data

**Goal:** Execute the plan and collect **quantitative** and **qualitative** UR data.

### Types of User Research Data

| Data Type | Definition | Examples |
|---|---|---|
| **Quantitative Data** | Numeric data — "how much", "how many", "how often" about needs of the users | Ratings, frequencies, counts, task completion times |
| **Qualitative Data** | Non-numerical data — users' opinions, preferences, daily task information | Interview quotes, observations, open-ended survey responses |

### What UR Data Captures

- Existing problems / pain points
- Workflows
- Familiarity with technology
- Preferences
- New needs

---

## 11. User Research Methods — Deep Dive

This section expands on the three foundational UR methods introduced in Step 6.

---

## 12. Interviews

> **Interviews** are a user research method for collecting **(mostly) qualitative data** from a **few** participants using structured / semi-structured / unstructured sets of questions.

### Three Types of Interviews

| Type | Structure | Flexibility | Best For | Limitation |
|---|---|---|---|---|
| **Structured** | Questions are standardized and predetermined; interviewer sticks closely to the script | Low | Collecting **comparable data** across many participants | Less room for unexpected insights |
| **Semi-Structured** | Guiding framework with key questions + topics; flexibility to probe deeper based on responses | Medium | **Most insightful** for user research — balances structure with openness | Requires skilled interviewer |
| **Unstructured** | No fixed list of questions; flows like a free-form conversation | High | **Exploratory stage** — discovering unexpected user needs or behaviors | Harder to compare results between participants |

### Examples of Each Type

| Type | Example |
|---|---|
| **Structured** | Banking app redesign: every participant asked the same fixed questions about login security — e.g., "Do you face any difficulty entering your PIN?" |
| **Semi-Structured** | Food delivery app: ask planned questions like "What do you usually look for when choosing a restaurant?" but also follow up — e.g., "You said you often cancel orders — tell me more about that." |
| **Unstructured** | Fitness routines: chat openly — "Tell me about how you track your workouts" — leading to varied, rich stories |

### Typical User Interview Flow

```
Introduction ──> Warm up ──> Main Interview ──> Wrap up
```

| Phase | What to Do |
|---|---|
| **Introduction** | Introduce yourself, explain the session purpose, confirm duration, ask permission to record, get signed consent form |
| **Warm up** | Get to know the participant — occupation, daily activities, goals, motivations, frustrations |
| **Main Interview** | Ask core research questions; use "What," "How," and "Why" questions; probe deeper when needed |
| **Wrap up** | Thank the participant, ask if they have anything else to add, explain next steps |

### How to Prepare, Schedule, and Conduct Interviews

| Stage | What to Do |
|---|---|
| **Prepare** | Define research goals; draft questions (structured / semi-structured / unstructured); prepare consent form & short intro script; test recording setup |
| **Schedule & Setup** | Decide location (lab, cafe, online, user's natural environment); arrange equipment (laptop, recorder, tripod, online meeting tool); ensure quiet & comfortable space |
| **Conduct & Collect Data** | Start with warm welcome & informed consent; listen actively and probe when needed; record audio/video (with permission); take notes (or have a note-taker) |

### Question Design Best Practices

#### Open-Ended vs. Closed Questions

| Type | Purpose | Examples |
|---|---|---|
| **Open-ended** | Get people talking/writing freeform; exploratory situations; **qualitative data** | "Walk me through a typical daily task." / "Tell me about the last time you lost your wallet." / "How did you feel about the e-wallet when you used it first time?" |
| **Closed** | Limited answer scope; short clarifying response; **quantitative data** | "What's your job title?" / "Have you used the website before?" / "How many times do you do this task during the day?" |

#### "What," "How," and "Why" Questions

| Question Type | Purpose |
|---|---|
| **What** is the problem you're trying to solve? | Identify the problem space |
| **How** do people currently get things done? | Understand current workflows |
| **Why** do people do things the way they currently do? | Uncover motivations and reasoning |
| **What** helps users get things done right now? | Identify existing tools and support |

> **Prefer "how" and "why" questions** over hypothetical questions.
> - GOOD: "Can you show me **how** you manage your campaign's finances?" / "**Why** do you submit that form on a quarterly basis?"
> - BAD: "Do you think you'd use this if we designed it differently?" (hypothetical — unreliable answers)

#### Common Mistakes to Avoid

**1. Avoid leading questions:**
- BAD: "How much did you **enjoy** with your teachers during sports day at school?" (assumes enjoyment)
- GOOD: "Share with us your experience at the sports day."

**2. Avoid multiple questions in a single sentence:**
- BAD: "How do you like this smartphone app compared with the previous ones that you have owned?"
- GOOD: "How do you like this smartphone app?" / "Have you owned other smartphone apps?" / "How did you like them?"

### Taking Notes During Interviews

- **No need** to transcribe everything said in the interview
- **Capture only** the quotes and parts of the discussion that are **relevant to the user research**
- If you use transcription tools, **anonymize before pasting into AI**; **never upload sensitive data**

---

## 13. Surveys

> **Surveys** are a method for collecting **(mostly) quantitative data** from a **larger audience** using standardized questions.

### Key Advantages Over Interviews

| Advantage | Details |
|---|---|
| **Scalability** | Efficiently gathers data from many participants |
| **(More) Quantitative data** | Enables statistical analysis and trend identification |
| **Cost-Effectiveness** | Often quicker and less resource-intensive than in-depth interviews |

### How to Prepare, Schedule, and Conduct Surveys

| Stage | What to Do |
|---|---|
| **Prepare** | Define research goals (what you need to learn); draft survey questions (clear, concise, avoid bias); decide format (online / paper / in-person kiosk); design survey flow: background info & consent statement, clear instructions, logical order (easy -> harder, general -> specific), mix of closed (ratings, multiple-choice) & open questions |
| **Schedule & Setup** | Decide survey open & close dates; communicate deadlines clearly; set cut-offs for responses (e.g., target sample size or time limit) |
| **Conduct & Collect Data** | Launch survey (send link or distribute papers); track response rate, send reminders if needed; check for unusual submissions (duplicates, spam, incomplete); close survey and thank participants |

---

## 14. Observations

> **Observations** are a qualitative user research method where researchers **watch users interact with products or environments in real-time**.

### What to Observe

| Category | Details |
|---|---|
| **Context** | Physical, Social, Organizational, Technical |
| **Tasks at hand and their purpose** | What are users trying to accomplish? |
| **Specific actions and interactions** | How do users behave and interact? |
| **Current support options** | What tools/help do users currently rely on? |

### Four Types of Context

| Context | Examples |
|---|---|
| **Physical** | Lighting, noise, movement, dust, humidity, vibration; crowded spaces |
| **Social** | Shared data, synchronous sharing, physical location of collaborators |
| **Organizational** | Facilities and resources, communication infrastructure |
| **Technical** | Technologies the product will run on and need to be compatible with, technological limitations |

### Direct vs. Indirect Observations

| Type | Description | Examples |
|---|---|---|
| **Direct Observation** | Researcher watches users in person | Observations in the field; observations in controlled environments |
| **Indirect Observation** | Track users indirectly, without being physically present | Diaries; Experience Sampling Method (ESM); web analytics; video/photographs collected remotely; fly-on-the-wall method |

### How to Prepare, Schedule, and Conduct Observations

| Stage | What to Do |
|---|---|
| **Prepare** | Define research goal (what behaviors to understand); plan time & duration; decide granularity (broad vs. fine-grained); prepare consent form & participant briefing (if not covert observation) |
| **Schedule & Setup** | Choose the setting/environment (lab, home, workplace, public space); decide on equipment (notebook, camera, screen recorder, tally sheets); consider safety, privacy, and ethical issues; do a pilot run to test logistics |
| **Conduct & Collect Data** | Observe systematically: **Who** (users, roles), **Where** (environment, layout), **What** (tasks, actions, objects, interactions), **When** (timing, sequences), **How** (tools used, emotions, body language); record data with field notes, audio/video (with permission), sketches or photos |

### Example: Observation for Campus Navigation App

| Stage | Details |
|---|---|
| **Research Goal** | Understand how new students find their way to lecture halls or key facilities (library, cafe) during their first weeks on campus |
| **Behavior to observe** | Stopping, asking others for directions, checking maps/signs |
| **Prepare** | Observe during first week of semester; time slots: 8:30-9:30am, 12-1pm, 4-5pm; each session ~45 minutes across 3 days |
| **Granularity** | Broad: track general movement patterns from main entrance to buildings. Fine-grained: record micro-behaviors at a tricky junction (Do students pause? Look at signs? Ask peers? Pull out phones? How long do they hesitate?) |
| **Consent** | Overt: consent form explaining purpose, anonymous data handling. Covert: ethical approval required, collect only non-identifiable data |
| **Schedule & Setup** | Pick 3 busy times of day, use notebook + map to record paths |
| **Conduct** | Observe students entering the main building — note where they stop, ask for directions, or get confused; record short notes and sketches of common "stuck points" |

---

## 15. Supporting Techniques

### Dogfooding

> **Dogfooding** is the practice of using the product or service that your team is developing.

| Aspect | Details |
|---|---|
| **Type** | An **internal evaluative** technique — **NOT** user evidence |
| **Purpose** | Helps identify issues and improvements firsthand |
| **How** | The user research team takes on the role of active users |
| **Limitation** | Team members are not representative of real users — cannot replace actual user research |

**History:** The term originates from the 1970s-80s (Alpo dog food ads). Microsoft's Paul Maritz popularized it in 1991 with an email titled "Eating our own Dogfood." Today, most major tech companies practice dogfooding (Google, HP, Mozilla, etc.).

---

## 16. AI in User Research

### Core Principle: AI = Accelerator, Not Evidence

> **Class rule for User Research:** AI outputs are hypotheses only; **validate with real users**.

### Good Uses vs. Not Acceptable

| Good Uses | Not Acceptable |
|---|---|
| Draft user research questions | Invented user quotes or statistics |
| Draft interview guides/planning | "Users feel..." without speaking to users |
| Rewrite questions to be neutral | Assumptions presented as evidence |
| Cluster notes into themes (then **verify**) | Sensitive data pasted into tools |
| Summarize transcripts (after **anonymizing**) | |

### Where AI Fits in the 8-Step Flow

| Steps | AI Role |
|---|---|
| **Steps 1-7** (Who are users, motivations, context, segmentation, recruitment, method selection, planning) | **AI helps you go faster** — drafts & structure |
| **Step 8** (Conduct UR and collect data) | **Humans own this step** + validity (real users, ethics, interpretation) |

### AI Prompt Template for User Research

```
ROLE: Act as a senior UX researcher.
CONTEXT: Here is my product/service concept: <...>
GOAL: My research goal is: <...>
CONSTRAINTS: Hypotheses only. Do NOT invent facts or statistics.
             Use neutral wording.
OUTPUT FORMAT: Bullet list with headings; include assumptions explicitly.
```

### AI Prompts for Each Step

| Step | What AI Can Help Draft |
|---|---|
| **Steps 1-4** | User groups (goal, pain points, context), motivations per group ("I want to..., so that..."), key scenarios (where/when/how often), primary vs. secondary users |
| **Step 5** | Recruitment plan: research goals, population & sample, sampling method (with reason), inclusion/exclusion criteria, screener questionnaire, participant invitation email, consent form |
| **Step 6** | Suitable UR method selection with justification, interview questions (10-12 neutral questions + probe examples), survey questions (15-20 max), observation plan (direct/indirect, what to observe) |
| **Step 7** | Practical research plan: what will be done, when, where, who does what |
| **Step 8** | **Humans own this step** — AI cannot replace real user data collection |

---

## 17. Summary and Key Takeaways

### Core Points

1. **User Research** is the Empathize phase of Design Thinking — discovering and understanding users' problems, workflows, tech familiarity, preferences, and new needs.
2. There are **two types**: **Discovery** (new product) and **Evaluative** (redesign).
3. The **8-step process** covers: identifying users, understanding motivations, mapping context, segmenting users, recruitment, method selection, planning, and execution.
4. **Three foundational UR methods**: Interviews (qualitative, deep), Surveys (quantitative, scalable), Observations (behavioral, contextual).
5. **Interviews** come in three flavors: Structured, Semi-structured (most common and insightful), and Unstructured.
6. **Question design** matters: use open-ended "what/how/why" questions, avoid leading questions, avoid compound questions.
7. **Sampling methods** include Random, Convenience, Purposive (recommended for class), and Saturated.
8. **Inclusion/exclusion criteria** must be tied to the research goal.
9. **AI is an accelerator, not evidence** — use it for drafting and planning (Steps 1-7), but humans must own data collection and validation (Step 8).

### Concept Map

```
User Research
    |
    |-- Definition: Discover & understand users' problems, workflows,
    |               tech familiarity, preferences, new needs
    |
    |-- Types:
    |   |-- Discovery Research (new product)
    |   |-- Evaluative Research (redesign)
    |
    |-- 8-Step Process:
    |   |-- Phase A: Identify & Recruit
    |   |   |-- 1. Who are the users?
    |   |   |-- 2. Why do they need it? (motivations)
    |   |   |-- 3. Where/when/how often? (context)
    |   |   |-- 4. User segmentation (primary vs. secondary)
    |   |   |-- 5. Recruitment (population, sample, sampling, criteria)
    |   |-- Phase B: Select Method
    |   |   |-- 6. Choose UR method(s)
    |   |-- Phase C: Plan & Execute
    |       |-- 7. Make a plan (what/when/where/who/data)
    |       |-- 8. Execute & collect data (quant + qual)
    |
    |-- Foundational Methods:
    |   |-- Interviews (mostly qualitative, few participants)
    |   |   |-- Structured / Semi-structured / Unstructured
    |   |   |-- Flow: Introduction -> Warm up -> Main -> Wrap up
    |   |-- Surveys (mostly quantitative, many participants)
    |   |   |-- Scalable, cost-effective, statistical analysis
    |   |-- Observations (qualitative, real-time)
    |       |-- Direct (field / controlled) vs. Indirect (diaries, analytics)
    |       |-- Observe: context, tasks, actions, support options
    |
    |-- Supporting Techniques:
    |   |-- Dogfooding (internal evaluative, NOT user evidence)
    |
    |-- AI in User Research:
        |-- Accelerator for Steps 1-7 (drafts & structure)
        |-- Humans own Step 8 + validity
        |-- AI outputs = hypotheses only; validate with real users
```

---

## 18. Quick-Fire Exam Review (Test Yourself)

1. What is **User Research**? What is it trying to discover and understand?
2. What is the difference between **Discovery Research** and **Evaluative Research**? Which one applies to designing a new product?
3. Name the **8 steps** of the user research process and group them into their three phases.
4. What is the difference between **population** and **sample**?
5. Name and describe the **four sampling methods**. Which one is recommended for CS3240 class projects?
6. What are **inclusion criteria** and **exclusion criteria**? Give an example of each for a food delivery app.
7. How do inclusion/exclusion criteria change based on the **research goal**? Explain with one example.
8. What are the **three foundational UR methods**? Briefly describe each.
9. Compare **structured**, **semi-structured**, and **unstructured** interviews. Which is most common in user research and why?
10. What are the **four phases** of a typical user interview flow?
11. What is the difference between **open-ended** and **closed** questions? Give an example of each.
12. What are **"What," "How," and "Why"** questions? Why are they preferred over hypothetical questions?
13. Why should you **avoid leading questions**? Give an example of a leading question and its neutral alternative.
14. Why should you **avoid multiple questions in a single sentence**?
15. What are the **key advantages of surveys** over interviews?
16. What should you observe during an **observation** study? Name the four categories.
17. What is the difference between **direct** and **indirect** observation? Give two examples of each.
18. What are the **four types of context** to consider during observations?
19. What is **dogfooding**? Is it user evidence? Why or why not?
20. How can **AI be used** in user research? What are acceptable uses vs. not acceptable uses?
21. Which steps of the 8-step process can AI help with? Which step must be owned by humans?
22. What is the **class rule** for AI in user research?
23. What are **primary** and **secondary** users? Give an example using Spotify.
24. In the context checklist for Step 3, what are the **five factors** you should capture?
25. Why is a **mixed-method approach** often recommended in user research?

---

*Notes based on CS3240 L3 lecture slides by Assoc Prof Bimlesh Wadhwa, NUS. For personal study use only.*
