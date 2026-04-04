# Lecture 11: Visual Design and Interaction Design Principles

> **What this covers:** This lecture introduces the **design principles** that sit between the Prototype and Evaluation stages of the design thinking process. You will learn how to evaluate whether a prototype is usable, clear, and consistent — before any user testing — by applying principles from visual perception, design communication, and interaction design. The lecture covers **UI Layers** (Templates, Patterns, Controls), **Visual Design Principles** (7 Gestalt perception principles + 3 design communication principles), **6 Key Concepts of Usable Interactions**, **6 Attention & Cognition Concepts**, **6 Patterns of Human Behaviours**, and **AI tools for visual design**. Being able to identify, name, and apply these principles to real designs is essential for the exam.

---

## 1. Where Design Principles Fit in the Process

**Context:** In the User-Centred Design / Design Thinking process (Empathize → Define → Ideate → Prototype → Test), design principles sit **between Prototype and Evaluation**. Once you have built a prototype, the question becomes: *Is your design usable? Clear? Consistent?* Design principles help you answer this before conducting user tests.

**Why this matters:** Design principles allow you to:
1. **Identify usability issues BEFORE testing** — catching problems early saves time and resources.
2. **Improve clarity, consistency, and interaction** — systematic checks ensure nothing is overlooked.
3. **Justify design decisions** — principles give you a vocabulary and rationale for why a design works or does not work.

---

## 2. Setting Context Through Examples

The lecture begins with examples of good and bad design to build intuition for what visual design principles address. The key takeaway is that design quality is immediately perceptible — users form judgments within seconds — and that both visual appeal and functional clarity matter.

### 2.1 Sutton Maddock Van Hire (Good Example)

A vehicle hire website with a clear layout: prominent company branding, a visible phone number, a "Quick Quote" form immediately accessible on the homepage, and a clear call-to-action. The design communicates its purpose quickly and reduces friction for the user.

### 2.2 Yale School of Art (Bad Example)

A chaotic, visually overwhelming website with a tiled background image, clashing colors, inconsistent typography, and poor information hierarchy. Despite being from a prestigious art school, the design makes it difficult to find information and creates a disorienting user experience. This illustrates that **visual design is not just about aesthetics — it directly affects usability**.

### 2.3 Liberty Games (Good Example)

A well-structured e-commerce site with clear navigation categories, prominent search bar, trust signals (reviews, delivery info, price guarantee), and organized product categories with consistent card layouts. Demonstrates good use of hierarchy and proximity.

### 2.4 Square Swipe (Bad Example — Icons)

A mobile game app where the icons (star, play, chart, speaker) lack clear affordance. Users cannot easily tell what each icon does because the visual design does not communicate function through appearance. This illustrates the importance of **affordance** — visual cues that indicate what actions are possible.

### 2.5 Inconsistent Icons (Bad Example)

Three icons (clock, house, envelope) rendered in different visual styles — different stroke weights, fill styles, and proportions. This inconsistency violates the principle of **similarity** (Gestalt) and makes the interface feel disjointed. Icons should share a consistent visual language.

### 2.6 Tipping UI (Dark Pattern Example)

A tipping interface with a countdown timer ("26 seconds left"), a minimum tip of $50 displayed in red, and preset buttons of $50, $250, and $5000 — with a faded, hard-to-see "Cancel" button. This is an example of a **dark pattern**: a design that deliberately manipulates users into unintended actions through pressure (time limit), anchoring (high preset amounts), and reduced visibility of the escape option.

---

## 3. UI Layers: Templates, Patterns, Controls

UI layers give us the **"what"** — the pieces of the interface. The design principles (covered in later sections) give us the **"how"** — how those pieces are presented and behave. A UI designer looks through multiple layers or lenses when examining any interface.

### 3.1 Templates (Screen Layout)

A **template** defines the **macroscopic, page-level structure** — the arrangement of major content areas or columns. It is the highest-level organizational unit of a screen.

**What a template defines:** The overall spatial arrangement — where the navigation goes, where the main content sits, where secondary information lives. Templates would typically provide pages (screens) laid out with controls for typical solutions.

