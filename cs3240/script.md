# PropBank Final Prototype — Demo Script

> Format: spoken line first. `[ACTION: …]` in brackets where a tap/scroll/click happens. Start in Figma Presentation view on the Shared combined page, on **Feed** (`1738:2306`).

---

## Open (0:00)

> "Hi everyone, I'm Jay. My assigned service for PropBank is **CreatorHub** — the social layer where cosplayers post content, discover creators, and curate what inspires them. I also designed the **Settings and Profile** utility for the group. Today I'll walk through three tasks I defined, and show how the combined prototype enables each of them end to end. I'll drive everything from our shared combined file so you can see how CreatorHub connects to my teammates' services."

[ACTION: Start prototype in Presentation mode from **Feed** (`1738:2306`) on the "Group - Shared App" page.]

---

## Settings / Profile quick tour (0:30)

> "Before the tasks, a quick tour of the utility I built for the group. Every service in PropBank has a hamburger that opens a shared sidebar, and from the sidebar the user reaches Settings."

[ACTION: Tap the **hamburger icon** at top right of Feed.]

> "The sidebar is a component shared across all five services, so the pattern is consistent. Tap Settings."

[ACTION: In the sidebar, tap the **Settings** item.]

> "This is My Profile — avatar, display name, bio, stats."

[ACTION: Show **My Profile** (`2319:5464`). Scroll briefly if needed.]

> "Any field is editable inline — tap and the profile swaps into editing state."

[ACTION: Tap a profile field → SWAPs to **My Profile (Editing)** (`2397:11381`).]

> "Save returns to the read view. And from Account Settings the user manages notifications, privacy, and linked accounts."

[ACTION: Navigate to **Account Settings** (`2319:5508`). Scroll briefly.]

> "This utility is reused by every teammate, so the identity management works from anywhere in the app. Now the three tasks."

[ACTION: Go back to **Feed** (`1738:2306`).]

---

## Task 4.1 — Share cosplay/prop content (1:30)

> "**Task 4.1 — share cosplay or prop content.** A creator wants to post a photo, tag collaborators, apply the right tags, and set visibility."

### Phase 1 — Prepare

> "The entry point is the Create Post card pinned at the top of the Feed."

[ACTION: Tap the **"+ Create Post"** card at the top of the Feed.]

> "Notice the Media screen is empty — nothing populated. This is the start of step 1.3. The Next button is dimmed because no media is selected yet — a disabled CTA guides the user to add content first."

[ACTION: Pause on **Create Post - Media (empty)** (`1738:2653`). Hover over the dimmed Next button with the cursor.]

> "Tapping the dashed drop zone populates selected media — three thumbnails load, representing steps 1.3 and 1.4."

[ACTION: Tap the dashed **"+ Select Photos & Videos"** area → navigates to **Media (Populated)** (`3189:15927`).]

> "Caption goes directly on this screen — step 1.5. I moved it out of the Details screen because caption feels paired with media."

[ACTION: Point to the populated Caption field showing "My Demon Slayer Nezuko cosplay at AFA 2026!".]

> "Next is active now."

[ACTION: Tap **Next** → advances to **Create Post - Details (empty)** (`1738:2335`).]

### Phase 2 — Enrich & Review

> "Details is Phase 2. Every enrichment section has its own add trigger, matching steps 2.1 through 2.3 exactly."

[ACTION: Pause on empty Details. Point at each dashed add button (Linked Content, Tags, Tag Collaborators) from top to bottom.]

> "Tapping any add button populates everything — this is a prototype abstraction, in production each would open its own picker."

[ACTION: Tap **"+ Add Linked Content"** → advances to **Details (Populated)** (`3189:15959`).]

> "Linked Content — step 2.1 of the enrichment — the user attaches a Workshop guide. This is a cross-service link into Yuhao's Workshop."

[ACTION: Point at the **Nezuko Bamboo Mouthpiece Guide** card.]

> "Collaborators — step 2.1 as well — photographer tagged."

[ACTION: Point at the **@photo_mei** chip.]

> "Tags the user wrote, plus AI-suggested tags from PropScan — that's step 2.2, cross-service integration with Yuhao's PropScan."

