# Requirements Engineering (RE)

---

## 1. RE Overview

### 1.1 What is Requirements Engineering?

Requirements Engineering is a **wicked problem** — it is inherently complex, ill-defined, and difficult to solve in a straightforward manner.

### 1.2 Wicked Problems (Rittel & Webber, "Dilemmas in a General Theory of Planning")

A **wicked problem** has the following characteristics:

1. **No definitive formulation** — The problem cannot be definitively stated.
2. **No stopping rule** — There is no clear point at which the problem is "solved."
3. **Solutions are not true-or-false, but better or worse** — There is no objectively correct solution.
4. **No immediate and no ultimate test** — You cannot immediately or ultimately verify that a solution works.
5. **Every solution is a "one-shot operation"** — There is no opportunity to learn by trial and error; every attempt counts significantly.
6. **Every wicked problem is essentially unique** — No two wicked problems are alike.
7. **No enumerable set of potential solutions** — There is no well-described set of permissible operations that may be incorporated into the plan.
8. **Every wicked problem can be a symptom of another problem** — Problems are interconnected.
9. **Multiple explanations for discrepancies** — The choice of explanation determines the nature of the problem's resolution.
10. **The planner has no right to be wrong** — Planners are liable for the consequences of their actions.

### 1.3 Why RE?

#### Cost of Defects Curve
- The **cost of fixing defects increases exponentially** the later they are found in the software lifecycle.
- Stages (in order of increasing cost): **Concept → Design → Development → Testing → Commit → Integration → Production**
- Fixing a defect at the **Concept** stage is cheapest; at **Production** it is the most expensive.

#### Requirements as a Foundation for Key Activities

| Activity | Requirements as basis for ... |
|---|---|
| Customer relation and communication | Communication between stakeholders |
| Software design | Structure and behavior of a software system |
| Quality assurance and acceptance | As a basis for testing and acceptance of final product |
| Maintenance and evolution | Dictate the quality of the system realization, risks, and overhead in operations |

#### Statistics on Bad Requirements (Deloitte, 2022)
- **Origin of software defects** (Source: Crosstalk, the Journal of defence software engineering):
  - **64%** of defects originate in the **requirement analysis and design phase**
  - **36%** originate in **coding/implementation**
- **Relative costs to fix software defects** (Source: IBM Systems Sciences Institute):
  - Requirements phase: **1x**
  - Design phase: **6.5x**
  - Testing phase: **15x**
  - Production: **100x**

### 1.4 Stakeholders

- **Stakeholder**: A person or organization who **influences a system's requirements** or who **is impacted by that system**.

#### Identifying Stakeholders: Snowball Sampling
- A technique for identifying stakeholders by starting with known stakeholders and asking them to identify others, progressively expanding the set of known stakeholders (like a snowball growing as it rolls).

### 1.5 Key Activities in RE

The three key activities in Requirements Engineering form a pipeline:

1. **Elicitation and Analysis** — Discovering and understanding requirements from stakeholders
2. **Specification** — Documenting the requirements
3. **Validation** — Checking that the documented requirements are correct and complete

### 1.6 Process Models

- RE activities are organized using **process models** that define how elicitation, specification, and validation are coordinated and iterated.

### 1.7 Types of Requirements

#### Functional Requirements
- **Functional requirements** describe **what the system should do**.
- They specify the services, features, and functions the system must provide.

#### Non-Functional Requirements
- **Non-functional requirements** are requirements that are **not directly concerned with the specific services delivered by the system** to its users.
- They specify qualities/constraints such as performance, security, usability, reliability, etc.

#### Conflicting Non-Functional Requirements
- Non-functional requirements can **conflict with each other**.
- Example: **Security vs. Usability** — Strong security measures (e.g., complex passwords) can reduce usability, while easy-to-use systems may be less secure.

### 1.8 Case Study: Google Chrome

#### Key Non-Functional Requirements of Chrome:
- **Simple, fast, secure, and stable**