**Examples:**
- **Product Detail View Template** — a primary navigation area at the top, a large image gallery on the left, and product information (name, price, size selector, "Add to Cart" button, description) on the right. This is the template Nike uses for individual product pages.
- **Product Listing Page Template** — a primary navigation area at the top, a sidebar with category links and filters on the left, and a grid of product cards on the right. This is the template Nike uses for browsing categories.
- **Payment Page Template** — form fields for payment method, personal information, and card details with a prominent action button.
- **Shopping Cart Template** — a cart page with product details, edit options, a summary section with totals, and checkout buttons.

**Key insight:** Different pages on the same site share a **consistent top-level navigation template** (the header/nav bar stays the same) but differ in their **content area template** (product detail vs. product listing vs. cart). This consistency is a deliberate design choice.

### 3.2 Patterns (Common Information/Interaction Models)

A **pattern** is a **reusable grouping of controls** designed **to solve a specific user problem** or **display a structured piece of content**. Patterns sit at a middle level between the page-level template and the individual controls.

**Examples of patterns on a Product Detail page:**
- **Hierarchical Navigation Pattern** — the top nav bar with categories (Men, Women, Boys, Girls, Customize) and search.
- **Product View Pattern** — the image gallery showing multiple angles of the product.
- **Product Color Choice Pattern** — thumbnail images showing available color options.
- **Sizing and Fit Pattern** — the grid of size buttons (XS, S, M, L, XL, 2XL, 3XL).
- **Product Description Pattern** — text block with product details and "Read More" link.

**Examples of patterns on a Product Listing page:**
- **Product Finder Pattern** — the overall layout for browsing products.
- **Filter Pattern** — the sidebar with category, sport, and "best for" filters with checkboxes and dropdowns.
- **Product Card Pattern** — each individual product tile showing image, name, type, and price.
- **Product Attribute Pattern** — the consistent display of product metadata within cards.

**Shopping Cart Patterns (detailed example):**

The shopping cart flow involves three distinct patterns:

1. **Viewing Product and Adding an Item to Cart** — Present a button with the product to add the item to a shopping cart (e.g., Nike's "Add to Bag" button).

2. **Updating and Previewing Cart** — When the user selects the Add to Cart action, provide **feedback** that the item has been added: show the item count increase in a **numeric indicator** next to the cart icon, optionally show a preview of the cart with the item and selected options, and provide **feedback about the next steps** (editing the cart, viewing full cart, or beginning checkout). Nike shows an "Added to Bag" confirmation popup with "View Bag" and "Checkout" buttons.

3. **Displaying the Shopping Cart** — Provide a **separate Shopping Cart View** so the user may **modify or complete** their order. Provide **actions for editing quantities, removing items**. Provide an **action for "checking out"** or completing their purchase. Nike's bag page shows product details, size/quantity selectors, a summary with subtotal, and checkout options (Guest Checkout, Member Checkout, PayPal).

### 3.3 Controls / Widgets (Interactive Elements)

**Controls** (also called **widgets**) are the **building blocks** of any UI. They are the smallest interactive elements.

**Examples:** Button, Text Input, Dropdown, Radio Button, Checkbox, Link, Alert, Validation, Tooltip, Icon, Menu bar, Tab, Vertical Navigation, Breadcrumb, Accordion, and more.

**Controls serve three functions:**
1. **Accept input** from the user (e.g., text fields, checkboxes, dropdowns)
2. **Allow users to move around** in the app or site (e.g., navigation bars, links, breadcrumbs, tabs)
3. **Communicate information** to the user (e.g., alerts, tooltips, headings, body text, images)

**When Choosing a UI Control:**
- Match the control to the **type of user interaction** (e.g., input, choice, entry, command)
- Consider **frequency and context** of use
- Ensure **consistency across screens**
- Keep interactions **simple and discoverable**

> **Appendix note:** The lecture includes an appendix with a reference guide of common UI controls with examples of good use and design tips for clarity and usability. Use this as a design checklist when prototyping in Figma — look up the relevant control to ensure you are using it effectively and consistently.

---

## 4. Visual Design Principles

These principles mirror how our brains organize information — they leverage the strengths of **human perception** and **communication** with design elements. They are divided into two categories:

**Design Perception (Gestalt Principles):** Similarity, Proximity, Figure & Ground, Common Region, Closure, Continuity, Symmetry

**Design Communication:** Contrast, Hierarchy, Alignment

### 4.1 Gestalt Perception Principles

Gestalt psychology is the study of how the human mind perceives and organizes visual stimuli as a whole. The key Gestalt insight is that **the brain actively organizes what it sees** — it groups, separates, completes, and interprets visual information according to predictable rules. Designers can leverage these rules to create interfaces that feel intuitive.

#### 4.1.1 Similarity

**Principle:** The brain looks for differences and similarities in an image, and **links the similar elements**. Elements that share visual characteristics (shape, color, size, texture) are perceived as belonging to the same group.

**Why it matters for UI:** This helps users quickly scan and group related items, improving findability. If every call-to-action button looked different, users would have to relearn each one.

**Example:** Consistency of card layouts across product tiles illustrates similarity — when all product cards on an e-commerce site share the same structure (image, name, price, heart icon), users immediately understand they are all browsable products of the same type.

#### 4.1.2 Proximity

**Principle:** The human brain **groups elements that are close together**, and separates them from those that are farther apart. Physical closeness implies relatedness.

**Why it matters for UI:** This helps users quickly scan and group related items, improving findability. **Labels placed near fields reduce cognitive load.** If spacing is inconsistent, users struggle to know which label belongs to which field.

**Examples:**
- Product cards in a grid: cards that are too close together (no whitespace between them) make it hard to distinguish individual products — proper spacing creates clear groupings.
- Form fields: placing labels very close to their respective text fields (e.g., "Name" directly above the name field, "Email" directly above the email field) makes the association immediately clear.

#### 4.1.3 Figure and Ground

**Principle:** The brain **distinguishes between the objects it considers to be in the foreground** (the "figure") **and the background** (the "ground"). We naturally focus on the figure and push the ground behind it.

**Examples in logos:**
- **Finder (macOS) icon** — can be perceived as either a happy face from the front or a happy face in profile looking at a computer screen, depending on which part you treat as figure vs. ground.
- **FedEx logo** — an arrow hidden in the negative space between the 'E' and 'x'. The letters are the figure; the arrow emerges from what most people initially process as ground.

**In UI, this principle is behind the idea of focus** — modals, overlays, and pop-ups "bring something forward." The modal is the figure; the dimmed page behind it is the ground. This is how news sites show subscription prompts (e.g., Boston Globe's overlay, The New Yorker's newsletter popup) — the content behind dims and blurs, pushing the prompt into the foreground.

#### 4.1.4 Common Region

**Principle:** Elements placed **within the same region** (bounded area) are perceived as grouped, even if they differ in other properties like color or shape.

**Why it matters for UI:** Designers use this to **separate content zones and reduce visual clutter**. A shared background color, a card boundary, or a bordered section signals "these things belong together."

**Examples:**
- Wireframes that use alternating background colors (light blue, dark blue, white) to separate hero sections, feature cards, pricing tables, and testimonial areas — each distinct region is perceived as a coherent group.
- A testing tool website (e.g., with Tree Testing, Preference Test, Five Second Test, Survey, Session Recording, First Click Test) where each service is placed in its own card with a shared background — common region groups the icon, title, and link within each card.

#### 4.1.5 Closure

**Principle:** Humans can **perceive a complete shape by filling in missing visual information**. The brain "closes" gaps to see whole forms even when parts are missing.

**Why it matters for UI:** Users complete incomplete shapes in their minds — this is useful for **simplifying icons or progress indicators**.

**Examples:**
- Abstract geometric shapes: two curved lines that suggest a circle; four arrows around empty space that suggest a diamond; scattered shapes that suggest a star — the brain completes each form.
- **Icons** that use open/simplified outlines: a magnifying glass, a house, an envelope, a shopping cart, a car — each uses minimal lines, relying on the brain to perceive the complete object.
- **WWF logo** — the panda is depicted with just a few black shapes, yet the brain fills in the white areas to perceive a complete panda.
- **Progress indicators** — a circular progress bar at 78% shows an incomplete ring, yet users perceive it as a circle that is "78% full" rather than as a random arc.

#### 4.1.6 Continuity

**Principle:** The human mind perceives and organizes visual stimuli to create a sense of **continuity and flow**. Our eyes naturally follow lines, curves, or patterns, **even when they are interrupted**, leading us to perceive a continuous flow of elements.

**Relationship to Closure:** This principle is closely related to closure. In closure, the mind fills in missing information to make a complete and unified perception out of a figure. In continuity, the mind follows established visual paths.

**Why it matters for UI:** Continuity **guides the eye** — visual flow helps users scan naturally from one section to another.

**Examples:**
- A restaurant booking app (e.g., OpenTable) where sections are stacked vertically (party size → timeslots → find availability → favorite/share → menu → ratings/reviews) — the vertical flow guides the eye downward through a natural sequence.
- A film streaming app where horizontal scrolling content rows guide the eye left-to-right within each row, while vertical stacking of rows guides the eye top-to-bottom across categories.

#### 4.1.7 Symmetry

**Principle:** The human brain naturally inclines towards **balance and order**. Symmetry is a visual **balance** achieved by arranging elements to mirror each other or follow a pattern, producing a sense of **harmony** and **order**.

**Examples:**
- Foursquare's action menu with three icons (Check In, Review, Photo or Video) arranged symmetrically around a center point — the balanced arrangement feels stable and orderly.
- A navigation bar with a centered logo and menu items evenly distributed on either side — the centered logo version feels more balanced than the off-center version.
- **Starbucks logo** — a nearly perfectly symmetrical circular design that conveys stability and brand authority.
- **Intel and Google logos** — note that while symmetry creates harmony, **asymmetry can add energy or emphasis**. Both use slight asymmetries to create visual interest.

### 4.2 Design Communication Principles

#### 4.2.1 Contrast

**Principle:** Contrast refers to **differences between things — in size, color, or weight**. It is how we draw attention to what is important.

**Critical rule: "If everything is bold or bright, nothing stands out."** Contrast only works when it is selective — the high-contrast element must be the exception, not the norm.

**Examples:**
- A login form where the LOGIN button is bright purple against grey input fields — the button immediately draws the eye because it contrasts with everything else on the screen.
- McDonald's app: the McCafé promotional card uses warm brown tones and a large "FREE" in white to contrast against the surrounding content, drawing the eye to the offer.
- An account deletion dialog: a red background with white text for the warning, and a white "Go Back" button that contrasts with the red "Delete My Account" text — the safe action (Go Back) stands out more, guiding users toward the non-destructive choice.

#### 4.2.2 Hierarchy

**Principle:** **Ranking items by importance** or displaying the **relationship** of different information pieces. Visual hierarchy shows what to read first, second, and last.

**How it is achieved:** Typography, color, and spacing all work together to signal importance. Larger, bolder, or more prominent elements are perceived as more important.

**Example:** An interior design studio website where "WE LISTEN, DESIGN & DELIVER" is in massive bold text (primary hierarchy), "We make experiencing your ideas possible" is in smaller text (secondary), and "OUR SERVICES →" is a subtle link (tertiary). The hierarchy guides the eye through the content in the intended order.

#### 4.2.3 Alignment

**Principle:** Alignment makes it easier for users to process information by **guiding their eyes through aligned objects**. Elements arranged along a common axis (left, center, or right) create visual order.

**What alignment achieves:**
- Alignment gives **order**
- It makes interfaces **readable** and **trustworthy**
- **Misalignment subtly signals inconsistency**

**Examples:**
- A Balsamiq wireframe showing red alignment guides — elements are snapped to a grid, creating clean columns and rows. The result is a page that feels organized and professional.
- A mobile article app where the home screen and article detail screen share consistent left-alignment for headings, body text, and metadata — the aligned elements create a sense of coherence across screens.

### 4.3 Visual Design Quick Checklist for Designers

- Are **related items visually grouped**? (Similarity, Proximity, Common Region)
- Is there **clear visual hierarchy**? (Hierarchy, Contrast)
- Is **alignment consistent**? (Alignment)
- Do **color and contrast guide attention**? (Contrast, Figure & Ground)

**Key formula:** Good UI Design = Visual Clarity + Organized Information → **Reduces mental load** → Supports **clear thinking** and **better decisions**.

---

## 5. Key Concepts of Usable Interactions

This section addresses **how people interact** with interfaces and **what they expect**, based on guidance from Human Cognition and Behaviour Principles. There are **6 key concepts** for a usable interactive UI.

### 5.1 Affordance

**Definition:** Affordance refers to **visual cues that indicate what actions are possible**. Without these cues, users hesitate or miss the main action. Affordance helps them know what they can do.

**Principle:** Design communicates function through appearance.

**Examples:**
- A door handle's shape tells you whether to push or pull — flat plates afford pushing, protruding handles afford pulling.
- In UI: a raised, colored button affords clicking; a text field with a border and cursor affords typing; a slider with a draggable thumb affords sliding.

### 5.2 Feedback

**Definition:** The system **reacts to user actions** to confirm what happened. Feedback reduces uncertainty and builds trust.

**Why it matters:** Without feedback, users wonder: "Did my action work? Is the system processing? Did something go wrong?" Good feedback answers these questions immediately.

**Examples:** Loading spinners, success/error messages after form submission, button color changes on hover/click, the "Added to Bag" popup in Nike's shopping flow, progress bars during file uploads.

### 5.3 Clarity

**Definition:** **Unambiguous labels and instructions**. Every element should communicate its purpose clearly, leaving no room for confusion about what it is or what it does.

**Why it matters:** If users have to guess what a button does or what a field expects, the interface has failed at clarity.

**Examples:** "Add to Cart" is clear; a cryptic icon with no label is not. "Enter your email address" is clear; a blank field with no label or placeholder is not.

### 5.4 Error Prevention

**Definition:** **Minimize mistakes before they occur**. Design for guidance, not punishment — it is better to prevent errors than to display error messages after the fact.

**Examples:** Greying out unavailable options, using date pickers instead of free-text date fields, showing password requirements before the user submits, confirmation dialogs before destructive actions ("Are you sure you want to delete your account?"), constraining input fields to valid formats.

### 5.5 Consistency

**Definition:** **Similar elements behave the same way everywhere**. Consistency creates familiarity — once a user learns how one part of the interface works, that knowledge transfers to other parts.

**Why it matters:** Inconsistency forces users to relearn interactions. If the "Submit" button is green on one page and blue on another, or at the top of one form and the bottom of another, users waste cognitive effort figuring out the pattern.

**Examples:** Using the same button style for all primary actions, consistent navigation placement across all pages, consistent icon styles throughout the app.

### 5.6 Hierarchy (Interaction Hierarchy)

**Definition:** **Visually distinguishes primary from secondary actions**, creating clarity about what the user should do first, second, and optionally.

**Why it matters:** In any interface, some actions are more important than others. A product page's "Add to Cart" button should be more prominent than the "Add to Wishlist" button. Interaction hierarchy ensures users can quickly identify the most important action.

**Examples:** Primary buttons (filled, bold color) vs. secondary buttons (outlined, muted color), prominent "Checkout" button vs. subtle "Continue Shopping" link.

---

## 6. Key Concepts of Attention and Cognition

These six concepts are drawn from John Walen's **"Six Minds of UX"** — a framework that addresses the cognitive dimensions of user experience. Human attention and cognition are limited. A usable design supports better decisions during interaction.

### 6.1 Attention

**Question to ask:** Is important information and CTAs (calls-to-action) **clearly seen**?

**What this means:** Users scan rather than read. Critical elements (primary buttons, key information, navigation) must be visually prominent enough to catch the user's attention during scanning. If the most important action on the page does not stand out, users may never find it.

### 6.2 Sense of Location

**Question to ask:** Do users **know where they are** in the app or site?

**What this means:** At any point in the experience, users should understand: what page am I on? How did I get here? How do I go back? Breadcrumbs, highlighted navigation items, page titles, and progress indicators all help establish a sense of location.

### 6.3 Language

**Question to ask:** Are you using **familiar words** and a **trustworthy tone**?

**What this means:** Labels, instructions, error messages, and microcopy should use language the user understands — not technical jargon or internal terminology. The tone should be appropriate to the context (professional for banking, friendly for social apps).

### 6.4 Memory

**Question to ask:** Are you using **recognizable patterns** and **industry conventions**?

**What this means:** Users bring expectations from other apps and websites. A shopping cart icon in the top-right corner, a hamburger menu for mobile navigation, a magnifying glass for search — these are conventions users have memorized. Leveraging them means users do not have to learn new interactions. **Recognition over recall** — show users things they can recognize rather than requiring them to remember.

### 6.5 Decision Making

**Question to ask:** Are users **helped to make decisions**?

**What this means:** Reduce cognitive load by limiting choices, providing recommendations, showing comparisons, using defaults, and surfacing relevant information at the right time. Too many options lead to decision fatigue (as seen in the Gojek case study from L9).

### 6.6 Emotion

**Question to ask:** Does the design evoke appropriate **delight, values, or desires**?

**What this means:** Emotional design goes beyond functionality. Micro-animations, pleasant color palettes, celebratory moments (e.g., confetti after completing a task), and thoughtful microcopy can create positive emotional associations with the product. The goal is to make users feel good about using the product — satisfied, confident, delighted.

---

## 7. Patterns of Human Behaviours

These are **6 best practices** derived from observed patterns in how humans interact with digital products. They serve as design guidelines grounded in real user behaviour.

### 7.1 Pattern #1: Same Information, Same Place, Same Visual Treatment

**Principle:** When the same type of information appears in multiple places, it should always appear in the **same location** and with the **same visual treatment** (typography, color, spacing, layout).

**Why it matters:** Users build mental models of where to find things. If the price is below the product name on one card and above it on another, users' mental models break and they have to search harder.

### 7.2 Pattern #2: Proficient Users Prefer Keyboard Shortcuts

**Principle:** As users become more experienced, they want **faster ways to perform frequent actions**. Keyboard shortcuts, command palettes, and power-user features cater to this need.

**Why it matters:** Novice users need visible buttons and menus. Expert users find these slow. Designing for both (visible controls for novices, shortcuts for experts) serves the full spectrum.

### 7.3 Pattern #3: Users Need Reminders and Notes

**Principle:** Users benefit from **contextual reminders, tooltips, and helper text** that reduce the burden on memory.

**Why it matters:** Users should not be expected to memorize information from a previous screen or a previous session. Providing reminders (e.g., "You last ordered from this restaurant 3 days ago") and notes (e.g., "Password must be at least 8 characters") supports the user's working memory.

### 7.4 Pattern #4: Information Before Action + Instant Feedback After

**Principle:** Provide **sufficient information before asking the user to act**, and provide **instant feedback** after they have acted.

**Why it matters:** Users need to understand the consequences of an action before they commit (e.g., "This will permanently delete your account") and need immediate confirmation that their action was received (e.g., "Item added to cart" popup). This pattern combines **error prevention** (information before action) with **feedback** (instant response after action).

### 7.5 Pattern #5: Users Like Control and Flexibility

**Principle:** Users want to feel in control of the interaction. Key sub-patterns include:

- **Undo / Cancel** — always provide a way to reverse actions
- **Good defaults** — pre-fill forms with sensible default values so users can proceed quickly
- **Constrained input** — use dropdowns, date pickers, and toggles instead of free-text fields where possible to reduce errors
- **Save half-filled forms** — if a user navigates away and comes back, preserve their progress
- **Predefined options** — offer common choices (e.g., shipping addresses, payment methods) instead of requiring re-entry

**Why it matters:** Feeling in control reduces anxiety and increases trust. Users who feel trapped by an interface (no back button, no undo, lost form data) become frustrated and may abandon the task.

### 7.6 Pattern #6: Social Proof

**Principle:** Users are influenced by what others have done. **Reviews, ratings, likes, "also viewed" sections, and popularity indicators** all provide social proof that builds confidence in decisions.

**Why it matters:** When users are uncertain (which restaurant to pick, which product to buy), seeing that others have made a choice — and were satisfied — reduces uncertainty and accelerates decision-making. This is why Amazon shows star ratings and review counts, Netflix shows "Top 10" lists, and restaurants display "X people ordered this."

---

## 8. Case Studies: Applying Principles to Real Apps

### 8.1 Paytm Redesign

The Paytm app redesign demonstrates the application of both visual design and interaction design principles to improve usability. Key design changes can be analyzed through the lens of hierarchy (making primary actions more prominent), proximity (grouping related services together), consistency (standardizing card layouts), and affordance (making interactive elements clearly tappable).

### 8.2 Grab App

The Grab super-app demonstrates the application of design principles across a multi-service platform. Relevant principles include hierarchy (distinguishing between ride-hailing, food delivery, and financial services), similarity (consistent card patterns across service categories), and feedback (real-time tracking updates and order confirmations).

### 8.3 Gojek Redesign

The Gojek app redesign illustrates how visual design and interaction design principles improve navigation in a super-app. Key principles applied include common region (grouping services into distinct sections), contrast (making primary CTAs stand out), alignment (consistent grid layout for service icons), and clarity (clear, unambiguous labels for each service).

---

## 9. AI Tools for Visual Design

### 9.1 AI Colors (aicolors.co)

**What it is:** A free AI-powered color palette generator.

**Features:**
- Generate palettes from **keyword searches** (e.g., "ocean sunset," "corporate trust")
- AI-powered **recommendations** for complementary colors
- **Visualization** of palettes applied to sample UI components
- **Export** palettes in various formats for use in design tools

**Cost:** Free

### 9.2 ColorAI (Figma Plugin)

**What it is:** A Figma plugin that generates color palettes from text descriptions.

**Features:**
- **Text-to-palette** — describe the mood, theme, or context and get a matching palette
- Integrates directly into the Figma design workflow

**Cost:** Free

### 9.3 AI Color Palette Generator (Figma Plugin)

**What it is:** A Figma plugin that extracts color palettes from photos and applies them to designs.

**Features:**
- **Photo-based extraction** — upload an image and the plugin identifies its dominant colors
- **Smart Apply** — automatically applies the extracted palette to selected design elements
- Works within Figma for seamless integration

**Cost:** Freemium (basic features free, advanced features paid)

---

## 10. Summary

| Concept | Key Point |
|---|---|
| Design Principles placement | Sit between Prototype and Evaluation — help identify usability issues before testing |
| UI Layers | **Templates** (page-level structure), **Patterns** (reusable control groupings), **Controls** (individual building blocks) |
| Gestalt Perception (7 principles) | **Similarity** (link similar elements), **Proximity** (closeness = relatedness), **Figure & Ground** (foreground vs. background focus), **Common Region** (shared area = group), **Closure** (brain completes incomplete shapes), **Continuity** (eyes follow lines/flow), **Symmetry** (balance and order) |
| Design Communication (3 principles) | **Contrast** (draw attention through difference), **Hierarchy** (rank by importance), **Alignment** (guide eyes through aligned objects) |
| 6 Usable Interaction Concepts | **Affordance** (visual cues → possible actions), **Feedback** (system reacts to confirm), **Clarity** (unambiguous labels), **Error Prevention** (prevent before punish), **Consistency** (same behavior everywhere), **Hierarchy** (primary vs. secondary actions) |
| 6 Attention & Cognition Concepts | **Attention** (is CTA clearly seen?), **Sense of Location** (do users know where they are?), **Language** (familiar words?), **Memory** (recognizable patterns?), **Decision Making** (are users helped?), **Emotion** (delight and trust?) |
| 6 Behaviour Patterns | #1 Same info/same place/same treatment, #2 Experts prefer shortcuts, #3 Users need reminders, #4 Info before action + instant feedback after, #5 Users want control & flexibility, #6 Social proof |
| AI Tools | AI Colors (free palette generator), ColorAI (Figma text-to-palette), AI Color Palette Generator (Figma photo extraction) |

**Key takeaways:**
1. **Visual design principles are rooted in human psychology.** Gestalt perception principles (similarity, proximity, figure & ground, common region, closure, continuity, symmetry) describe how the brain automatically organizes visual information — designers leverage these to create interfaces that feel intuitive without explicit instruction.
2. **Design communication is about directing attention.** Contrast, hierarchy, and alignment work together to tell users what to look at first, what to read next, and how information is related. The critical rule is: "If everything is bold, nothing stands out."
3. **Interaction design bridges visual design and user behaviour.** The 6 usable interaction concepts (affordance, feedback, clarity, error prevention, consistency, hierarchy) ensure that interfaces not only look right but also behave as users expect.
4. **Good design reduces cognitive load.** Across all frameworks in this lecture — Gestalt, communication, interaction, attention, behaviour patterns — the unifying theme is that good design makes thinking easier. It reduces the mental effort required to perceive, understand, decide, and act.
