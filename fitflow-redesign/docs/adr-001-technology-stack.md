# ADR-001: Technology Stack for FitFlow Redesign

**Status:** Accepted
**Date:** 2026 (IT3060 HCI, Lab Exercise 05)
**Author:** Malsha Munasinghe (IT23679344)

## Context

The FitFlow redesign must ship a Minimum Lovable Product (MLP) quickly, support an AI workout engine that adapts to logged soreness/fatigue (FR-01/FR-02), camera-based nutrition logging (FR-03), private real-time social circles (FR-05/FR-06), and must meet NFR-02 (offline capability), NFR-04 (GDPR/CCPA-compliant data security), and NFR-05 (SUS ≥ 80). The team is small/mid-sized, so learning curve, code reusability across iOS/Android, and maintenance cost are as important as raw performance.

Four frontend options were evaluated (Flutter, React Native, Kotlin Multiplatform, Swift/SwiftUI), three backend frameworks (Node.js/Express, Python/FastAPI, Go), four database options (PostgreSQL, MongoDB, Firebase Firestore, DynamoDB), and four auth providers (Firebase Auth, AWS Cognito, Auth0, Supabase). Full comparison tables are in `docs/tech-comparison-matrix.md`.

## Decision

We will build FitFlow using:

- **React Native** for the mobile frontend (iOS + Android from one codebase).
- **Node.js + Express** for the backend API (REST for standard calls, WebSocket for real-time social/notification events).
- **Firebase Firestore** as the primary database, with **Firebase Auth** for authentication and **Firebase Storage** for meal photos.
- **TensorFlow Lite** for on-device AI inference (soreness-adaptive plans, computer-vision meal recognition), backed by a cloud model for heavier training/updates.
- **Firebase Cloud Messaging** for push notifications (peer check-ins, celebratory feedback).

This combination scored highest (23/25 weighted) in the Activity 3 decision matrix, ahead of a Flutter + NestJS + PostgreSQL + Auth0 stack (18/25) and a Kotlin Multiplatform + Go + MongoDB + AWS Cognito stack (16/25).

## Consequences

**Positive**
- Single JavaScript/TypeScript skillset spans frontend and backend, reducing onboarding time for the team.
- Firestore's real-time listeners map directly onto the private-circle and peer-check-in requirements (FR-05/FR-06) with minimal custom infrastructure.
- Firebase Auth + Firestore security rules give GDPR-aligned access control out of the box, satisfying NFR-04 without building a custom auth service.
- On-device TensorFlow Lite keeps the soreness-adaptive workout engine fast and functional offline, satisfying NFR-02.
- Firebase's generous free tier keeps early-stage hosting/infrastructure cost low, matching the startup's need for a fast, inexpensive MLP.

**Negative / Trade-offs**
- Firestore is a NoSQL document store; complex relational analytics (e.g., cohort retention queries) are harder than in PostgreSQL. Mitigation: export events to a lightweight PostgreSQL or BigQuery analytics store once the product scales past MLP stage.
- React Native, while performant for this use case, can lag fully native Swift/Kotlin UI for very heavy animation-bound screens. Mitigation: use native modules selectively if a specific screen (e.g., a complex workout animation) needs it.
- Vendor lock-in to the Firebase/Google Cloud ecosystem. Mitigation: keep the API layer (Node/Express) as an abstraction boundary so the database/auth provider could be swapped with moderate effort if needed later.

## Alternatives Considered

| Alternative Stack | Reason Not Selected |
|---|---|
| Flutter + NestJS + PostgreSQL + Auth0 | Strong performance and typed backend, but introduces a second language (Dart) for the team and more setup effort for real-time features than Firestore offers out of the box. |
| Kotlin Multiplatform + Go + MongoDB + AWS Cognito | Best raw native performance, but Compose Multiplatform's web/UI-sharing story is still maturing, and the team would need separate UI implementations per platform, slowing the MLP timeline. |
| Swift/SwiftUI (native iOS only) | Rejected outright — does not meet the cross-platform iOS/Android/web requirement in the case study. |
