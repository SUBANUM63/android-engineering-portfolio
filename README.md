# Android Engineering Portfolio

Production Android applications, scalable mobile architecture, Firebase backend systems, dynamic content delivery, and AI-powered learning experiences built for real-world users.

I am an **Electrical Engineer and Software Engineer** specializing in Android application development with **Kotlin, Jetpack Compose, Firebase, Room, Hilt, Coroutines, and modern Android architecture**.

My work includes educational applications used by students in Ethiopia, multi-textbook learning platforms, Firebase-backed services, AI-powered learning features, utility applications, and applications published and maintained on Google Play.

---

# 📱 Production Android Applications

## 📚 Grade 9 Educational Apps — 200K+ Combined Downloads

I designed, developed, published, and maintain two production educational Android applications for Ethiopian Grade 9 students:

### 🧮 Mathematics Grade 9 Textbook — 100K+ Downloads

A production educational Android application providing Grade 9 students with digital access to their mathematics textbook together with learning-oriented functionality designed for mobile devices.

**100K+ downloads on Google Play**

### 🌍 Geography Grade 9 Textbook — 100K+ Downloads

A production educational Android application built around Ethiopia's Grade 9 geography curriculum.

**100K+ downloads on Google Play**

Rather than being unrelated applications, Mathematics Grade 9 and Geography Grade 9 use a similar underlying Android architecture. This allowed the engineering approach to be reused across multiple educational products while adapting the content and learning experience to individual subjects.

### Engineering experience demonstrated

- Kotlin Android development
- Jetpack Compose UI
- Modern Android architecture
- ViewModel-based state management
- Repository pattern
- Room local database
- Hilt dependency injection
- Kotlin Coroutines
- PDF/document reading experience
- Local application state and persistence
- Firebase integration
- Cloud Firestore
- Firebase Cloud Functions
- Firebase Remote Config
- AI-assisted learning
- Production release management
- Google Play deployment and maintenance
- Google Mobile Ads integration
- Supporting applications with large real-world user bases

### Production reach

Together, the two Grade 9 educational applications have achieved:

> **200K+ combined downloads on Google Play**

This represents practical experience designing, releasing, maintaining, and evolving Android software used by a significant real-world audience.

### Google Play

**Mathematics Grade 9 Textbook**