#### Examples of Conflicting NFRs and Trade-off Decisions:
- **Compression proxy**: Prioritized **security over performance** (removed the feature because it introduced security risks, even though it improved speed)
- **Removals of features threatening security**: Prioritized **stability over security** (carefully balanced removing insecure features against breaking existing functionality)
- **Site isolation**: Prioritized **security over performance** (each site runs in its own process for security, at the cost of higher memory usage)

---

## 2. Elicitation and Analysis

Elicitation is the first key activity in RE — **discovering requirements by interacting with stakeholders**.

### 2.1 Identifying Stakeholders

- Start by identifying **"baseline" stakeholders** (e.g., users, decision-makers)
- Then **explore their network** (snowball sampling) to find additional stakeholders

### 2.2 Mendelow's Matrix (Stakeholder Prioritization)

A 2x2 matrix for categorizing stakeholders based on their **Power** (y-axis) and **Interest** (x-axis):

|  | **Low Interest** | **High Interest** |
|---|---|---|
| **High Power** | **Keep Satisfied** | **Manage Closely** |
| **Low Power** | **Minimum Effort** | **Keep Informed** |

- **Manage Closely** (High Power, High Interest): Most important stakeholders — engage actively
- **Keep Satisfied** (High Power, Low Interest): Keep them happy but don't overwhelm with detail
- **Keep Informed** (Low Power, High Interest): Communicate regularly but less effort needed
- **Minimum Effort** (Low Power, Low Interest): Monitor only, lowest priority

### 2.3 Stakeholder Roles in the System

Stakeholders can be classified by their role:
- **Primary stakeholders** — e.g., users (directly interact with the system)
- **Secondary stakeholders** — e.g., supervisors (indirectly affected)
- **Tertiary stakeholders** — e.g., regulatory agencies (external constraints)

### 2.4 Requirements Elicitation Techniques

The main elicitation techniques are:
1. Interviews
2. Document Analysis
3. Questionnaires/Surveys
4. Workshops
5. Focus Groups
6. Prototyping
7. Observation/Ethnography
8. Personas

---

### 2.5 Interviewing

- Can be conducted with **individuals** or **small groups**

#### Types of Interviews:
- **Closed/Structured interview**: Stakeholder answers a **predefined set of questions** (uses interview guides)
- **Open/Unstructured interviews**: **No predefined agenda** — free-form conversation
- **Semi-structured interviews**: A **combination** of structured and unstructured — **most common case**

#### Suggestions for User Interviews:
- **Establish rapport** — build trust and comfort
- **Stay in scope** — keep the conversation focused
- **Prepare questions and (pen-and-paper) prototypes ahead of time**
- **Suggest ideas** — propose features/solutions to get reactions
- **Listen actively** — pay close attention to what stakeholders say

### 2.6 Questions and Bias

Interview questions can introduce bias. Examples of progressively better questions:

1. **Biased (leading)**: *"Would you like our new application in a browser?"*
   - Leads the user toward a "yes" answer
2. **Biased (negative framing)**: *"You wouldn't be willing to trade performance and a rich user experience just for having the software in a browser, would you?"*
   - Leads the user toward a "no" answer
3. **Biased (loaded with negatives)**: *"Would you like our new application in a browser rather than as a native Windows application even if it means reduced performance, a poorer overall user experience, and less interactivity?"*
   - Overloads with negative consequences to bias toward "no"
4. **Better (open-ended, neutral)**: *"What would you be willing to give up in order to have our next generation product run within a browser?"*
   - Neutral framing that lets the user express their own priorities

### 2.7 Document Analysis

- **Document analysis**: Examine any **existing documentation or resources** available
- Useful for understanding the current system, domain, and constraints before engaging stakeholders

### 2.8 Questionnaires/Surveys

- Structured forms for collecting information from many stakeholders at scale
- Can include both closed (multiple-choice, rating) and open-ended questions
- Tools: **Google Forms**, **Qualtrics** (NUS provides access)

