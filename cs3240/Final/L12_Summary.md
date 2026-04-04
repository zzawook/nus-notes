# Lecture 12: Heuristic Evaluation

> **What this covers:** This lecture introduces **Heuristic Evaluation**, a key method for evaluating the usability of user interfaces. You will learn where evaluation fits in the design thinking process, the difference between formative and summative evaluation, and the two core evaluation methods (heuristic evaluation vs usability testing). The bulk of the lecture covers **Nielsen's 10 Heuristics** in detail with examples, the **heuristic evaluation process** (including severity rating), a worked **case study** (oxygen flow control knob), **heuristics for evaluating AI features** (9 principles), and **chatbot-specific heuristics** (12 heuristics for conversational UX). Understanding each heuristic, being able to identify violations in a given interface, and rating severity are essential exam skills.

---

## 1. Where Evaluation Fits in Design Thinking

Evaluation sits at the **Test** stage of the design thinking process (Empathize → Define → Ideate → Prototype → **Test**). This lecture covers two evaluation methods across two lectures:

- **Lecture 12:** Heuristic Evaluation (expert-based)
- **Lecture 13:** Usability Testing (user-based)

---

## 2. Why, When, What, Who, Where of Evaluation

**Why evaluate?** To identify usability problems before users encounter them. Usability is not optional — it directly affects user success and adoption.

**When?** Evaluation can happen at any stage of the design process, but it is most useful during prototyping and before launch. The earlier you catch problems, the cheaper they are to fix.

**What do we evaluate?** The interface, the interaction flow, the information architecture — anything that the user interacts with.

**Who evaluates?** Depends on the method. Heuristic evaluation uses **experts** (UX professionals, domain experts). Usability testing uses **real users**.

**Where?** Can be done in a lab, in the field, or remotely.

---

## 3. Formative vs Summative Evaluation

| Aspect | Formative | Summative |
|---|---|---|
| **When** | During the design process | After the design is complete |
| **Goal** | Identify problems and improve the design | Measure how well the design performs overall |
| **Analogy** | A chef tasting soup while cooking | Guests rating the soup after the meal |

**Formative evaluation** is about finding and fixing issues iteratively. **Summative evaluation** is about assessing the final product's quality. Both are valuable, but heuristic evaluation is primarily a formative method — used to catch problems early.

---

## 4. Two Core Evaluation Methods Compared

| Aspect | Heuristic Evaluation | Usability Testing |
|---|---|---|
| **Who** | Experts (UX, domain, accessibility) | Real users from the target audience |
| **How** | Experts review the interface using a set of heuristics (design principles) | Users attempt real tasks while observers watch and record |
| **Speed** | Fast, can be done quickly | Slower, requires recruitment, scheduling, facilitation |
| **What it finds** | Known, predictable issues (violations of established principles) | Real, sometimes unexpected issues (actual user struggles) |
| **Cost** | Lower (no user recruitment needed) | Higher (user recruitment, lab setup, facilitator time) |
| **Limitation** | May miss problems that only emerge in real use | Small sample sizes; results depend on task design |

**Other UX evaluation methods** (mentioned but not the focus): interviews, surveys/SUS (System Usability Scale), field studies, A/B testing, analytics, diary studies.

**Key insight:** Heuristic evaluation tells you **what might be wrong** (based on principles). Usability testing tells you **what actually goes wrong** (based on real behavior). The two are complementary — use heuristic evaluation early to catch obvious issues, then usability testing later to validate with real users.

---

## 5. What Is Heuristic Evaluation?

**Heuristic evaluation** is a usability inspection method where a small group of **experts** reviews an interface against a set of recognized usability principles (heuristics). Experts independently examine the interface, identify violations of these heuristics, and rate the severity of each issue.

### 5.1 How Many Evaluators?

The recommended number is **3 to 5 experts**. Here is why:

- **Too few (1–2):** Miss too many issues. A single evaluator typically finds only about 35% of usability problems.
- **3–5:** The sweet spot. Each additional evaluator finds new issues, but with diminishing returns. Five evaluators typically catch about 75% of issues.
- **Too many (6+):** Diminishing returns — each new evaluator adds fewer unique findings relative to the cost.

