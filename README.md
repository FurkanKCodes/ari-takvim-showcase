# 🐝 Arı Takvim

**Arı Takvim** ("Bee Calendar") is a cross-platform (Android & iOS) mobile app I built with Flutter for students at Istanbul Technical University to track their class schedule, assignments, and final exam calendar from a single place.

> This repository showcases the project's architecture and development process. The full source code lives in a separate, access-restricted repository, since it contains technical details tied to the university's internal authentication systems.

---

## 🎯 Motivation

ITU students have to log into three separate web systems to keep track of their academic life: one portal for class schedules and final exam dates, and a completely different learning management system for assignments. None of these systems has an official mobile app or a public API.

Arı Takvim consolidates all three into **a single screen**, removing the need for students to visit multiple websites just to check what's due.

## ✨ Features

- **One-tap sync** — all data sources are refreshed with a single "Refresh" action
- **Persistent caching** — the latest fetched data remains available offline
- **Smart sorting** — classes are ordered by day, assignments and exams by active status + closest deadline
- **Customizable notifications** — users can set their own reminder time (days/hours/minutes) per assignment or exam
- **Server-free architecture** — no user data is ever stored on a server; credentials are stored encrypted on-device (Keychain / Keystore), and every request is made directly from the user's own device
- **Dark theme, native feel** — a consistent, Material 3-based UI across platforms

## 🏗️ Architecture

```
┌─────────────────────────────────────────────┐
│                   UI Layer                     │
│         (Flutter Widgets + Provider)          │
└───────────────────┬───────────────────────────┘
                     │
┌───────────────────▼───────────────────────────┐
│            UnifiedRepository                  │
│   (state management, cache orchestration,     │
│    rate limiting, notification scheduling)    │
└─────┬─────────────────────────────┬───────────┘
      │                             │
┌─────▼─────────┐           ┌───────▼──────────┐
│  Auth & API    │           │  Local Cache      │
│  Services      │           │  (SharedPreferences)│
│  (Dio + cookie │           └───────────────────┘
│  jar-based SSO │
│  sessions)     │
└────────────────┘
```

- **Layered service architecture**: each data source (schedule, assignments, exam calendar) is isolated in its own service class
- **Shared SSO login helper**: authentication logic common across the different university systems is consolidated in one place instead of duplicated
- **Repository pattern**: the UI never talks to a data source directly — everything flows through a single `UnifiedRepository`
- **`ChangeNotifier` + `Provider`**: reactive state management, optimized with `Consumer` boundaries to avoid unnecessary rebuilds

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Framework | Flutter / Dart |
| State Management | Provider |
| Networking | Dio + cookie_jar (session handling) |
| Persistent Storage | shared_preferences, flutter_secure_storage |
| Notifications | flutter_local_notifications + timezone |
| CI/CD | Codemagic (automated iOS/Android builds and TestFlight delivery) |
| Distribution | TestFlight (iOS), Google Play Internal Testing (Android) |

## 🔒 Why the Source Code Is Private

Since this app interacts with university systems that don't expose an official API, publishing the technical implementation details (endpoints, form structures) could expose them to unwanted or excessive traffic. For that reason:

- The source code lives in a private repository, shared only with invited collaborators
- The app is distributed only to a small, invite-only test group (TestFlight / Play Internal Testing) — it is not listed on any public store
- The relevant university department has been informed, and the current scope of use has been agreed upon with them

Feel free to reach out if you'd like to review the code or discuss the implementation in an interview setting.

## 📱 Screenshots

<!-- Add your own screenshots here, e.g.: -->
<!-- ![Class Schedule](screenshots/schedule.png) ![Assignments](screenshots/assignments.png) ![Exams](screenshots/exams.png) -->

## 📚 What I Learned

- Reverse-engineering web systems without an official API (inspecting network traffic, decoding form-based SSO flows)
- Turning unstructured HTML into reliable, type-safe data models through scraping
- Structuring a layered Flutter architecture: services, repository, and state management as distinct concerns
- Setting up a mobile CI/CD pipeline (Codemagic) with automated delivery to both App Store Connect and Google Play
- Handling user data responsibly in a server-free architecture, including secure on-device credential storage

---

*This is a personal project built for learning and personal/limited use, and is not an official product of Istanbul Technical University.*