### 2.9 Workshops

- **Workshops**: Facilitated sessions with **multiple stakeholders** from users to developers to testers
- Key roles in a workshop:
  - **Scribe** — takes notes/records outcomes
  - **Stakeholders** — provide requirements and feedback
  - **Facilitator** — guides the discussion and keeps it on track

#### Suggestions for Workshops:
- Establish and enforce **ground rules**
- **Fill all of the team roles** (scribe, facilitator, stakeholders)
- **Plan an agenda**
- **Stay in scope**
- Use **"parking lots"** to capture items for later consideration
- **Keep the team small** but include the right stakeholders
- **Keep everyone engaged**

#### Tools for Workshops:
- **Miro** — online collaborative whiteboard for planning and running workshops

### 2.10 Focus Groups

- **Focus Groups**: Facilitated sessions with a group of **users** specifically (not mixed stakeholders like workshops)
- Key difference from workshops: Focus groups target **users only**, workshops include **multiple stakeholder types**

### 2.11 Prototyping

- Creating visual representations of the system to gather feedback

#### Types of Prototypes:
- **Low-fidelity prototype**: Hand-drawn sketches, paper prototypes, wireframes
  - Quick and cheap to create
  - Good for early-stage requirements exploration
- **High-fidelity prototype**: Detailed, interactive mockups (e.g., using **Figma**)
  - Looks closer to the final product
  - Better for validating specific design decisions

### 2.12 Ethnography/Observation

- **Ethnography/Observation**: Observing users in their **natural work environment** to understand how they actually work
- Helps uncover requirements that users may not articulate in interviews (tacit knowledge)

### 2.13 Personas

- **Persona**: An **archetype of a user group** — a fictional character representing a typical user
- Include **hypothetical** names, goals, frustrations, photo, etc.
- Can be used in meetings to guide decisions: *"Would Dave use this feature?"*
- Can be **based on research** (data-driven) or **based on assumptions** ("*ad-hoc personas*")

#### Persona Example (Julia's Cafe):
- **Janelle Robinson**, Age 26, Undergraduate
- **Customer Profile**: Busy PhD student who needs a quiet place to study, major coffee lover
- **Motivations**: High quality strong coffee, quiet atmosphere, close to campus
- **Frustrations**: Hates waiting, doesn't want to be bothered by staff, relies on strong coffee
- **Goals**: Needs information on coffee types, wants to find menu quickly, wants to order from smartphone

### 2.14 Summary of Elicitation

