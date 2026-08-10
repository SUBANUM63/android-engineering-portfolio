# Android Education Architecture

## Shared Architecture for Production Educational Android Applications

This document describes the shared Android architecture used across my educational applications, including:

- Grade 9 Mathematics
- Grade 9 Geography
- Grade 11 Textbooks

The goal is not to document one specific application screen-by-screen.

Instead, this document explains the recurring architecture used to support:

- textbook reading;
- chapter navigation;
- summaries;
- quizzes;
- AI Tutor functionality;
- local persistence;
- Firebase-backed resources;
- production monetization;
- and, where applicable, multi-textbook content delivery.

---

# 1. Architecture Goals

The educational applications need to support more than static textbook content.

The architecture is designed around several goals:

- keep UI code separate from business and data logic;
- preserve learning state across application sessions;
- support reusable educational features across different subjects;
- allow learning resources to come from local or cloud sources;
- keep privileged backend operations outside the Android client;
- support AI-assisted learning without exposing provider credentials;
- allow selected application behavior to be remotely configurable;
- support production monetization without tightly coupling ads to learning screens;
- make the architecture reusable across multiple educational applications.

The resulting design separates the application into several responsibility layers.

---

# 2. High-Level Architecture

A simplified representation is:

```text
┌─────────────────────────────────────┐
│          Jetpack Compose UI         │
│                                     │
│ Home / Chapters / Reader / Quiz     │
│ Summary / AI Tutor / Progress       │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│              ViewModels             │
│                                     │
│ UI state / events / coordination    │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│          Repository Layer           │
│                                     │
│ Data access / orchestration         │
└───────────┬─────────────┬───────────┘
            │             │
            ▼             ▼
┌──────────────────┐   ┌─────────────────────┐
│   Local Layer    │   │     Cloud Layer     │
│                  │   │                     │
│ • Room           │   │ • Cloud Firestore   │
│ • DataStore      │   │ • Cloud Functions   │
│ • Local assets   │   │ • Remote Config     │
└──────────────────┘   └──────────┬──────────┘
                                  │
                                  ▼
                         ┌──────────────────┐
                         │   AI Provider    │
                         │                  │
                         │  LLM inference   │
                         └──────────────────┘
```

Each layer has a distinct responsibility.

---

# 3. Presentation Layer

The presentation layer is built with **Jetpack Compose**.

Its responsibilities include:

- rendering application state;
- presenting textbook and chapter navigation;
- showing reading content;
- displaying summaries;
- presenting quizzes;
- providing the AI Tutor interface;
- showing progress;
- presenting loading and error states;
- reacting to user input.

The Compose layer should not become responsible for:

- directly performing database operations;
- directly calling Firebase;
- containing tariff or learning business logic;
- embedding AI provider credentials;
- deciding how cloud data is persisted.

A simplified interaction flow is:

```text
User Action
    │
    ▼
Compose UI
    │
    ▼
ViewModel
    │
    ▼
Repository / Domain Logic
    │
    ▼
Updated State
    │
    ▼
Compose UI
```

This keeps rendering and application logic separated.

---

# 4. ViewModel Layer

ViewModels provide the state-management boundary between Compose screens and application/data layers.

Typical responsibilities include:

- loading application state;
- coordinating repository operations;
- processing user actions;
- exposing observable UI state;
- handling asynchronous results;
- mapping infrastructure results into UI-friendly state.

Conceptually:

```text
Compose Screen
      │
      ▼
   ViewModel
      │
      ├── Load textbook state
      ├── Request summary
      ├── Load quiz
      ├── Send AI Tutor request
      └── Update progress
      │
      ▼
 Repository Layer
```

The ViewModel coordinates operations but should not become a replacement for data repositories or backend services.

---

# 5. Repository Layer

The repository layer provides a stable interface between ViewModels and data sources.

A repository can coordinate:

- Room;
- Firebase;
- local assets;
- remote learning content;
- Cloud Function calls;
- progress state;
- cached resources.

Conceptually:

```text
ViewModel
    │
    ▼
Repository
    │
    ├────────► Room
    │
    ├────────► Cloud Firestore
    │
    ├────────► Cloud Functions
    │
    └────────► Other application data sources
```

