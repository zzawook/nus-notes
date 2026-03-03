# CS3240 Lecture 6: Design Systems

> **Module:** CS3240 Interaction Design | **Semester:** S2 AY2025-26 | **Instructor:** Assoc Prof Bimlesh Wadhwa

---

## Table of Contents

1. [What is a Design System?](#1-what-is-a-design-system)
2. [Why Create a Design System?](#2-why-create-a-design-system)
3. [Design Systems vs. Style Guides](#3-design-systems-vs-style-guides)
4. [Good Design System Examples](#4-good-design-system-examples)
5. [How to Start a Simple Design System](#5-how-to-start-a-simple-design-system)
6. [Styles (in Figma)](#6-styles-in-figma)
7. [Variables (in Figma)](#7-variables-in-figma)
8. [Variables vs. Styles](#8-variables-vs-styles)
9. [Colours — Choosing a Colour Scheme](#9-colours--choosing-a-colour-scheme)
10. [The Colour Wheel](#10-the-colour-wheel)
11. [The 60-30-10 Rule](#11-the-60-30-10-rule)
12. [Choosing Colours for a Design System](#12-choosing-colours-for-a-design-system)
13. [Design Tokens](#13-design-tokens)
14. [Aliasing (Token Referencing)](#14-aliasing-token-referencing)
15. [Light / Dark Modes](#15-light--dark-modes)
16. [Typography](#16-typography)
17. [Choosing Typography](#17-choosing-typography)
18. [Atomic Design](#18-atomic-design)
19. [Bringing It Together — Creating a Button Component](#19-bringing-it-together--creating-a-button-component)
20. [Summary and Key Takeaways](#20-summary-and-key-takeaways)
21. [Quick-Fire Exam Review](#21-quick-fire-exam-review)

---

## 1. What is a Design System?

> **Definition (Nielsen Norman Group):** A Design System is **a set of living standards** to manage design at scale by **reducing redundancy** while creating a **shared language** and **visual consistency** across different pages and channels.

### Key words to remember

| Term | Meaning |
|---|---|
| **Living standards** | The system evolves — it is maintained and updated over time, not a static document |
| **At scale** | Designed to support large products with many screens, teams, and channels |
| **Reducing redundancy** | Reuse components and tokens instead of re-inventing elements for every screen |
| **Shared language** | Designers and developers use the same names, tokens, and components |
| **Visual consistency** | Every page and channel looks and feels like it belongs to the same product |

---

## 2. Why Create a Design System?

A Design System provides three core benefits:

1. **Speed & Scale** — Design (and development) work can be created and **replicated quickly and at scale** because reusable components already exist.
2. **Consistency** — Keeps visual and interaction consistency **throughout the entire design**; every screen feels unified.
3. **Efficient Updates** — Makes updating different elements of the design (colour, typography, spacing, etc.) **much faster** because changes propagate from a single source of truth.

> **Exam tip:** A Design System is not just about aesthetics — it is a **productivity and governance tool** that connects design to development.

---

## 3. Design Systems vs. Style Guides

### Relationship

```
┌─────────────────────────────────────────┐
│            Design System                │
│                                         │
│   ┌─────────────────────────────────┐   │
│   │         Style Guide             │   │
│   │  (Logo, Colours, Fonts)         │   │
│   └─────────────────────────────────┘   │
│                                         │
│   + Components                          │
│   + Code                                │
│   + Patterns                            │
│   + Design Tokens                       │
│   + Guidelines & Documentation          │
└─────────────────────────────────────────┘
```

**Style Guides are a subset of Design Systems.**

| Aspect | Style Guide | Design System |
|---|---|---|
| **Scope** | Visual identity elements only | Visual identity + components + code + patterns + documentation |
| **Typical contents** | Logo, colours, fonts | Everything in a Style Guide **plus** reusable components, code snippets, interaction patterns, design tokens |
| **Focus** | Visual consistency | Visual consistency **and** functional consistency across design and development |
| **Origin** | Graphic Design field | Product / Digital Design field |

> **Key distinction:** A Style Guide tells you *what things look like*. A Design System also tells you *how things behave*, *how to build them*, and *how to name them*.

---

## 4. Good Design System Examples

| Design System | URL | Notable For |
|---|---|---|
| **Atlassian Design System** | atlassian.design | Comprehensive component library with clear guidelines |
| **Material Design 3 (Google)** | m3.material.io | Industry-standard design language for Android and web |
| **Carbon Design System (IBM)** | carbondesignsystem.com | Enterprise-grade system with accessibility built in |

> These are worth browsing to understand what a mature, real-world design system looks like.

---

## 5. How to Start a Simple Design System

A minimal Design System requires three foundational pillars:

```
Simple Design System = COLOUR + TYPOGRAPHY + COMMON COMPONENTS
```

| Pillar | What It Covers |
|---|---|
| **Colour** | Brand colours, neutrals, semantic colours (error, success, warning, info) |
| **Typography** | Font families, sizes, weights, line heights |
| **Common Components** | Buttons, inputs, cards, navigation elements — built from colour + typography |

> **Practical advice:** Start small. Define your colours and typography first, then build your most-used components on top of those foundations.

---

## 6. Styles (in Figma)

### What Are Styles?

Styles define the **color, text, and effects** applied to objects, or the **structure and appearance** of layout guides. They act as reusable design decisions.

### Types of Styles You Can Create

| Style Category | Properties |
|---|---|
| **Color Styles** | Fill, stroke, background color, gradients |
| **Text Styles** | Font family, size, line height, letter spacing |
| **Effect Styles** | Drop shadow, inner shadow, layer blur, background blur, texture, noise |

### How Styles Help

- Apply a style once → reuse it everywhere
- Change the style definition → every instance updates automatically
- Ensures consistency without manual checking

> **Visual cue in Figma:** Styles are shown as **circles** in the Figma interface.

---

## 7. Variables (in Figma)

### What Are Variables?

Variables store **reusable values** that can be applied to design properties. They are more powerful and flexible than Styles.

### How to Access Variables

- **Create:** Using the Local Variables modal
- **Apply:** Via the Library (slider icon)

### Key Feature: Modes

Variables support **multiple modes** (e.g., Light Mode / Dark Mode), allowing the same variable to resolve to different values depending on context.

> **Note:** Adding multiple modes requires a Figma Education or paid plan.

> **Visual cue in Figma:** Variables are shown as **squares** in the Figma interface.

---

## 8. Variables vs. Styles

| Aspect | Variables | Styles |
|---|---|---|
| **Visual indicator** | Squares | Circles |
| **Source of truth** | Yes | Yes |
| **Multiple modes** | Yes — supports Light/Dark, brand themes, etc. | No |
| **Scalability** | Easier to scale into a full design system | More limited |
| **Nesting (Aliasing)** | Can reference other variables | Cannot reference other styles |
| **Best for** | Systematic, token-based design (colours, spacing, sizing) | "Combo" elements that combine multiple values (e.g., gradients, complex text styles) |

### When to Use Which

- **Use Variables** when you need a single value that might change across modes or themes (e.g., `color/primary` resolving to blue in Light Mode and light-blue in Dark Mode).
- **Use Styles** when you need a composite value that combines multiple properties (e.g., a gradient that blends two colours, or a text style combining font + size + weight + line-height).

> **Exam tip:** Both act as a **single source of truth**, but Variables are more flexible because they support modes and aliasing (nesting).

---

## 9. Colours — Choosing a Colour Scheme

### When to Choose Colours

Before you start on your **Mid-Fi / High-Fi** prototypes, select a colour scheme.

### How to Choose Colours

1. **Start with a main hue** — Ideally your brand / primary colour
2. **Use an online colour palette generator** — e.g., [Coolors](https://coolors.co)
3. **Look for pre-made palettes** — Find colour matches that complement your main hue
4. **Explore Figma Plug-ins** — Many plugins generate palettes directly inside Figma
5. **Check Accessibility** — Use plugins or tools to verify colour contrast meets accessibility standards (WCAG)

> **Key principle:** Don't pick colours arbitrarily. Use colour theory and tooling to ensure harmony and accessibility.

---

## 10. The Colour Wheel

The colour wheel provides four main strategies for selecting harmonious colour combinations:

### Colour Harmony Types

| Harmony Type | Description | Character |
|---|---|---|
| **Analogous** | Colours positioned **next to each other** on the colour wheel | **Harmonious** — creates a calm, cohesive feel |
| **Triadic** | Three colours **evenly spaced** (120 apart) on the colour wheel | **Bold & vibrant** — colours contrast each other without looking out of place |
| **Complementary** | Two colours on **opposite sides** of the colour wheel | **High contrast, high impact** — creates strong visual tension |
| **Monochromatic** | Lighter, darker, and more/less saturated versions of **one base hue** | **Simple & cohesive** — very unified but can lack variety |

### Visual Summary

```
        Analogous              Triadic            Complementary        Monochromatic
     ┌───────────┐        ┌───────────┐        ┌───────────┐        ┌───────────┐
     │  ● ● ●    │        │  ●        │        │  ●        │        │  ●        │
     │ (adjacent) │        │ / \       │        │  |        │        │  ● (same  │
     │            │        │●   ●      │        │  ●        │        │  ●  hue)  │
     └───────────┘        └───────────┘        └───────────┘        └───────────┘
      Harmonious         Bold, vibrant        High contrast        Simple, cohesive
```

> **Exam tip:** Be able to identify and describe each harmony type and its visual character.

---

## 11. The 60-30-10 Rule

A guideline for proportional colour usage in UI Design, originally developed in **Interior Design**.

### The Rule

| Proportion | Role | Typical Usage in UI |
|---|---|---|
| **60%** | Dominant colour | **Background** — the largest visual area |
| **30%** | Secondary colour | **Cards / Special sections** — surfaces that sit on top of the background |
| **10%** | Accent colour | **Buttons / Graphics / CTAs** — small, high-impact elements that draw attention |

### Why It Works

- Creates **visual hierarchy** — the eye naturally reads from dominant to accent
- Prevents **colour overload** — limits the palette to manageable proportions
- Ensures **balance** — enough variety to be interesting, enough restraint to be cohesive

> **Practical application:** Your background (60%) should be neutral or subdued, your cards/sections (30%) provide structure, and your buttons/accents (10%) pop with your brand colour.

---

## 12. Choosing Colours for a Design System

### Categories of Colours Needed

When building a design system, you need to create a **range of colours** (with shades/tints) for each of the following categories:

| Colour Category | Purpose | Typical Hue(s) |
|---|---|---|
| **Primary (Brand) Colour** | Main identity colour used for key UI elements | Defined by brand |
| **Secondary Colour** | Complementary accent (optional — not all systems need one) | Complements primary |
| **Base Colours** | Fundamental extremes | **Black** + **White** |
| **Neutral / Greys** | Backgrounds, borders, disabled states, subtle text | Grey scale |
| **Alert** | Warnings that need user attention | **Yellow / Orange** |
| **Information** | Informational messages, tooltips | **Blue** |
| **Error** | Error states, validation failures, destructive actions | **Red** |
| **Success** | Confirmation, completion, positive feedback | **Green** |

### Building a Colour Scale

For each colour category, you typically create a scale of shades (e.g., `primary-50`, `primary-100`, ... `primary-900`) ranging from lightest to darkest. This gives you flexibility for hover states, disabled states, backgrounds, and borders.

> **Key principle:** Define the full range upfront so that every shade you need already exists in the system. Ad-hoc colour picks break consistency.

---

## 13. Design Tokens

### What Are Design Tokens?

Each token stores a **piece of design information** — such as colour, sizing, spacing, font, animations, etc. — and is given a **human-readable name** for easy reference.

### Examples of Tokens

| Token Name | Value | Category |
|---|---|---|
| `color/primary/500` | `#3B82F6` | Colour |
| `spacing/md` | `16px` | Spacing |
| `font/body/size` | `14px` | Typography |
| `radius/button` | `8px` | Border radius |

### Benefits of Design Tokens

| Benefit | Explanation |
|---|---|
| **Single source of truth** | Maintains consistency between design and code |
| **Scalability** | Improves management of a scaling design system |
| **Removes guesswork** | Developers don't have to eyeball values from mockups |
| **Efficiency** | Helps you build more efficiently by reusing defined values |

> **How it bridges Design ↔ Code:** Design tokens can be exported as JSON, CSS variables, or platform-specific formats, ensuring that the values designers define are the exact values developers use.

---

## 14. Aliasing (Token Referencing)

### What is Aliasing?

Aliasing allows any token to **reference (or take on) the value of another token**. If the referenced token changes, any token aliasing it will **automatically update** as well.

### How Aliasing Works

```
Primitive Token                  Semantic (Alias) Token
─────────────────               ─────────────────────────
yellow-100 = #FEF3C7     ◄──── button/primary/bg
yellow-300 = #FCD34D            (references yellow-100)
yellow-500 = #F59E0B
```

### Why Aliasing Matters

**Scenario:** The company decides to change their primary button colour from `yellow-100` to `yellow-300`.

| Without Aliasing | With Aliasing |
|---|---|
| Must find and update **every instance** where `#FEF3C7` is used for buttons | Change **one reference**: `button/primary/bg` now points to `yellow-300` |
| Error-prone, slow, may miss instances | Instant, reliable, single change propagates everywhere |

### Token Hierarchy

```
┌─────────────────────────────────────────────────┐
│  Primitive Tokens (raw values)                  │
│  e.g., yellow-100, blue-500, grey-200           │
│                                                 │
│      ▼ referenced by                            │
│                                                 │
│  Semantic / Alias Tokens (contextual names)     │
│  e.g., button/primary/bg, text/error,           │
│        background/surface                       │
│                                                 │
│      ▼ applied to                               │
│                                                 │
│  Components                                     │
│  e.g., Button, Card, Input                      │
└─────────────────────────────────────────────────┘
```

> **Exam tip:** Aliasing is what makes a design system **scalable**. Without it, a single brand colour change could require hundreds of manual updates. With it, one change at the primitive level cascades through the entire system.

---

## 15. Light / Dark Modes

### How Modes Work with Variables

Variables support **multiple modes**, meaning the same semantic token can resolve to different primitive values depending on the active mode.

### Example

| Semantic Token | Light Mode Value | Dark Mode Value |
|---|---|---|
| `background/surface` | `white` (#FFFFFF) | `grey-900` (#1A1A1A) |
| `text/primary` | `grey-900` (#1A1A1A) | `white` (#FFFFFF) |
| `button/primary/bg` | `blue-500` (#3B82F6) | `blue-400` (#60A5FA) |

### Why This is Powerful

- Define your semantic tokens **once**
- Assign values per mode (Light, Dark, High Contrast, etc.)
- Switch modes and the **entire UI updates** — no manual recoloring needed

> **Connection to Aliasing:** Modes build on top of aliasing. Your components reference semantic tokens, and those tokens resolve differently per mode.

---

## 16. Typography

### Types of Fonts

| Font Type | Characteristics | Best For | Example |
|---|---|---|---|
| **Serif** | Has small decorative strokes ("serifs") at the ends of letters | Authoritative, professional feel; more legible at **smaller scales** — good for **print** | Times New Roman |
| **Sans-Serif** | No decorative strokes ("sans" = without) | Modern feel; more legible on **digital screens** | Futura, Helvetica, Inter |

### Key Differences

```
Serif:       T  (notice the small "feet" on the letter)
Sans-Serif:  T  (clean, no decorations)
```

> **Exam tip:** Know that serif fonts are traditionally better for print legibility, while sans-serif fonts are better for digital screen legibility.

---

## 17. Choosing Typography

### Guidelines for Selecting Fonts

1. **Mix-and-match is allowed** — You may combine a serif and a sans-serif font to create visual synergy (e.g., serif headings + sans-serif body text)
2. **Limit font families** — Rule of thumb: **1-2 text styles per application** (too many fonts create visual noise)
3. **Avoid Script Typefaces** — Unless it is for a special scenario (such as a logo or decorative heading)

### Ensuring Readability

| Property | What to Check |
|---|---|
| **Font size** | Large enough to read comfortably on target devices |
| **Kerning** | Distance between individual letters — avoid too tight or too loose |
| **Line height** | Distance between lines of text — sufficient spacing improves readability |
| **Colour contrast** | Font colour vs. background colour must meet accessibility standards (WCAG) |
| **Title vs. Paragraph hierarchy** | Title fonts should **"pop" more** than paragraph fonts (larger, bolder, or different family) |

> **All of these properties are adjustable in Figma** — use the text properties panel to fine-tune.

### Typography Hierarchy Example

```
Heading 1    — 32px, Bold, Sans-Serif     (most prominent)
Heading 2    — 24px, Semi-Bold, Sans-Serif
Heading 3    — 20px, Semi-Bold, Sans-Serif
Body         — 16px, Regular, Sans-Serif
Caption      — 12px, Regular, Sans-Serif   (least prominent)
```

---

## 18. Atomic Design

### What is Atomic Design?

A **modular system** for creating complex designs by starting from the most basic and simple components and **building up** in layers of increasing complexity.

### The Atomic Design Hierarchy

```
Atoms ──────> Molecules ──────> Organisms ──────> Templates ──────> Pages

Smallest,         Grouped           Grouped             Page-level         Final,
indivisible       atoms             molecules            layouts            live
UI elements                         & atoms                                 pages
```

| Level | Description | Examples |
|---|---|---|
| **Atoms** | Most basic, indivisible UI elements | Button, Input field, Label, Icon, Colour swatch |
| **Molecules** | Groups of atoms that function together as a unit | Search bar (input + button), Form field (label + input + error text) |
| **Organisms** | Groups of molecules and/or atoms forming a distinct section | Navigation bar, Header, Card with image + text + button |
| **Templates** | Page-level layouts composed of organisms | Homepage layout, Dashboard layout |
| **Pages** | Specific instances of templates with real content | The actual homepage with real data |

### Why Atomic Design Matters for Design Systems

Each **Atom** has its own unique properties (set by variables). When that property changes:

```
Atom changes ──> Molecules update ──> Organisms update ──> Templates update ──> Pages update
```

A single change at the atomic level **cascades through the entire design**. This is the same principle as design tokens and aliasing — change once, update everywhere.

> **Reference:** Brad Frost's *Atomic Design* — atomicdesign.bradfrost.com/chapter-2/

> **Exam tip:** Atomic Design is the architectural philosophy that underlies component-based design systems. It explains *why* small, reusable building blocks make complex UIs manageable.

---

## 19. Bringing It Together — Creating a Button Component

### How Everything Connects

Building a single Button component demonstrates the full design system stack:

```
Step 1: COLOUR TOKENS
   Define primitive colours → alias to semantic tokens
   e.g., blue-500 → button/primary/bg

Step 2: TYPOGRAPHY
   Define text styles for button label
   e.g., font-family: Sans-Serif, size: 14px, weight: Semi-Bold

Step 3: VARIABLES
   Store colour, sizing, spacing, border-radius as variables
   Enable Light/Dark modes

Step 4: BUILD THE COMPONENT (Atom)
   Combine colour + typography + spacing + border-radius
   into a reusable Button component

Step 5: USE IN MOLECULES → ORGANISMS → PAGES
   The Button appears in forms (molecule), headers (organism),
   and full pages — always consistent, always up to date
```

### What Changes When the Brand Colour Changes?

1. Update the **primitive token** (e.g., `blue-500` → new hex value)
2. Every **semantic token** referencing it updates automatically (via aliasing)
3. Every **component** using those tokens updates automatically
4. Every **screen** using those components updates automatically

> **This is the power of a Design System:** one change, total consistency.

---

## 20. Summary and Key Takeaways

### Core Concepts

1. **A Design System** is a set of living standards for managing design at scale — reducing redundancy and creating shared language and visual consistency.
2. **Style Guides are a subset** of Design Systems. Design Systems go further with components, code, patterns, and tokens.
3. **Start simple:** Colour + Typography + Common Components is enough to begin.
4. **Styles** define reusable color/text/effects (shown as circles). **Variables** store reusable values with mode support (shown as squares). Both are sources of truth, but variables are more flexible.
5. **Colour selection** should use colour theory (Analogous, Triadic, Complementary, Monochromatic) and the **60-30-10 rule** for proportional usage.
6. **Design Tokens** store named design values (colour, spacing, font, etc.) bridging design and code as a single source of truth.
7. **Aliasing** lets tokens reference other tokens — enabling scalable, cascading updates across the entire system.
8. **Modes** (Light/Dark) let the same semantic token resolve to different values depending on context.
9. **Typography** choices affect readability and tone. Use 1-2 font families, ensure contrast and hierarchy, prefer sans-serif for digital screens.
10. **Atomic Design** builds complex UIs from simple building blocks (Atoms → Molecules → Organisms → Templates → Pages), where changes at the atomic level cascade upward.

### Concept Map

```
Design System
    │
    ├── Foundation
    │   ├── Colour
    │   │   ├── Colour Wheel (Analogous, Triadic, Complementary, Monochromatic)
    │   │   ├── 60-30-10 Rule (Background, Cards, Accents)
    │   │   └── Categories (Primary, Secondary, Base, Neutral, Alert, Info, Error, Success)
    │   │
    │   ├── Typography
    │   │   ├── Serif vs. Sans-Serif
    │   │   ├── 1-2 font families max
    │   │   └── Readability (size, kerning, line height, contrast, hierarchy)
    │   │
    │   └── Common Components (Buttons, Inputs, Cards, etc.)
    │
    ├── Figma Implementation
    │   ├── Styles (circles) → composite values, gradients
    │   └── Variables (squares) → single values, modes, aliasing
    │
    ├── Design Tokens
    │   ├── Named design values (colour, spacing, font, radius, etc.)
    │   ├── Primitive Tokens → raw values (blue-500, grey-200)
    │   ├── Semantic / Alias Tokens → contextual names (button/primary/bg)
    │   └── Bridge between design and code
    │
    ├── Aliasing
    │   ├── Tokens referencing other tokens
    │   └── Change once → updates cascade everywhere
    │
    ├── Modes (Light / Dark)
    │   ├── Same semantic token → different values per mode
    │   └── Built on top of variables and aliasing
    │
    └── Atomic Design
        ├── Atoms → Molecules → Organisms → Templates → Pages
        ├── Build complex from simple
        └── Changes at atomic level cascade upward
```

### Key Relationships Across Lectures

```
L1-L4: Understanding Users ──> L6: Building the Visual Language
                                    │
                                    ├── Colour    ─┐
                                    ├── Typography ─┤── Design System
                                    ├── Components ─┘
                                    ├── Tokens + Aliasing (scalability)
                                    ├── Modes (adaptability)
                                    └── Atomic Design (architecture)
```

---

## 21. Quick-Fire Exam Review (Test Yourself)

1. **Define** a Design System in your own words. What are its key characteristics?
2. What are the **three core benefits** of creating a Design System?
3. What is the **relationship** between a Style Guide and a Design System? How do they differ?
4. What are the **three foundational pillars** of a simple Design System?
5. In Figma, what are the **three types of Styles** you can create? Give examples of each.
6. How do **Variables** differ from **Styles** in Figma? When would you use each?
7. How are Variables and Styles **visually distinguished** in Figma?
8. Name and describe the **four colour harmony types** from the colour wheel.
9. Explain the **60-30-10 rule**. What does each proportion typically map to in UI design?
10. List the **eight colour categories** needed in a design system and their typical hues.
11. What is a **Design Token**? What information does it store?
12. List **four benefits** of using Design Tokens.
13. What is **Aliasing** in the context of design tokens? Why is it important?
14. Walk through the **scenario**: a company changes their primary colour from `yellow-100` to `yellow-300`. How does aliasing help? What happens without it?
15. Explain how **Light/Dark modes** work with variables. Give an example of a token that resolves differently in each mode.
16. What is the difference between **Serif** and **Sans-Serif** fonts? Which is better for digital screens?
17. What is the **rule of thumb** for the number of font families in an application?
18. List **five properties** to check when ensuring typography readability.
19. What is **Atomic Design**? Name and define all five levels of the hierarchy.
20. Why does Atomic Design matter for Design Systems? What happens when an Atom's property changes?
21. Describe how building a **Button component** ties together colour tokens, typography, variables, and modes.
22. What is the **token hierarchy** (three levels)? How do they relate to each other?
23. A designer picks random hex colours for each screen without defining any colour tokens. What problems will this cause?
24. Explain the difference between a **primitive token** and a **semantic/alias token** with a concrete example.
25. How does the concept of a **single source of truth** connect Styles, Variables, Design Tokens, and Aliasing?

---

*Notes based on CS3240 L6 lecture slides by Assoc Prof Bimlesh Wadhwa, NUS. For personal study use only.*
