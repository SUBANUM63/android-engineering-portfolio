# Android Engineering Portfolio

Production Android applications, mobile architecture, Firebase backend systems, and AI-powered learning experiences built for real-world users.

I am an Electrical Engineer and Software Engineer specializing in Android application development with **Kotlin, Jetpack Compose, Firebase, Room, Hilt, Coroutines, and modern Android architecture**.

My work includes educational applications used by students in Ethiopia, utility applications, Firebase-backed services, AI-powered learning features, and applications published and maintained on Google Play.

---

## 📱 Production Android Applications

### 🧮 Mathematics Grade 9 Textbook — 100K+ Downloads

A production educational Android application designed for Grade 9 students in Ethiopia.

**100K+ downloads on Google Play**

The application provides students with digital access to their mathematics textbook together with learning-oriented functionality designed for mobile devices.

**Engineering experience demonstrated:**

- Kotlin Android development
- Jetpack Compose UI
- PDF/document reading experience
- Local application state and persistence
- Firebase integration
- Production release management
- Google Play deployment and maintenance
- Ad-supported monetization
- Supporting a large real-world user base

[View Mathematics Grade 9 on Google Play](https://play.google.com/store/apps/details?id=com.subanum63.mathematicsgr9)

> Detailed engineering case study will be added under `case-studies/mathematics-grade-9.md`.

---

### ⚡ Electric Bill Calculator Ethiopia

A production Android utility application designed to help Ethiopian electricity customers calculate and understand electricity costs.

The project combines my background in **Electrical Engineering** with Android software engineering.

**Engineering areas include:**

- Kotlin
- Jetpack Compose
- Ethiopian electricity tariff calculations
- Local data persistence
- Firebase services
- Camera-assisted meter functionality
- Multiple meter profiles
- Production Android architecture
- Google Play deployment
- AdMob monetization

> Detailed engineering case study will be added under `case-studies/electric-bill-calculator.md`.

---

### 🌍 Geography Grade 9 Textbook

A production educational Android application built around Ethiopia's Grade 9 geography curriculum.

The application has evolved beyond basic textbook reading to include interactive learning capabilities and cloud-backed educational features.

**Engineering areas include:**

- Kotlin
- Jetpack Compose
- Room
- Hilt dependency injection
- Firebase
- Cloud Firestore
- Cloud Functions
- Remote Config
- AI-assisted learning
- Google Mobile Ads
- Production release and policy compliance

> Detailed engineering case study will be added under `case-studies/geography-grade-9.md`.

---

## 🤖 AI Tutor & Cloud Architecture

Some of my educational Android applications integrate an **AI Tutor** architecture rather than communicating directly with an AI provider from the mobile client.

A simplified production architecture is:

```text
┌─────────────────────────────────────┐
│            Android Client           │
│                                     │
│     Kotlin / Jetpack Compose        │
│     ViewModel / Repository          │
└─────────────────┬───────────────────┘
                  │
                  │ Secure / validated request
                  ▼
┌─────────────────────────────────────┐
│       Firebase Cloud Functions      │
│                                     │
│  • Request validation               │
│  • Authorization                    │
│  • Server-side business logic       │
│  • Usage / access controls          │
│  • API secret protection            │
└──────────┬─────────────────┬────────┘
           │                 │
           │                 │ Read / write
           │                 ▼
           │       ┌─────────────────────────┐
           │       │    Cloud Firestore      │
           │       │                         │
           │       │ • Application data      │
           │       │ • Learning content      │
           │       │ • User / access state   │
           │       └─────────────────────────┘
           │
           │ Server-side API request
           ▼
┌─────────────────────────────────────┐
│            AI Provider              │
│                                     │
│          LLM / AI inference         │
└─────────────────┬───────────────────┘
                  │
                  │ AI response
                  ▼
┌─────────────────────────────────────┐
│       Firebase Cloud Functions      │
│                                     │
│  Validate / process response        │
└─────────────────┬───────────────────┘
                  │
                  ▼
┌─────────────────────────────────────┐
│            Android Client           │
│                                     │
│          AI Tutor experience        │
└─────────────────────────────────────┘
