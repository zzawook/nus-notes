# CS3240 L13 — Usability Testing

## 1. What is Usability Testing?

**Usability testing** is a method of evaluating a product by observing **representative users** attempting to complete **representative tasks** with it. The product can be a prototype, a beta, or a shipped system. The output is evidence: where users struggle, what they misunderstand, how long things take, and how they feel.

Usability testing differs from **heuristic evaluation** (which we covered earlier) in a fundamental way:

| | Heuristic Evaluation | Usability Testing |
| --- | --- | --- |
| Who | UX experts apply heuristics | Real (representative) users perform tasks |
| What it finds | Violations of known principles | Actual breakdowns in real usage |
| Cost | Cheap, fast | More expensive, slower |
| Bias | Expert bias | Subject to test-design bias |
| Catches | Many small surface issues | Deep conceptual / workflow issues |

The two are **complementary**, not substitutes. Best practice is to do heuristic evaluation early and cheaply to clean up obvious issues, then do usability testing to discover the issues that experts cannot anticipate.

> Exam detail: you should be able to articulate why you cannot replace usability testing with heuristic evaluation: experts predict problems based on principles, but real users have *unpredictable* mental models, and the empirical signal that comes from a user actually struggling cannot be derived from a checklist.

---

## 2. Planning a Usability Test

A test plan must specify, *before* recruiting any users:

1. **Goals & research questions** — what decisions does this test need to inform?
2. **Participants** — who counts as a representative user, and how many?
3. **Tasks** — what will they be asked to do?
4. **Environment** — moderated vs. unmoderated, in-person vs. remote, lab vs. field.
5. **Metrics** — what will be measured, and how?
6. **Roles** — moderator, note-taker, observer, analyst.
7. **Script** — introduction, instructions, debrief, consent.
8. **Pilot** — one practice run before the real test.

### 2.1 How Many Users? — The 5-User Rule

Nielsen's classic finding: **5 users find about 85% of usability problems** in a given test. The relationship is

$$\text{problems found} = N(1 - (1 - L)^n),$$

where $N$ is the total number of problems, $L$ is the probability that a single user encounters any one problem (empirically about $0.31$), and $n$ is the number of users. Plug in $n = 5$, $L = 0.31$ → $\approx 0.85$.

**Caveats** (the lecturer is explicit about these):

- The 5-user rule assumes a homogeneous user group. If you have several distinct user populations, you need 5 *per population*.
- It applies to qualitative formative testing, not summative quantitative testing where you want statistical confidence intervals on metrics.
- It assumes you can iterate: do 5, fix issues, do 5 more, fix again. **Iterate, don't pile up users.**

### 2.2 Designing Tasks

A good usability-test task is:

- **Realistic** — something a real user would actually want to do.
- **Specific enough to perform** but **vague enough to not lead** — don't tell the user where to click.
- **Achievable** in a reasonable time (a few minutes typically).
- **Scenario-framed** — start from a context ("Imagine you're planning a trip to Tokyo and need to ...") so the user enters the right mindset.

**Bad task:** "Click the search bar, type 'flight to Tokyo,' then click the cheapest result."
**Good task:** "You need to fly from Singapore to Tokyo next month for under SGD 800. Find a suitable option."

### 2.3 Moderated vs. Unmoderated; Remote vs. In-Person

| | Moderated | Unmoderated |
| --- | --- | --- |
| Pros | Probe deeply, follow-ups, see body language | Cheap, scalable, no scheduling |
| Cons | Expensive, scheduling, observer effect | No probing, lower-quality data |
| Tools | Zoom, lab equipment, eye trackers | Maze, UserTesting.com, Lookback |

**Maze** (called out in lecture as the user-test tool) is a popular unmoderated usability-testing tool: you upload prototypes or live URLs, define tasks and success paths, and Maze records click paths, time-on-task, mis-clicks, and a "usability score." It is widely used because it scales to hundreds of users at near-zero marginal cost.

---

## 3. Metrics

Two big families: **performance metrics** and **subjective metrics**.

### 3.1 Performance Metrics

