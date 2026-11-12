<div align="center">

# Ascension

**AI-powered climbing coach — objective technique analysis, personalized feedback**

[![Flutter](https://img.shields.io/badge/Flutter-Dart%20%5E3.11.0-02569B?logo=flutter)](https://flutter.dev)
[![Go](https://img.shields.io/badge/Backend-Go%201.27.1%20%2F%20Gin-00ADD8?logo=go)](https://go.dev)
[![Python](https://img.shields.io/badge/AI%20Workers-Python%203.11%20%2F%20MediaPipe-3776AB?logo=python)](https://python.org)
[![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL%2018-336791?logo=postgresql)](https://postgresql.org)

</div>

---

## What is Ascension?

Ascension is a mobile application that turns any smartphone into an intelligent climbing coach through computer vision and AI biomechanical analysis, delivering concrete, objective feedback on technique — the kind of feedback that usually requires an experienced human coach.

Film yourself on the wall during a climbing session to get a biomechanical breakdown of your movement and joint-by-joint coaching advice, or photograph an empty route to get a step-by-step Ghost Mode guide showing a feasible beta tailored to your morphology.

---

## The problem we're solving

Professional climbing coaching is expensive (50 €/hour), subjective, and hard to access. Most climbers train without real-time feedback and only correct bad habits when they plateau or get injured. Climbing "beta" (the sequence of moves needed to complete a route) is also becoming increasingly complex and harder to self-analyze alone.

Ascension bridges that gap by making objective, AI-driven analysis available to any climber — from beginner to competition-level — at a fraction of the cost.

---

## Core features

Ascension structures its capabilities into structured workout tracking and **4 distinct analysis workflows**:

**Climbing Sessions**
Start a gym session, record ascents, and analyses automatically. Ending the session displays total workout duration and aggregated session statistics.

**2D Pose Analysis & Feedback**
Extracts a 33-point skeleton per frame from a climbing video using MediaPipe Pose. Computes kinematic metrics (joint angles, center of mass, limb velocities, pauses) and generates targeted coaching advice via LLM.

**Ghost Mode**
Strictly photo-based workflow on an empty route (no climber). Detects and clusters holds by color/group (user selects a detected group or selects holds manually), then computes a **feasible beta** adapted to user morphology, rendered as a sequence of **step-by-step guidance images** derived from the initial photo.

**Social & Same-Wall Comparisons**
Share climb analyses and compare two climbers' video pose analyses side by side on the **same wall/route** to evaluate technique differences.

**Personalized Training Programs**
Custom routines generated from user profile (level, goals, injuries, morphology) and updated based on analysis history.

**3D Pose Analysis & Interactive Scene**
Pipeline extracting 3D posture and spatial metrics (hip-to-wall distance, 3D angles) with an interactive 3D scene on mobile (orbit, zoom, limb inspection).

**Hold Qualification**
Advanced automatic qualification of holds (type, difficulty, usability), extending BTP detection and grouping.

**Assisted Climbing (AR Audio)** 
Accessibility mode (notably for visually impaired climbers): stationary phone performs real-time pose tracking with audio cues during the ascent.

---

## Architecture

Ascension is built as an event-driven monorepo with CQRS, edge rendering, and direct-to-storage uploads:

```
Ascension/
├── apps/
│   ├── mobile/    Flutter app (iOS & Android)
│   ├── server/    Go / Gin REST API + WebSocket
│   └── ai/        Python AI workers (MediaPipe, PyTorch, OpenCV)
├── docs/          Complete user, technical & administrative documentation
└── .moon/         moonrepo workspace & toolchain configuration
```

**Data flow**

```
Climbing video:
User records → presigned URL → direct upload to S3 (RustFS dev / OVHcloud prod)
→ API publishes RabbitMQ job → Python AI worker computes skeleton & metrics
→ results stored in PostgreSQL (JSONB) → mobile client renders overlay locally on original video

Route photo (Ghost Mode):
User photographs empty wall → selects hold group / clicks holds → API queues job
→ AI worker pathfinding computes feasible beta → step-by-step images generated
→ mobile displays sequential guide
```

Heavy workloads are queued via RabbitMQ and processed asynchronously. The mobile client renders video overlays locally from lightweight JSON data (~50 KB), avoiding expensive server-side video re-encoding.

**Performance targets**
- API response time: p95 < 200 ms
- Video analysis processing: < 30 s for a 30-second video
- Time to result (upload + processing): ~45 s

---

## Tech stack

| Layer | Technology |
|---|---|
| Mobile | Flutter / Dart `^3.11.0`, Forui design system, CustomPainter |
| API | Go `1.27.1`, Gin, pgx, JWT auth, REST + WebSocket |
| AI Workers | Python `3.11`, MediaPipe, PyTorch, OpenCV, Pika |
| Queue | RabbitMQ `4.2.4` (durable queues, AMQP) |
| Database | PostgreSQL `18` (JSONB for analysis results) |
| Object storage | RustFS (dev) / OVHcloud S3 (prod) |
| Infrastructure | OVHcloud VPS-4 (Milan) + Gravelines Storage (S3), Docker Compose |
| Monorepo | moonrepo `2.5.4` |

---

## Repositories

| Repository | Description |
|---|---|
| [Ascension](../Ascension) | **Main Monorepo** — Core ecosystem including Flutter mobile app, Go API, and Python AI workers. |
| [benchmark](../benchmark) | **Performance Suite** — Technical benchmarks and stress tests for the mobile and backend stack. |
| [.github](../.github) | **Organization Profile** — Global GitHub organization profile configuration. |

---

## Subscription model

| Tier | Price | Analyses / month | Ghost Mode / month | Max Routines | Ads | Server Priority |
|---|---|---|---|---|---|---|
| Freemium | Free | 10 | ✗ | 5 | ✓ | ✗ |
| Premium | €20 / month | 30 | 30 | Unlimited | ✗ | ✗ |
| Infinity | €30 / month | 100 | 100 | Unlimited | ✗ | ✓ |

---

## Status

Active development for EIP (Epitech Innovative Project - Technical Track). Core 2D pose analysis pipeline, climbing session tracking, and mobile interface are functional. Ghost Mode pathfinding and 3D pipelines are structured for BTP and ATP milestones.

---

<div align="center">

*Epitech Innovative Project — 2025/2028*

</div>
