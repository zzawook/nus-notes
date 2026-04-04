# Lecture 09: AI in Everyday Apps

> **What this covers:** This lecture examines how AI is integrated into real-world consumer applications — not as a separate feature users interact with, but as an invisible or semi-visible layer that improves the user experience. You will learn the **three roles of AI** in apps (Orchestration, Personalization, Automation), their different **visibility levels**, and how they manifest across major products like Gojek/GoFood, Zhixiaobao/Alipay, Google Maps, Uber, Netflix, Spotify, Amazon, Gmail, Visa, Grab, WeChat, and Paytm. The key design insight is that **effective AI in apps is often invisible** — it improves decision-making by reducing scrolling, filtering, and cognitive load without requiring users to learn new interfaces.

---

## 1. Case Study: Gojek — Smart Defaults Through Personalization

### 1.1 What Is Gojek?

Gojek is a leading Southeast Asian super-app founded in Jakarta, Indonesia. It brings multiple everyday services into a single platform — users can book rides, order food and groceries, send parcels, pay bills, and make cashless transactions through its integrated wallet and payment ecosystem. **GoFood** is Gojek's food-delivery service, allowing users to order meals from local restaurants and street-food vendors.

### 1.2 The Problem: Before Personalization

GoFood's "NEAR ME" feature used a **simple distance-based ranking** — it showed the closest restaurants first and then continued in an endless list as distance increased. Both a 24-year-old student who loves variety ("Foodie Mila") and a 35-year-old manager who wants familiar noodle shops ("Workaholic Husain") saw **the exact same list**.

This design exposed users to **too many options with little guidance**, forcing them to scroll and compare extensively. The high cognitive load often led to **decision fatigue and drop-off** before placing an order.

### 1.3 The Solution: AI-Driven Relevance Ranking

After introducing AI-driven relevance ranking and personalization, the same entry point ("NEAR ME") now produces **different default results for different users**. Instead of ranking purely by distance, the system reorders the list based on each user's **past behavior, cuisine preferences, and ordering patterns**.

For Mila, restaurants aligned with her interest in variety and fast food exploration appear higher by default, even if they are slightly farther away. For Husain, the list prioritizes familiar noodle and quick-delivery options that match his habit of ordering fast and reliably.

### 1.4 Key Design Insights

This is a strong example of **smart defaults** in action. Rather than adding explicit AI controls, intelligence is embedded into the default ordering of results, making the most relevant options appear first with no extra effort from the user. The **interface looks the same, but what is shown first is different.**

**Effective AI in super-apps is often invisible** — it improves decision-making by reducing scrolling, filtering, and cognitive load. The AI reduces cognitive load by surfacing the most relevant choices first, making decision-making faster while keeping the interface itself unchanged.

---

## 2. Case Study: Zhixiaobao (Alipay) — AI as a Unified Entry Point

### 2.1 What Is Zhixiaobao?

Zhixiaobao is an **AI assistant** developed by Alibaba's Ant Group. It is available as a standalone app and can also be accessed directly inside Alipay by searching for its name. Within Alipay, Zhixiaobao functions as an AI interaction layer, helping users find and operate different services through conversation, cards, and smart actions.

### 2.2 Interface Design

**Home view:** A set of AI-generated cards representing different services and tasks — transportation, delivery, daily utilities. These cards act as clickable shortcuts, allowing users to jump directly to related services. The assistant proactively provides context-aware alerts (e.g., high-temperature weather warnings, package delivery updates) shown as cards with clear visuals and actionable information.

**"Quick Commands":** Common commands on the left side of the input bar — check the weather, view daily news, review monthly expenses, find nearby shared bikes. When a user selects "Find a nearby bike," the service provider (e.g., Hello Bike) appears within the chat window, and users can scan a QR code to start riding directly. Tapping the arrow on a service card redirects to the corresponding Alipay mini-program for further actions.

**"Intelligent Agents" list:** Different AI agents grouped by topics — fitness, travel, career, daily advice. Users can select an agent and start a conversation.

**Real-time translation:** A well-designed small, draggable widget. A single click switches between languages. Instead of burying this function in a "Language" menu under "Settings," Alipay made it a separate, floating element. This provides two key benefits: users can find and use it instantly, and the development team avoids the complex task of creating entirely new interfaces for each language.

### 2.3 Key Design Insights

Zhixiaobao shows how an AI assistant can act as a **unified entry point for a super-app with many services**. By combining chat, cards, and smart actions, it reduces the need for users to search through complex menus. **AI is not presented as a separate feature, but as a new way to access and operate the super-app.**

