# FitFlow Redesign

FitFlow is a fitness-tracking mobile app redesign project for IT3060 – Human Computer Interaction (SLIIT, Year 3, Semester 2, 2026). This repository contains the technology stack decisions, architecture, and supporting documentation for rebuilding FitFlow around three research-driven pillars: AI-powered personalised workout plans, small private social accountability circles, and camera-based nutrition tracking.

Student: Malsha Munasinghe (IT23679344)

## Background

FitFlow's original app suffered from a 68% post-onboarding drop-off and a decline in App Store rating from 4.6 to 3.8 stars. User research (Lab Exercises 01–02) identified five core themes — **Personalization Gap**, **Social Isolation**, **Tracking Friction**, **Motivation & Reward**, and **Trust & Onboarding** — which shaped the requirements, wireframes (Lab 03), usability testing plan (Lab 04), and the technology decisions in this repository (Lab 05).

## Recommended Technology Stack

| Layer | Technology | Rationale (short) |
|---|---|---|
| Mobile Frontend | **React Native** | Single codebase for iOS/Android, mature ecosystem, strong animation support for fitness UI, same language (JavaScript/TypeScript) as backend team |
| Backend | **Node.js + Express** | Fast MLP development, non-blocking I/O suits real-time social features, shares JS/TS skillset with frontend |
| Database | **Firebase Firestore** (+ optional PostgreSQL for analytics later) | Real-time listeners for private social circles, generous free tier for a startup-stage MLP, scales automatically |
| Authentication | **Firebase Auth** | Native integration with Firestore security rules, supports email/social login, GDPR-compliant EU data residency |
| AI / ML | **TensorFlow Lite** (on-device) + cloud model fallback | On-device inference keeps soreness-adaptive plans fast and privacy-preserving (NFR-02 offline, NFR-04 data security) |
| Notifications | **Firebase Cloud Messaging** | Native push integration for peer check-ins and celebratory feedback |
| Storage | **Firebase Storage** | Stores meal photos captured by the nutrition logger |

See `docs/tech-comparison-matrix.md` and `docs/adr-001-technology-stack.md` for the full comparison and decision record.

## Project Structure

```
fitflow-redesign/
├── frontend/        # React Native mobile app
├── backend/         # Node.js + Express API (REST + WebSocket)
├── ai-service/      # TensorFlow Lite models + cloud AI microservice
├── docs/            # Comparison matrices, ADRs, architecture diagram
├── .gitignore
└── README.md
```

## Traceability

Every technology decision in this repository traces back to a functional or non-functional requirement defined in Lab Exercise 02:

- FR-01 / FR-02 (AI workout engine + manual override) → `ai-service/`
- FR-03 (camera-based nutrition logging) → `ai-service/` + `frontend/`
- FR-05 / FR-06 (private social circles, peer check-in) → Firestore real-time listeners in `backend/`
- NFR-02 (offline capability) → on-device TensorFlow Lite + local cache in `frontend/`
- NFR-04 (data security & GDPR/CCPA) → Firebase Auth + encryption, documented in `docs/adr-001-technology-stack.md`

## Getting Started (placeholder for future implementation)

```bash
# frontend
cd frontend && npm install && npm start

# backend
cd backend && npm install && npm run dev
```

## License

Coursework project — IT3060 HCI, SLIIT. Not for commercial use.