**Diversity matters.** The team should include different types of expertise for best coverage:

- **UX expert:** Knows interaction design principles and common patterns
- **Domain expert:** Understands the specific context (e.g., healthcare, finance, education)
- **Accessibility expert:** Catches issues for users with disabilities
- **AI/system expert:** Relevant when evaluating AI-powered features

---

## 6. Nielsen's 10 Heuristics

These are the 10 usability heuristics originally developed by Jakob Nielsen. They are good practices — based on observation of real users and the problems they face — that can be applied to evaluate any interface. They have been used for many years and continue to be a valid instrument for detecting usability problems today.

### H-1: Visibility of System Status

**Principle:** Show system status and provide immediate feedback.

The system should always keep users informed about what is going on through appropriate feedback within reasonable time. Users should never have to wonder whether their action was registered or what the system is currently doing.

**What this looks like in practice:**
- Progress bars and loading indicators during lengthy operations
- Highlighting the current page or section in navigation (e.g., active tab)
- Real-time feedback on form input (e.g., password strength indicator)
- Confirmation messages after actions (e.g., "Item added to cart")

**Example:** A file upload showing a progress bar and percentage — the user knows the upload is happening, how far along it is, and when it completes.

### H-2: Match Between System and the Real World (Mapping)

**Principle:** Use familiar metaphors and language; match the user's mental model.

The system should speak the user's language, with words, phrases, and concepts familiar to them rather than system-oriented jargon. Follow real-world conventions, making information appear in a natural and logical order.

**What this looks like in practice:**
- Using a "shopping cart" icon for e-commerce (maps to real-world shopping)
- Organizing information the way users expect it (e.g., chronological for a timeline)
- Using language appropriate to the audience (not developer jargon for end users)
- Icons that look like the real-world objects they represent

**Example:** A "trash can" icon for deleting files — it maps to the real-world action of throwing something away.

### H-3: User Control and Freedom

**Principle:** Provide good defaults and undo; let users exit unwanted states.

Users often choose system functions by mistake and need a clearly marked "emergency exit" to leave the unwanted state without going through an extended dialogue. Support undo and redo.

**What this looks like in practice:**
- Undo/redo functionality (e.g., Ctrl+Z)
- "Back" and "Cancel" buttons in multi-step processes
- A clear way to close dialogs, pop-ups, and overlays
- "Are you sure?" confirmations before destructive actions
- Allowing users to navigate freely without being trapped in a flow

**Example:** Gmail's "Undo Send" feature — after sending an email, users have a brief window to undo the action.

### H-4: Consistency and Standards

**Principle:** Use the same interface and language throughout.

Users should not have to wonder whether different words, situations, or actions mean the same thing. Follow platform conventions. The same action should always produce the same result, and the same concept should always use the same label.

**What this looks like in practice:**
- Consistent button styles, colors, and placement throughout the app
- Same terminology everywhere (do not call it "Cart" on one page and "Basket" on another)
- Following platform conventions (e.g., "Settings" gear icon in the expected location)
- Consistent interaction patterns (e.g., swipe-to-delete behaves the same everywhere)

**Example:** If a "Save" button is blue and in the top-right corner on one screen, it should be blue and in the top-right corner on every screen.

### H-5: Error Prevention

**Principle:** Help users avoid making mistakes.

Even better than good error messages is a careful design which prevents a problem from occurring in the first place. Either eliminate error-prone conditions or check for them and present users with a confirmation option before they commit to the action.

**What this looks like in practice:**
- Disabling or graying out invalid actions (e.g., a "Submit" button that is disabled until all required fields are filled)
- Inline validation as the user types (e.g., "This email is already registered")
- Confirmation dialogs before destructive actions (e.g., "Delete this file permanently?")
- Smart defaults that reduce the chance of input errors
- Constraining input formats (e.g., date pickers instead of free-text date entry)

**Example:** A form that shows "bert is already taken. Please choose a different username" as the user types — preventing the error before submission, not after.

### H-6: Recognition Rather Than Recall

**Principle:** Make information easy to discover; minimize memory load.