[View Mathematics Grade 9 on Google Play](https://play.google.com/store/apps/details?id=com.subanum63.mathematicsgr9)

**Geography Grade 9 Textbook**

[View Geography Grade 9 on Google Play](https://play.google.com/store/apps/details?id=com.et.subanu.geographygrade9)

> A detailed engineering case study is maintained under `case-studies/grade-9-education-platform.md`.

---

# 📚 Grade 11 Textbooks — Multi-Textbook Learning Platform

Unlike the Grade 9 applications, which are centered around a single textbook, the **Grade 11 Textbooks** application is designed as a multi-textbook educational platform.

The application allows multiple Grade 11 textbooks to be distributed and accessed through a single Android application.

This introduces additional engineering challenges involving content organization, large educational assets, textbook-specific state management, progress tracking, and efficient application delivery.

## Multi-textbook architecture

The application organizes educational content using a hierarchy similar to:

```text
Grade 11 Application
        │
        ├── Subject / Textbook A
        │       ├── Chapter 1
        │       ├── Chapter 2
        │       └── ...
        │
        ├── Subject / Textbook B
        │       ├── Chapter 1
        │       ├── Chapter 2
        │       └── ...
        │
        └── Subject / Textbook N
                ├── Chapter 1
                ├── Chapter 2
                └── ...
```

The application therefore has to manage content and user state independently across multiple textbooks.

### Engineering areas include

- Kotlin
- Jetpack Compose
- Multi-textbook content architecture
- Textbook and chapter navigation
- Per-textbook state management
- Reading progress tracking
- Persistent progress storage
- Large educational asset management
- Google Play Asset Delivery
- On-demand asset delivery
- Dynamic asset availability handling
- Download-state handling
- Production Android architecture
- Google Play deployment

---

# 📦 Play Asset Delivery

Bundling several complete textbooks directly inside the base Android application can substantially increase the initial application download size.

The Grade 11 application addresses this problem using **Google Play Asset Delivery**.

Instead of requiring every large textbook asset to be included in the initial application installation, content can be delivered when required.

A simplified content-delivery flow is:

```text
┌─────────────────────────────┐
│       Android Client        │
│                             │
│   Grade 11 Textbook App     │
└──────────────┬──────────────┘
               │
               │ User selects textbook
               ▼
┌─────────────────────────────┐
│ Check textbook asset state  │
└──────────────┬──────────────┘
               │
       ┌───────┴────────┐
       │                │
       ▼                ▼
   Available        Not Available
       │                │
       │                ▼
       │      ┌──────────────────────┐
       │      │ Google Play Asset    │
       │      │ Delivery             │
       │      │                      │
       │      │ On-demand asset pack │
       │      └──────────┬───────────┘
       │                 │
       │                 ▼
       │          Track delivery
       │                 │
       │          Ready / Failure
       │                 │
       └────────┬────────┘
                ▼
┌─────────────────────────────┐
│ Resolve textbook resources  │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│      Open textbook          │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│ Track textbook progress     │
│                             │
│ • Current chapter           │
│ • Reading position          │
│ • Progress state            │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│ Persist reading state       │
└─────────────────────────────┘
```

This architecture separates application functionality from large educational content and allows the application to scale to multiple textbooks without requiring every textbook asset to be delivered as part of the initial installation.

> A detailed engineering case study will be maintained under `case-studies/grade-11-multi-textbook-platform.md`.

---

# ⚡ Electric Bill Calculator Ethiopia

A production Android utility application designed to help Ethiopian electricity customers calculate and understand electricity costs.

Unlike the educational applications, this project addresses a different problem domain and combines my background in **Electrical Engineering** with Android software engineering.

The application translates electricity-domain calculations and tariff structures into functionality usable by electricity customers.

## Engineering areas include

- Kotlin
- Jetpack Compose
- Electricity tariff calculation logic
- Domain-specific business rules
- Local data persistence
- Multiple meter profiles
- Camera-assisted meter functionality
- Firebase services
- Production Android architecture
- Google Play deployment
- Google Mobile Ads monetization

This project demonstrates the implementation of domain-specific engineering knowledge as consumer software rather than simply presenting information stored in educational content.

### Google Play

[View Electric Bill Calculator Ethiopia on Google Play](https://play.google.com/store/apps/details?id=com.et.subanum63.electricbillcalculator)

> A detailed engineering case study will be maintained under `case-studies/electric-bill-calculator.md`.

---

# 🤖 AI Tutor & Cloud Architecture

Some of my educational Android applications integrate an **AI Tutor architecture**.

The Android client does not need to communicate directly with an external AI provider using a sensitive API credential.

Instead, AI requests are handled through a server-side architecture built around Firebase services.

A simplified architecture is:

```text
┌─────────────────────────────────┐
│          Android Client         │
│                                 │
│     Kotlin / Jetpack Compose    │
│     ViewModel / Repository      │
└────────────────┬────────────────┘
                 │
                 │ Authenticated /
                 │ validated request
                 ▼
┌─────────────────────────────────┐
│     Firebase Cloud Functions    │
│                                 │
│ • Request validation            │
│ • Authorization                 │
│ • Server-side business logic    │
│ • Usage / access controls       │
│ • API secret protection         │
└──────────┬──────────────┬───────┘
           │              │
           │              │ Read / write
           │              ▼
           │     ┌───────────────────────┐
           │     │    Cloud Firestore    │
           │     │                       │
           │     │ • Application data    │
           │     │ • Learning content    │
           │     │ • Cloud-backed state  │
           │     └───────────────────────┘
           │
           │ Server-side API request
           ▼
┌─────────────────────────────────┐
│          AI Provider            │
│                                 │
│       LLM / AI inference        │
└────────────────┬────────────────┘
                 │
                 │ Generated response
                 ▼
┌─────────────────────────────────┐
│     Firebase Cloud Functions    │
│                                 │
│ • Validate provider response    │
│ • Apply application logic       │
│ • Return controlled response    │
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│          Android Client         │
│                                 │
│       AI Tutor experience       │
└─────────────────────────────────┘
```

## Why use a server-side architecture?

Embedding a private AI provider API key directly inside an Android APK would expose that credential to extraction.

The Firebase backend therefore provides a security boundary between the Android application and the external AI service.

This architecture allows server-side control over:

- Authentication
- Authorization
- Request validation
- API credential protection
- Usage controls
- Application-specific business logic
- AI request handling
- Response validation
- Cloud-backed educational data

Cloud Firestore provides persistent cloud storage for application and educational data where required, while Firebase Cloud Functions handle privileged server-side operations.

---

# 🏗 Android Architecture

The applications in this portfolio generally follow layered Android architecture rather than placing business logic directly inside UI components.

A simplified structure is:

```text
┌───────────────────────────────────┐
│         Jetpack Compose UI        │
│                                   │
│ Screens / Components / Navigation │
└──────────────────┬────────────────┘
                   │
                   ▼
┌───────────────────────────────────┐
│             ViewModel             │
│                                   │
│ UI state / events / coordination  │
└──────────────────┬────────────────┘
                   │
                   ▼
┌───────────────────────────────────┐
│            Repository             │
│                                   │
│ Data abstraction / orchestration  │
└───────────┬───────────┬───────────┘
            │           │
            ▼           ▼
┌─────────────────┐  ┌────────────────────┐
│   Local Data    │  │    Cloud Data      │
│                 │  │                    │
│ Room / Storage  │  │ Firebase services  │
└─────────────────┘  └─────────┬──────────┘
                               │
                               ▼
                     ┌────────────────────┐
                     │ Cloud Firestore    │
                     │ Cloud Functions    │
                     │ Remote Config      │
                     └────────────────────┘
```

This structure helps separate:

- UI rendering
- UI state
- application business logic
- local persistence
- remote data access
- privileged backend operations

---

# 🔥 Firebase Backend Engineering

Firebase is used as more than a simple database in several of these applications.

Depending on the application, the backend architecture can include:

### Cloud Firestore

Used for cloud-backed application and educational data.

### Firebase Cloud Functions

Used for server-side operations that should not be trusted to the Android client.

Examples include:

- AI API communication
- Request validation
- Authorization
- Server-side business logic
- Protected operations
- API credential isolation
- Controlled access to backend resources

### Firebase Remote Config

Used where application behavior needs to be controlled remotely without requiring a new Android release.

This can support controlled rollout of application behavior and operational configuration.

---

# 🧰 Core Technology Stack

| Area | Technologies |
|---|---|
| Android | Kotlin |
| UI | Jetpack Compose, Material Design |
| Architecture | ViewModel, Repository Pattern |
| Dependency Injection | Hilt |
| Persistence | Room |
| Asynchronous Programming | Kotlin Coroutines |
| Backend | Firebase |
| Cloud Database | Cloud Firestore |
| Server-side Logic | Firebase Cloud Functions |
| Configuration | Firebase Remote Config |
| AI Integration | Server-mediated LLM integration |
| Large Content Delivery | Google Play Asset Delivery |
| Distribution | Google Play |
| Monetization | Google Mobile Ads |
| Version Control | Git / GitHub |

---

# 🔐 Security & Production Engineering

Production mobile development involves more than implementing application screens.

My Android work also includes engineering concerns such as:

- keeping sensitive API credentials outside Android clients;
- server-side validation of privileged requests;
- Firebase authorization and access controls;
- separating client and backend responsibilities;
- persistent local state;
- release configuration;
- application signing;
- dependency management;
- Google Play deployment;
- advertising policy compliance;
- production maintenance;
- controlled configuration through Firebase Remote Config;
- large-content delivery through Play Asset Delivery.

---

# 📊 Engineering Problems Demonstrated

The projects in this portfolio represent three different classes of Android engineering problems.

| Project | Primary Engineering Problem |
|---|---|
| Mathematics + Geography Grade 9 | Reusable production educational architecture deployed across multiple applications |
| Grade 11 Textbooks | Multi-textbook architecture, progress management and on-demand large-content delivery |
| Electric Bill Calculator Ethiopia | Domain-specific utility software combining electrical engineering and Android development |

Together they demonstrate experience beyond creating isolated Android UI projects.

They involve:

**architecture → backend → persistence → content delivery → security → monetization → deployment → production maintenance**

---

# 📁 Portfolio Structure

```text
android-engineering-portfolio/
│
├── README.md
│
├── case-studies/
│   ├── grade-9-education-platform.md
│   ├── grade-11-multi-textbook-platform.md
│   └── electric-bill-calculator.md
│
├── architecture/
│   ├── android-education-architecture.md
│   ├── ai-tutor-backend.md
│   └── play-asset-delivery.md
│
└── assets/
    ├── mathematics-grade-9/
    ├── geography-grade-9/
    ├── grade-11-textbooks/
    └── electric-bill-calculator/
```

The repository intentionally focuses on **engineering documentation, architecture, production experience, and selected case studies** rather than publishing proprietary application source code.

---

# 🎯 Current Engineering Focus

My current work focuses on:

- Production Android development
- Kotlin
- Jetpack Compose
- Scalable Android architecture
- Firebase backend engineering
- Cloud Functions
- Cloud Firestore
- AI-assisted mobile applications
- Secure AI API integration
- Large educational content delivery
- Google Play production engineering

---

# 👨‍💻 About Me

I am an **Electrical Engineer and Software Engineer** with experience developing and maintaining production Android applications.

My work sits at the intersection of:

**Electrical Engineering + Android Development + Backend Engineering + AI-assisted applications**

I am particularly interested in building practical mobile software that solves real-world problems and can be deployed and maintained for real users.

---

## 🔗 Profiles

- GitHub: [SUBANUM63](https://github.com/SUBANUM63)
- LinkedIn: [Zeinu Kedir](https://www.linkedin.com/in/zeinu-kedir-426025222/)
- Portfolio: [jumanapps.com](https://jumanapps.com)

---

> This portfolio is continuously updated as production applications and engineering case studies evolve.
