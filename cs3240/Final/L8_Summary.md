# Lecture 08: Ideation

> **What this covers:** This lecture focuses on the **Ideation** stage of the design thinking process — the creative leap from understanding user problems to generating possible solutions. You will learn the full pipeline from the DEFINE stage (problem statements, key user insights) through IDEATE (brainstorming without judgment), including **How Might We (HMW) questions** (the bridge between Define and Ideate), **Crazy 8s** (sketching 8 ideas in 8 minutes), the **2-layer lens for ideation** (Layer 1: Screens, Layer 2: Interactions), **task flows**, and how **AI can assist** in generating HMW questions, solution concepts, wireframes, task flows, and interactions. Understanding the full pipeline — Task → Task Flow → Screens → Interactions → Prototype — and the role of each technique is essential for the exam and CS3240 projects.

---

## 1. Where We Are: The Design Thinking Process

The design thinking process has five stages:

**Empathize → Define → Ideate → Prototype → Test**

Previous lectures covered the Empathize stage (user research, personas, journey maps, task analysis, thematic analysis). This lecture spans the **Define** and **Ideate** stages.

### 1.1 The DEFINE Stage

The Define stage synthesizes everything you learned in Empathize into a clear, actionable problem framing. It produces three key outputs:

**Output 1 — Key User Insights:** These come from your persona analysis, journey map analysis, and thematic analysis. They capture what you now understand about the user that you did not know before.

**Output 2 — Problem Statement:** A concise statement in the format:

> **"[User] needs a way to [Goal] because [Insight]"**

This format forces you to connect the user (from personas), the goal (from task analysis), and the underlying reason (from thematic or journey analysis). A well-written problem statement scopes the design effort — it tells you what your solution will target.

**Output 3 — Scope:** Defining what the solution will and will not address, based on the insights and problem statement.

### 1.2 The IDEATE Stage

The Ideate stage is about generating a **broad set of ideas** — as many as possible — with **no attempt to judge or evaluate** them. The goal is divergent thinking: quantity over quality, wild ideas welcome, build on others' ideas.

Key principle: ideas should be documented in some **tangible form** (sketches, notes, wireframes) during the ideation session. If an idea stays only in your head, it cannot be shared, discussed, or iterated on.

> **Key exam insight:** Ideation is explicitly a judgment-free zone. Evaluation comes later (in prototyping and testing). The purpose is to explore the solution space as widely as possible before narrowing down.

---

## 2. How Might We (HMW) Questions

### 2.1 What Is an HMW Question?

HMW questions are the **bridge between the Define and Ideate stages**. They take a problem statement or user insight and reframe it as an open-ended question that invites creative solutions.

### 2.2 The HMW Formula

> **"How Might We"** + **Intended Action** + **For** + **Potential User** + **So That** + **Desired Outcome**

Each word in "How Might We" matters:

| Word | Meaning |
|---|---|
| **How** | The problem is not yet solved — we are looking for ways |
| **Might** | There are many possible paths — we are not committing to one |
| **We** | This is a collaborative, team effort |

### 2.3 Examples

**Example 1 — Student deadlines:**
> "How might we **simplify deadline tracking** for **overwhelmed university students** so that **they never miss an assignment submission**?"

**Example 2 — Children's books:**
> "How might we **make finding the right children's book** for **busy parents** so that **choosing a bedtime story takes under a minute**?"

**Example 3 — Campus events:**
> "How might we **make event discovery easy, personalized, and quick** for **university students** so that **they never miss relevant campus events**?"

### 2.4 Why HMW Questions Work

HMW questions are powerful because they are scoped enough to give direction (they reference a specific user and outcome) but open enough to allow many different solutions. They prevent the team from jumping straight to a single solution too early.

> **Key exam insight:** HMW is a good starting point for ideation. It bridges Define and Ideate by turning user problems into solution-inviting questions.

---

## 3. Drawing Inspiration from Similar Products

