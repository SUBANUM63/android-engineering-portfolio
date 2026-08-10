# AI Tutor Backend Architecture

## Server-Mediated AI Integration for Production Android Learning Applications

This document describes the backend architecture used to support AI Tutor functionality in my educational Android applications.

The architecture is designed around one central rule:

> **The Android client should not directly expose private AI-provider credentials or own privileged backend logic.**

Instead, AI requests are mediated through Firebase backend services.

The overall system combines:

- Android client logic
- Firebase Cloud Functions
- Cloud Firestore
- server-side validation
- authorization
- usage controls
- protected AI-provider access
- controlled responses back to the Android application

---

# 1. Engineering Objective

The AI Tutor needs to provide a useful learning experience while maintaining clear boundaries between:

- user-facing Android code;
- privileged backend operations;
- cloud-backed application data;
- external AI-provider communication.

The architecture therefore separates:

```text
Android Experience
        │
        ▼
Trusted Backend Boundary
        │
        ▼
AI Provider
```

The Android application handles the learning interface.

The backend handles privileged operations.

---

# 2. Why Not Call the AI Provider Directly?

A simple implementation could look like:

```text
Android App
    │
    ▼
AI Provider
```

But this creates a major problem if the Android application contains a private API credential.

An APK or Android App Bundle is distributed to end-user devices.

Application packages should therefore be treated as inspectable rather than trusted secret storage.

A direct-client design can risk:

- API credential exposure;
- unauthorized API usage;
- uncontrolled provider costs;
- bypassing application-specific rules;
- weaker server-side request validation;
- reduced operational control.

For that reason, the production architecture introduces a backend boundary.

---

# 3. High-Level AI Architecture

The simplified architecture is:

```text
┌──────────────────────────────────┐
│          Android Client          │
│                                  │
│     Kotlin / Jetpack Compose     │
│     ViewModel / Repository       │
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
           │      │ Application data    │
           │      │ Learning content    │
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
               │ Generated response
               ▼
┌──────────────────────────────────┐
│     Firebase Cloud Functions     │
│                                  │
│ • Process provider response      │
│ • Apply application rules        │
│ • Return controlled response     │
└──────────────┬───────────────────┘
               │
               ▼
┌──────────────────────────────────┐
│          Android Client          │
│                                  │
│       AI Tutor experience        │
└──────────────────────────────────┘
```

---

# 4. Trust Boundary

The architecture distinguishes between an untrusted distributed client and trusted backend execution.

Conceptually:

```text
UNTRUSTED CLIENT
────────────────────────────

Android Application
        │
        ▼
Request

────────────────────────────
TRUST BOUNDARY
────────────────────────────

Firebase Cloud Functions
        │
        ├── Validate
        ├── Authorize
        ├── Apply usage controls
        ├── Access protected secrets
        └── Call external services

────────────────────────────
EXTERNAL SERVICE
────────────────────────────

AI Provider
```

The Android client can request an operation.

It should not automatically be trusted to perform privileged operations directly.

---

# 5. Android Client Responsibility

The Android application remains responsible for the user-facing learning experience.

Typical client responsibilities include:

- AI Tutor Compose UI;
- user input;
- loading states;
- conversation presentation;
- local UI state;
- calling the application repository;
- sending requests to the backend;
- presenting controlled backend responses;
- handling user-visible errors.

The Android client should not be responsible for:

- storing private provider credentials;
- directly enforcing authoritative usage limits;
- bypassable authorization;
- privileged server logic.

---

# 6. ViewModel and Repository Flow

A simplified client-side flow is:

```text
Student Question
       │
       ▼
AI Tutor Compose UI
       │
       ▼
ViewModel
       │
       ▼
Repository
       │
       ▼
Firebase Cloud Function
```

The UI does not communicate directly with the AI provider.

The repository or service layer coordinates backend communication.

---

# 7. Firebase Cloud Functions

Firebase Cloud Functions provide the server-side execution layer for AI Tutor requests.

Possible responsibilities include:

- validating incoming requests;
- verifying request context;
- authorization;
- applying usage controls;
- applying application-specific business rules;
- accessing protected configuration;
- building provider requests;
- communicating with the AI provider;
- processing provider responses;
- returning controlled results to the Android application.

Conceptually:

```text
Incoming Request
       │
       ▼
Cloud Function
       │
       ├── Validate structure
       ├── Validate application context
       ├── Check authorization
       ├── Check usage rules
       ├── Build AI request
       ├── Call AI provider
       ├── Process response
       └── Return result
```

---

# 8. Cloud Firestore Responsibility

Cloud Firestore serves a different purpose from Cloud Functions.

Cloud Functions provide trusted execution.

Firestore provides persistent cloud-backed structured data.

Depending on the feature, Firestore can support:

