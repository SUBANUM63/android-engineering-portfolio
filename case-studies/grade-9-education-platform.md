# Grade 9 Educational Android Platform

## Production Case Study

**Applications**

- Mathematics Grade 9 Textbook — **100K+ Google Play downloads**
- Geography Grade 9 Textbook — **100K+ Google Play downloads**
- **200K+ combined downloads**

**Platform:** Android  
**Primary language:** Kotlin  
**UI:** Jetpack Compose  
**Backend:** Firebase  
**Local persistence:** Room  
**Dependency injection:** Hilt  
**Cloud services:** Cloud Firestore, Cloud Functions, Remote Config  
**AI capability:** AI Tutor  
**Distribution:** Google Play  
**Monetization:** Google Mobile Ads

---

# 1. Overview

The Grade 9 educational platform represents a reusable Android architecture used to build and maintain subject-specific educational applications for Ethiopian students.

Two production applications currently demonstrate this architecture:

### Mathematics Grade 9 Textbook

A digital learning application centered around the Ethiopian Grade 9 Mathematics textbook.

The application has reached:

> **100K+ downloads on Google Play**

[View Mathematics Grade 9 on Google Play](https://play.google.com/store/apps/details?id=com.subanum63.mathematicsgr9)

### Geography Grade 9 Textbook

A corresponding educational application built around the Ethiopian Grade 9 Geography curriculum.

The application has also reached:

> **100K+ downloads on Google Play**

[View Geography Grade 9 on Google Play](https://play.google.com/store/apps/details?id=com.et.subanu.geographygrade9)

Together, these applications demonstrate the use of a common engineering approach across multiple production Android products with more than:

> **200K+ combined Google Play downloads**

---

# 2. Engineering Objective

The objective was not simply to display textbook content inside an Android application.

The platform needed to support a broader learning workflow around textbook content while remaining usable on mobile devices.

The architecture therefore evolved to support functionality such as:

- textbook and chapter navigation;
- PDF/document reading;
- reading progress;
- bookmarks and local state;
- educational summaries;
- quizzes;
- AI-assisted learning;
- persistent local data;
- cloud-backed educational resources;
- remotely configurable application behavior;
- production monetization;
- Google Play distribution and maintenance.

This transformed the applications from basic document readers into mobile educational platforms.

---

# 3. Shared Architecture

Mathematics Grade 9 and Geography Grade 9 contain different educational content but use a similar underlying engineering architecture.

A simplified representation is:

```text
┌────────────────────────────────────┐
│          Jetpack Compose UI        │
│                                    │
│ Home / Chapters / PDF / Quiz / AI │
└─────────────────┬──────────────────┘
                  │
                  ▼
┌────────────────────────────────────┐
│             ViewModels             │
│                                    │
│ UI State / Events / Coordination   │
└─────────────────┬──────────────────┘
                  │
                  ▼
┌────────────────────────────────────┐
│          Repository Layer          │
│                                    │
│ Data access / synchronization      │
└────────────┬─────────────┬─────────┘
             │             │
             ▼             ▼
┌──────────────────┐  ┌────────────────────────┐
│    Local Data    │  │      Firebase          │
│                  │  │                        │
│ • Room           │  │ • Cloud Firestore      │
│ • Reading state  │  │ • Cloud Functions      │
│ • Saved content  │  │ • Remote Config        │
└──────────────────┘  └────────────┬───────────┘
                                   │
                                   │ Server-side
                                   │ integration
                                   ▼
                         ┌──────────────────────┐
                         │     AI Provider      │
                         │                      │
                         │    LLM inference     │
                         └──────────────────────┘
```

The architecture separates presentation, application state, persistence, cloud services, and privileged server-side operations.

---

# 4. Android Client Architecture

## Jetpack Compose

The applications use **Jetpack Compose** for their Android user interfaces.

Compose is responsible for presenting application state and providing the interaction layer for functionality such as:

- textbook navigation;
- chapter selection;
- PDF reading;
- summaries;
- quizzes;
- AI Tutor interaction;
- application settings and related screens.

UI components are kept separate from data-access responsibilities.

---

## ViewModel Layer

ViewModels coordinate screen state and application events.

Rather than allowing composables to directly manage database or network operations, application state flows through ViewModels.

A simplified flow is:

```text
User interaction
       │
       ▼
Compose UI
       │
       ▼
ViewModel
       │
       ▼
Repository
       │
   ┌───┴────┐
   ▼        ▼
Room     Firebase
```

This provides clearer separation between UI rendering and application logic.

---

# 5. Local Persistence with Room

Educational applications need to continue functioning efficiently without repeatedly downloading the same information.

**Room** provides local persistence for application data that should remain available on the device.

Depending on the feature, this can include locally stored educational data and application state.

The general data flow is:

```text
Cloud / Generated Content
          │
          ▼
      Repository
          │
          ▼
        Room
          │
          ▼
   Persistent local data
          │
          ▼
       UI Layer
```

This reduces unnecessary remote requests and provides a better mobile experience.

---

# 6. Educational Content Workflow

The applications are structured around textbook chapters.

A typical user flow resembles:

```text
Application
     │
     ▼
Textbook Chapters
     │
     ▼
Selected Chapter
     │
     ├──────────────► Read textbook content
     │
     ├──────────────► Access summary
     │
     ├──────────────► Access quiz
     │
     └──────────────► AI-assisted learning
```

This keeps the textbook as the central learning resource while adding interactive functionality around it.

---

# 7. Cloud Firestore

**Cloud Firestore** is used as part of the cloud-backed educational architecture.

It allows educational resources and application data to be managed remotely rather than requiring all dynamic information to be permanently embedded inside the Android application.

The general architecture is:

```text
Android Application
        │
        ▼
Repository / Firebase Layer
        │
        ▼
Cloud Firestore
        │
        ├── Educational resources
        ├── Cloud-backed application data
        └── Content used by learning features
```

This separates application releases from some forms of educational content management.

---

# 8. Firebase Cloud Functions

Some operations should not be performed directly by an Android client.

The platform therefore uses **Firebase Cloud Functions** for server-side operations.

This is particularly important for AI functionality and other privileged backend operations.

Cloud Functions can provide a controlled layer for:

- request validation;
- authorization;
- server-side business logic;
- usage controls;
- access to protected credentials;
- communication with external services;
- controlled responses to the Android client.

A simplified request path is:

```text
Android Client
      │
      │ Validated request
      ▼
Firebase Cloud Function
      │
      ├── Validate request
      ├── Apply access rules
      ├── Apply server logic
      └── Protect API credentials
      │
      ▼
External Service
```

This prevents sensitive backend credentials from needing to reside directly inside the Android APK.

---

# 9. AI Tutor Architecture

The AI Tutor extends the textbook experience by allowing students to interact with an AI-assisted learning feature.

A major architectural requirement is that sensitive AI service credentials should not be embedded directly inside the Android application.

Instead, the architecture uses Firebase Cloud Functions as the server-side boundary.

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
               │ Authenticated /
               │ validated request
               ▼
┌──────────────────────────────────┐
│      Firebase Cloud Function     │
│                                  │
│ • Request validation             │
│ • Authorization                  │
│ • Usage/access control           │
│ • Server-side logic              │
│ • API credential protection      │
└─────────────┬────────────────────┘
              │
              │ Server-side request
              ▼
┌──────────────────────────────────┐
│           AI Provider            │
│                                  │
│          LLM inference           │
└─────────────┬────────────────────┘
              │
              │ AI response
              ▼
┌──────────────────────────────────┐
│      Firebase Cloud Function     │
│                                  │
│ Response processing              │
└─────────────┬────────────────────┘
              │
              ▼
┌──────────────────────────────────┐
│          Android Client          │
│                                  │
│       AI Tutor response          │
└──────────────────────────────────┘
```

This design creates a separation between the public mobile application and privileged AI-service access.

---

# 10. Summaries and Quizzes

The platform extends textbook reading with structured learning resources.

Chapter-related content can include:

```text
Chapter
   │
   ├── Textbook content
   │
   ├── Summary
   │
   └── Quiz
```

Where appropriate, remote educational content can be obtained through the backend and persisted locally.

A simplified flow is:

```text
Student selects learning resource
              │
              ▼
        Android Client
              │
              ▼
      Firebase / Backend
              │
              ▼
      Educational Content
              │
              ▼
          Repository
              │
              ▼
            Room
              │
              ▼
     Local reusable content
```

Once locally available, content does not necessarily need to be fetched again for every access.

---

# 11. Firebase Remote Config

**Firebase Remote Config** provides operational control over selected application behavior without requiring every configuration change to be shipped as a new Android release.

The pattern is:

```text
Firebase Remote Config
          │
          ▼
Remote configuration values
          │
          ▼
Android configuration layer
          │
          ▼
Application behavior
```

This provides an additional control layer for production applications.

---

# 12. Monetization Architecture

The applications are supported through **Google Mobile Ads**.

Advertising in a production educational application is not simply a matter of displaying ads wherever technically possible.

Placement has to account for:

- user experience;
- navigation transitions;
- accidental-click risk;
- Google advertising policies;
- consent state;
- frequency;
- lifecycle behavior;
- application stability.

The applications therefore treat monetization as part of the production architecture rather than as an isolated UI component.

---

# 13. Production Safety and Policy Engineering

Operating applications on Google Play introduces requirements that do not exist in a simple demonstration project.

Production engineering includes considerations such as:

- release signing;
- dependency management;
- consent handling;
- advertising policy compliance;
- safe ad placement;
- lifecycle-aware advertising behavior;
- Remote Config safeguards;
- backend credential protection;
- application updates;
- compatibility across Android devices;
- production monitoring and maintenance.

This operational work is an important part of maintaining software for a real user base.

---

# 14. Architecture Reuse

One of the most important engineering outcomes of the Grade 9 applications is architectural reuse.

Instead of treating every textbook application as a completely unrelated codebase, a common architectural approach can be applied across subjects.

Conceptually:

```text
                Shared Engineering Approach
                          │
              ┌───────────┴───────────┐
              │                       │
              ▼                       ▼
     Mathematics Grade 9      Geography Grade 9
              │                       │
              ▼                       ▼
     Mathematics content       Geography content
              │                       │
              └───────────┬───────────┘
                          │
                          ▼
                  Production users
```

The important engineering lesson is not merely that two applications were published.

It is that a production Android architecture was successfully applied to multiple products serving different educational content.

---

# 15. Scale

The strongest validation of this architecture is actual production usage.

| Application | Google Play Downloads |
|---|---:|
| Mathematics Grade 9 Textbook | 100K+ |
| Geography Grade 9 Textbook | 100K+ |
| **Combined** | **200K+** |

These are production applications rather than portfolio-only demonstrations.

Maintaining applications at this scale introduces practical concerns involving:

- backwards compatibility;
- device diversity;
- application updates;
- crash prevention;
- dependency upgrades;
- monetization;
- policy compliance;
- backend reliability;
- user-state preservation.

---

# 16. Technology Stack

| Layer | Technology |
|---|---|
| Language | Kotlin |
| UI | Jetpack Compose |
| Architecture | ViewModel + Repository |
| Dependency Injection | Hilt |
| Local Database | Room |
| Async Operations | Kotlin Coroutines |
| Backend Platform | Firebase |
| Cloud Database | Cloud Firestore |
| Server-side Logic | Firebase Cloud Functions |
| Runtime Configuration | Firebase Remote Config |
| AI | Server-mediated LLM integration |
| Monetization | Google Mobile Ads |
| Distribution | Google Play |
| Version Control | Git / GitHub |

---

# 17. Engineering Lessons

Building and maintaining these applications has involved substantially more than implementing Android screens.

The platform demonstrates practical experience with:

### Mobile architecture

Separating presentation, state, persistence, backend communication, and application logic.

### Reusable engineering

Applying a common architectural approach to multiple educational applications.

### Offline-oriented design

Persisting appropriate learning resources locally rather than assuming constant network availability.

### Backend security

Moving privileged operations and sensitive API credentials away from the Android client.

### AI integration

Integrating AI capabilities through a controlled server-side architecture.

### Production operations

Maintaining applications after release rather than treating deployment as the end of development.

### Real-world scale

Supporting applications with a combined audience exceeding **200K downloads**.

---

# 18. Relationship to the Grade 11 Platform

The Grade 9 applications solve the **single-textbook application problem**.

The Grade 11 application extends this engineering experience into a different architecture:

```text
Grade 9 Platform
      │
      │ Single textbook per application
      │
      ▼
Reusable educational architecture
      │
      ▼
Grade 11 Platform
      │
      │ Multiple textbooks
      │
      ├── Multi-book navigation
      ├── Independent progress
      ├── Larger content management
      └── Play Asset Delivery
```

The Grade 11 platform therefore represents an architectural evolution rather than simply another textbook application.

See:

`grade-11-multi-textbook-platform.md`

---

# 19. Source Code and Repository Scope

The production application source repositories are private.

This portfolio therefore focuses on:

- architecture;
- engineering decisions;
- production workflows;
- system design;
- technology choices;
- selected diagrams;
- screenshots;
- publicly verifiable Google Play applications.

Sensitive implementation details, API credentials, signing information, backend secrets, and proprietary production source code are intentionally excluded.

---

# 20. Result

The Grade 9 educational platform demonstrates the complete lifecycle of production Android engineering:

```text
Educational requirement
          │
          ▼
Android architecture
          │
          ▼
Compose application
          │
          ▼
Local persistence
          │
          ▼
Firebase backend
          │
          ▼
AI-assisted features
          │
          ▼
Google Play deployment
          │
          ▼
Production maintenance
          │
          ▼
200K+ combined downloads
```

The result is not a prototype architecture.

It is an engineering approach used by Android applications deployed to and maintained for a substantial real-world audience.

---

[← Back to Android Engineering Portfolio](../README.md)