Each card links directly to an existing Alipay service (payments, transport, food delivery), turning the assistant into a **service navigation and execution layer** rather than a standalone chatbot. It demonstrates a good balance between fast actions in chat and full control through service pages — a key design challenge in large multi-service apps.

---

## 3. The Three Roles of AI in Apps

When we abstract AI use and roles across different domains (payments, maps, feeds, logistics, marketplaces) and across AI design guidelines (Microsoft HAX AI guideline, Google PAIR AI guideline), a repeated pattern emerges. AI is used to: (a) coordinate complexity, (b) shape choices, (c) remove/reduce work.

Accordingly, there are **3 roles** which AI can take up:

| Role | Core Function | Typical Visibility | Responsibility |
|---|---|---|---|
| **Orchestration** (co-ordinate) | Coordinates people, time, resources, matching, scheduling, load balancing, forecasting demand | **Invisible** — visible only when broken | Makes the system run |
| **Personalization** (suggest) | Shapes what each user sees & gets — discovery, ranking, recommendation, decision support | **Semi-visible** — visible enough to feel helpful | Shapes choices without deciding |
| **Automation** (act) | Acts, filters, blocks, executes, generates, detects fraud, flags behaviour | **Semi-visible** — visible enough to feel helpful | Acts on user's behalf (or stops user) |

---

## 4. App-by-App Breakdown of the Three Roles

### 4.1 Google Maps

**UX goal: Don't interrupt the journey, improve it.**

| Role | Visibility | Examples |
|---|---|---|
| Orchestration | Invisible | Coordinates millions of drivers simultaneously; predicts traffic, reroutes for jams; analyzes vast amounts of imagery to detect changes and keep the map fresh |
| Personalization | Semi-visible | Mode preferences (walking vs driving vs public transport); place recommendations (restaurants, cafes, saved spots) |
| Automation | Semi-visible | Automatic rerouting when traffic changes; speed trap / incident detection; ETA updates without user input |

### 4.2 Uber

**UX goal: Users experience shorter waits.**

| Role | Visibility | Examples |
|---|---|---|
| Orchestration | Invisible | Driver–rider matching across segments; demand prediction; city-scale optimization and dynamic pricing; graceful degradation — clear status when coordination breaks |
| Personalization | Semi-visible | Suggested pickup points; ride type ranking (UberX, Premier, Auto, Pool); habit learning (frequent routes, commute timing) |
| Automation | Semi-visible | Live ETA recalculation, automatic rerouting during traffic; trip progress monitoring; safety anomaly detection (long stops, route deviation) |

### 4.3 Netflix

**UX goal: Reducing decision fatigue.**

| Role | Visibility | Examples |
|---|---|---|
| Personalization | Semi-visible | Over 80% of content watched is driven by its AI-powered recommendation engine, which analyzes viewing habits, pauses, skips, and search history; AI selects different thumbnails and artwork for the same show to match individual user preferences; fast, high-quality subtitling and voiceover dubbing in multiple languages |
| Automation | Semi-visible | Autoplay next episode; skip intro / recap; resume across devices; dynamic buffering & quality adjustment |

### 4.4 Spotify

**UX goal: Hyper personalize song suggestions.**

| Role | Visibility | Examples |
|---|---|---|
| Orchestration | Invisible | Balancing artist exposure and platform constraints; traffic & usage forecasting |
| Personalization | Semi-visible | Taste profiling; context inference (time, activity, mood); personalized mixes, "Daylist" playlists, and song suggestions; uses OpenAI tech for song discovery |
| Automation | Semi-visible | Autoplay after a playlist ends; seamless transitions between tracks; cross-device session continuation; smart shuffle ordering |

### 4.5 Amazon

**UX goal: Improve efficiency, and enhance customer experience.**

| Role | Visibility | Examples |
|---|---|---|
| Orchestration | Invisible | Predicting demand before orders happen; deciding which warehouse should fulfill your order; coordinating robots, pickers, trucks, last-mile delivery; load balancing during sales (Prime Day) |
| Personalization | Semi-visible | Cross-sell nudging; analyze browsing history for personalized product recommendations; price sensitivity modeling |
| Automation | Semi-visible | One-click checkout; automatic refunds & returns; fraud detection |

### 4.6 Gmail

**UX goal: Increase productivity.**