- educational resources;
- application data;
- learning content;
- state used by server-backed features;
- other structured cloud-managed information.

Conceptually:

```text
Cloud Functions
       │
       ├── Read
       └── Write
             │
             ▼
      Cloud Firestore
             │
             ├── Learning content
             ├── Application data
             └── Cloud-backed state
```

Firestore should not be described as though it replaces server-side validation or privileged execution.

---

# 9. Request Lifecycle

A typical AI Tutor request can be represented as:

```text
Student
   │
   ▼
Enter Question
   │
   ▼
Android ViewModel
   │
   ▼
Repository
   │
   ▼
Cloud Function
   │
   ├── Validate request
   ├── Validate context
   ├── Apply access rules
   ├── Apply usage controls
   └── Build provider request
   │
   ▼
AI Provider
   │
   ▼
Generated Response
   │
   ▼
Cloud Function
   │
   ├── Validate response
   ├── Apply application rules
   └── Return controlled result
   │
   ▼
Android Client
   │
   ▼
AI Tutor UI
```

This creates one controlled path for AI communication.

---

# 10. Request Validation

The backend should not blindly forward every request it receives.

Conceptually, validation can include checking:

- whether required fields are present;
- whether values are within expected limits;
- whether the request structure is valid;
- whether the application context is acceptable;
- whether the request is allowed for that user/session;
- whether the request should proceed.

A simplified flow is:

```text
Incoming Request
       │
       ▼
Validation
       │
  ┌────┴────┐
  │         │
Valid     Invalid
  │         │
  ▼         ▼
Continue   Reject
```

Validation belongs on the server because client-side validation alone can be modified or bypassed.

---

# 11. Authorization

Authorization answers a different question from validation.

Validation asks:

> Is this request structurally acceptable?

Authorization asks:

> Is this request allowed to perform this operation?

Conceptually:

```text
Validated Request
       │
       ▼
Authorization Check
       │
  ┌────┴────┐
  │         │
Allowed   Denied
  │         │
  ▼         ▼
Continue   Reject
```

Privileged application operations should not depend entirely on UI-level restrictions.

---

# 12. API Credential Protection

A central reason for the backend architecture is protecting AI-provider credentials.

The undesirable structure is:

```text
Android APK
    │
    └── AI API Key
```

The preferred structure is:

```text
Android APK
     │
     ▼
Cloud Function
     │
     ▼
Protected Server Configuration
     │
     ▼
AI Provider
```

The Android application requests the capability.

It does not receive the provider credential.

---

# 13. Server-Side Provider Request

After validation and authorization, the backend constructs the external AI request.

Conceptually:

```text
Validated Application Request
            │
            ▼
Server-Side Prompt /
Request Construction
            │
            ▼
Protected Provider Credential
            │
            ▼
External AI Request
            │
            ▼
AI Provider
```

This allows provider integration details to remain on the backend.

---

# 14. AI Provider

The AI provider is responsible for model inference.

Conceptually:

```text
Cloud Function
     │
     ▼
AI Provider
     │
     ├── Process request
     └── Generate response
     │
     ▼
Cloud Function
```

The provider is an external dependency.

The application therefore needs to account for provider-side failures and unexpected responses.

---

# 15. Response Processing

The raw AI-provider response does not necessarily need to be passed directly to the Android client without processing.

The backend can:

- validate the provider response;
- extract required content;
- normalize output;
- reject malformed results;
- apply application-specific rules;
- return a controlled response shape.

Conceptually:

```text
AI Provider Response
        │
        ▼
Cloud Function
        │
        ├── Validate
        ├── Normalize
        ├── Apply application logic
        └── Build client response
        │
        ▼
Android Client
```

This gives the Android application a more stable interface.

---

# 16. Usage Controls

AI requests can create variable backend cost.

For that reason, usage should not depend only on a client-side counter that can be modified.

Conceptually:

```text
AI Request
    │
    ▼
Server Usage Check
    │
 ┌──┴────┐
 │       │
Allowed  Limit reached
 │       │
 ▼       ▼
Call AI  Reject / controlled response
```

Usage controls can be part of a broader cost-management strategy.

---

# 17. Cost Control

AI-backed mobile applications require cost awareness.

Unlike purely local application features, each AI request can consume external resources.

Relevant engineering concerns include:

- number of requests;
- provider pricing;
- prompt size;
- response size;
- model selection;
- usage limits;
- abuse prevention;
- optional monetization strategy.

The architecture therefore needs to consider both technical correctness and operating cost.

---

# 18. Remote Configuration

Firebase Remote Config can be used for selected client-side operational parameters.

Conceptually:

```text
Remote Config
      │
      ▼
Android Application
      │
      ├── Feature parameter
      ├── Threshold
      └── Selected runtime behavior
```