Key topics covered:
- **Stakeholder analysis and categorization** (Mendelow's Matrix, roles)
- **Requirements elicitation techniques**:
  - Interviewing
  - Document Analysis
  - Questionnaires
  - Workshops
  - Focus Groups
  - Prototyping
  - Ethnography/observation
  - Personas

---

## 3. Specification

Specification is the second key activity in RE — **converting the requirements into a standard form**.

Two main approaches:
- **Agile** — User Stories
- **Plan-driven** — Use Cases and Software Requirements Specification (SRS)

### 3.1 Use Cases vs. User Stories

| | **Plan-driven** | **Agile** |
|---|---|---|
| **Starting point** | Use Case Name | User Story |
| **Process** | Conversations → Use Case Specification → Analysis → Functional Requirements + Tests | Conversations → Refined User Stories → Conversations → Acceptance Tests |

### 3.2 User Stories (Agile)

#### Format:
> **As a \<type of user\>, I want \<some goal\> so that \<some reason\>.**

#### Example (Job Search Site):
- *"As a company, I can post job openings to hire new employees"*
- *"As a job seeker, I can search for a job that I can subsequently apply for."*

#### The Three Cs of User Stories (Ron Jeffries):
1. **Card**: A **written description** of the story used for planning and as a reminder
2. **Conversation**: A **verbal exchange** with the customer to flesh out the details of the story
3. **Confirmation**: **Acceptance tests** specified by the customer can be used to determine when a story is complete

#### Mapping the Three Cs to RE Key Activities:
- **Conversation** → **Elicitation and analysis**
- **Card** → **Specification**
- **Confirmation** → **Validation**

#### Acceptance Tests
- Written on the **back** of the user story card
- Define specific conditions that must be met for the story to be considered complete
- Example (Job Search Site):
  - Front: *"Users can view information about each job that is matched by a search."*
  - Back (Acceptance Tests):
    - The system displays a job title, company name, and location for each result.
    - The system displays key job attributes such as salary (if available), job type (full-time/part-time/contract), and posting date.
    - The system displays a brief job summary/description snippet.
    - The system displays the job's main skills or requirements (if available).

#### Epics
- An **Epic** is a large user story that can be **broken down** into smaller user stories
- Example:
  - **Epic**: *"As a job seeker, I can search for jobs by attributes like location, salary range, job title, company name, and the date the job was posted."*
  - **Child stories**:
    - *"As a job seeker, I can view information about each job that is matched by a search."*
    - *"As a job seeker, I can view detailed information about a company that has posted a job."*

#### Notes on User Story Cards
- Cards can include **additional notes** from conversations
- Example: *"Users can view information about each job that is matched by a search. Dylan says show description, salary, and location."*

#### Non-Functional Requirements and User Stories
- NFRs can be written as **constraints** on user story cards
- Example: *"The system should be available 99.999% of the time."* (labeled as **Constraint**)

#### User Stories and Agile
- User stories were originally developed as part of **Extreme Programming**
- User stories are often combined with **Scrum**
- Can also be integrated into other agile processes

#### Why User Stories Work (Mike Cohn):
- **Stories are understandable** — developers and customers understand them
- **People are better able to remember events if they are organized into stories**
- **Support and encourage iterative development** — can easily start with epics and disaggregate closer to development time

### 3.3 Plan-driven Requirements Specification

#### Software Requirements Specification (SRS)
- A comprehensive document used in **plan-driven** approaches

#### SRS Template (Wiegers and Beatty):
1. **Introduction**
   - 1.1 Purpose
   - 1.2 Document conventions
   - 1.3 Project scope
   - 1.4 References
2. **Overall description**
   - 2.1 Product perspective
   - 2.2 User classes and characteristics
   - 2.3 Operating environment
   - 2.4 Design and implementation constraints
   - 2.5 Assumptions and dependencies
3. **System features**
   - 3.x System feature X
     - 3.x.1 Description
     - 3.x.2 Functional requirements
4. **Data requirements**
   - 4.1 Logical data model
   - 4.2 Data dictionary
   - 4.3 Reports
   - 4.4 Data acquisition, integrity, retention, and disposal
5. **External interface requirements**
   - 5.1 User interfaces
   - 5.2 Software interfaces
   - 5.3 Hardware interfaces
   - 5.4 Communications interfaces
6. **Quality attributes**
   - 6.1 Usability
   - 6.2 Performance
   - 6.3 Security
   - 6.4 Safety
   - 6.x [others]
7. **Internationalization and localization requirements**
8. **Other requirements**
- Appendix A: Glossary
- Appendix B: Analysis models

### 3.4 Use Cases (Plan-driven)

- **Use case**: Describes an interaction between an **actor** and the **system**
- Typically named as: **verb followed by an object** (e.g., "Check in for a flight")
- Example use cases for an airport kiosk:
  - Check in for a flight
  - Print boarding pass
  - Change seats
  - Purchase an upgrade

#### Use Case Diagrams in UML
- A use case diagram shows **actors** and **use cases** and their relationships
- **Actors** are represented as stick figures
- **Use cases** are represented as ovals
- Lines connect actors to the use cases they participate in
- A **system boundary** box encloses the use cases
- A use case can **include** another use case (for decomposition, shown with `<<include>>` dashed arrow)

#### Example: Medical System Use Case Diagram
- **Actors**: Medical receptionist, Manager, Nurse, Doctor
- **Use Cases**: Register patient, View personal info., Export statistics, Generate report, View record, Edit record, Setup consultation
- Different actors connect to different use cases

#### Simple Use Case Format (MHC-PMS Example)
A use case can also be written in a simpler tabular format:

| Field | Details |
|---|---|
| **MHC-PMS: Transfer data** | |
| **Actors** | Medical receptionist, patient records system (PRS) |
| **Description** | A receptionist may transfer data from the Mentcase system to a general patient record database that is maintained by a health authority. The information transferred may either be updated personal information (address, phone number, etc.) or a summary of the patient's diagnosis and treatment. |
| **Data** | Patient's personal information, treatment summary |
| **Stimulus** | User command issued by medical receptionist |
| **Response** | Confirmation that PRS has been updated |
| **Comments** | The receptionist must have appropriate security permissions to access the patient information and the PRS. |

#### Use Case Specification (Detailed Template)
A detailed use case specification includes:

| Field | Description |
|---|---|
| **ID and Name** | Unique identifier and descriptive name (e.g., UC-4 Request a Chemical) |
| **Created By / Date** | Author and creation date |
| **Primary Actor** | The main actor initiating the use case |
| **Secondary Actors** | Other actors involved |
| **Description** | Summary of what the use case does |
| **Trigger** | What initiates the use case |
| **Preconditions** | Conditions that must be true before the use case starts |
| **Postconditions** | Conditions that are true after the use case completes |
| **Normal Flow** | Step-by-step description of the main success scenario |
| **Alternative Flows** | Variations of the normal flow |
| **Exceptions** | Error/failure scenarios |
| **Priority** | Importance level |
| **Frequency of Use** | How often the use case occurs |
| **Business Rules** | Applicable business rules |
| **Other Information** | Additional constraints or notes |
| **Assumptions** | What is assumed to be true |

#### Use Case and Scenarios
- A single **Use Case** can have **multiple Scenarios** (Scenario 1, Scenario 2, ... Scenario n)
- Each scenario represents a specific path through the use case (normal flow, alternative flows, exception flows)

#### Scenarios as User Stories
- Use case scenarios can be converted to user stories
- Example (Epic → Child Stories):
  - **Epic**: *"As a chemist, I want to request a chemical so that I can perform experiments."*
  - **Story 1**: *"As a chemist, I want to request a chemical from the Chemical Stockroom so that I can use it immediately."*
  - **Story 2**: *"As a chemist, I want to request a chemical from a vendor because I don't trust the purity of any of the samples available in the Chemical Stockroom."*

### 3.5 Natural Language Guidelines

When writing requirements in natural language, follow these guidelines:

1. **Invent a standard format** and ensure that all requirement definitions adhere to that format
2. **Use text highlighting** (bold, *italic*, or color) to pick out key parts of the requirement
3. **Avoid the use of jargon**, abbreviations, and acronyms wherever possible
4. **Use language consistently** to distinguish between **mandatory** and **desirable** requirements: **"shall"** vs. **"should"**
5. **Associate a rationale** with each user requirement whenever possible — explain *why* the requirement exists

#### Natural Language Example (Insulin Pump System):
- *"3.2 The system **shall** measure the blood sugar and deliver insulin, if required, every 10 minutes."* (*Changes in blood sugar are relatively slow, so more frequent measurement is unnecessary; less frequent measurement could lead to unnecessarily high sugar levels.*)
- *"3.6 The system **shall** run a self-test routine every minute with the conditions to be tested and the associated actions defined in Table 1."* (*A self-test routine can discover hardware and software problems and alert the user to the fact the normal operation may be impossible.*)

#### Additional Information: Tables
- Tables can supplement natural language to specify precise conditions and actions
- Example (Insulin Pump — dose computation):

| Condition | Action |
|---|---|
| Sugar level falling (r2 < r1) | CompDose = 0 |
| Sugar level stable (r2 = r1) | CompDose = 0 |
| Sugar level increasing and rate of increase decreasing ((r2 − r1) < (r1 − r0)) | CompDose = 0 |
| Sugar level increasing and rate of increase stable or increasing (r2 > r1 & ((r2 − r1) >= (r1 − r0))) | CompDose = round((r2 − r1)/4); If rounded result = 0 then CompDose = MinimumDose |

### 3.6 Summary of Specification

Key topics covered:
- **Plan-driven**: Use cases (UML diagrams, detailed specifications, scenarios)
- **Agile**: User stories (Three Cs, epics, acceptance tests)
- **Use of natural language** (guidelines for clarity and consistency)

---

## 4. Validation

Validation is the third key activity in RE — **checking that the requirements define the system that the customer really wants**.

- Problems with requirements can lead to **misunderstood or ambiguous requirements** and **wrong intuition**
- Like specification, validation has both **Agile** and **Plan-driven** approaches

### 4.1 Peer Reviews

Peer reviews are a key validation technique, divided into two types:

#### Informal Reviews
- **Informal reviews**: Collecting **unstructured feedback**
- Three forms:
  - **Peer deskcheck**: Ask **one colleague** to look over your work product
  - **Passaround**: Invite **several colleagues** to examine a deliverable concurrently
  - **Walkthrough**: The **author describes** a deliverable and solicits comments on it

#### Formal Reviews / Inspections
- **Formal requirements reviews**: The requirements are analyzed **systematically by a team of reviewers** who check for errors and inconsistencies
- **Inspection**: Tedious and time-consuming, but one of the **highest-leverage software quality techniques** available

### 4.2 Inspections in Detail

#### What to Look for (Incomplete, Conflicting, and Ambiguous)
- **Incomplete** requirements — leaving much of the functionality unspecified
- **Conflicting** requirements — requirements that are specified conflict with each other, causing confusion and uncertainty
- **Ambiguous** requirements — expressed in an unclear manner, making them open to interpretation

#### Inspection Participants
- The **author** of the work product
- People who are the **sources of information**
- People who **will do work** (based on the requirements)
- People who are responsible for **interfacing systems**

#### Inspection Roles
- **Author** — created the work product being reviewed
- **Moderator** — facilitates the inspection process
- **Reader** — presents the material during the meeting
- **Recorder** — documents issues and decisions

#### Inspection Process
The formal inspection follows a structured process:

1. **Initial Work Product** — the starting artifact to be inspected
2. **Planning** — organize the inspection (schedule, assign roles, distribute materials)
3. **Preparation** — reviewers individually examine the work product before the meeting
4. **Inspection Meeting** — team meets to discuss and identify defects
5. **Rework** — author fixes the identified issues
6. **Follow-Up** — verify that rework was done correctly
7. **Baselined Work Product** — the final, approved artifact

- The process can loop back from **Follow-Up** to **Rework** if issues remain

### 4.3 Checks on the Requirements Document

During validation, the following checks should be performed:
- **Validity checks** — do the requirements reflect what the customer actually needs?
- **Consistency checks** — do any requirements conflict with each other?
- **Completeness checks** — are all required functions and constraints included?
- **Realism checks** — can the requirements be implemented with available budget and technology?
- **Verifiability** — can the requirements be tested?

#### Detailed Inspection Checklist (Wiegers and Beatty)

**Completeness:**
- Do the requirements address all known customer or system needs?
- Is any needed information missing? If so, is it identified as TBD?
- Are all external hardware, software, and communication interfaces defined?
- Is the expected behavior documented for all anticipated error conditions?
- Is the implementation priority of each requirement included?

**Correctness:**
- Do any requirements conflict with or duplicate other requirements?
- Is each requirement written in clear, concise, unambiguous, grammatically correct language?
- Is each requirement verifiable by testing, demonstration, review, or analysis?
- Are all requirements actually requirements, not solutions or constraints?
- Are the requirements technically feasible and implementable within known constraints?

**Quality Attributes:**
- Are all usability, performance, security, and safety objectives properly specified?
- Are other quality attributes documented and quantified, with acceptable trade-offs specified?
- Are the time-critical functions identified and timing criteria specified for them?
- Are all of the quality requirements measurable?

**Organization and Traceability:**
- Are the requirements organized in a logical and accessible way?
- Are all cross-references to other requirements and documents correct?
- Is each requirement uniquely and correctly labeled?
- Is each functional requirement traced back to its origin (e.g., system requirement, business rule)?

**Other Issues:**
- Are any use cases or process flows missing?
- Are any alternative flows, exceptions, or other information missing from use cases?
- Are all of the business rules identified?
- Are there any missing visual models that would provide clarity or completeness?

### 4.4 Prototyping

- **Prototyping** can be used as a validation technique — building a prototype to validate that the requirements are correct
- Paper prototypes (low-fidelity) and interactive mockups (high-fidelity) help stakeholders visualize the system and confirm requirements

### 4.5 Feasibility Study

A feasibility study assesses whether the system is worth building:
- Does the system contribute to the **overall objectives** of the organization?
- Can the system be implemented **within schedule and budget** using current technology?
- Can the system be **integrated with other systems** that are used?

### 4.6 Requirements Validation and Testing

- Validation is closely linked to **testing** — requirements should be written so they can be tested
- **Plan-driven**: Use case specifications lead to **tests** (derived from analysis)
- **Agile**: User stories lead to **acceptance tests** (derived from conversations)

#### V-Model and Testing
The V-Model shows the relationship between development phases and corresponding test levels:

| Development Phase (Left) | Test Level (Right) |
|---|---|
| User requirements; acceptance test planning | Acceptance testing |
| Functional requirements; system test planning | System testing |
| Architecture; integration test planning | Integration testing |
| Design; unit test planning | Unit testing |

- **Coding** is at the bottom of the V
- Each development phase on the left maps to a corresponding test phase on the right

### 4.7 Summary of Validation

Key topics covered:
- **Informal reviews** (deskcheck, passaround, walkthrough)
- **Inspections** (formal reviews with roles, process, and checklists)
- **Prototypes** (validating requirements through visual representations)
- **Feasibility studies** (assessing viability of the system)
- **Testable requirements** (linking validation to testing via V-Model)

---

## 5. RE Process Models

RE process models define how elicitation, specification, and validation are coordinated and iterated.

- **Agile**: Does **not** use a formal RE process model — RE activities are embedded within agile iterations
- **Plan-driven**: Uses a **process model to produce a Software Requirements Specification (SRS)**

### 5.1 Software Requirements Document

The Software Requirements Document (Sommerville) has a different structure from the SRS template (Wiegers and Beatty):

| Chapter | Description |
|---|---|
| **Preface** | Expected readership, version history, rationale for new version, summary of changes |
| **Introduction** | Describes the need for the system, its functions, how it works with other systems, and how it fits into business/strategic objectives |
| **Glossary** | Defines technical terms; should not assume reader expertise |
| **User requirements definition** | Services provided for the user; NFRs should also be described here; uses natural language, diagrams, or other notations understandable to customers; product and process standards specified |
| **System architecture** | High-level overview of anticipated system architecture; distribution of functions across modules; reusable components highlighted |
| **System requirements specification** | Functional and nonfunctional requirements in more detail; interfaces to other systems may be defined |
| **System models** | Graphical system models showing relationships between system components and environment (e.g., object models, data-flow models, semantic data models) |
| **System evolution** | Fundamental assumptions the system is based on; anticipated changes due to hardware evolution, changing user needs, etc. |
| **Appendices** | Detailed, specific information (hardware/database descriptions, hardware requirements, logical data organization) |
| **Index** | Alphabetic index, index of diagrams, index of functions, etc. |

### 5.2 User Requirements vs. System Requirements

Requirements can be written at two levels of abstraction, targeting **different audiences**:

#### User Requirements
- **High-level, abstract** statements of what the system should provide
- Written in **natural language** understandable to customers and managers
- Example: *"The user should be able to log in securely."*

#### System Requirements
- **Detailed, precise** descriptions of the system's functions, services, and constraints
- Written for developers and technical stakeholders
- Example: *"The system must authenticate users using OAuth 2.0 and support multi-factor authentication with a response time of < 2 seconds."*

#### Example: Mentcare System
- **User requirements definition**: *"The Mentcare system shall generate monthly management reports showing the cost of drugs prescribed by each clinic during that month."*
- **System requirements specification** (more detailed):
  - 1.1 On the last working day of each month, a summary of the drugs prescribed, their cost and the prescribing clinics shall be generated.
  - 1.2 The system shall generate the report for printing after 17.30 on the last working day of the month.
  - 1.3 A report shall be created for each clinic and shall list the individual drug names, the total number of prescriptions, the number of doses prescribed and the total cost of the prescribed drugs.
  - 1.4 If drugs are available in different dose units (e.g. 10mg, 20mg, etc) separate reports shall be created for each dose unit.
  - 1.5 Access to drug cost reports shall be restricted to authorized users as listed on a management access control list.

#### Requirements as an Ambiguous Term (Davis 1993)
- Requirements must be defined in a **sufficiently abstract way that a solution is not predefined** (so multiple contractors can bid)
- Once a contract is awarded, the contractor must write a **system definition for the client in more detail** so the client can validate what the software will do
- Both of these documents may be called "the requirements document"

### 5.3 Spiral Model for RE

The spiral model organizes RE activities in **concentric circles**, spiraling outward through increasing levels of detail. It has four quadrants:

1. **Requirements elicitation** (bottom-left) — discovering requirements
   - User requirements elicitation (inner spiral)
   - System requirements elicitation (outer spiral)
2. **Requirements specification** (top) — documenting requirements
   - Business requirements specification (inner)
   - User requirements specification (middle)
   - System requirements specification and modeling (outer)
3. **Requirements validation** (right) — checking requirements
   - Feasibility study (inner)
   - Prototyping (middle)
   - Reviews (outer)
4. **System requirements document** (bottom) — the final output

The process starts at the center and spirals outward:
- **Inner spiral**: Feasibility study, business requirements specification, start
- **Middle spiral**: User requirements elicitation, user requirements specification, prototyping
- **Outer spiral**: System requirements elicitation, system requirements specification and modeling, reviews
- **Output**: System requirements document

### 5.4 Process Model: Elicitation and Analysis

The elicitation and analysis sub-process is a **cycle** of four steps:

1. **Requirements discovery** — gathering requirements from stakeholders
2. **Requirements classification and organization** — grouping and structuring the discovered requirements
3. **Requirements prioritization and negotiation** — resolving conflicts and deciding what is most important
4. **Requirements specification** — documenting the agreed-upon requirements

These steps form a continuous loop, with each cycle refining the requirements further.

#### Viewpoints
- During elicitation, different stakeholder groups (e.g., students, professors, admins) bring **different viewpoints** to the requirements
- Requirements must be gathered and reconciled across these different perspectives

### 5.5 Requirements Change

Requirements are not static — they **evolve over time**:
- **Initial understanding of problem** leads to **initial requirements**
- Over time, understanding changes, leading to **changed understanding of problem**
- This results in **changed requirements**
- Requirements management planning is needed to handle this evolution

### 5.6 Summary of RE Process Models

Key topics covered:
- **Spiral Model** (organizing RE activities in concentric spirals through elicitation, specification, and validation)
- **User vs. system requirements** (different levels of abstraction for different audiences)
- **Viewpoints** (multiple stakeholder perspectives)
- **Requirements Management Planning** (handling requirements change over time)

---