[ACTION: Point at the three Tag chips, then the three **AI Suggested Tags** chips below.]

> "Visibility, step 2.3. Public, friends only, or private."

[ACTION: Point at the three Visibility rows.]

> "And Next takes us to Preview — step 2.4, review and publish."

[ACTION: Tap **Next** → **Create Post - Preview** (`1738:2383`).]

> "The user reviews and taps Publish. Phase 3 — Engage — I'll cover those interactions inside Task 4.2 since comments, likes, and sharing use the same UI on any post."

[ACTION: Back out to **Feed** (`1738:2306`).]

---

## Task 4.2 — Browse and interact with friend & event content (3:30)

> "**Task 4.2 — browse and interact with friend and event content.** Phase 1, browse the feed."

### Phase 1 — Browse

> "The feed opens friend-first — @cosplay_luna's post surfaces above algorithmic content because Luna is followed. That's step 1.2."

[ACTION: Point at the first post card (@cosplay_luna) and scroll the Feed briefly.]

> "The user taps the post to view it in detail — step 2.1."

[ACTION: Tap the **@cosplay_luna post card**.]

### Phase 2 — View & Interact

> "Post Detail. Now the engagement options — step 2.3 says like, comment, or skip."

[ACTION: Pause on **Post Detail** (`1738:2511`).]

> "Like — swap between unliked and liked states without polluting the back stack."

[ACTION: Tap the **heart icon** → SWAPs to **Post Detail (Liked)** (`1877:5736`). Tap again → swaps back.]

> "Comment — tap the comment count, the icon, or the comment bar itself to open the comment overlay."

[ACTION: Tap **"Comments (8)"** → **Comment overlay** opens (dim + bottom sheet).]

> "Sample comments list. Tapping the input bar populates a draft comment — this is the second variant via swap."

[ACTION: Inside the overlay, tap the **"Write a comment..."** bar → SWAPs to populated overlay showing "Nice Nezuko cosplay! 🔥".]

> "Send posts and closes. Tapping the dim outside also closes."

[ACTION: Tap **Send** → overlay closes. Or alternatively tap the dim backdrop.]

> "Next, step 3.3 of Task 4.1 — share the post."

[ACTION: Tap the **↗ Share** text.]

> "Share overlay. In-app destinations — PropMes and Copy Link — plus external socials and a Cancel."

[ACTION: Pause on **Share overlay** (`3191:16154`). Point at each option.]

[ACTION: Tap **Cancel** → overlay closes.]

> "Step 2.3 also says view tagged collaborators, event tags, and linked workshops. The post shows all of that — and the tags are tappable."

[ACTION: Scroll on Post Detail to the tag row.]

> "Content tag — Demon Slayer — opens search results filtered by that keyword."

[ACTION: Tap the **"Demon Slayer"** tag chip → **Search Results** (`1801:4016`). Pause briefly.]

[ACTION: Back to Post Detail.]

> "Event tag — AFA 2026 — takes the user into the Event Collection, which is step 1.4 of this task: click on event collection."

[ACTION: Tap the **"AFA 2026"** tag chip → **Event Collection** (`1738:2467`).]

> "Event Collection groups every AFA 2026 post in one place. And notice this CoNews pill at the top — tapping it crosses over into XiaoAo's CoNews service, Event 1 Home Page."

[ACTION: Point at the **"CoNews ›"** coin at top right of the banner.]

[ACTION: Tap the **CoNews coin** → navigates to **Event 1 Home Page** (`1884:1161`) in CoNews.]

> "Cross-service link one. Back to Post Detail."

[ACTION: Navigate back to **Post Detail** (`1738:2511`).]

> "Linked workshop content — the user taps the guide card and lands in the How-To-Make screen of Yuhao's Workshop service."

[ACTION: Tap the **"Nezuko Bamboo Mouthpiece Guide"** card → navigates to **HTM** (`2123:10185`) in Workshop.]

> "Cross-service link two."

[ACTION: Navigate back to **Post Detail**.]

### Phase 3 — Curate

> "Phase 3 of this task — curate into a collection. Step 3.1, tap Add to Collection."

[ACTION: Tap the **🔖 Save to Collection** button → **Add to Collection overlay** (`1738:2562`) opens.]