Before sketching solutions, it helps to look at how existing products solve similar problems. This is not about copying — it is about recognizing **patterns** and **interaction paradigms** that already work.

**Example — for a children's book reading app (Tiny Tales):**

| Category | Example Products |
|---|---|
| Personalized content discovery platforms | Netflix, Kindle |
| Smart aggregators | Amazon Prime |
| AI content companions | Various recommendation engines |

The idea is to identify what these products do well (e.g., Netflix's personalized recommendations, Kindle's reading-level filtering) and consider whether similar patterns could apply to your design problem.

---

## 4. Crazy 8s (Idea-8s)

### 4.1 What Is Crazy 8s?

Crazy 8s is a rapid sketching technique where you **sketch 8 different ideas in 8 minutes** (roughly 1 minute per idea). The sketches are rough — just enough to communicate the core concept. Google uses this technique in their design sprint process.

### 4.2 Key Principles

The sketches do not need to be beautiful or detailed. They need to communicate the idea. The time pressure forces you to think quickly and avoid over-refining any single idea.

**Process:** Doodle for 8 minutes → Sketch 8 ideas in 8 minutes → Each sketch should be just enough to communicate the idea to someone else.

### 4.3 Why Crazy 8s Work

The technique works because it forces **divergent thinking** under time pressure. You cannot spend all 8 minutes polishing one idea — you must generate variety. Ideas are **not evaluated** during the exercise. Collaboration spurs diverse ideas and promotes **parallel design** (multiple team members sketching simultaneously).

> **Key exam insight:** Crazy 8s = a method to generate a wide variety of solution ideas in the form of sketches for the HMW statement at hand. 8 ideas, 8 minutes, no judgment.

---

## 5. Task Flows

### 5.1 What Is a Task Flow?

A **task flow** is the sequence of steps a user takes to complete a specific task. It maps out the logical order of actions from the user's initial action to the final step.