This prevents the UI from needing to understand where each piece of data originates.

---

# 6. Local Persistence with Room

Room provides structured local persistence.

Educational applications benefit from local storage because previously obtained learning resources should not always require another cloud request.

Depending on the feature, Room can persist:

- summaries;
- quizzes;
- chapter-related state;
- reading progress;
- reusable educational resources;
- other structured application data.

A simplified flow is:

```text
Remote / Generated Content
          │
          ▼
       Repository
          │
          ▼
         Room
          │
          ▼
Persistent Local Content
          │
          ▼
       ViewModel
          │
          ▼
      Compose UI
```

This supports a more resilient learning experience.

---

# 7. Local and Cloud Responsibilities

Local and cloud storage solve different problems.

## Local layer

Room and other device-side storage are appropriate for:

- persistent user state;
- reusable content;
- progress;
- previously downloaded or generated resources;
- reducing repeated network operations.

## Cloud layer

Cloud services are appropriate for:

- remotely managed learning content;
- server-backed application state;
- privileged operations;
- protected external API communication;
- remotely configurable behavior.

The architecture therefore avoids treating either local storage or Firebase as the sole solution to every data problem.

---

# 8. Cloud Firestore

Cloud Firestore provides structured cloud-backed data.

Depending on the application, Firestore can support:

- learning content;
- quiz resources;
- summary resources;
- server-managed educational data;
- application state used by cloud-backed functionality.

Conceptually:

```text
Android Application
        │
        ▼
Repository
        │
        ▼
Cloud Firestore
        │
        ├── Educational content
        ├── Structured application data
        └── Cloud-backed state
```

Firestore provides persistent cloud storage but is not the same as trusted server-side execution.

---

# 9. Firebase Cloud Functions

Firebase Cloud Functions provide the trusted backend execution layer.

They are used where operations should not be performed directly by the Android client.

Possible responsibilities include:

- request validation;
- authorization;
- application-specific server logic;
- protected AI API communication;
- usage controls;
- response processing;
- protected access to backend credentials.

Conceptually:

```text
Android Client
      │
      ▼
Cloud Function
      │
      ├── Validate
      ├── Authorize
      ├── Apply server logic
      └── Access protected services
      │
      ▼
Controlled Response
```

This creates a boundary between distributed Android clients and privileged backend operations.

---

# 10. AI Tutor Architecture

AI Tutor functionality is implemented through a server-mediated architecture.

The Android client does not need to expose private AI provider credentials.

A simplified architecture is:

```text
┌──────────────────────────────────┐
│          Android Client          │
│                                  │
│      AI Tutor Compose UI         │
│              │                   │
│          ViewModel               │
│              │                   │
│          Repository              │
└──────────────┬───────────────────┘
               │
               │ Secure /
               │ validated request
               ▼
┌──────────────────────────────────┐
│     Firebase Cloud Functions     │
│                                  │
│ • Request validation             │
│ • Authorization                  │
│ • Usage / access controls        │
│ • Server-side business logic     │
│ • API credential protection      │
└──────────┬───────────────┬───────┘
           │               │
           │               │ Read / write
           │               ▼
           │      ┌─────────────────────┐
           │      │   Cloud Firestore   │
           │      │                     │
           │      │ Learning content    │
           │      │ Application data    │
           │      │ Cloud-backed state  │
           │      └─────────────────────┘
           │
           │ Server-side API request
           ▼
┌──────────────────────────────────┐
│          AI Provider             │
│                                  │
│        LLM / AI inference        │
└──────────────┬───────────────────┘
               │
               ▼
┌──────────────────────────────────┐
│     Firebase Cloud Functions     │
│                                  │
│ Response processing              │
└──────────────┬───────────────────┘
               │
               ▼
┌──────────────────────────────────┐
│          Android Client          │
│                                  │
│       AI Tutor experience        │
└──────────────────────────────────┘
```

The mobile client remains responsible for the learning experience.

The backend remains responsible for privileged AI communication.

---

# 11. Why the AI Provider Is Not Called Directly

A mobile APK is distributed to users and should be treated as an untrusted client.