> "Dimmed overlay lists the user's existing collections — step 3.2, select one. Or create a new one."

[ACTION: Point at the four existing collection rows, then at the **"+ Create New Collection"** button at the top.]

> "Let's take the new-collection path — this is the more interesting branch."

[ACTION: Tap **"+ Create New Collection"** → **Create New Collection overlay** (`2397:11368`) opens over the dim.]

> "Name it — AFA 2026 Favorites — and tap Create."

[ACTION: Tap **Create Collection**.]

> "Lands in the Empty Collection screen — step 3.3, post added to collection. The empty state converts the moment a post is saved."

[ACTION: Pause on **11b Empty Collection** (`2397:11556`). Point at the Luna card now showing "1 post".]

> "And from here the user can tap back into the post at any time."

---

## Task 4.3 — Discover and follow creators (6:30)

> "**Task 4.3 — discover and follow creators.** Phase 1, discover."

### Phase 1 — Discover

[ACTION: Navigate back to **Feed** (`1738:2306`).]

> "The user taps the search icon — step 1.2, tap search or browse feed."

[ACTION: Tap the **Search icon** in the bottom nav or header → **Search** screen (`1738:2413`).]

> "Enter a search query — series name, keyword, creator name. Step 1.3."

[ACTION: Tap into the **search bar** → navigates to **Search Results** (`1801:4016`).]

> "Step 1.4 — view results. Posts matching the query, creator profiles matching the query, and even events. Three categories."

[ACTION: Scroll through Search Results. Point at the **Posts** row, then **Creators**, then **Events**.]

> "Step 1.5 — tap a creator profile to evaluate."

[ACTION: Tap the **@cosplay_luna creator row** → **Creator Profile** (`1738:2700`).]

### Phase 2 — Evaluate

> "Phase 2 of this task is evaluate. Step 2.1 — view post history and portfolio. The grid below shows Luna's recent posts."

[ACTION: Pause on Creator Profile. Point at the post grid.]

> "Step 2.2 — cross-service reputation. Gear Lib reviews and Workshop contributions surface on this profile, so the user judges the creator holistically, not just by their CreatorHub posts."

[ACTION: Point at the reputation or contribution section on the profile.]

> "Step 2.3 — last active and posting activity. Step 2.4 — good fit decision. Let's say yes."

### Phase 3 — Follow & Connect

> "Step 3.1 — tap Follow."

[ACTION: Tap the **Follow** button → SWAPs to **Creator Profile (Following)** (`1877:5787`).]

> "The button now reads Followed. Step 3.2 — Luna's posts now enter the user's friend-first feed and the user gets notified of new posts. And if the user changes their mind, tapping Followed again undoes it."

[ACTION: Tap the **Followed** button → SWAPs back to unfollowed state (`1738:2700`). Then tap **Follow** once more to land in the followed state.]

> "Step 3.3 — optionally send a message. The profile has a Message button that opens PropMes. That's a third cross-service link — into Hongshan's messaging layer."

[ACTION: Point at the **Message** button on the profile (no tap needed if out of time, or tap to show PropMes briefly).]

---

## Close (8:00)

> "Three tasks, each mapped to the phases and steps in my workflow diagram. Every add, engage, discover, and curate interaction has a corresponding UI. The prototype also links to three teammate services — CoNews, Workshop, and PropMes — so the demo isn't just CreatorHub in isolation but CreatorHub inside the full PropBank experience. And the Settings and Profile utility supports any user across all five services. Happy to take questions."

[ACTION: Exit Presentation. Leave Figma on the Shared combined page, zoomed out to show the full task flow laid out.]

---

## Recovery playbook (speaker-only)

- **Prototype stalls:** press `R` to restart from the current flow start point; if worse, `Cmd/Ctrl+.` to exit and re-enter.
- **Wrong click:** use the on-screen back button instead of panicking — nearly every CreatorHub screen has working back nav.
- **Running long:** cut in this priority order — (1) Workshop HTM detour, (2) Followed-undo demo, (3) Search Results detour from Demon Slayer tag, (4) Account Settings tour.
- **Running short:** linger on Details (Populated) and narrate steps 2.1/2.2/2.3 slowly; add the Followed-undo demo; show Account Settings.