However, Remote Config should not replace authoritative server-side controls for privileged AI access.

A client-readable configuration value can help shape UX.

The backend remains responsible for protected enforcement where required.

---

# 19. AI Tutor Learning Context

The AI Tutor exists inside an educational application rather than as a generic chatbot.

The application can therefore associate requests with learning context.

Conceptually:

```text
Student
   │
   ▼
Selected Textbook
   │
   ▼
Selected Chapter
   │
   ▼
AI Tutor
   │
   ▼
Contextual AI Request
```

The exact production context-building implementation is private, but the architectural goal is to keep AI functionality connected to the learning workflow.

---

# 20. Relationship with Textbook Features

AI Tutor is one component of a broader learning system.

```text
Textbook Chapter
       │
       ├── Read
       ├── Summary
       ├── Quiz
       └── AI Tutor
```

This means AI is an extension of the educational experience rather than the entire application.

---

# 21. Local Persistence and AI

Not every piece of AI-related UI state necessarily belongs in Firestore.

Local persistence and cloud persistence solve different problems.

Conceptually:

```text
Android State
     │
     ├── Local UI/session state
     ├── Reusable local content
     └── Device-side persistence

Cloud State
     │
     ├── Shared structured data
     ├── Server-managed state
     └── Backend-supported resources
```

The architecture should choose storage based on responsibility rather than forcing all data into one system.

---

# 22. Failure Modes

Several components in the AI path can fail:

```text
Android
   │
   ├── Connectivity failure
   │
Cloud Function
   │
   ├── Validation failure
   ├── Authorization failure
   ├── Internal backend failure
   │
Firestore
   │
   ├── Read/write failure
   │
AI Provider
   │
   ├── Timeout
   ├── Service failure
   ├── Invalid response
   └── Rate / usage failure
```

The Android application should avoid presenting all these cases as one generic unexplained error.

---

# 23. Error Flow

A simplified error architecture is:

```text
Operation
   │
   ▼
Backend / Provider
   │
 ┌─┴─────────────┐
 │               │
Success        Failure
 │               │
 ▼               ▼
Response     Controlled Error
 │               │
 └──────┬────────┘
        ▼
Android ViewModel
        │
        ▼
UI State
```

The backend should return controlled errors rather than exposing unnecessary infrastructure details.

---

# 24. Timeout and Retry Considerations

External AI requests can take longer than local application operations.

Potential concerns include:

- network timeout;
- provider latency;
- duplicate retries;
- user submitting the same request repeatedly;
- application lifecycle changes while waiting.

The UI should represent request progress clearly and avoid uncontrolled duplicate operations.

---

# 25. Abuse Resistance

Any public backend endpoint connected to a paid AI service requires some consideration of abuse.

Useful architectural controls can include:

- authorization;
- usage limits;
- input validation;
- size restrictions;
- server-side checks;
- monitoring;
- controlled feature access.

Client-side UI restrictions alone should not be treated as sufficient protection.

---

# 26. Logging and Monitoring

Backend AI operations benefit from operational visibility.

Useful signals can include:

- function failures;
- provider failures;
- validation failures;
- unusual usage patterns;
- latency;
- request volume;
- cost-related indicators.

Monitoring helps distinguish:

> "The AI Tutor is broken"

from:

> "The provider timed out"

or:

> "The request was rejected by server-side rules."

---

# 27. Backend Evolution

One benefit of the server-mediated design is that provider-side implementation can evolve without exposing all backend details to the Android client.

Conceptually:

```text
Android Client
      │
      ▼
Stable Backend Contract
      │
      ▼
Cloud Functions
      │
      ├── Provider A
      ├── Provider B
      └── Future backend logic
```

The Android application can depend on an application-specific contract rather than directly depending on every external provider detail.

---

# 28. Security Responsibility Map

| Responsibility | Location |
|---|---|
| User-facing AI UI | Android |
| UI state | ViewModel |
| Backend request coordination | Repository |
| Request validation | Cloud Functions |
| Authorization | Cloud Functions |
| Provider API credential | Backend only |
| AI provider communication | Cloud Functions |
| Cloud data | Firestore |
| Provider inference | AI provider |
| Controlled application response | Cloud Functions |
| Final presentation | Android |

This keeps security-sensitive responsibilities on the trusted side of the architecture.

---

# 29. Data Flow Summary

```text
Student Question
       │
       ▼
Android UI
       │
       ▼
ViewModel
       │
       ▼
Repository
       │
       ▼
Cloud Function
       │
       ├── Validate
       ├── Authorize
       ├── Check usage
       ├── Read Firestore if required
       └── Build provider request
       │
       ▼
AI Provider
       │
       ▼
Generated Response
       │
       ▼
Cloud Function
       │
       ├── Process
       └── Return controlled result
       │
       ▼
Repository
       │
       ▼
ViewModel
       │
       ▼
Compose UI
       │
       ▼
Student
```