Embedding a private AI-provider API key inside the APK would make that credential recoverable.

The server-mediated architecture therefore supports:

- API credential isolation;
- server-side validation;
- authorization;
- usage controls;
- centralized provider communication;
- backend-side business rules;
- response processing.

This is an architectural boundary, not merely an implementation detail.

---

# 12. Summary Workflow

Chapter summaries extend textbook reading with reusable learning content.

A simplified workflow is:

```text
Student selects Summary
          │
          ▼
     ViewModel
          │
          ▼
Check Local Content
          │
     ┌────┴────┐
     │         │
 Available   Missing
     │         │
     ▼         ▼
Display    Request Backend
               │
               ▼
      Obtain Summary Content
               │
               ▼
            Room
               │
               ▼
       Persist for reuse
               │
               ▼
            Display
```

The architectural goal is to avoid unnecessary repeated backend work once reusable content exists locally.

---

# 13. Quiz Workflow

The quiz architecture follows the same general principle.

```text
Student selects Quiz
         │
         ▼
    ViewModel
         │
         ▼
Check Local Quiz
         │
    ┌────┴─────┐
    │          │
 Available   Missing
    │          │
    ▼          ▼
Launch      Backend / Cloud
Quiz            │
                ▼
          Quiz Resource
                │
                ▼
              Room
                │
                ▼
          Persist Locally
                │
                ▼
             Launch
```

The learning experience can therefore combine cloud-backed content with local reuse.

---

# 14. Chapter-Centered Learning Model

The educational architecture is organized around textbook chapters.

Conceptually:

```text
Textbook
   │
   ▼
Chapter
   │
   ├── Reading content
   ├── Summary
   ├── Quiz
   └── AI Tutor context
```

This keeps learning features tied to the educational structure rather than existing as unrelated application screens.

---

# 15. Reading Progress

Reading progress is persistent application state.

For single-textbook applications, the reading identity is simpler.

For multi-textbook applications, progress must also include textbook identity.

Conceptually:

```text
Reading Progress
      │
      ├── Textbook ID
      ├── Chapter
      ├── Page / position
      └── Progress value
```

The important architectural principle is:

> **Progress must belong to the content being read.**

---

# 16. Grade 9 vs Grade 11 State Model

The shared architecture supports both single-textbook and multi-textbook applications.

## Grade 9

```text
Application
     │
     ▼
Primary Textbook
     │
     ├── Chapters
     ├── Progress
     └── Learning Features
```

## Grade 11

```text
Application
     │
     ▼
Textbook Catalog
     │
     ├── Textbook A
     │      ├── Chapters
     │      ├── Progress
     │      └── Learning Features
     │
     ├── Textbook B
     │      ├── Chapters
     │      ├── Progress
     │      └── Learning Features
     │
     └── Textbook N
```

The Grade 11 architecture extends the same educational concepts with explicit textbook identity.

---

# 17. Dependency Injection with Hilt

Hilt manages application dependencies.

Conceptually:

```text
Application
    │
    ▼
Hilt Modules
    │
    ├── Room database
    ├── DAO interfaces
    ├── Repositories
    ├── Firebase services
    ├── Cloud Function clients
    └── Application services
```

This prevents screens from manually constructing infrastructure dependencies.

It also improves separation of concerns.

---

# 18. Kotlin Coroutines and Flow

Educational applications perform several asynchronous operations:

- database queries;
- Firestore access;
- Cloud Function calls;
- AI requests;
- Remote Config retrieval;
- asset delivery in the Grade 11 platform.

These operations should not block the main UI thread.

Conceptually:

```text
User Action
     │
     ▼
ViewModel
     │
     ▼
Coroutine / Flow
     │
     ├── Room
     ├── Firebase
     ├── AI request
     └── Asset delivery
     │
     ▼
Updated State
     │
     ▼
Compose UI
```

State-driven UI allows Compose to render the result of asynchronous operations.

---

# 19. Firebase Remote Config

Remote Config provides operational control over selected application behavior.

Conceptually:

```text
Firebase Remote Config
          │
          ▼
Configuration Values
          │
          ▼
Application Config Layer
          │
          ▼
Selected Runtime Behavior
```