Minimize the user's memory load by making objects, actions, and options visible. The user should not have to remember information from one part of the dialogue to another. Instructions for use of the system should be visible or easily retrievable whenever appropriate.

**Key concept:** Recognition (seeing something and knowing what it is) is easier than recall (retrieving something from memory without cues). The more cues you provide, the easier retrieval becomes.

**What this looks like in practice:**
- Showing recently used items, recent searches, or recent documents
- Dropdown menus and autocomplete instead of requiring users to type from memory
- Visible labels on icons (not just icons alone)
- Breadcrumbs showing where the user is in the navigation hierarchy
- Previewing items before selection

**Example:** A search bar that shows recent searches and suggestions as the user types — the user recognizes the right option rather than recalling the exact query.

### H-7: Flexibility and Efficiency of Use

**Principle:** Allow for tasks that cater to both new and advanced users.

Accelerators — unseen by the novice user — may often speed up the interaction for the expert user such that the system can cater to both inexperienced and experienced users. Allow users to tailor frequent actions.

**What this looks like in practice:**
- Keyboard shortcuts for power users (while keeping menu options for beginners)
- Customizable interfaces (drag-and-drop dashboards, pinning favorites)
- "Advanced" settings or modes for experienced users
- Quick actions and batch operations
- Personalized defaults based on usage patterns

**Example:** In text editors, beginners use the menu bar (Format → Bold), while experts press Ctrl+B. Both achieve the same result through different paths optimized for different skill levels.

### H-8: Aesthetic and Minimalist Design (Minimalism)

**Principle:** Provide only necessary information in an elegant way.

Dialogues should not contain information which is irrelevant or rarely needed. Every extra unit of information in a dialogue competes with the relevant units of information and diminishes their relative visibility.

**What this looks like in practice:**
- Clean layouts with adequate white space
- Progressive disclosure — showing basic options first, with advanced options available on demand
- Removing or hiding rarely used features
- Using visual hierarchy to emphasize what matters most
- Avoiding decorative elements that do not serve a functional purpose

**Example:** Google's search homepage — a single search bar with minimal distractions. The interface has almost nothing on it, which makes the primary action (searching) immediately obvious.

### H-9: Error Recovery

**Principle:** Help users recognise, diagnose, and recover from errors.

Error messages should be expressed in plain language (no codes), precisely indicate the problem, and constructively suggest a solution.

**What this looks like in practice:**
- Use clear, human-readable messages (not "Error 404" or "Exception at line 231")
- Explain what went wrong in language the user can understand
- Suggest how to fix the problem
- Provide recovery actions (e.g., a link to go back, a button to retry)

**Example (good):** A sign-up form showing "bert is already taken. Please choose a different username" alongside "Passwords must be at least 6 characters and can only contain letters and numbers" — each error is specific, explains the issue, and tells the user exactly what to do.

**Example (good):** A 404 page that says "We looked really hard, but it appears the page you seek doesn't exist anymore" with a link to continue to the homepage or report the error — friendly tone, explains the situation, and provides next steps.

### H-10: Help and Documentation

**Principle:** Use pro-active and in-place hints to guide users.

Even though it is better if the system can be used without documentation, it may be necessary to provide help and documentation. Any such information should be easy to search, focused on the user's task, list concrete steps to be carried out, and not be too large.

**What this looks like in practice:**
- Use inline hints (tooltips, placeholders) directly where users need them
- Provide contextual help (help that appears relevant to what the user is currently doing)
- Enable search within help documentation
- Avoid forcing users to leave their current task flow to find help

**Example:** A form field with a tooltip icon (?) that shows "Names should appear exactly as on passport" — the help is right where it is needed, at the moment it is needed, without requiring the user to leave the page.

**Example:** Slack's help center with "Hi. How can we help?" — a searchable help page with common troubleshooting topics. Etsy's help page with categorized help topics and a search bar.

---

## 7. The Heuristic Evaluation Process

The process follows four sequential steps:

**Step 1: Choose heuristics** — Select the set of heuristics to evaluate against (e.g., Nielsen's 10 Heuristics, or a domain-specific set).

**Step 2: Experts review** — Each evaluator independently inspects the interface, going through all screens and interactions, looking for violations of the chosen heuristics.

**Step 3: Record issues** — Each violation is documented, noting which heuristic is violated and describing the specific problem.

**Step 4: Rate severity** — Each issue is assigned a severity rating to help prioritize fixes.

### 7.1 Severity Rating Scale

Severity is rated on a 0–4 scale:

| Rating | Level | Description |
|---|---|---|
| **0** | Not a problem | I don't agree that this is a usability problem at all |
| **1** | Cosmetic | Cosmetic problem only; need not be fixed unless extra time is available on the project |
| **2** | Minor | Minor usability problem; fixing this should be given **low priority** |
| **3** | Major | Major usability problem; important to fix, so should be given **high priority** |
| **4** | Catastrophe | Usability catastrophe; **imperative to fix this before the product can be released** |

### 7.2 What Determines Severity?

Severity depends on three factors:

1. **Frequency** — How often does the problem occur? Very often or rarely?
2. **Impact** — How severe is the impact on users? Would this be a huge headache, or can they easily overcome it?
3. **Persistence** — Is this something the user can overcome once they know about it, or would it be a recurring concern again and again?

A problem that is frequent, high-impact, and persistent would rate 3 or 4. A problem that is rare, low-impact, and easily overcome might rate 0 or 1.

---

## 8. Case Study: Oxygen Flow Control Knob (Heuristic Spotting)

This is a real-world example of how heuristic violations can have serious consequences.

**Scenario:** A physician treating a patient with oxygen set the flow control knob between 1 and 2 liters per minute, not realizing that the scale numbers represented **discrete** settings, not continuous ones. There was no oxygen flow between the settings, yet the knob rotated smoothly, suggesting that intermediate settings were possible. The patient, an infant, became hypoxic before the error was discovered.

**Heuristic violations identified:**

1. **H-2 Mapping violation:** The knob rotates smoothly (giving the illusion of continuous control), but oxygen only flows at discrete settings. The physician's expectation of how the system worked was different from how it actually worked. The physical behavior of the knob did not match the underlying system behavior.

2. **H-5 Error Prevention violation:** The physician's expectation differed from the system's actual behavior, and the design did nothing to prevent this mismatch. The oxygen flow control should have "snapped" from one setting to the next with no chance of pausing between settings.

3. **H-1 Visibility violation:** No clear indication of the current flow rate or whether oxygen was actually flowing. The equipment should have been designed with a digital panel displaying the flow rate.

**Key lesson:** This case demonstrates that heuristic violations are not just cosmetic concerns — in critical domains like healthcare, they can be life-threatening. Good design in safety-critical systems must be especially rigorous about mapping, error prevention, and visibility.

---

## 9. Heuristics for Evaluating AI Features

When evaluating interfaces that include AI-driven features, the standard Nielsen heuristics still apply but need to be supplemented with additional considerations. The lecture identifies **9 key principles** for evaluating AI in UX:

### 9.1 Visibility of AI Presence

Clearly indicate when AI-generated content or actions occur. Users should know when they are interacting with AI vs human-created content.

**Example:** Gmail clearly labels its suggested Smart Replies. An icon or label that states "Generated by AI" signals AI involvement.

### 9.2 Transparency of AI Operations

Clearly communicate when and how AI is operating. Users should understand what drives the AI's behavior.

**Example:** Shopify's tooltip explaining "Haven't decided on a store name yet? We'll give you a temporary store name that you can change at any time." Netflix showing "Because you added Riverdale to your list" to explain why certain shows are recommended.

### 9.3 Aesthetics of AI

Differentiate AI-driven content visually through color, icon, or styling. AI elements should be visually distinct so users can tell at a glance what is AI-generated.

**Example:** AI suggestions highlighted in a specific color. Many AI tools use a distinctive sparkle/star icon and purple/blue color scheme to indicate AI features (e.g., Grammarly's AI suggestions shown in green underlines with a distinct popup).

### 9.4 Protection from Harmful Content

Safeguard against inappropriate, misleading, or harmful content generated by AI.

**Example:** Content moderation systems that block offensive language or inappropriate images, with "Approved" / "Rejected" labels on AI-filtered content.

### 9.5 Clarity of AI Capabilities and Limitations

Clearly state what AI can and cannot do. Users should understand the boundaries so they do not have unrealistic expectations.

**Example:** Alexa responding "I can't help you with that yet. I can help you plan a trip, though. What city would you like to visit?" — clearly stating its limitation while offering an alternative within its capabilities.

### 9.6 Delegation and User Control

Users should be able to easily delegate tasks to AI or reclaim control. The user should always remain in the driver's seat.

**Example:** Notion AI gives contextual suggestions as you draft, summarize, and edit content while you control the final version (Accept / Discard / Insert below / Try again). Photo editing apps that offer AI-powered adjustments alongside manual controls.

### 9.7 Predictability and Consistency of AI Behavior

AI should behave consistently, allowing users to predict its responses. Inconsistent AI behavior erodes user trust.

**Example:** Spotify's consistent music recommendations based on listening history. Consistent patterns in chatbot replies to similar queries (not giving wildly different answers to the same question).

### 9.8 Context-Sensitive AI Recommendations/Suggestions

AI should provide relevant suggestions based on the current context, not generic or out-of-place recommendations.

**Example:** AI auto-complete providing relevant message responses based on the conversation context (e.g., messaging apps suggesting "Good", "Fine", "Great, thanks!" in response to "Hey, how are you?").

### 9.9 Error Recovery and Fallback Mechanisms

AI interactions should support recovery when things go wrong. Since AI can be unpredictable, robust fallbacks are essential.

**What this looks like in practice:**
- Provide Regenerate / Retry options
- Allow users to edit AI inputs or outputs
- Maintain history/versioning when possible
- Offer fallback to manual control

**Example:** MagicWrite in Canva offers a "Generate" button and "Clear" button, letting users retry or start over. In ChatGPT, users can refine outputs through follow-up prompts, but this is not a true undo mechanism — it provides "undo-like" affordances such as edit prompt / regenerate response / retry / regenerate / conversation history. When you say "Undo the last changes," the model does not remember a structured previous state, does not truly revert deterministically — it reinterprets your request.

---

## 10. Appendix: Chatbot Heuristics (12 Heuristics for Conversational UX)

When evaluating chatbots, the standard Nielsen heuristics can be adapted and extended. The lecture presents **12 Heuristics for Conversational UX Analysis**, based on Nielsen [2005] + Moore & Arar [2019] + Shevat [2017].

### 10.1 Typical Features to Evaluate in a Chatbot

From chatbottest.com, the key features to assess are: **Personality** (clear voice and tone fitting the context), **Onboarding** (users understand what the chatbot does and how to interact from the start), **Understanding** (handles requests, smalltalk, idioms, emojis), **Answering** (relevant, contextual responses), **Navigation** (easy to go through the conversation without feeling lost), **Error Management** (graceful error handling and recovery), and **Intelligence** (remembers context, manages conversation state).

### 10.2 The 12 Heuristics

| # | Heuristic | Key Sub-criteria |
|---|---|---|
| **1** | Visibility of system status | (a) Information about chatbot's state throughout the process (b) Immediate feedback on user actions (c) Compel user action — what does the chatbot think the user will do next? |
| **2** | Match between system and real world | (a) Uses language familiar to target users (b) Visual components (emojis, GIFs, icons) linked to real-world objects (c) Understandable metaphors |
| **3** | User control and freedom | (a) Supports undo/redo (b) Offers permanent menu (c) Provides navigation options (d) Understands repair initiations |
| **4** | Consistency and standards | (a) Uses domain model from user perspective (b) Has personality with consistency in language and style |
| **5** | Error prevention | (a) Prevents slips by meaningful constraints (b) Prevents slips by spelling error detection (c) Requests confirmation before significant actions (d) Explains consequences of user actions |
| **6** | Recognition rather than recall | (a) Clear options through descriptive visual elements and explicit instructions (b) Shows summary of collected information before transactions (c) Offers permanent menu and help option |
| **7** | Flexibility and efficiency of use | (a) Understands synonyms, not just special instructions (b) Deals with different formulations (c) Offers multiple ways to achieve the same goal |
| **8** | Aesthetic and minimalist design | (a) Concise dialogues with only relevant information (b) Visual information in a personality-consistent manner, not random decoration |
| **9** | Help users recognise, diagnose, and recover from errors | (a) Clearly indicates errors (b) Uses plain language to explain (c) Explains recovery actions (d) Offers shortcuts to fix errors quickly |
| **10** | Help and documentation | (a) Clear description of capabilities (b) Keyword search (c) Help focused on user task (d) Concrete steps for tasks |
| **11** | Context understanding | Understands context within one turn or within a small number of turns (usually 2–3 user-bot turn pairs) |
| **12** | Interaction management capabilities | (a) Understands conversation openings (e.g., "hello") (b) Understands sequence closings (e.g., "ok", "thank you") (c) Understands repair initiations and replies with repairs (d) Initiates repair to handle potential user errors |

Note that heuristics 1–10 parallel the standard Nielsen heuristics but are adapted for conversational interfaces. Heuristics 11 and 12 are **new additions** specific to chatbots — they address conversational context and interaction management, which have no direct parallel in traditional GUI evaluation.

---

## 11. Appendix: Using AI for Heuristic Evaluation

AI can be used as a tool to assist in heuristic evaluation itself.

**Goal:** Use AI to quickly identify usability issues in an interface based on heuristic evaluation principles.

**What AI can evaluate:** AI can evaluate the interface based on all 10 of Nielsen's Heuristics (visibility, mapping, control and freedom, consistency, error prevention, recognition, flexibility, minimalism, error recovery, help and documentation).

**Process followed:**
1. Generate a prototype in **Stitch** (an AI prototyping tool)
2. Record the prototype's interaction process using screen-recording software
3. Use **Gemini** (or another AI) to conduct heuristic analysis on the recording
4. Refine the prototype based on the AI's findings

This demonstrates how AI can augment (not replace) the expert evaluation process — useful for rapid iteration, especially in early design stages.

---

## 12. Summary

| Concept | Key Point |
|---|---|
| Where evaluation fits | Test stage of design thinking |
| Formative vs Summative | Formative = finding issues during design; Summative = measuring final quality |
| Heuristic Evaluation | Experts review interface against heuristics; fast, catches known issues |
| Usability Testing | Real users attempt tasks; slower, catches real unexpected issues |
| Number of evaluators | 3–5 recommended (diminishing returns beyond 5) |
| Nielsen's 10 Heuristics | H-1 Visibility, H-2 Mapping, H-3 Control & Freedom, H-4 Consistency, H-5 Error Prevention, H-6 Recognition, H-7 Flexibility, H-8 Minimalism, H-9 Error Recovery, H-10 Help & Documentation |
| Severity scale | 0 (not a problem) → 4 (catastrophe); depends on frequency, impact, persistence |
| Evaluation process | Choose heuristics → Experts review → Record issues → Rate severity |
| AI feature heuristics | 9 principles: Visibility of AI, Transparency, Aesthetics, Harm Protection, Clarity of Capabilities, Delegation & Control, Predictability, Context-Sensitivity, Error Recovery |
| Chatbot heuristics | 12 heuristics adapting Nielsen's 10 + Context Understanding + Interaction Management |
| AI for evaluation | AI tools (Gemini) can assist in conducting heuristic evaluation on prototypes |

**Key takeaways:**
1. **Heuristic evaluation is a fast, expert-driven method** for catching usability problems early — before involving real users. It complements (but does not replace) usability testing.
2. **Nielsen's 10 Heuristics** are the gold standard for evaluating interface usability. Each heuristic addresses a different aspect of the user experience, from visibility and mapping to error handling and help.
3. **Severity rating (0–4)** helps prioritize which issues to fix first, based on frequency, impact, and persistence.
4. **Heuristic violations can have serious real-world consequences** — the oxygen knob case study shows that poor mapping and visibility in safety-critical systems can endanger lives.
5. **AI features and chatbots require additional heuristics** beyond the standard 10 — including transparency, user control over AI, predictability, and context understanding.
6. **Heuristics tell you what might be wrong; usability testing shows you what actually goes wrong.** Use heuristic evaluation early to catch obvious issues, then usability testing later to validate with real users.