| Role | Visibility | Examples |
|---|---|---|
| Orchestration | Invisible | Routing billions of emails reliably; managing load spikes (news, sales, spam waves); synchronizing messages across devices |
| Personalization | Semi-visible | Priority Inbox (important vs everything else); highlighting important senders, behaviour-based filtering; nudging reminders ("Reply?" / "Follow up?") |
| Automation | Semi-visible | Summarizes emails to help users catch up on key points; Smart Reply suggests detailed, personalized responses; blocks spam, phishing, and malware |

### 4.7 Visa

**UX goal: Secure, optimize, and modernize the global payment ecosystem.**

| Role | Visibility | Examples |
|---|---|---|
| Orchestration | Invisible | Coordinating transactions across banks, countries, currencies; managing peak loads (sales, festivals, global events); ensuring near-zero downtime worldwide |
| Personalization | Invisible | Learning spending patterns per card; understanding location, merchant type, frequency; adjusting risk tolerance dynamically |
| Automation | Semi-visible | Evaluates fraud risk in real-time for transactions; spots bot-driven attacks; escalation triggering; clear paths for recovery when AI fails |

### 4.8 Grab (Singapore)

**UX goal: A seamless, user-centric "everyday superapp."** Began as ride-hailing and expanded into food delivery, courier services, and financial services.

| Role | Visibility | Examples |
|---|---|---|
| Orchestration | Invisible | Ensuring delivery reliability with automated order splitting (LLM evaluates orders based on size and weight); AI-assisted voice reporting tool for drivers to update road conditions in real time |
| Personalization | Invisible | Overcoming language barriers with accurate local menu translations; AI Voice Assistant for visually impaired users; AI Merchant Assistant as 24/7 business advisor |
| Automation | Semi-visible | Visual pickup guides using AI-generated videos from static images; real-time driver status updates; AI ride-guidance directing drivers towards hotspots |

### 4.9 WeChat (China)

**UX goal: Friendly, efficient, and cohesive digital ecosystem.** Began as messaging, expanded into social media, payments, mini programs, work.

| Role | Visibility | Examples |
|---|---|---|
| Orchestration | Invisible | Seamlessly connecting content discovery with social sharing (reducing friction in user journeys); AI in group chats for efficiency and companionship |
| Personalization | Invisible | Chatbots handle customer inquiries, recommend products, track orders 24/7; news and video mini programs analyze user behavior for personalized content |
| Automation | Semi-visible | AI agents handle routine customer inquiries with escalation to human agents; shared access to customer histories; generating images based on text prompts |

### 4.10 Paytm (India)

**UX goal: A seamless, user-centric "everyday superapp."** Began as mobile payments, expanded into e-commerce, bill payments, ticketing, and travel.

| Role | Visibility | Examples |
|---|---|---|
| Orchestration | Invisible | AI-powered POS devices with real-time voice-based transaction updates and merchant performance insights; Paytm ARMS and Paytm Pi automate merchant onboarding, fraud detection, segmentation, and pricing optimization |
| Personalization | Invisible | Paytm Playback turns recent transactions into a personalized rap song; conversational AI agents for personalized assistance via text and voice across 11 languages |
| Automation | Semi-visible | Translating natural language into complex database queries; AI-powered Monthly Spend Summary organizes expenses across shopping, bills, travel |

---

## 5. Summary

| Concept | Key Point |
|---|---|
| Smart defaults (Gojek) | AI personalizes the default ordering of results — interface stays the same, but what appears first changes per user |
| AI as unified entry point (Zhixiaobao) | AI assistant acts as a navigation and execution layer for a super-app, combining chat + cards + smart actions |
| Three AI roles | **Orchestration** (invisible, coordinates), **Personalization** (semi-visible, shapes choices), **Automation** (semi-visible, acts on behalf) |
| Visibility principle | Effective AI in apps is often invisible — it improves the experience without requiring users to learn new interfaces |
| Design guidelines referenced | Microsoft HAX AI guideline, Google PAIR AI guideline |

**Key takeaways:**
1. **Effective AI in everyday apps is often invisible.** Rather than adding explicit AI controls, the best implementations embed intelligence into what users already see and do — smart defaults, personalized ranking, automated actions behind the scenes.
2. **AI plays three roles** across all domains: Orchestration (making the system run), Personalization (shaping choices without deciding), and Automation (acting on the user's behalf). Every major app uses all three.
3. **The interface can stay the same** while the AI changes what is shown, when, and how — this is the most powerful and least disruptive form of AI integration in UX.
4. **Across super-apps** (Gojek, Alipay/Zhixiaobao, Grab, WeChat, Paytm), AI is increasingly used as a **unified interaction layer** that connects users to multiple services through conversation, cards, and smart actions rather than traditional menu navigation.