---

# 30. Why Firestore and Functions Are Separate

It is useful to distinguish these technologies clearly.

## Cloud Firestore

Provides:

- persistent cloud data;
- structured educational data;
- application state where cloud persistence is appropriate.

## Cloud Functions

Provides:

- trusted code execution;
- validation;
- authorization;
- provider communication;
- protected configuration access;
- business rules.

Conceptually:

```text
              Firebase Backend
                     │
          ┌──────────┴──────────┐
          │                     │
          ▼                     ▼
     Firestore              Functions
          │                     │
Persistent Data       Trusted Execution
```

Neither should be described as though it performs the other's role.

---

# 31. Client vs Server Enforcement

Some UX behavior can be configured or checked on the Android client.

However, security-relevant or cost-sensitive enforcement should not rely only on client logic.

Conceptually:

```text
CLIENT
│
├── Display remaining usage
├── Disable unavailable UI
└── Guide user behavior

SERVER
│
├── Authoritative validation
├── Authorization
├── Usage enforcement
└── Provider access
```

The client improves the experience.

The server protects the resource.

---

# 32. Production Considerations

Operating an AI Tutor in production introduces concerns that do not exist in a simple local chatbot prototype.

These include:

- provider availability;
- provider cost;
- backend availability;
- credential protection;
- user access control;
- validation;
- monitoring;
- request limits;
- latency;
- production updates;
- Android lifecycle behavior.

The AI architecture therefore has to be considered part of the application's production system.

---

# 33. Architectural Principles

The backend follows several important principles.

## Never trust the distributed client with private provider credentials

The APK should not contain secrets that authorize paid external services.

## Validate on the server

Client-side validation improves UX but is not authoritative.

## Separate persistence from execution

Firestore stores structured data.

Cloud Functions execute privileged logic.

## Keep provider integration behind an application-specific backend contract

The Android app should not need intimate knowledge of external-provider implementation details.

## Make usage observable and controllable

AI usage has operating cost.

## Return controlled responses

The backend should normalize provider behavior before returning data to the app.

## Treat AI as one subsystem

The learning application should still function as a coherent educational product rather than becoming merely an AI chat interface.

---

# 34. Technology Stack

| Layer | Technology |
|---|---|
| Android language | Kotlin |
| Android UI | Jetpack Compose |
| Client state | ViewModel |
| Client data coordination | Repository Pattern |
| Backend platform | Firebase |
| Cloud database | Cloud Firestore |
| Backend execution | Firebase Cloud Functions |
| Runtime client configuration | Firebase Remote Config |
| AI integration | Server-mediated LLM API |
| Async Android operations | Kotlin Coroutines / Flow |
| Distribution | Google Play |

---

# 35. Relationship to Educational Architecture

This document focuses specifically on the AI/backend boundary.

For the broader Android architecture, see:

`android-education-architecture.md`

That document covers:

- Compose;
- ViewModels;
- repositories;
- Room;
- shared learning architecture;
- summaries;
- quizzes;
- progress;
- monetization;
- Grade 11 extension.

---

# 36. Relationship to Grade 9

The Grade 9 educational platform uses this backend architecture to support AI-assisted learning alongside textbook reading, summaries, and quizzes.

See:

`../case-studies/grade-9-education-platform.md`

---

# 37. Relationship to Grade 11

The Grade 11 platform uses the same AI/backend principles while adding:

- multiple textbooks;
- explicit textbook identity;
- independent progress;
- Play Asset Delivery.

See:

`../case-studies/grade-11-multi-textbook-platform.md`

The AI architecture therefore needs to operate correctly inside a richer multi-book context.

---

# 38. Source Code and Security

The production backend source remains private.

This document intentionally describes:

- architecture;
- trust boundaries;
- system responsibilities;
- data flow;
- server/client separation;
- high-level security decisions.

It intentionally excludes:

- provider API keys;
- production secrets;
- private Firebase configuration;
- protected function implementation;
- detailed authorization internals;
- production environment configuration.

---

# 39. Result

The AI Tutor backend architecture can be summarized as:

```text
Android Client
      │
      ▼
Cloud Functions
      │
      ├── Validation
      ├── Authorization
      ├── Usage Controls
      ├── Protected Credentials
      │
      ├──────────► Cloud Firestore
      │
      ▼
AI Provider
      │
      ▼
Cloud Functions
      │
      ▼
Controlled Response
      │
      ▼
Android AI Tutor
```

The key engineering result is that AI functionality is integrated into a production Android learning application **without requiring the mobile client to directly own privileged provider access**.

---

[← Back to Android Engineering Portfolio](../README.md)
