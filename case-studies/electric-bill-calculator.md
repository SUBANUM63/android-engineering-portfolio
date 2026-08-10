# ⚡ Electric Bill Calculator Ethiopia

## Production Android Engineering Case Study

**Application type:** Electrical utility / consumer finance tool  
**Platform:** Android  
**Primary language:** Kotlin  
**UI:** Jetpack Compose  
**Architecture:** ViewModel + Repository Pattern  
**Local persistence:** Room  
**Dependency injection:** Hilt  
**Asynchronous programming:** Kotlin Coroutines / Flow  
**Backend services:** Firebase  
**Reporting:** CSV and PDF export  
**Distribution:** Google Play  
**Monetization:** Google Mobile Ads  

---

# 1. Overview

**Electric Bill Calculator Ethiopia** is a production Android utility application designed to help Ethiopian electricity customers calculate, understand, and manage electricity-related billing information.

The project is technically different from my educational Android applications.

While the educational applications focus on textbook content, learning workflows, Firebase-backed educational resources, AI Tutor functionality, and large-content delivery, Electric Bill Calculator Ethiopia focuses on translating **electrical-engineering domain rules into reliable mobile software**.

The application represents the intersection of my two engineering backgrounds:

> **Electrical Engineering + Software Engineering**

The engineering challenge is not simply displaying tariff information.

The application needs to transform electricity consumption and tariff rules into structured calculations, maintain user and meter-related state, present results clearly, support historical tracking, and export useful data in formats such as **CSV and PDF**.

### Google Play