**Example — Tiny Tales (children's book reading app):**

1. Login
2. Age selection
3. Interests selection
4. Browse book collection
5. Check book details
6. Read the book

**Example — Campus events discovery app:**

1. Student opens campus app
2. System shows personalized events
3. Student browses events
4. Student clicks an interesting event
5. Student reads details
6. Student saves or RSVPs

### 5.2 The Ideation Pipeline

Task flows are part of a larger pipeline that structures ideation:

> **Task → Task Flow → Screens → Interactions → Prototype**

This pipeline progressively adds detail. You start with a high-level task, break it into a flow of steps, then figure out what screens are needed, then specify the interactions on each screen, and finally build a prototype.

---

## 6. The 2-Layer Lens for Ideation

This is a structured way to move from task flows to concrete design ideas. It has two layers:

### 6.1 Layer 1: From Task Flow to Screens

For each step in the task flow, ask: **what screen does the user see?** This maps abstract steps to concrete UI elements.

**Example — Campus events app:**

| Task Flow Step | Screen |
|---|---|
| 1. Student opens campus app | — |
| 2. System shows personalized events | **Home Feed** |
| 3. Student browses events | **Explore Screen** |
| 4. Student clicks an interesting event | — |
| 5. Student reads details | **Event Details Screen** |
| 6. Student saves or RSVP | **Confirmation Screen** |

### 6.2 Layer 2: From Screens to Interactions

For each screen, specify **how the user interacts** with it. There are two techniques for identifying interactions:

**Technique 1 — "Action Checklist":** For each screen, ask four questions:

| Question | What It Covers |
|---|---|
| **Input** | What does the user need to enter/do here? |
| **Choice** | What options does the user have to select? |
| **Feedback** | What system response should they see? |
| **Error** | What if something goes wrong? |

**Technique 2 — "Storyboard-Micro Moments":** Visualize a user using each screen step by step. Ask: What is the **first tap or action**? What **response appears**? What is the **next action**? How does the user **exit the screen**?

**Example — Campus events app interactions:**

| Screen | Interaction |
|---|---|
| Home Feed | **Scroll** personalized events |
| Explore Screen | **Filter** by interests |
| Event Card | **Tap** to open details |
| Event Details | **RSVP button** |
| Save Event | **Add** to calendar |

### 6.3 Another Example: Login Task

**Task:** Login

**Screens** (from Task Flow): Login Screen → Landing page/Dashboard

**Interactions** on the Login Screen:

- Typing into fields on login screen
- Tapping login
- Error message feedback
- Password toggle (show/hide)
- Auto-focus (when the login screen loads, the cursor automatically blinks inside the username/email field, or the keyboard is already up)
- Transition to Dashboard/Landing page (fade-in or slide-in or a loading spinner)

> **Key exam insight:** The 2-layer lens structures ideation by forcing you to think about both **what the user sees** (screens) and **how they get things done** (interactions). Task flows help in ideating for screens and interactions to be designed.

---

## 7. Using AI in Ideation

AI can assist at multiple points in the ideation process. The lecture demonstrates AI usage through two running examples: (1) a children's book reading app for parents ("Tiny Tales"), and (2) a Spotify "AI DJ for Study" mode for university students.

### 7.1 AI for Generating HMW Questions

**How it works:** Give AI the problem context and ask it to generate HMW questions.

**Example 1 — Children's book app:**

*Prompt:* Provide the problem statement (finding the right book for children is frustrating for parents — they must consider time, interests, and comprehension level) and ask: "Please generate a series of 'How Might We' questions to inspire design solutions for helping parents select books for their children."

*What AI produces:* HMW questions organized into categories, such as:
- Streamlining the Search (Efficiency): "HMW reduce the book selection process to under 60 seconds?"
- Perfecting the Fit (Comprehension & Interest): "HMW instantly verify if a book matches a child's specific reading level?"
- Enhancing the Parent Experience (Reducing Friction): "HMW turn choosing a book into a relaxing wind-down ritual?"

*AI also suggests next steps:* "Would you like me to take one of these HMW questions and brainstorm some specific features or app concepts for it?" — you can continue the conversation iteratively.

**Example 2 — Spotify "AI DJ for Study":**

*Approach:* In the previous design idea generation process (from thematic analysis), a problem was proposed for each theme. You can use these problems as the basis for HMW questions. Provide the concept description, the identified problem (e.g., "Students lose focus the moment they have to interact with their phone or the app to fix the music"), and ask for HMW questions.

*What AI produces:* HMW questions organized by category:
- Anticipation & Context Awareness: "HMW detect when a student is losing focus and dynamically adjust the music?"
- Frictionless & Hands-Free Interaction: "HMW allow users to reject a song using zero-UI methods?"
- Audio & Transition Design: "HMW engineer transitions between tracks so smoothly the student never registers a gap?"
- Motivation & Trust: "HMW build absolute trust within the first 5 minutes of a session?"

### 7.2 AI for Crazy 8s (IDEA-8s)

**How it works:** Give AI the problem context and ask it to generate 8 different solution concepts. For each concept, ask for the UI layout, key features, and how it improves the solution.

**Example 1 — Children's book app:**

*Prompt:* "Create 8 different solution concepts. Focus on features like categorization, filters, and recommendations based on age, interests, or reading level. For each concept, describe the UI layout, key features, and how it improves the efficiency of the book selection process."

*What AI produces:* 8 distinct concepts such as:
1. The "Mad Libs" Prompt Builder — fill-in-the-blank sentence: "I have [Time] to read a [Genre] book for a [Age] child"
2. The Time-Dial Selector — circular dial for time, grid of book covers below
3. The "Mood & Energy" Matrix Grid — 2×2 grid (Educational↔Fun × High Energy↔Calm/Sleepy)
4. The Dual-Profile Swipe Interface — kid mode (swipe on book covers) + parent mode (filtered dashboard)
5. The Dynamic Visual Bookshelf — reading-level slider + color-coded spines by theme
6. The "If You Liked That..." Netflix-Style Carousel — behavioral recommendations
7. The Daily "Bento Box" Dashboard — exactly 3 curated cards per day, no infinite scroll
8. The Interest "Tag Cloud" Explorer — floating bubble-like tags for intersection searching

**Key observations from the lecture:**
- AI does not require the usual eight minutes — it generates multiple solutions within seconds.
- The real challenge lies in **evaluating the practicality and suitability** of these solutions for real-world implementation.
- The prompt mentions child interests, reading level, comprehension, but the AI ideas may focus heavily on some aspects (e.g., time filtering) and miss others (e.g., child engagement, bedtime mood, parent–child interaction). The solution ideas may be parent-centric rather than parent–child centric.
- Different people may have different perspectives on these solutions — it is important for team members to **discuss together and brainstorm** to arrive at the most suitable solution.

**Example 2 — Spotify "AI DJ for Study":**

*Prompt:* "Based on the identified problem and the HMW questions, generate 8 different solution concepts. For each concept, describe the UI layout, key features, and explain how it solves the problem. Avoid abstract or academic language."

*AI produces 8 concepts:*
1. The "Focus Flip" Mode — phone face-down activates study mode; proximity sensor hides notifications
2. The "Air-Gesture" Skip — front camera detects hand gestures above phone
3. Biometric "Auto-Vibe" (Smartwatch Sync) — monitors heart rate/movement, auto-adjusts BPM
4. The "Focus Pomodoro" Roadmap — vertical timeline with study phases and automated transitions
5. Hardware Button Remapping — double-click volume buttons to signal "more like this" or "change genre"
6. The "Ambient Whisper" Transitions — AI-generated soundscapes bridge tracks seamlessly
7. Interactive Lock-Screen Widget — "Focus Slider" from Ambient to Energizing on lock screen
8. Voice-Activated "Study Buddy" — low-friction voice commands like "Spotify, less distracting"

**Lecture annotations on these ideas:**
- Ideas like using hardware buttons or earbud controls are clever and practical — they make the experience feel intuitive and seamless.
- The "Desk Tap" feature (phone listens for vibrations) raises **privacy concerns** — people may not be comfortable with their phone always listening.
- The biometric approach relies on a **smartwatch**, which limits the audience.
- The lock-screen slider provides easy access without unlocking the phone, but **might not be precise enough** for users who want more control.
- Voice commands are great but **may not be convenient in quiet environments** like a library.

### 7.3 AI for Generating Wireframes

**How it works:** After generating solution concepts with AI (which include UI layout descriptions and key features), you can ask AI to create wireframes based on those descriptions.

**Two approaches:**

**Approach 1 — Generate all 8 wireframes together:**

*Prompt:* "Based on the UI layout descriptions provided above, create 8 wireframes that outline the basic structure and layout of the user interface for each concept. Focus on displaying the essential UI elements and their placement, without detailed design elements like colors, images, or logos. Each wireframe should highlight the layout of buttons, sliders, text, and key interactive components."

**Approach 2 — Generate one wireframe at a time:** Adjust the description for each concept individually, with more detailed instructions. The requirements remain the same — no colors, images, or logos; focus on layout and structure.

**Key observations from the lecture:**
- AI-generated wireframes highlight essential features of each solution concept — they have strong structural foundations with clear interactions and intuitive layouts.
- However, they are often **too detailed to be considered true wireframes** (they include visual polish that real wireframes should not have). To make them true wireframes, the visual details should be minimized.
- Generating one wireframe at a time with more specific descriptions produces **better, more simplified results** with more detailed interaction flows.
- These images serve as a **good starting point** for designing the final interface and interaction methods. The AI has essentially helped move us a step forward.

### 7.4 AI for Identifying Interactions (Layer 2)

**Step 1 — Identify tasks and generate task flow:**

*Prompt:* Provide the concept, key user tasks (with sub-tasks and assumptions), and ask: "Generate a detailed task flow based on the following user behaviors and assumptions. Break down each task into clear steps that the user will take to achieve their goal. The task flow should focus on the logical sequence of actions from the user's perspective."

*What AI produces:* A phased task flow (e.g., for Spotify "AI DJ for Study"):
- Phase 1: Setting the Stage (open app, select study task, select mood, start session, put phone away)
- Phase 2: Deep Work & Flow (begin studying, experience background flow, gentle transitions, maintain momentum)
- Phase 3: Handling Friction (notice distraction, check app, review AI's reasoning, take corrective action, return to work)
- Phase 4: Session Wrap-up & Evaluation (end session, assess "click count," assess overall flow, final adoption decision)

**Step 2 — Generate interactions from the task flow:**

*Prompt:* "Generate the interactions based on the task flow. For each step in the task flow, describe the user's actions and interactions with the interface. This should include how the user moves from one screen to another, what inputs are required, and how the system responds. Include any feedback mechanisms, transitions, and error handling when necessary. Avoid abstract or academic language."

*What AI produces:* Detailed interactions organized by phase, including:
- **User Actions** (e.g., "Taps the 'AI Study DJ' card on the Spotify home screen")
- **System Responses** (e.g., "The screen dims into a deep purple Focus Mode")
- **User Inputs** (e.g., "Taps a 'Task Chip' — Reading, Coding, Design")
- **Transitions** (e.g., "The music fades in over 2 seconds")
- **Error Handling** (e.g., "If the user's internet dips, the AI DJ switches to a 'Smart Offline Buffer' — pre-downloaded emergency study tracks")
- **Feedback Mechanisms** (e.g., "A tiny 'Reasoning Bubble' appears: 'Sorry! Switching to something more ambient.'")

> **Key exam insight:** AI assists in every stage of ideation — from HMW questions to solution concepts to wireframes to task flows to interactions. However, AI outputs are **starting points, not final designs**. The critical human tasks are evaluating practicality, identifying missing considerations, and discussing as a team to arrive at the best solution.

---

## 8. Summary

| Concept | Key Point |
|---|---|
| Design Thinking stages | Empathize → Define → Ideate → Prototype → Test |
| DEFINE stage | Produces key user insights, problem statement ("[User] needs a way to [Goal] because [Insight]"), and scope |
| IDEATE stage | Generate broad ideas without judgment — quantity over quality |
| HMW questions | Bridge between Define and Ideate; formula: "How Might We" + Action + For + User + So That + Outcome |
| Crazy 8s | Sketch 8 ideas in 8 minutes — rapid divergent thinking, no evaluation |
| Task Flow | Sequence of user steps: Task → Task Flow → Screens → Interactions → Prototype |
| 2-Layer Lens | Layer 1: Task Flow → Screens (what user sees); Layer 2: Screens → Interactions (how user acts) |
| Interaction techniques | "Action Checklist" (Input/Choice/Feedback/Error) and "Storyboard-Micro Moments" |
| AI in Ideation | Can generate HMW questions, solution concepts (Crazy 8s), wireframes, task flows, and interactions |
| AI limitations | May miss considerations, outputs are too polished for wireframes, team discussion still essential |

**Key takeaways:**
1. **HMW is the bridge** between the Define and Ideate stages of the design thinking process. It is a good starting point for ideation.
2. **Crazy 8s** is a method to generate a wide variety of solution ideas in the form of sketches. 8 ideas, 8 minutes, ideas are not evaluated.
3. **Collaboration** spurs diverse ideas and promotes parallel design.
4. **Task Flows** help in ideating for **screens** and **interactions** to be designed. The pipeline is: Task → Task Flow → Screens → Interactions → Prototype.
5. **AI** can accelerate every stage of ideation but requires human judgment to evaluate practicality, completeness, and suitability for real-world implementation.