- **Task success rate** — fraction of users who completed the task. The most fundamental metric.
- **Time on task** — elapsed time per task. Lower is generally better, but context matters.
- **Error count** — number of mis-steps, mis-clicks, wrong selections.
- **Error severity** — minor (recovered immediately) vs. critical (forced abandonment).
- **Efficiency** — clicks/keystrokes vs. theoretical minimum.

> Detail: be careful not to *assume* that lower time = better. For exploratory tasks (e.g., browsing a museum site), longer engagement may indicate success.

### 3.2 Subjective Metrics — Surveys

There are many. The lecture goes through them deliberately because the exam often asks you to identify which scale fits which situation.

**ASQ — After-Scenario Questionnaire** (Jim Lewis, IBM, 1991). Three Likert items asked **after each task**:

1. Overall, I am satisfied with the **ease of completing** this task.
2. Overall, I am satisfied with the **amount of time** it took to complete this task.
3. Overall, I am satisfied with the **support information** (online help, messages, documentation) when completing the task.

Average the three; lower-numbered = more satisfied (depending on direction). Used for **per-task** satisfaction.

**SUS — System Usability Scale** (John Brooke, 1986). Ten items asked **once after the entire test**. Five-point scale 0–4 (Strongly Disagree → Strongly Agree). Items alternate positive/negative phrasing (odd items positive, even items negative). Scoring:

- Odd items: $(\text{response} - 1)$.
- Even items: $(5 - \text{response})$.
- Sum the contributions and **multiply by 2.5** to scale to 0–100.

A SUS score of 68 is roughly average; > 80 is good; < 50 is poor. SUS is the most-used post-test usability questionnaire in the world.

**SUPR-Q — Standardized Universal Percentile Rank Questionnaire** (Sauro). Measures four dimensions of user experience for *websites*:

1. **Usability**
2. **Credibility / Trust**
3. **Loyalty**
4. **Appearance**

Returns percentile-ranked scores benchmarked against a database of hundreds of websites.

**Other questionnaires the lecture name-drops:**

- **SMEQ** — Subjective Mental Effort Questionnaire (single-item 0–150 scale, mental effort).
- **QUIS** — Questionnaire for User Interface Satisfaction (long, detailed, multi-factor).
- **PSSUQ** — Post-Study System Usability Questionnaire (IBM, 16 items, similar to SUS but longer; provides three sub-scales: System Usefulness, Information Quality, Interface Quality).
- **CUSI** — Computer User Satisfaction Inventory.
- **UEQ** — User Experience Questionnaire (six factors: Attractiveness, Perspicuity, Efficiency, Dependability, Stimulation, Novelty).
- **meCUE** — modular evaluation of key Components of User Experience.
- **AttrakDiff2** — semantic differentials for hedonic vs. pragmatic quality.
- **CUS for chatbots** — Chatbot Usability Scale.

> For the exam: ASQ is **per task**, SUS / PSSUQ / SUPR-Q are **per session**. SUS is general-purpose; SUPR-Q is website-specific; UEQ adds hedonic dimensions; AttrakDiff2 emphasizes the pragmatic / hedonic split.

---

## 4. Think Aloud (Concurrent Think Aloud — CTA)

**Think Aloud** asks users to verbalize their thoughts while performing the task: what they're looking at, what they expect, what they're confused by. It is the single richest qualitative data source in usability testing.

**Concurrent Think Aloud (CTA)** — verbalize *during* the task. Pros: real-time, captures emotion in the moment. Cons: can slow users down, can affect natural behavior.

**Retrospective Think Aloud (RTA)** — verbalize *after* the task, often while watching a screen recording. Pros: doesn't interfere with the task. Cons: relies on recall and rationalization, less raw.

Good moderator probes during CTA:

- "What are you thinking right now?"
- "What did you expect to happen?"
- "What are you looking for?"

**Avoid leading probes**: "Why didn't you click the obvious button?" — this signals the answer.

---

## 5. Eye Tracking

An eye tracker records exactly where the user is looking on the screen, sampled at 60–1000 Hz. Two basic eye movements:

- **Fixations** — moments when the eye is roughly stationary on a region (typically 100–600 ms). This is when *visual processing* happens.
- **Saccades** — rapid jumps between fixations (20–40 ms). Vision is essentially suppressed during a saccade.

Two visualizations dominate:

- **Gaze plots** — overlays each individual fixation (usually a numbered circle whose size encodes duration) and the saccades between them on a screenshot. Good for showing one user's path.
- **Heat maps** — aggregate fixation density across many users, colored from cool (rare) to hot (frequent). Good for showing what regions attracted attention.

**Use cases:** detecting that users miss a critical UI element ("blindness"), confirming that information hierarchy is read in the intended order, comparing layouts.

**Limits.** Looking is not understanding. A heat map showing many fixations on a button does not mean the button is effective — it might mean it's confusing.

---

## 6. Light Mode vs. Dark Mode (Lecture Appendix)

The lecturer goes through this because students always ask. The empirical literature is **not** unanimous in favor of dark mode despite its popularity.

- **Piepenbrock et al. 2013** — light mode (positive polarity, dark text on light background) yields better proofreading performance and better visual acuity for typical users.
- **Dobres et al. 2017** — light mode is better, especially under nighttime ambient conditions and when using small fonts; the advantage *grows* as text shrinks.
- **Alema et al. 2018** — light mode is associated with **reduced risk of myopia** in long-term use, due to the larger pupil size in dark mode allowing more spherical aberration and longer accommodation.
- **Legge et al. 1985** — exception: people with **cloudy ocular media** (cataracts, certain corneal conditions) read **better in dark mode**, because dark mode reduces glare and intra-ocular light scatter.

So: light mode is the default winner for *typical* users, but dark mode is genuinely better for users with specific visual conditions, and is widely preferred subjectively for emotional / aesthetic / battery reasons.

> Detail: on OLED screens dark mode also saves power, because OLED pixels emit no light when displaying black. This is *not* true on LCD screens.

---

## 7. Useful Tools (Lecture Appendix — Google's UX Toolkit)

The lecture lists tools the instructor recommends students try:

- **Google Forms** — simple surveys, free, integrates with Sheets.
- **NotebookLM** — Google's grounded LLM tool: feed it your sources, ask questions, get answers cited back to those sources. Useful for synthesizing usability-test transcripts.
- **Gemini** — general-purpose LLM, helpful for drafting test scripts, generating personas, summarizing sessions.
- **Stitch** — Google's AI UI generator (turns text descriptions or sketches into UI mock-ups).
- **AI Studio** — Google's prototyping environment for Gemini applications.
- **Google Analytics** — quantitative behavioral telemetry on shipped products; usability testing covers the *why*, Analytics covers the *how many*.

---

## 8. Cheat-Sheet

| | |
| --- | --- |
| **What** | Observe representative users doing representative tasks. |
| **vs. Heuristic Eval** | UT finds real breakdowns; HE finds principle violations. Complementary. |
| **5-user rule** | Find ~85% of issues with 5 users; iterate, don't accumulate. |
| **Tasks** | Realistic, scenario-framed, not leading. |
| **Performance metrics** | Success rate, time, errors, error severity, efficiency. |
| **Per-task survey** | ASQ (3 items, Lewis). |
| **Per-test survey** | SUS (10 items, 0–4 scale, ×2.5 → 0–100; avg ≈ 68). |
| **Website UX** | SUPR-Q — Usability, Credibility, Loyalty, Appearance. |
| **Other surveys** | SMEQ, QUIS, PSSUQ, CUSI, UEQ, meCUE, AttrakDiff2, CUS-chatbot. |
| **Think aloud** | CTA (during) richer; RTA (after) less interfering. |
| **Eye tracking** | Fixations (processing), saccades (jumps); gaze plots vs. heat maps. |
| **Maze** | Unmoderated remote testing platform; scales cheaply. |
| **Light vs. dark** | Light wins for typical users (Piepenbrock, Dobres, Alema); dark for cloudy ocular media (Legge). |