[View Electric Bill Calculator Ethiopia on Google Play](https://play.google.com/store/apps/details?id=com.et.subanum63.electricbillcalculator)

---

# 2. Engineering Objective

The primary engineering objective is to convert electricity billing rules into a practical Android application that consumers can use without manually performing complex calculations.

Conceptually:

```text
Electricity Domain Rules
          │
          ▼
     Domain Model
          │
          ▼
 Calculation Engine
          │
          ▼
 Application Logic
          │
          ▼
   Android UI / UX
          │
          ▼
Persistent User Data
          │
          ▼
 Reporting / Export
          │
      ┌───┴───┐
      ▼       ▼
     CSV     PDF
```

The application therefore sits between technical electricity-domain rules and ordinary electricity customers.

---

# 3. Core Engineering Problem

Electricity billing is not simply:

```text
Bill = Consumption × One Fixed Rate
```

A production billing calculator may need to account for multiple components of the applicable billing model.

The application therefore separates:

- meter readings;
- energy consumption;
- tariff logic;
- tariff blocks;
- applicable charges;
- calculation results;
- meter profiles;
- historical records;
- presentation logic;
- exported reports.

The objective is to prevent complex domain calculations from becoming tightly coupled to Android UI code.

---

# 4. High-Level Architecture

A simplified representation of the application is:

```text
┌───────────────────────────────────────┐
│            Android Client             │
│                                       │
│       Kotlin / Jetpack Compose        │
└──────────────────┬────────────────────┘
                   │
                   ▼
┌───────────────────────────────────────┐
│              ViewModel                │
│                                       │
│   UI state / events / coordination    │
└──────────────────┬────────────────────┘
                   │
                   ▼
┌───────────────────────────────────────┐
│         Application / Domain          │
│                                       │
│ • Electricity calculations            │
│ • Tariff rules                        │
│ • Meter logic                         │
│ • Billing logic                       │
└──────────────────┬────────────────────┘
                   │
                   ▼
┌───────────────────────────────────────┐
│           Repository Layer            │
└───────────┬───────────────┬───────────┘
            │               │
            ▼               ▼
      Room / Local       Firebase
       Persistence        Services
            │
            ▼
      Historical Data
            │
            ▼
       Reporting Layer
        ┌──────┴──────┐
        ▼             ▼
       CSV           PDF
```

The architecture separates the electricity-domain rules from presentation and infrastructure responsibilities.

---

# 5. Electrical Engineering Domain

One of the defining characteristics of this project is that its application logic originates from an electrical-utility domain rather than a generic software exercise.

The software needs to represent concepts related to electricity consumption and billing in a form suitable for an Android application.

Conceptually:

```text
Meter Information
        │
        ▼
Consumption Data
        │
        ▼
Electricity Tariff Rules
        │
        ▼
Billing Calculation
        │
        ▼
Charges / Components
        │
        ▼
Final Result
```

This requires converting domain rules into deterministic software behavior.

---

# 6. From Meter Reading to Consumption

A typical calculation starts with electricity-consumption information.

For meter-reading-based calculations, the conceptual relationship is:

```text
Previous Meter Reading
          +
Current Meter Reading
          │
          ▼
Determine Consumption
          │
          ▼
Apply Billing Rules
```

At the simplest conceptual level:

```text
Consumption = Current Reading - Previous Reading
```

The resulting consumption value then becomes an input to the tariff calculation process.

Validation is important because invalid readings should not silently produce misleading billing results.

---

# 7. Tariff Calculation Engine

The tariff engine is one of the core domain components of the application.

Rather than placing calculation formulas directly inside Compose screens, tariff calculation belongs to application/domain logic.

Conceptually:

```text
Consumption
     │
     ▼
Tariff Engine
     │
     ├── Determine applicable tariff structure
     │
     ├── Evaluate consumption blocks
     │
     ├── Apply applicable rates
     │
     ├── Apply applicable charges
     │
     └── Produce structured result
     │
     ▼
Billing Result
```

This separation makes tariff logic easier to reason about and maintain independently of UI implementation.

---

# 8. Block-Based Tariff Calculation

Where electricity pricing uses consumption blocks, a customer's entire consumption should not necessarily be treated as though it belongs to one flat rate.

Conceptually:

```text
Total Consumption
       │
       ▼
┌─────────────────┐
│ Consumption     │
│ Block 1         │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Consumption     │
│ Block 2         │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Consumption     │
│ Block 3         │
└────────┬────────┘
         │
         ▼
       ...
         │
         ▼
Combine applicable
block charges
         │
         ▼
Energy Charge
```

The calculation engine therefore needs to determine how much consumption belongs to each applicable range and apply the appropriate rules.

---

# 9. Structured Billing Result

A useful calculator should return more than a single unexplained number.

The calculation result can be modeled conceptually as structured information:

```text
Billing Result
│
├── Consumption
├── Energy charge
├── Applicable service charges
├── Other applicable components
└── Total
```

This structure allows the same calculation result to be used by several parts of the application:

```text
Calculation Engine
       │
       ▼
Structured Result
       │
       ├──► Compose UI
       │
       ├──► History
       │
       ├──► CSV Export
       │
       └──► PDF Report
```

This is preferable to coupling the calculation result directly to one screen.

---

# 10. Separation of Domain Logic and UI

One of the important architectural principles of the project is keeping electricity calculations separate from the presentation layer.

The undesirable approach would be:

```text
Compose Screen
     │
     ├── Read input
     ├── Calculate tariffs
     ├── Apply charges
     ├── Format result
     └── Display output
```

That approach mixes too many responsibilities.

The preferred structure is:

```text
Compose UI
     │
     ▼
ViewModel
     │
     ▼
Domain / Calculation Logic
     │
     ▼
Structured Result
     │
     ▼
ViewModel State
     │
     ▼
Compose UI
```

The UI is responsible for presentation and interaction rather than becoming the source of billing rules.

---

# 11. Multiple Meter Profiles

The application supports a workflow where users can manage multiple meter-related profiles.

This is useful when a user needs to track more than one electricity meter or property.

Conceptually:

```text
User
 │
 ├── Meter Profile A
 │      ├── Meter information
 │      ├── Billing history
 │      └── Reading state
 │
 ├── Meter Profile B
 │      ├── Meter information
 │      ├── Billing history
 │      └── Reading state
 │
 └── Meter Profile N
        ├── Meter information
        ├── Billing history
        └── Reading state
```

The same architectural principle seen in the multi-textbook application applies here in a different domain:

> **State needs ownership.**

Billing and reading data must be associated with the correct meter profile.

---

# 12. Meter Identity and Data Ownership

Once multiple meter profiles exist, calculation history cannot be treated as one anonymous global list.

Conceptually:

```text
Meter Profile
      │
      ├── Meter ID
      ├── Meter metadata
      ├── Previous readings
      ├── Calculations
      └── Billing history
```

A history record should retain enough context to identify which meter it belongs to.

This allows the application to provide meaningful historical tracking across multiple meters.

---

# 13. Camera-Assisted Meter Workflow

The application also explores camera-assisted functionality for meter-related workflows.

Instead of requiring every meter value to be entered manually, camera-assisted input can reduce friction in the reading process.

Conceptually:

```text
Electricity Meter
       │
       ▼
Device Camera
       │
       ▼
Meter Reading Capture
       │
       ▼
Reading Processing
       │
       ▼
User Verification
       │
       ▼
Validated Meter Value
       │
       ▼
Calculation Workflow
```

The important design principle is that automated or camera-assisted recognition should not silently become trusted billing input without an opportunity for validation.

The user should remain able to verify or correct the detected reading before it is used for calculation.

---

# 14. Local Persistence with Room

The application uses Room for structured local persistence.

Depending on the feature, locally persisted information can include:

- meter profiles;
- calculation records;
- billing history;
- user preferences or application state;
- data required for reporting.

A simplified flow is:

```text
User / Calculation
        │
        ▼
     ViewModel
        │
        ▼
    Repository
        │
        ▼
       Room
        │
        ▼
Persistent Local Data
        │
        ├──► History
        ├──► Meter Profiles
        └──► Reporting
```

Local persistence allows the application to retain useful information between sessions without requiring every operation to depend on a network connection.

---

# 15. Historical Billing Data

Persisting calculations allows the application to become more than a one-time calculator.

Instead of:

```text
Enter values
     │
     ▼
Calculate
     │
     ▼
Display result
     │
     ▼
Result disappears
```

the application can support:

```text
Enter values
     │
     ▼
Calculate
     │
     ▼
Structured result
     │
     ├──► Display
     │
     └──► Persist
              │
              ▼
          History
              │
              ├──► Review
              ├──► Compare
              └──► Export
```

This turns calculation data into reusable user information.

---

# 16. Reporting & Data Export

The application includes data-export functionality for generating **CSV and PDF files**.

This feature extends persisted billing/calculation data beyond the application itself.

A simplified architecture is:

```text
Stored Calculation /
Billing History
        │
        ▼
    Repository
        │
        ▼
  Reporting Layer
        │
   ┌────┴────┐
   │         │
   ▼         ▼
CSV Export  PDF Export
   │         │
   └────┬────┘
        │
        ▼
Generated File
        │
        ▼
Android Share /
File Workflow
```

Reporting is treated as a separate responsibility rather than being embedded directly into the history UI.

---

# 17. CSV Export

CSV provides a structured representation of historical calculation data.

Conceptually:

```text
Room / Historical Data
          │
          ▼
Select Export Records
          │
          ▼
Normalize Data
          │
          ▼
Generate CSV Rows
          │
          ▼
Create CSV File
          │
          ▼
Share / Save
```

CSV is useful because exported records can be opened or processed by spreadsheet and data-analysis tools.

A conceptual export might contain fields such as:

```text
Date
Meter
Previous Reading
Current Reading
Consumption
Energy Charge
Additional Charges
Total
```

The exact exported schema depends on the application's implementation.

---

# 18. PDF Export

PDF export serves a different purpose from CSV.

CSV is primarily structured data.

PDF provides a human-readable report.

Conceptually:

```text
Calculation / History Data
          │
          ▼
     Report Model
          │
          ▼
     PDF Generator
          │
          ▼
Formatted PDF Report
          │
          ▼
     Share / Save
```

A PDF report can present billing information in a format that is easier to read, archive, print, or share.

---

# 19. Why Support Both CSV and PDF?

The two export formats solve different problems.

| Format | Primary Purpose |
|---|---|
| CSV | Structured data, spreadsheet use, analysis |
| PDF | Human-readable reporting, sharing, archiving |

Supporting both formats makes the reporting system more useful than forcing one format to serve every use case.

The architecture can therefore be represented as:

```text
                Report Data
                    │
           ┌────────┴────────┐
           │                 │
           ▼                 ▼
      Data-oriented     Presentation-oriented
           │                 │
           ▼                 ▼
          CSV               PDF
```

---

# 20. Export Architecture

A maintainable export implementation should avoid duplicating business logic.

The billing engine should calculate the result once.

The UI, CSV exporter, and PDF exporter should consume structured result data.

Conceptually:

```text
                  Calculation Engine
                         │
                         ▼
                 Structured Result
                         │
            ┌────────────┼────────────┐
            │            │            │
            ▼            ▼            ▼
        Compose UI      CSV          PDF
                       Export       Export
```

This helps ensure that exported information is based on the same underlying data presented by the application.

---

# 21. Android File Handling

Generating reports introduces Android-specific engineering concerns beyond creating strings or byte arrays.

The application needs to consider:

- file creation;
- file location;
- supported Android storage behavior;
- content URIs where applicable;
- safe file sharing;
- file naming;
- MIME types;
- lifecycle and error handling.

Conceptually:

```text
Generated Report
       │
       ▼
Application File Handling
       │
       ▼
Safe URI / File Access
       │
       ▼
Android Share Intent
       │
       ▼
Compatible External App
```

The objective is to export useful information without relying on unsafe or outdated storage assumptions.

---

# 22. Multilingual User Experience

An electricity utility application intended for Ethiopian users benefits from language accessibility.

The application architecture therefore needs to avoid hard-coding user-facing text directly into business logic.

Conceptually:

```text
Domain Logic
     │
     ▼
Structured Result
     │
     ▼
Presentation Layer
     │
     ▼
Localized Resources
     │
     ├── English
     ├── Amharic
     ├── Afaan Oromo
     └── Tigrinya
```

The calculation engine should remain independent of the language used to present its result.

---

# 23. Firebase Services

Firebase services are used where cloud-backed application functionality is appropriate.

The application architecture keeps Firebase responsibilities separate from the electricity calculation engine.

Conceptually:

```text
Android Application
       │
       ├────────────► Local Domain Logic
       │                    │
       │                    ▼
       │             Billing Engine
       │
       └────────────► Firebase Services
```

This separation is important because a tariff calculation should not become unnecessarily dependent on cloud availability when the underlying calculation can be performed locally.

---

# 24. Offline-Oriented Calculation

A utility calculator should remain useful even when network connectivity is unreliable.

The core calculation path can therefore be designed around locally available rules and inputs where possible.

Conceptually:

```text
User Input
    │
    ▼
Local Validation
    │
    ▼
Local Tariff Logic
    │
    ▼
Calculation
    │
    ▼
Room Persistence
    │
    ▼
Result
```

Cloud services can enhance the application without becoming an unnecessary dependency for every basic calculation.

---

# 25. Jetpack Compose UI

Jetpack Compose provides the presentation layer for the application.

The UI is responsible for:

- accepting user input;
- displaying meter information;
- presenting calculation results;
- showing history;
- presenting meter profiles;
- initiating export actions;
- communicating errors and validation states.

The UI does not need to own tariff rules.

Conceptually:

```text
Compose UI
     │
     ▼
User Event
     │
     ▼
ViewModel
     │
     ▼
Domain / Repository
     │
     ▼
Updated State
     │
     ▼
Compose Recomposition
```

---

# 26. ViewModel State Management

ViewModels coordinate user actions and application state.

A typical flow is:

```text
User enters meter values
          │
          ▼
      Compose UI
          │
          ▼
       ViewModel
          │
          ▼
 Validate Input
          │
          ▼
Calculation Engine
          │
          ▼
Structured Result
          │
          ▼
   ViewModel State
          │
          ▼
      Compose UI
```

This prevents the composable layer from becoming the application's calculation engine.

---

# 27. Repository Layer

The repository layer provides coordination between application logic and data sources.

Conceptually:

```text
ViewModel
    │
    ▼
Repository
    │
    ├──► Room
    │
    ├──► Firebase
    │
    └──► Application Data Sources
```

This provides a clearer boundary between UI-facing state and persistence/infrastructure responsibilities.

---

# 28. Hilt Dependency Injection

Hilt manages dependencies across the application.

Conceptually:

```text
Application
    │
    ▼
Hilt
    │
    ├── Room Database
    ├── DAO
    ├── Repository
    ├── Firebase Services
    ├── Reporting Components
    └── Other Application Services
```

This reduces manual construction of infrastructure inside Android UI components.

---

# 29. Kotlin Coroutines and Flow

Database operations, Firebase communication, file generation, and other potentially long-running tasks should not block the Android main thread.

Kotlin Coroutines and Flow can support asynchronous and observable application behavior.

Conceptually:

```text
User Action
     │
     ▼
ViewModel
     │
     ▼
Coroutine
     │
     ├── Database
     ├── File Export
     └── Remote Operation
     │
     ▼
Updated State
     │
     ▼
Compose UI
```

---

# 30. Input Validation

A calculation tool must protect users from obviously invalid input.

Possible validation concerns include:

- missing values;
- invalid numeric input;
- negative values where inappropriate;
- current reading lower than previous reading where that is not valid;
- malformed meter data;
- unsupported calculation conditions.

Conceptually:

```text
User Input
     │
     ▼
Validation
     │
 ┌───┴────┐
 │        │
Valid   Invalid
 │        │
 ▼        ▼
Calculate  Explain error
```

Validation is part of calculation reliability, not merely UI decoration.

---

# 31. Error Handling

The application contains operations that can fail independently.

Examples include:

- invalid calculation input;
- database operations;
- Firebase operations;
- camera-related processing;
- CSV generation;
- PDF generation;
- file sharing.

A production application should distinguish between these failure types rather than presenting every problem as a generic error.

---

# 32. Monetization

The application uses **Google Mobile Ads** as part of its production model.

Advertising in a utility application needs to coexist with important user workflows such as:

- entering meter readings;
- reviewing calculated bills;
- reading historical records;
- exporting reports.

Ad placement therefore needs to consider:

- user intent;
- accidental-click risk;
- consent;
- frequency;
- lifecycle state;
- policy compliance;
- usability.

Monetization is treated as part of production engineering rather than simply as an SDK integration.

---

# 33. Production Android Engineering

Publishing and maintaining the application involves responsibilities beyond feature development.

These include:

- application signing;
- release configuration;
- dependency management;
- Android compatibility;
- Google Play requirements;
- privacy and consent;
- advertising policy compliance;
- testing across devices;
- application updates;
- maintaining calculation correctness;
- adapting domain rules when required.

A production calculator must remain maintainable as both Android and the electricity-domain requirements evolve.

---

# 34. Why Domain Logic Should Be Isolated

Tariff rules can change independently of the Android user interface.

If tariff calculations are embedded directly into Compose screens, updating the domain rules becomes unnecessarily risky.

A better conceptual structure is:

```text
              Android Application
                     │
          ┌──────────┴──────────┐
          │                     │
          ▼                     ▼
 Presentation Layer       Domain Layer
                                │
                                ▼
                         Tariff Engine
                                │
                                ▼
                         Billing Rules
```

This allows domain behavior to evolve without requiring presentation logic to become the source of truth.

---

# 35. Testing Strategy

The calculation engine is a particularly important candidate for automated testing.

For example, tests can verify:

```text
Known Input
    │
    ▼
Calculation Engine
    │
    ▼
Actual Result
    │
    ▼
Compare with
Expected Result
```

Useful test categories include:

- consumption boundaries;
- tariff-block boundaries;
- zero consumption;
- high consumption;
- invalid readings;
- charge calculations;
- rounding behavior;
- historical data mapping;
- report-data generation.

Calculation correctness should be testable independently of the Compose UI.

---

# 36. Reporting Testability

CSV and PDF generation also introduce testable behavior.

For CSV, tests can verify:

- column order;
- record mapping;
- escaping;
- formatting;
- empty datasets;
- multiple meter profiles.

For PDF reports, testing can focus on:

- correct report data;
- expected sections;
- valid document generation;
- handling missing data;
- file-generation failures.

The reporting layer should consume structured data rather than recalculate electricity bills independently.

---

# 37. Technology Responsibility Map

| Responsibility | Technology / Layer |
|---|---|
| Primary language | Kotlin |
| UI | Jetpack Compose |
| UI state | ViewModel |
| Architecture | Repository + domain separation |
| Dependency injection | Hilt |
| Local persistence | Room |
| Async operations | Kotlin Coroutines / Flow |
| Electricity calculations | Domain / calculation engine |
| Meter management | Application + persistence layer |
| Camera-assisted workflow | Android camera-related functionality |
| Cloud-backed functionality | Firebase |
| Structured export | CSV |
| Human-readable reports | PDF |
| Distribution | Google Play |
| Monetization | Google Mobile Ads |

---

# 38. Engineering Skills Demonstrated

The project demonstrates practical experience with:

- Kotlin;
- Jetpack Compose;
- modern Android architecture;
- ViewModel state management;
- repository pattern;
- domain-driven calculation logic;
- Hilt dependency injection;
- Room persistence;
- Kotlin Coroutines / Flow;
- electricity tariff modeling;
- consumption calculations;
- block-based tariff calculations;
- meter-profile management;
- historical data persistence;
- camera-assisted meter workflows;
- Firebase integration;
- multilingual Android UI;
- CSV data export;
- PDF report generation;
- Android file handling;
- share workflows;
- input validation;
- error handling;
- Google Mobile Ads;
- Google Play deployment;
- production Android maintenance.

---

# 39. Engineering Lessons

## Domain knowledge can become software architecture

Electrical-engineering knowledge is useful only when it can be translated into deterministic application behavior.

The application converts electricity-domain concepts into data models, validation rules, calculation logic, and user-facing results.

---

## Calculation logic should not belong to the UI

Compose screens should present results rather than define electricity tariffs.

---

## Persistent data makes a calculator more useful

Saving meter profiles and calculation history turns a one-time calculator into a continuing utility.

---

## Export creates interoperability

CSV allows structured data to leave the application and be used by spreadsheet or analysis tools.

PDF provides a portable human-readable report.

---

## Multiple meters require explicit identity

Just as multiple textbooks require textbook identity, multiple electricity meters require meter identity.

State without ownership becomes ambiguous.

---

## Offline capability matters for utility applications

Core calculations should not unnecessarily fail merely because network connectivity is unavailable.

---

## Production correctness matters

A visual defect may inconvenience a user.

An incorrect billing calculation can mislead them financially.

Domain calculations therefore deserve stronger validation and testing than ordinary presentation logic.

---

# 40. Comparison with the Educational Applications

The Electric Bill Calculator demonstrates a substantially different engineering problem from the educational applications.

| Engineering Area | Educational Apps | Electric Bill Calculator |
|---|---|---|
| Kotlin | Yes | Yes |
| Jetpack Compose | Yes | Yes |
| ViewModel / Repository | Yes | Yes |
| Room | Yes | Yes |
| Hilt | Yes | Yes |
| Firebase | Yes | Yes |
| Main domain | Education | Electricity utility |
| Primary content | Textbooks / learning | Meter / billing data |
| Core business logic | Learning workflow | Tariff calculations |
| AI Tutor | Yes | Not core |
| Play Asset Delivery | Grade 11 | Not core |
| Multiple identities | Textbooks | Meter profiles |
| Historical records | Learning state | Billing calculations |
| CSV export | Not primary | Yes |
| PDF export | Not primary | Yes |
| Camera workflow | Not primary | Meter-related |
| Domain expertise | Educational software | Electrical engineering |

This distinction is important because the project demonstrates that the Android architecture is being applied to a completely different problem domain.

---

# 41. Electrical Engineering + Software Engineering

This application represents the intersection of two engineering disciplines:

```text
Electrical Engineering
        │
        ├── Electricity consumption
        ├── Meter readings
        ├── Tariff understanding
        └── Billing-domain knowledge
        │
        ▼
     Domain Model
        │
        ▼
Software Engineering
        │
        ├── Kotlin
        ├── Android architecture
        ├── Room
        ├── Compose
        ├── Firebase
        ├── CSV/PDF reporting
        └── Production deployment
        │
        ▼
Electric Bill Calculator Ethiopia
```

The value of the project is therefore not simply that an electrical engineer created an Android application.

The engineering value comes from **translating domain knowledge into maintainable production software**.

---

# 42. End-to-End Application Flow

A simplified end-to-end workflow is:

```text
User
 │
 ▼
Select Meter Profile
 │
 ▼
Enter / Capture Meter Reading
 │
 ▼
Validate Input
 │
 ▼
Determine Consumption
 │
 ▼
Apply Tariff Rules
 │
 ▼
Generate Structured Bill
 │
 ├────────────► Display Result
 │
 └────────────► Persist Result
                     │
                     ▼
                  Room
                     │
                     ▼
             Calculation History
                     │
             ┌───────┴───────┐
             │               │
             ▼               ▼
         CSV Export       PDF Export
             │               │
             └───────┬───────┘
                     ▼
                Share / Save
```

This demonstrates the full path from raw meter information to reusable user data.

---

# 43. Source Code and Repository Scope

The production application source repository is private.

This case study therefore focuses on:

- architecture;
- engineering decisions;
- domain modeling;
- system responsibilities;
- production workflows;
- technology choices;
- high-level diagrams;
- publicly verifiable Google Play distribution.

It intentionally does not expose:

- proprietary source code;
- signing credentials;
- API keys;
- private Firebase configuration;
- protected production configuration;
- other sensitive implementation details.

The reporting functionality may also evolve in the private production repository before being included in a future public application release.

---

# 44. Result

Electric Bill Calculator Ethiopia demonstrates the complete transformation of an engineering-domain problem into a production Android application:

```text
Electrical Engineering Rules
           │
           ▼
      Domain Model
           │
           ▼
   Calculation Engine
           │
           ▼
    Android Architecture
           │
           ▼
   Persistent Meter Data
           │
           ▼
     Billing History
           │
           ▼
    Reporting / Export
        ┌──┴──┐
        ▼     ▼
       CSV   PDF
           │
           ▼
    Production Utility
           │
           ▼
       Google Play
```

The project demonstrates more than Android UI development.

It combines:

> **electrical-engineering domain knowledge + calculation architecture + local persistence + meter management + reporting + production Android engineering**

---

# 45. Portfolio Context

This project completes the three primary Android engineering systems documented in this portfolio.

## 📚 Grade 9 Educational Platform

A reusable single-textbook educational architecture used by:

- Mathematics Grade 9 — **100K+ downloads**
- Geography Grade 9 — **100K+ downloads**

**200K+ combined Google Play downloads**

Engineering focus:

- production educational architecture;
- Room;
- Firebase;
- summaries and quizzes;
- AI Tutor;
- Cloud Functions;
- real-world application scale.

---

## 📚 Grade 11 Multi-Textbook Platform

The educational architecture expanded into a multi-textbook system.

Engineering focus:

- multiple textbooks;
- explicit textbook identity;
- independent reading progress;
- aggregated progress;
- summaries and quizzes;
- AI Tutor;
- Firebase backend;
- Google Play Asset Delivery;
- on-demand large-content delivery.

---

## ⚡ Electric Bill Calculator Ethiopia

A domain-specific production utility.

Engineering focus:

- electrical-engineering domain logic;
- electricity tariff calculations;
- meter profiles;
- historical billing data;
- camera-assisted meter workflows;
- CSV export;
- PDF reporting;
- multilingual user experience;
- production Android deployment.

Together, these projects demonstrate three different engineering challenges:

```text
Reusable Educational Architecture
             │
             ▼
Multi-Content Platform Architecture
             │
             ▼
Domain-Specific Utility Engineering
```

---

# 46. Technology Stack

| Area | Technology |
|---|---|
| Language | Kotlin |
| UI | Jetpack Compose |
| Architecture | ViewModel + Repository Pattern |
| Dependency Injection | Hilt |
| Local Database | Room |
| Async Programming | Kotlin Coroutines / Flow |
| Domain Logic | Electricity tariff / billing engine |
| Cloud Services | Firebase |
| Camera Functionality | Android camera-assisted workflow |
| Structured Reporting | CSV |
| Document Reporting | PDF |
| Localization | Android resource-based localization |
| Distribution | Google Play |
| Monetization | Google Mobile Ads |
| Version Control | Git / GitHub |

---

# 47. Final Engineering Perspective

The defining engineering challenge of Electric Bill Calculator Ethiopia is not displaying a bill result.

It is maintaining a reliable chain from:

> **meter input → validation → consumption → tariff rules → billing result → persistence → reporting**

Each stage has a different responsibility.

The calculation engine should not depend on Compose.

The reporting layer should not independently recalculate the bill.

The history layer should preserve structured results.

CSV and PDF exporters should consume consistent application data.

Meter-specific information should remain associated with the correct meter identity.

That separation allows a domain-specific utility application to remain maintainable as its features and underlying electricity-domain requirements evolve.

---

[← Back to Android Engineering Portfolio](../README.md)