Remote Config is useful where production parameters may need to change without publishing a new application release.

It should not replace application architecture or server-side authorization.

---

# 20. Production Monetization Architecture

The educational applications use Google Mobile Ads.

Advertising is treated as a separate concern rather than being mixed directly into learning business logic.

Conceptually:

```text
Learning Experience
        │
        ▼
Application Navigation
        │
        ▼
Ad Placement Policy
        │
        ├── Is this placement allowed?
        ├── Is consent satisfied?
        ├── Is frequency acceptable?
        ├── Is lifecycle state safe?
        └── Is user intent respected?
        │
        ▼
Google Mobile Ads
```

The important principle is:

> **An advertisement being technically available does not mean it should be shown at every possible transition.**

---

# 21. Rewarded Learning Flows

Rewarded advertising can be used where a user explicitly chooses to exchange an ad view for access to an optional learning feature.

A conceptual flow is:

```text
Student chooses optional feature
            │
            ▼
Explain unlock option
            │
            ▼
Student explicitly accepts
            │
            ▼
Rewarded Ad
            │
        ┌───┴────┐
        │        │
    Rewarded   Not rewarded
        │        │
        ▼        ▼
Continue      No unlock
feature
```

The explicit user choice is important.

Rewarded ads should not behave like unexpected interstitial advertising.

---

# 22. Consent-Aware Advertising

Production advertising needs to account for privacy and consent requirements.

Conceptually:

```text
Application Start
       │
       ▼
Consent State
       │
       ├── Not ready ─────► Do not initialize/show ads
       │
       └── Ready
              │
              ▼
        Ad Initialization
              │
              ▼
         Allowed Ads
```

Advertising behavior should respect the application's current consent state.

---

# 23. Lifecycle-Aware Behavior

Android screens and processes do not have permanent lifetimes.

Applications can experience:

- activity recreation;
- configuration changes;
- navigation transitions;
- process interruption;
- foreground/background changes.

Important state should therefore exist outside temporary composable instances.

Conceptually:

```text
Compose Screen
      │
      ▼
Observe State
      │
      ▼
ViewModel / Persistent Layer
      │
      ▼
Current Application State
```

This is particularly important for:

- learning progress;
- active requests;
- downloaded resources;
- ad state;
- asset delivery;
- AI operations.

---

# 24. Error-State Architecture

Each infrastructure component can fail independently.

Examples include:

- Room operation failure;
- Firestore failure;
- Cloud Function failure;
- AI provider failure;
- Remote Config failure;
- network interruption;
- Play Asset Delivery failure.

The application should avoid representing all failures as one undifferentiated error.

Conceptually:

```text
Operation
   │
   ├── Loading
   ├── Success
   └── Failure
          │
          ├── Recoverable
          └── Non-recoverable
```

The UI should respond appropriately to the actual application state.

---

# 25. Reusable Architecture Across Subjects

The educational architecture is designed to support reuse across subject-specific applications.

Conceptually:

```text
Shared Engineering Architecture
            │
    ┌───────┼──────────┐
    │       │          │
    ▼       ▼          ▼
Maths    Geography   Other Subject
    │       │          │
    ▼       ▼          ▼
Different educational content
            │
            ▼
Similar application architecture
```

This allows different educational products to benefit from common architectural principles.

---

# 26. Grade 11 Architectural Extension

Grade 11 introduces a further layer:

```text
Shared Educational Architecture
            │
            ▼
Multi-Textbook Layer
            │
            ├── Catalog
            ├── Textbook identity
            ├── Independent progress
            └── Asset-pack mapping
            │
            ▼
Google Play Asset Delivery
```

The shared educational architecture remains useful while the content-management layer becomes more complex.

---

# 27. Data Responsibility Map

| Data / Operation | Primary Responsibility |
|---|---|
| UI state | ViewModel |
| Textbook reading state | Local persistence |
| Learning content | Local and/or cloud layer |
| Summaries | Backend + local reuse |
| Quizzes | Backend + local reuse |
| AI request execution | Cloud Functions |
| AI provider credentials | Backend only |
| Cloud application data | Cloud Firestore |
| Runtime feature configuration | Remote Config |
| Multi-textbook progress | Local structured persistence |
| Large Grade 11 textbook assets | Play Asset Delivery |

This separation prevents one subsystem from becoming responsible for unrelated concerns.

---

# 28. Security Boundary

The Android application should be treated as an untrusted distributed client.

That means private credentials and privileged backend logic should not rely on secrecy inside the APK.

The trust boundary is conceptually:

```text
UNTRUSTED CLIENT
─────────────────────────
Android Application
        │
        ▼
Validated Request
─────────────────────────
TRUSTED BACKEND
Cloud Functions
        │
        ├── Authorization
        ├── Validation
        ├── Protected credentials
        └── Server-side logic
```

This distinction is especially important for AI integration.

---

# 29. Production Engineering

The shared architecture has to operate in production rather than only inside Android Studio.

Production responsibilities include:

- application signing;
- release configuration;
- dependency upgrades;
- Google Play deployment;
- Firebase production configuration;
- advertising compliance;
- user consent;
- Android device compatibility;
- application lifecycle handling;
- backend maintenance;
- Remote Config safeguards;
- release testing.

These responsibilities are part of the architecture because they affect how software behaves after deployment.

---

# 30. Technology Stack

| Area | Technology |
|---|---|
| Language | Kotlin |
| UI | Jetpack Compose |
| UI State | ViewModel |
| Architecture | Repository Pattern |
| Dependency Injection | Hilt |
| Local Database | Room |
| Local Preferences | DataStore where appropriate |
| Async Programming | Coroutines / Flow |
| Cloud Database | Cloud Firestore |
| Trusted Backend | Firebase Cloud Functions |
| Runtime Configuration | Firebase Remote Config |
| AI | Server-mediated LLM integration |
| Large Content Delivery | Google Play Asset Delivery for Grade 11 |
| Monetization | Google Mobile Ads |
| Distribution | Google Play |
| Version Control | Git / GitHub |

---

# 31. Architecture Principles

The shared educational architecture follows several recurring principles.

## UI should render state

Compose screens should not become repositories, databases, or backend clients.

## State needs ownership

Reading progress, learning content, and multi-book state should belong to explicit application entities.

## Local and cloud data are complementary

Room and Firestore solve different problems.

## Protected operations belong on the backend

Cloud Functions provide trusted server-side execution.

## AI credentials should never be shipped as client secrets

The Android APK should not contain private provider credentials.

## Reusable content should be persisted

Repeated cloud work should be avoided when learning resources are already available locally.

## Production monetization needs policy

Advertising should respect user intent, consent, lifecycle state, and platform requirements.

## Architecture should evolve with product complexity

The Grade 11 system extends the Grade 9 architecture instead of forcing a single-textbook model onto a multi-textbook product.

---

# 32. Relationship to Case Studies

This architecture document provides the shared technical foundation for the individual case studies.

## Grade 9 Educational Platform

See:

`../case-studies/grade-9-education-platform.md`

Focus:

- reusable single-textbook architecture;
- Mathematics and Geography applications;
- 200K+ combined downloads;
- summaries;
- quizzes;
- AI Tutor;
- Firebase backend.

---

## Grade 11 Multi-Textbook Platform

See:

`../case-studies/grade-11-multi-textbook-platform.md`

Focus:

- all shared educational functionality;
- multiple textbooks;
- independent progress;
- Google Play Asset Delivery;
- on-demand large-content delivery.

---

# 33. Result

The shared architecture can be summarized as:

```text
Student
   │
   ▼
Jetpack Compose
   │
   ▼
ViewModel
   │
   ▼
Repository
   │
   ├────────────► Room / Local State
   │
   └────────────► Firebase
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
     Firestore   Functions  Remote Config
                    │
                    ▼
               AI Provider

Grade 11 additionally:

Repository
   │
   ▼
Play Asset Delivery
   │
   ▼
On-Demand Textbook Assets
```

The architecture supports a progression from:

> **single-textbook application → reusable educational platform → multi-textbook learning system**

while preserving separation between UI, persistence, backend services, AI integration, and production infrastructure.

---

[← Back to Android Engineering Portfolio](../README.md)
