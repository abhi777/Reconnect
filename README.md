# Reconnect (Starecho)

> Reconnect is your AI-powered grief and memory companion concept — an app designed to help people reconnect with the memories, personality, stories, and conversational style of someone they have lost.

The idea is to create a digital space where memories feel alive, allowing users to revisit meaningful conversations and feel connected to someone who is no longer physically present.



---

## Table of Contents

- [Project Overview](#project-overview)
- [Screenshots and Demo](#screenshots-and-demo)
- [Features](#features)
- [Technology Stack](#technology-stack)
- [Architecture and How It Works](#architecture-and-how-it-works)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation and Setup](#installation-and-setup)
- [Environment Variables and Configuration](#environment-variables-and-configuration)
- [Usage Guide](#usage-guide)
- [API and Integration Reference](#api-and-integration-reference)
- [Database and Data Flow](#database-and-data-flow)
- [Development and Testing](#development-and-testing)
- [Deployment](#deployment)
- [Security and Privacy](#security-and-privacy)
- [Troubleshooting and FAQ](#troubleshooting-and-faq)
- [Limitations and Roadmap](#limitations-and-roadmap)
- [Contributing](#contributing)
- [License and Acknowledgements](#license-and-acknowledgements)
- [Additional Resources](#additional-resources)

---

## Project Overview

### What it is

Reconnect lets an adult create an AI companion from a deceased loved one's voice clip, biographical details, and personality traits. The user records a voice note; the system transcribes it, moderates it, retrieves relevant memories, generates a persona reply, moderates the reply, synthesizes it in the cloned voice, and delivers it back asynchronously.

The product **always presents itself as an AI companion built from memories**. It must never claim to be the real person, to be conscious, alive, or in an afterlife.

### Problem and audience

Grief support and remembrance products often reduce a person to a text chatbot. Reconnect focuses on the voice and the feeling of a remembered conversation, while treating consent, living-person protection, crisis routing, and data rights as first-class requirements.

The current product targets **adults 18+ in India**:

- INR pricing and Indian crisis helplines.
- DPDP-oriented consent, export, erasure, grievance, and audit flows.
- English/Hindi conversation support with automatic spoken-language detection.
- Hosted AI providers for the MVP, with an explicit cross-border processing record.

### Key differentiators

- **Asynchronous voice-note loop**, not a real-time phone call.
- **Consent-gated activation**: a companion cannot become active without a stored estate/next-of-kin consent record.
- **Living-person block** in the self-serve creation flow.
- **Two-layer moderation architecture**: deterministic self-harm routing always runs and breaks character with Indian helpline information; an optional hosted classifier adds hazard coverage and is required in production.
- **Rights and safety are free for every tier** — deletion, export, grievances, appeals, and crisis routing are never paywalled.
- **Provider abstraction**: STT, TTS, LLM, embeddings, moderation, and billing each have a swappable interface plus local fakes for tests.
- **Best-effort view-once delivery** for the free tier, with single-use tokens and a purge worker. Copy says “plays once · no in-app download,” never “undownloadable.”
- **Self-hostable data plane for development** via Docker Compose (Postgres + pgvector, Redis, MinIO), while the MVP uses hosted AI APIs.

### Current status

Implemented and exercised by tests or code paths:

- Clerk authentication, 18+ onboarding, local user provisioning, and Clerk webhooks.
- Companion creation, consent activation, voice cloning, one-time greeting, and voice deletion.
- Presigned upload → STT → moderation → memory retrieval → LLM → moderation → TTS → delivery.
- Streaming replies and the buffered worker fallback.
- View-once/replayable delivery primitives, WebSocket events, and polling.
- DPDP rights surfaces, abuse reports, disputes, grievances, and appeals.
- Razorpay order/subscription billing with a signature-verified webhook state machine.
- Admin console with TOTP MFA, RBAC, review queues, metrics, budget alerts, and audit log.
- Row-Level Security (RLS), audit logging, rate limiting, structured logs, Prometheus metrics, and Sentry hooks.

Partially implemented or not implemented:

- Letters/SMS/writings ingestion and redaction tooling.
- Companion edit/delete and a DPDP correction endpoint.
- Conversation-history/replay UI.
- Summary-memory consolidation.
- Provider usage metering on the streaming reply path.
- Voiceprint/public-figure likeness detection.
- KMS/envelope encryption at rest.
- True inaudible audio watermarking.
- PWA/offline support.
- CI and a configured frontend lint setup.
- A fully wired self-harm support panel in the conversation UI.

See [Limitations and Roadmap](#limitations-and-roadmap) for the verified list.

---

## Screenshots and Demo

No screenshots, demo GIFs, videos, or hosted deployment URLs are committed to this repository. There is therefore no verified demo link to reference.
<img width="2498" height="1039" alt="image" src="https://github.com/user-attachments/assets/ae13f789-eac1-457d-88c1-b1a473bd2a6f" />
<img width="2504" height="1038" alt="image" src="https://github.com/user-attachments/assets/9999c74d-72c1-4a67-9395-cd40d8574eac" />
<img width="2500" height="1037" alt="image" src="https://github.com/user-attachments/assets/20872944-767a-4e05-b843-f710b9e8d06e" />
<img width="2496" height="1045" alt="image" src="https://github.com/user-attachments/assets/147464cd-27bd-4f3b-a4ab-7f52d06658fa" />
<img width="2495" height="1045" alt="image" src="https://github.com/user-attachments/assets/94560ddd-3fd1-473e-80de-ff80c44d98f4" />
<img width="2491" height="1039" alt="image" src="https://github.com/user-attachments/assets/fe1b2785-341a-43a6-bcb0-789f543fa614" />
<img width="2496" height="991" alt="image" src="https://github.com/user-attachments/assets/db6d2b60-dce9-4a4c-b022-7815f0a9a42a" />
<img width="2498" height="1040" alt="image" src="https://github.com/user-attachments/assets/78157ebd-2110-4efb-8ba3-a2be5c4a5a65" />
<img width="872" height="994" alt="image" src="https://github.com/user-attachments/assets/755c6ff6-5a16-40d4-bda1-2e6fb7f72b56" />
<img width="625" height="1004" alt="image" src="https://github.com/user-attachments/assets/010a4e3d-1f87-4200-9434-0168ceec1b40" />
<img width="761" height="999" alt="image" src="https://github.com/user-attachments/assets/930aab30-ce55-437b-9dcc-af394b331533" />
<img width="2496" height="1040" alt="image" src="https://github.com/user-attachments/assets/caa55980-5508-4f2d-a7f1-206a7e5b5011" />



<!-- Screenshots/demo placeholder: add images under docs/assets/ (or similar) and link them here with relative paths once available. Do not invent a deployment URL. -->

You can run the full stack locally with Docker Compose and open the routes listed in [Usage Guide](#usage-guide). The landing page, companion flow, conversation screen, pricing page, and admin console are all reachable from `http://localhost:3000`.

---

## Features

### Implemented

| Area | Feature | What it does |
|---|---|---|
| Authentication | Clerk sign-in/sign-up | Email/social authentication, email verification, password reset, MFA, and lockout are handled by Clerk. |
| Authentication | Local user provisioning | The backend maps a verified Clerk session JWT to a canonical local `users` row keyed by `clerk_user_id`. |
| Authentication | Clerk webhook sync | `user.created`, `user.updated`, and `user.deleted` are verified with Svix signing and synced. Deletion routes through the DPDP erasure path. |
| Onboarding | 18+ age gate | A date of birth is captured after sign-up and enforced server-side. Use of the app is blocked until an eligible date of birth is confirmed. |
| Companions | Create/list/get | A user can create and view companions. The self-serve flow only accepts `is_deceased=true`. |
| Companions | Living-person block | `is_deceased=false` is rejected with `403`, audited, and not persisted. Living-person onboarding would require a separate review process that is not built. |
| Companions | Consent gate | A companion starts as `pending_verification`. Storing an estate/next-of-kin/self-legacy consent activates it and seeds persona memories. |
| Voice | Voice cloning | One 30–50 second recording (≤5 MB; WAV, MP3, M4A, AAC, FLAC, OGG, Opus, or WebM) is uploaded and cloned through Fish Audio. |
| Voice | One-time greeting | Once a voice model is ready, the companion can speak a short auto-greeting exactly once. |
| Voice | Voice deletion | Deleting a voice model marks it unusable immediately, frees the tier slot, and schedules provider/blob erasure after the retention grace window. |
| Conversation | Voice-note pipeline | Presigned S3 upload → STT → input moderation → memory retrieval → LLM → output moderation → TTS → delivery. |
| Conversation | Streaming replies | The default path streams LLM tokens, groups them into sentences, synthesizes each unit with Fish Audio, and streams MP3 chunks to the client. |
| Conversation | Buffered fallback | When streaming is disabled, arq workers run the full `stt_job → llm_job → tts_job` state machine. |
| Conversation | Progress events | Job-state and delivery events are published over Redis pub/sub and delivered through a WebSocket, with client polling as a fallback. |
| Conversation | Reply language | Each message requests a Hindi or English reply. STT auto-detects the spoken language; the reply language is steered separately. |
| Memory | Persona card | A stable persona card is assembled from the companion profile and used as the prompt prefix. |
| Memory | Multi-layer memory | Profile, semantic, and episodic rows live in Postgres. Episodic memory is written back after each reply. |
| Memory | Hybrid retrieval | Uses pgvector cosine search when an embedding provider is configured; otherwise falls back to Postgres `pg_trgm` lexical similarity. Candidates are re-ranked by similarity, salience, and recency. |
| Safety | Keyword layer | Deterministic self-harm and living-target patterns always run locally and never fail open. |
| Safety | Hosted classifier | Optional Groq `gpt-oss-safeguard-20b` classification maps MLCommons hazard codes to internal categories. |
| Safety | Self-harm routing | A self-harm disclosure is logged as a `safety_events` row and produces a break-character support response with the configured Indian helpline. It never continues the persona. |
| Safety | Output guardrails | The deterministic layer blocks claims of being alive/conscious/in an afterlife. The hosted classifier adds regulated-advice and broader hazard coverage; production requires it. The streaming path runs the deterministic keyword check per spoken unit. |
| Safety | Human review | Held messages, abuse reports, consent disputes, and appeals appear in the admin queues. Non-self-harm safety events can create flags and warnings. |
| Rights | Data export | `GET /api/account/export` returns a DPDP access/export bundle. |
| Rights | Erasure | `DELETE /api/account` soft-deletes immediately, propagates account closure to Clerk, and schedules irreversible purge after the retention window. |
| Rights | Grievance and appeals | Users can file DPDP grievances and appeal suspensions. |
| Rights | Public objection channel | Heirs and third parties can object to a companion without an account via `/object`. |
| Billing | Free and premium tiers | Free: 5 voice messages/day, up to 3 companions, 1 live voice per companion, view-once replies. Premium: 200/day, up to 3 companions, 3 live voices per companion, standard/replayable delivery at the API level (the current UI is live-only). |
| Billing | Razorpay | One-time order verification plus recurring subscriptions, plan switching at period end, one-click cancellation, grace periods, payment history, and idempotent webhooks. |
| Billing | Local simulator | With `BILLING_PROVIDER=fake` outside production, a local checkout page simulates activation without real payment. |
| Admin | Separate admin identity | Admins are stored in `admin_users`, never as end users. Login is password + TOTP MFA. |
| Admin | RBAC | Roles: `super_admin`, `moderator`, `support_agent`, with permission checks per action. |
| Admin | Operations console | Metrics, budget alerts, users, suspended accounts, appeals, review queue, disputes, abuse reports, DPDP rights requests, audit log, and runtime config. |
| Operations | RLS | Postgres Row-Level Security is enforced on users, companions, conversations, messages, memories, voice models, and voice samples, with an explicit bypass scope for workers/admin/public paths. |
| Operations | Audit log | Sensitive actions write an `audit_log` row with actor type and metadata. |
| Operations | Rate limiting | Per-IP/per-account limits protect auth, admin login, MFA, disputes, webhooks, and other sensitive paths. |
| Operations | Observability | Structured JSON logs, Prometheus metrics (`/metrics` on API and worker), Sentry (optional), `/health`, and `/ready`. |
| Operations | Production guards | The API refuses to boot in production with missing Clerk/JWT secrets, moderation disabled, or wildcard credentialed CORS. |

### Experimental, partial, or known-incomplete

| Area | Current state |
|---|---|
| Writings/SMS ingestion | Companions are built from a typed bio, personality traits, and one voice sample. There is no upload/parse/redaction path for letters, SMS, or other writings. |
| Companion management | There is no edit/rename/delete endpoint for a companion itself. Only the voice model can be deleted. |
| Conversation history | The UI is live-only: the previous reply is cleared on the next send, and there is no history/replay screen. Episodic memory is still persisted server-side. |
| Consent verification | Consent is self-attested; `attestation` and optional `doc_url` are not independently verified and there is no mandatory human review before activation. |
| DPDP correction | Export and erasure exist; a correction endpoint does not. |
| Summary memory | The `summary` memory kind is modeled and retrievable, but no periodic consolidation job exists. |
| Semantic retrieval | The pgvector path is implemented, but embeddings are only used when `EMBEDDING_API_KEY` is configured. The default local setup uses lexical retrieval. |
| Voiceprint detection | Living-person protection is based on the self-declared `is_deceased` flag and keyword checks. There is no acoustic voiceprint or public-figure matching. |
| Encryption at rest | No application-level KMS/envelope encryption is implemented. Storage and database encryption depend on the managed provider. |
| Audio watermark | Generated audio is AI-labeled via object metadata, the `X-AI-Generated` response header, a UI label, and a stored watermark ID. The current production paths intentionally do not modify the audio bytes. |
| Self-harm UI | The backend emits a break-character support response, but the current conversation event type and UI do not render it; the user sees a generic review state. This must be fixed before production. |
| Streaming safety | Per-sentence streaming checks use the deterministic keyword layer only. The hosted classifier is not run per sentence. |
| PWA | No manifest, service worker, or offline shell is present. |
| CI / lint | No `.github/workflows/` exists. The frontend has no ESLint configuration and `npm run lint` currently prompts for setup; the backend has no committed ruff/black/mypy configuration. |
| E2E script | `backend/scripts/e2e_smoke.py` predates the Clerk migration and still assumes legacy email/password signup. Treat it as a reference, not a working test today. |

---

## Technology Stack

| Category | Technology | Role in the project |
|---|---|---|
| Language | Python 3.11+ | Backend API, workers, migrations, tests. |
| Language | TypeScript 5 | Next.js frontend and shared client helpers. |
| Backend framework | FastAPI 0.115 | Async REST API, WebSocket endpoint, dependency-injected auth/ownership/entitlements. |
| Backend validation | Pydantic 2.10 / pydantic-settings | Request/response schemas and environment configuration. |
| ORM / migrations | SQLAlchemy 2.0 async, Alembic 1.14 | Async persistence and 25 sequential schema migrations. |
| Background jobs | arq 0.26 | Redis-backed job queue for STT, LLM, TTS, memory write-back, voice cloning, purge, billing, and budget crons. |
| Queue/cache/pubsub | Redis 7 | arq queue, WebSocket pub/sub, distributed TTS concurrency gate, rate limiting, lockout, and cooldowns. |
| Database | PostgreSQL 16 + pgvector | Relational data, RLS, vector embeddings (HNSW), and trigram lexical search. |
| Object storage | S3-compatible API / MinIO | Private audio and voice-sample storage; browser access only through short-lived presigned URLs or authenticated streaming. |
| Frontend | Next.js 15.5 (App Router), React 19 | Server/client pages, routing, auth middleware, and the conversation UI. |
| Styling | Tailwind CSS 3.4, custom CSS | Responsive app UI, landing page, legal page, and dark-twilight theme. |
| Auth | Clerk 6 | Hosted sign-in/up, sessions, social login, MFA, verification, and password reset. Backend verifies Clerk JWTs and receives Svix webhooks. |
| STT | Groq Whisper Large v3 Turbo | Hosted speech-to-text with automatic language detection. |
| LLM | DeepSeek V4 | Premium persona replies via the hosted DeepSeek API. |
| LLM | Groq Llama 3.1 8B Instant | Fast free-tier replies and one-time greetings (current implementation routing). |
| LLM fallback | Google Gemini 2.5 Flash | Circuit-breaker fallback when `ALLOW_EXTERNAL_LLM=true` and a Gemini key is configured. |
| TTS / cloning | Fish Audio | Hosted voice cloning and streaming speech synthesis. |
| Moderation | Groq `gpt-oss-safeguard-20b` | Optional hosted layer-2 hazard classifier; required by the production boot guard. |
| Embeddings | Pluggable hosted API | OpenAI, Jina, Voyage, or Gemini-compatible embeddings; no key means lexical retrieval. |
| Payments | Razorpay | India-focused orders/subscriptions, webhooks, recurring consent, and payment history. |
| Email | SMTP / generic HTTP / log | Transactional notifications, receipts, and lifecycle alerts. |
| Observability | structlog, Prometheus, Sentry | Structured logs, metrics, and optional error reporting. |
| Testing | pytest, pytest-asyncio, Vitest, jsdom | Backend unit/service tests and frontend helper tests. |
| Packaging | Docker, Docker Compose | Six-service local stack: Postgres, Redis, MinIO, API, worker, frontend. |
| Orchestration | Kubernetes manifests (templates) | API/worker Deployments, Services, HPA, Ingress, ConfigMap, Secret template, and migration init container. |

**Runtime model note:** the application runtime uses DeepSeek and hosted AI APIs. Claude Code is a development tool only; there is no Anthropic/Claude runtime key or API call in the application.

Current routing differs slightly from the original spec: free-tier and greeting replies use Groq Llama 3.1 8B Instant for latency, while premium replies use DeepSeek V4 Flash by default. The model names are configurable through `LLM_MODEL_FREE` and `LLM_MODEL_PREMIUM`.

---

## Architecture and How It Works

### System architecture

```mermaid
flowchart LR
  subgraph Client["Browser: Next.js 15"]
    UI["App Router pages"]
    REC["Push-to-talk recorder"]
    PLAY["Audio playback"]
  end

  subgraph API["FastAPI API"]
    AUTH["Auth, RLS scope, entitlements"]
    ROUTES["REST routers"]
    STREAMS["Streaming / audio endpoints"]
  end

  subgraph Data["Data plane"]
    PG[("PostgreSQL 16 + pgvector")]
    REDIS[("Redis 7")]
    S3[("S3 / MinIO private bucket")]
  end

  subgraph Workers["arq workers"]
    STT["STT + input moderation"]
    LLM["LLM + output moderation"]
    TTS["TTS / voice clone"]
    CRON["Retention, billing, budget crons"]
  end

  subgraph Providers["Hosted providers"]
    CLERK["Clerk auth"]
    GROQ["Groq: STT, Llama, moderation"]
    DEEPSEEK["DeepSeek LLM"]
    GEMINI["Gemini fallback"]
    FISH["Fish Audio TTS"]
    EMB["Embeddings API"]
    RAZORPAY["Razorpay"]
  end

  UI --> ROUTES
  REC --> UI
  PLAY --> UI
  ROUTES --> AUTH
  AUTH <--> CLERK
  ROUTES --> PG
  ROUTES --> REDIS
  ROUTES --> S3
  STREAMS --> FISH
  STREAMS --> DEEPSEEK
  ROUTES --> REDIS
  REDIS --> Workers
  STT --> GROQ
  STT --> S3
  LLM --> DEEPSEEK
  LLM --> GROQ
  LLM --> GEMINI
  LLM --> PG
  TTS --> FISH
  TTS --> S3
  CRON --> PG
  RAZORPAY --> ROUTES
  REDIS -.->|"WebSocket events"| UI
```

### Component responsibilities

- **`frontend/app`** — Next.js App Router pages for the landing page, Clerk auth, 18+ onboarding, companions, conversation, voice upload, pricing, settings, support, public objections, legal, and admin.
- **`frontend/components`** — Push-to-talk recorder, audio players, greeting player, job-status poller, billing UI, admin panels, landing page, and shared UI primitives.
- **`frontend/lib`** — Typed API clients (`api.ts`, `adminApi.ts`), Clerk-to-backend token bridge, recording helpers, theme handling, and conversation event subscription.
- **`backend/app/api`** — Thin HTTP routers for auth, companions, messages, conversations, billing, DPDP rights, and admin operations. Routers enforce authentication, ownership, entitlement, and audit logging.
- **`backend/app/services`** — Business logic: consent, entitlements, memory/prompt assembly, moderation, billing, rights, review queues, usage, budgets, notifications, and streaming reply orchestration.
- **`backend/app/workers/pipeline`** — Idempotent arq jobs for STT, LLM, TTS, voice cloning, episodic memory write-back, retention/purge, billing downgrades, and budget checks.
- **`backend/app/workers/providers`** — Protocol interfaces and concrete/ fake implementations for STT, TTS, LLM, embeddings, moderation, and billing.
- **`backend/app/core`** — Configuration, async DB/RLS, Redis, S3, Clerk verification, JWT/security, rate limiting, TTS gate, observability, metrics, and admin security/RBAC.
- **`backend/app/models`** — SQLAlchemy ORM tables and Pydantic request/response schemas.
- **`infra`** — RLS role bootstrap SQL and Kubernetes deployment templates.
- **`docs`** — Authoritative build spec, billing reference, DPDP transfer record, breach playbook, and migration/UX plan.

### Key flows

#### 1. Authentication and onboarding

1. The browser signs in through Clerk.
2. The frontend registers a per-request Clerk token getter and sends `Authorization: Bearer <clerk-session-token>` to the API.
3. The API verifies the Clerk RS256 JWT offline with `CLERK_JWT_KEY`, resolves or provisions the local `users` row by `clerk_user_id`, rejects deleted/suspended accounts, and scopes the request for RLS.
4. If `dob` is missing, the frontend redirects to `/dob`; the backend enforces 18+ before accepting the date of birth.
5. Clerk lifecycle webhooks keep the local user row in sync. `user.deleted` enters the existing soft-delete → purge path.

#### 2. Companion creation, consent, and voice cloning

1. `POST /api/companions` creates a `pending_verification` companion. Living-person requests are rejected and audited.
2. `POST /api/companions/{id}/consent` stores an estate/next-of-kin/self-legacy consent record and activates the companion.
3. Activation seeds a profile memory (persona card) and semantic memories from the bio and personality traits.
4. `POST /api/companions/{id}/voice` validates and stores one 30–50 second sample, creates a `training` voice model, and enqueues `clone_voice_job`.
5. The worker calls Fish Audio, stores the provider `voice_id`, and marks the model `ready`.
6. Deleting a voice model marks it `deleted`, sets `purge_after`, and a daily cron removes the provider model and source samples after `RETENTION_DAYS`.

#### 3. Voice-note conversation

The default deployment uses streaming replies (`STREAMING_REPLIES=true`). A buffered worker pipeline remains available as a fallback.

```mermaid
sequenceDiagram
  autonumber
  actor U as User
  participant F as Next.js
  participant A as FastAPI
  participant S as S3 / MinIO
  participant Q as Redis / arq
  participant W as Worker
  participant P as Hosted providers

  U->>F: Hold to record, release to send
  F->>A: POST /api/messages/upload-url
  A->>A: Verify auth, ownership, consent, quota
  A-->>F: message_id, conversation_id, presigned PUT
  F->>S: PUT audio
  F->>A: POST /api/messages/{id}/submit
  A->>Q: enqueue stt_job
  Q->>W: stt_job
  W->>S: GET audio bytes
  W->>P: Groq Whisper transcription
  P-->>W: transcript
  W->>W: Input moderation
  alt Streaming path (default)
    W-->>F: WS stream_ready + short-lived token
    F->>A: GET /api/messages/{id}/reply-stream?token=...
    A->>P: Stream LLM tokens
    A->>P: Fish TTS per sentence group
    A-->>F: MP3 chunks as synthesized
    A->>S: Persist final reply audio
    A-->>F: WS delivered (live stream already played)
  else Buffered path
    W->>Q: enqueue llm_job
    Q->>W: llm_job
    W->>P: LLM completion
    W->>W: Output moderation
    W->>Q: enqueue tts_job
    Q->>W: tts_job
    W->>P: Fish TTS
    W->>S: Persist reply audio
    W-->>F: WS delivered + playback token
    F->>A: GET /api/messages/{reply_id}/stream?token=...
    A->>S: Stream audio
    A->>A: Mark view-once consumed
  end
```

**Pipeline state machine:**

```text
received → transcribed → moderated_in → retrieved → generated
        → moderated_out → synthesized → delivered
        | failed | held_for_review | stt_failed_retryable
```

Each stage re-checks the expected prior state, so retries and duplicate deliveries are idempotent. STT transient failures retry with exponential backoff (up to 5 attempts); exhausted/permanent failures degrade to `stt_failed_retryable` while preserving the uploaded audio.

#### 4. View-once delivery

- Free-tier reply rows are created with `delivery_mode='view_once'` and `purge_after = now + VIEW_ONCE_TTL_HOURS`.
- In the buffered path, the reply is stored with a single-use `playback_token`. In the streaming path, the live stream is the single play; the stored row is marked consumed and no playback token is returned to the client.
- `GET /api/messages/{id}/stream` requires session auth, ownership, the matching token, an unconsumed row, and an unexpired TTL.
- The token is consumed before streaming to avoid replay races. A second request returns `410 Gone`.
- Audio is streamed inline with `Content-Disposition: inline`, `Cache-Control: no-store`, `X-AI-Generated: true`, and `Referrer-Policy: no-referrer`.
- A purge cron removes consumed/expired blobs and nulls `audio_s3_key`.
- This is **best effort**: the UI says “Plays once · no in-app download” and never claims the audio cannot be saved or recorded.

#### 5. Billing lifecycle

1. `GET /api/billing/plans` returns the public monthly/annual catalog.
2. `POST /api/billing/order` + `POST /api/billing/verify` support Razorpay Standard Checkout.
3. `POST /api/billing/subscribe` creates a recurring subscription with explicit, non-pre-ticked consent.
4. The tier flips only after a signature-verified webhook (`subscription.*`). Events are idempotent on `provider_event_id`.
5. `active` grants premium; `pending` keeps premium during a configurable grace window; `halted`/`cancelled`/`completed`/`expired`/`paused` downgrade.
6. Plan switches happen at period end with `start_at` to avoid double charging. Cancellation is one-click and keeps access until period end.
7. An hourly cron downgrades accounts whose grace window lapsed.

#### 6. Retention and erasure

- View-once audio is purged every 5 minutes when consumed or past TTL.
- Deleted voice models and soft-deleted accounts are erased daily at 03:00 UTC after `RETENTION_DAYS` (default 60).
- Unverified legacy signups are swept daily (legacy pre-Clerk flow).
- Account purge cancels any live subscription, deletes provider voice models, removes S3 blobs, and completes the DPDP erasure request.
- Consent records, safety events, and audit logs are retained longer as disclosed in the privacy notice.

---

## Project Structure

```text
.
├── backend/
│   ├── app/
│   │   ├── main.py                 # FastAPI app, lifespan guards, health/ready/metrics
│   │   ├── api/                    # REST + WebSocket routers
│   │   │   ├── admin/              # Admin auth, metrics, queues, review, rights, audit, config
│   │   │   ├── auth.py             # Clerk ↔ local-user seam, 18+ onboarding, webhook
│   │   │   ├── companions.py       # Companion creation, consent, voice, greeting
│   │   │   ├── messages.py         # Upload URL, submit, status, stream, reply-stream
│   │   │   ├── conversations.py    # WebSocket delivery events
│   │   │   ├── account.py          # DPDP rights, reports, disputes, grievances, appeals
│   │   │   └── billing.py          # Razorpay orders, subscriptions, webhooks
│   │   ├── core/                   # Config, DB/RLS, Redis, S3, security, Clerk, metrics, rate limit
│   │   ├── models/                 # SQLAlchemy ORM + Pydantic schemas
│   │   ├── services/               # Consent, entitlements, memory, moderation, billing, rights, etc.
│   │   └── workers/
│   │       ├── pipeline/           # STT, LLM, TTS, memory write-back, purge, billing, budget crons
│   │       ├── providers/          # Groq, Fish, DeepSeek, Gemini, embeddings, Razorpay, fakes
│   │       └── settings.py         # arq WorkerSettings and cron schedule
│   ├── migrations/
│   │   ├── env.py                  # Alembic environment (reads DATABASE_URL)
│   │   └── versions/               # 25 sequential migrations (including RLS)
│   ├── scripts/                    # Admin provisioning, embedding backfill, probes, load/e2e helpers
│   ├── tests/                      # pytest suite (47 modules, 367 test functions)
│   ├── Dockerfile                  # Python 3.11-slim API/worker image
│   ├── alembic.ini
│   ├── pytest.ini
│   ├── requirements.txt
│   └── requirements-dev.txt
├── frontend/
│   ├── app/
│   │   ├── (auth)/login/           # Clerk <SignIn>
│   │   ├── (auth)/signup/          # Clerk <SignUp>
│   │   ├── admin/                  # Admin login + operations dashboard
│   │   ├── companions/             # List, create, conversation, voice upload
│   │   ├── legal/                  # Public disclaimer, privacy, terms, refunds
│   │   ├── pricing/                # Premium plans + Razorpay checkout
│   │   ├── settings/               # Account, security, plan, export/delete
│   │   ├── support/                # Abuse report, grievance, appeal
│   │   ├── object/                 # Public heir/third-party objection form
│   │   ├── dob/                    # 18+ onboarding gate
│   │   ├── layout.tsx
│   │   ├── providers.tsx           # Clerk bridge, theme, auth error surface
│   │   └── page.tsx                # Landing page
│   ├── components/
│   │   ├── admin/                  # Metrics, budget, users, review, queues, audit, config panels
│   │   ├── landing/                # Marketing landing page
│   │   ├── PushToTalkButton.tsx
│   │   ├── VoiceNote.tsx
│   │   ├── CompanionAudio.tsx
│   │   ├── GreetingPlayer.tsx
│   │   └── ...
│   ├── lib/
│   │   ├── api.ts                  # User API client + Clerk token bridge
│   │   ├── adminApi.ts             # Admin API client
│   │   ├── recording.ts            # MediaRecorder/MIME handling
│   │   ├── useConversationEvents.ts
│   │   └── ...
│   ├── middleware.ts               # Clerk route protection
│   ├── next.config.ts
│   ├── package.json / package-lock.json
│   ├── tailwind.config.ts
│   ├── tsconfig.json
│   ├── vitest.config.ts
│   └── Dockerfile                  # Dev server image (node:22-slim)
├── docs/
│   ├── spec.md                     # Authoritative build specification
│   ├── billing.md                  # Razorpay plan and webhook state machine
│   ├── dpdp-transfers.md           # Cross-border processor record
│   ├── breach-playbook.md          # Incident response runbook
│   └── linux-mint-migration-and-ux-plan.md
├── infra/
│   ├── db/rls_app_role.sql         # Non-superuser RLS application role
│   └── k8s/                        # API/worker Deployment, HPA, Service, Ingress, ConfigMap, Secret
├── docker-compose.yml              # postgres, redis, minio, api, worker, frontend
├── .env.example                    # Local environment template (no real secrets)
├── .gitignore                      # Ignores .env, caches, node_modules, build output
├── CLAUDE.md                       # Working conventions and golden rules
├── AUDIT_REPORT.md                 # Architecture/compliance/security audit
├── MASTER_AUDIT.md                 # Consolidated audit findings
├── SECURITY_AUDIT.md
├── PERFORMANCE_REPORT.md
├── TEST_COVERAGE_AUDIT.md
├── FEATURE_GAPS.md
├── BUSINESS_AUDIT.md
├── LAUNCH_READINESS.md             # Scale/launch failure analysis
└── README.md
```

Generated or local-only directories such as `node_modules/`, `.next/`, `__pycache__/`, `.pytest_cache/`, and virtual environments are intentionally omitted from the tree.

---

## Prerequisites

### Required

- **Python 3.11+** for the backend. The Docker image uses `python:3.11-slim`.
- **Node.js 22** and npm for the frontend. The Docker image uses `node:22-slim`.
- **Docker Engine + Docker Compose v2** for the recommended local stack.
- **PostgreSQL 16 with pgvector** if you run the backend outside Docker. The Compose stack uses `pgvector/pgvector:pg16`.
- **Redis 7** if you run the backend outside Docker.
- **S3-compatible object storage** (MinIO in Compose, or a managed private bucket).
- **Clerk account** — current sign-in/sign-up requires Clerk keys. Without them, the UI cannot authenticate users.
- **API accounts/keys** for the features you want to exercise:
  - Groq — STT, free-tier Llama replies, and moderation.
  - DeepSeek — premium replies.
  - Fish Audio — voice cloning and TTS.
  - Optional: Gemini (fallback), an embeddings provider, Razorpay, SMTP/HTTP email.

### Operating system

Linux and macOS are the primary development environments. Windows users should use WSL2; Node modules installed under native Windows may contain incompatible native binaries after moving the repository. The repository enforces LF line endings via `.gitattributes`.

### Hardware

No special hardware is required. Voice recording uses the browser microphone; browsers require a secure context (`localhost` is treated as secure) or HTTPS in production.

---

## Installation and Setup

### Option A — Docker Compose (recommended)

1. **Clone the repository**

   ```bash
   git clone <repository-url>
   cd reconnect-2
   ```

2. **Create your local environment file**

   ```bash
   cp .env.example .env
   ```

   Edit `.env` and fill in the provider keys. Never commit `.env`; it is ignored by Git.

3. **Choose a first-run configuration**

   For a local UI run, Clerk is required:

   ```dotenv
   APP_ENV=development
   NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_...
   CLERK_SECRET_KEY=sk_test_...
   CLERK_JWT_KEY="-----BEGIN PUBLIC KEY-----\n...\n-----END PUBLIC KEY-----"
   CLERK_WEBHOOK_SIGNING_SECRET=whsec_...
   NEXT_PUBLIC_CLERK_SIGN_IN_URL=/login
   NEXT_PUBLIC_CLERK_SIGN_UP_URL=/signup
   ```

   For a backend/API-only run without live AI calls, you can use the fakes:

   ```dotenv
   STT_PROVIDER=fake
   TTS_PROVIDER=fake
   LLM_PROVIDER=fake
   MODERATION_PROVIDER=fake
   BILLING_PROVIDER=fake
   NOTIFICATIONS_PROVIDER=log
   ```

   `APP_ENV=test` forces all provider factories to their fakes and is intended for tests.

4. **Start the stack**

   ```bash
   docker compose up --build
   ```

   The API container runs `alembic upgrade head` before starting Uvicorn. The MinIO bucket is created at API startup on a best-effort basis.

5. **Open the services**

   | Service | URL / address |
   |---|---|
   | Frontend | `http://localhost:3000` |
   | API | `http://localhost:8000` |
   | API docs (OpenAPI/Swagger) | `http://localhost:8000/docs` |
   | Health | `http://localhost:8000/health` |
   | Readiness | `http://localhost:8000/ready` |
   | Metrics | `http://localhost:8000/metrics` |
   | MinIO console | `http://localhost:9001` (dev default: `minioadmin` / `minioadmin`) |
   | PostgreSQL | `localhost:5432` |
   | Redis | `localhost:6379` |

6. **Optional: enable Row-Level Security for the runtime role**

   The API and worker connect with `APP_DATABASE_URL` when it is set. To make RLS apply, provision the non-superuser role once:

   ```bash
   docker compose up -d postgres
   docker compose exec -T postgres psql -U reconnect -d reconnect \
     -v app_pw=app_pass -f - < infra/db/rls_app_role.sql
   ```

   Then set `APP_DATABASE_URL` to the matching non-superuser DSN and recreate the API/worker:

   ```dotenv
   APP_DATABASE_URL=postgresql+asyncpg://reconnect_app:app_pass@postgres:5432/reconnect
   ```

   ```bash
   docker compose up -d --force-recreate api worker
   ```

   Leaving `APP_DATABASE_URL` empty is supported: the app falls back to `DATABASE_URL`, which bypasses RLS at the database layer but keeps application-level ownership checks. This is a development convenience, not the intended production posture.

7. **Create an admin (optional, for `/admin`)**

   ```bash
   docker compose exec api python scripts/create_admin.py admin@example.com super_admin
   ```

   The script prints a temporary password and a TOTP provisioning URI. Enroll it in an authenticator app before signing in to `/admin`.

8. **Configure the Clerk webhook (optional locally)**

   Clerk must be able to reach the webhook endpoint. Locally, expose the API through a tunnel (for example ngrok or cloudflared) and register:

   ```text
   https://<your-tunnel-host>/api/auth/clerk/webhook
   ```

   Use the same signing secret in `CLERK_WEBHOOK_SIGNING_SECRET`.

### Option B — Local development without Docker

1. Start PostgreSQL 16 with pgvector, Redis 7, and MinIO (or run only those services with `docker compose up -d postgres redis minio`).

2. Create and activate a Python virtual environment:

   ```bash
   cd backend
   python3 -m venv .venv
   source .venv/bin/activate
   pip install -r requirements-dev.txt
   ```

3. Make configuration available. `Settings` loads `.env` from the current working directory, so either copy the root file or export the variables:

   ```bash
   cp ../.env .env
   # or: set -a; source ../.env; set +a
   ```

4. Run migrations:

   ```bash
   alembic upgrade head
   ```

5. Start the API:

   ```bash
   uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
   ```

6. Start the worker in a second terminal:

   ```bash
   cd backend
   source .venv/bin/activate
   python -m arq app.workers.settings.WorkerSettings
   ```

7. Install and start the frontend:

   ```bash
   cd frontend
   npm ci
   npm run dev
   ```

   For local frontend development outside Compose, create `frontend/.env.local` (ignored by Git) with browser-reachable values:

   ```dotenv
   NEXT_PUBLIC_API_URL=http://localhost:8000
   INTERNAL_API_URL=http://localhost:8000
   NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_...
   NEXT_PUBLIC_CLERK_SIGN_IN_URL=/login
   NEXT_PUBLIC_CLERK_SIGN_UP_URL=/signup
   NEXT_PUBLIC_RAZORPAY_KEY_ID=rzp_test_...
   ```

### Database migrations

```bash
cd backend
alembic upgrade head          # apply all migrations
alembic current               # show the current revision
alembic downgrade -1          # roll back one migration (use with care)
```

The API container runs `alembic upgrade head` automatically in Compose. The Kubernetes API manifest also uses a migration init container; for many replicas, move migrations to a one-shot Job or Helm hook instead.

---

## Environment Variables and Configuration

Configuration is defined in [`backend/app/core/config.py`](backend/app/core/config.py) and loaded from environment variables and a `.env` file. The committed template is [`.env.example`](.env.example). Do not commit a real `.env`.

- Required values below are required for the stated mode (usually production), not necessarily for a fake-provider test run.
- `NEXT_PUBLIC_*` variables are embedded in the browser bundle. Never put secrets in them.
- The `.env.example` template covers the common path but not every advanced setting; this table and `config.py` together are the complete reference.

### Core, database, cache, and storage

| Variable | Required | Default / example | Purpose |
|---|---|---|---|
| `APP_ENV` | Yes | `development` | `development`, `test`, or `production`. `test` forces fake providers. `production` enables boot guards. |
| `DATA_RESIDENCY_REGION` | No | `in-south-1` | Residency label used for documentation/configuration. It does not itself enforce a region. |
| `DATABASE_URL` | Yes | `postgresql+asyncpg://reconnect:pass@postgres:5432/reconnect` | Owner/superuser DSN used by Alembic and as the fallback runtime DSN. |
| `APP_DATABASE_URL` | No | empty | Non-superuser runtime DSN so RLS applies. Empty falls back to `DATABASE_URL` (RLS bypassed). |
| `REDIS_URL` | Yes | `redis://redis:6379/0` | arq queue, pub/sub, rate limiting, TTS gate, lockout, and cooldowns. |
| `S3_ENDPOINT` | Yes | `http://minio:9000` | Internal S3-compatible endpoint used by the API/worker. |
| `S3_PUBLIC_URL` | No | `http://localhost:9000` | Browser-reachable S3 endpoint used when generating presigned URLs. Defaults to `S3_ENDPOINT`. |
| `S3_BUCKET` | Yes | `reconnect-audio` | Private bucket for user audio, reply audio, and voice samples. |
| `S3_ACCESS_KEY` | Yes | `minioadmin` (dev) | S3 access key ID. |
| `S3_SECRET_KEY` | Yes | `minioadmin` (dev) | S3 secret access key. |

### AI providers, voice, and safety

| Variable | Required | Default / example | Purpose |
|---|---|---|---|
| `LLM_PROVIDER` | Yes | `deepseek` | `deepseek` or `fake`. The runtime model family is DeepSeek, never Anthropic/Claude. |
| `DEEPSEEK_API_KEY` | Premium | empty | DeepSeek API key for premium replies. |
| `DEEPSEEK_BASE_URL` | No | `https://api.deepseek.com/v1` | Provider base URL seam for a future in-region/self-hosted DeepSeek endpoint. |
| `LLM_MODEL_FREE` | No | `llama-3.1-8b-instant` | Model used for free-tier replies and greetings. Routed to Groq by model name. |
| `LLM_MODEL_PREMIUM` | No | `deepseek-v4-flash` | Model used for premium replies. |
| `LLM_FALLBACK_MODEL` | No | `gemini-2.5-flash` | Circuit-breaker fallback model. |
| `GROQ_LLM_MODELS` | No | `llama-3.1-8b-instant` | Comma-separated model names routed to Groq. |
| `DEEPSEEK_REASONING_EFFORT` | No | empty | `low`, `medium`, `high`, `max`, `xhigh`, or empty for the provider default. |
| `GEMINI_API_KEY` | No | empty | Enables the hosted Gemini fallback when `ALLOW_EXTERNAL_LLM=true`. |
| `ALLOW_EXTERNAL_LLM` | No | `true` | Auditable switch for external LLM calls. Disabling it removes the Gemini fallback. |
| `STREAMING_REPLIES` | No | `true` | Enables the live LLM-token → per-sentence TTS streaming path. |
| `TTS_MAX_CONCURRENCY` | No | `5` | Account-wide Fish Audio concurrent-stream limit. Excess requests queue rather than immediately failing. |
| `TTS_SLOT_WAIT_SECONDS` | No | `45` | How long a caller waits for a TTS slot before returning `503`. |
| `TTS_SLOT_TTL_SECONDS` | No | `180` | Safety TTL for a stale TTS slot token. |
| `TTS_PROVIDER` | Yes | `fish` | `fish` or `fake`. |
| `FISH_AUDIO_API_KEY` | Yes (for fish) | empty | Fish Audio key for cloning and synthesis. |
| `FISH_BASE_URL` | No | `https://api.fish.audio` | Provider base URL seam. |
| `STT_PROVIDER` | Yes | `groq` | `groq` or `fake`. |
| `GROQ_API_KEY` | Yes | empty | Groq key for Whisper STT, free-tier/greeting Llama, and the moderation classifier. |
| `STT_MODEL` | No | `whisper-large-v3-turbo` | Groq speech-to-text model. |
| `STT_LANGUAGE` | No | empty | Empty auto-detects Hindi/English/code-switching; a code such as `hi` forces that language. |
| `MODERATION_PROVIDER` | Prod | `fake` | `groq_safeguard`, legacy `groq`, or `fake`. Production refuses to boot with `fake`. |
| `MODERATION_MODEL` | No | `openai/gpt-oss-safeguard-20b` | Hosted moderation model. |
| `EMBEDDING_PROVIDER` | No | `openai` | `openai`, `jina`, `voyage`, `gemini`, or `fake`. |
| `EMBEDDING_API_KEY` | No | empty | Enables semantic memory retrieval. No key means lexical retrieval. |
| `EMBEDDING_MODEL` | No | `text-embedding-3-small` | Hosted embedding model name. |
| `EMBEDDING_DIM` | No | `1536` | Must match the model and the database vector dimension. The ORM currently hardcodes `vector(1536)`; change both together. |
| `SELF_HARM_HELPLINE_IN` | No | Tele-MANAS + Vandrevala numbers | Helpline text included in the break-character support response. |

### Authentication, admin, and email

| Variable | Required | Default / example | Purpose |
|---|---|---|---|
| `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY` | Yes (frontend) | `pk_test_...` | Clerk browser publishable key. |
| `CLERK_SECRET_KEY` | Yes (backend, prod) | `sk_test_...` | Clerk Backend API key used to fetch users and delete closed accounts. |
| `CLERK_JWT_KEY` | Yes (backend, prod) | PEM public key | Offline RS256 verification of Clerk session tokens. Newlines may be written as `\n`. |
| `CLERK_ISSUER` | No | empty | Optional issuer binding for session tokens. |
| `CLERK_AUTHORIZED_PARTIES` | No | `http://localhost:3000` | Comma-separated allowed `azp` origins. |
| `CLERK_WEBHOOK_SIGNING_SECRET` | Yes (prod) | `whsec_...` | Svix signing secret for the Clerk webhook. |
| `NEXT_PUBLIC_CLERK_SIGN_IN_URL` | No | `/login` | Clerk sign-in route. |
| `NEXT_PUBLIC_CLERK_SIGN_UP_URL` | No | `/signup` | Clerk sign-up route. |
| `JWT_SECRET` | Yes (prod) | `<long-random-secret>` | HS256 secret for admin sessions and short-lived stream tokens. Generate with `openssl rand -hex 32`. |
| `JWT_ALGORITHM` | No | `HS256` | HS256 algorithm for the project's own admin/stream tokens. |
| `JWT_EXPIRY_MINUTES` | No | `10080` | Legacy user-token lifetime setting; Clerk owns end-user sessions. |
| `NEXTAUTH_SECRET` | No | empty | Legacy fallback used by `effective_jwt_secret` if `JWT_SECRET` is unset. Set `JWT_SECRET` in production. |
| `NEXTAUTH_URL` | No | `http://localhost:3000` | Frontend base URL used by the dev billing simulator. |
| `GOOGLE_CLIENT_ID` / `GOOGLE_CLIENT_SECRET` | No | empty | Legacy pre-Clerk Google auth settings; not used by the current Clerk flow. |
| `ADMIN_TOTP_ENC_KEY` | Prod recommended | empty | Fernet key for encrypting admin TOTP secrets. Dev falls back to a JWT-derived key. Generate with `python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"`. |
| `ADMIN_TOTP_ISSUER` | No | `Starecho Admin` | Issuer shown in the authenticator app. |
| `ADMIN_JWT_EXPIRY_MINUTES` | No | `480` | Admin session lifetime (8 hours). |
| `NOTIFICATIONS_PROVIDER` | No | `log` | `log`, `http`, or `smtp`. Use a real provider in production. |
| `NOTIFICATIONS_FROM_EMAIL` | No | `no-reply@starecho.app` | Sender address. |
| `NOTIFICATIONS_FROM_NAME` | No | `Starecho` | Sender display name. |
| `NOTIFICATIONS_TIMEOUT_SECONDS` | No | `10` | HTTP/SMTP send timeout. |
| `NOTIFICATIONS_HTTP_URL` | For `http` | empty | Transactional-email JSON endpoint. |
| `NOTIFICATIONS_HTTP_API_KEY` | For `http` | empty | Bearer key for the HTTP email provider. |
| `SMTP_HOST` | For `smtp` | `smtp.hostinger.com` (example) | Mail relay host. |
| `SMTP_PORT` | For `smtp` | `587` (STARTTLS) or `465` (implicit TLS) | SMTP port. |
| `SMTP_USERNAME` / `SMTP_PASSWORD` | For `smtp` | empty | SMTP credentials. |
| `SMTP_SSL` | No | `false` | Use implicit TLS; set `true` for port 465. |
| `SMTP_STARTTLS` | No | `true` | Use STARTTLS; set `false` when `SMTP_SSL=true`. |

### Billing, retention, and legacy settings

| Variable | Required | Default / example | Purpose |
|---|---|---|---|
| `BILLING_PROVIDER` | Yes | `razorpay` | `razorpay` or `fake`. The fake provider enables the local simulator only outside production. |
| `RAZORPAY_KEY_ID` / `RAZORPAY_KEY_SECRET` | For Razorpay | empty | Server-side Razorpay API credentials. |
| `RAZORPAY_WEBHOOK_SECRET` | For Razorpay | empty | Shared secret used to verify webhook HMAC signatures. |
| `RAZORPAY_PLAN_ID_MONTHLY` | For subscriptions | empty | Razorpay recurring plan ID for ₹499/month. |
| `RAZORPAY_PLAN_ID_ANNUAL` | For subscriptions | empty | Razorpay recurring plan ID for ₹4,990/year. |
| `BILLING_GRACE_PERIOD_DAYS` | No | `7` | Premium retention after a failed renewal before downgrade. |
| `NEXT_PUBLIC_RAZORPAY_KEY_ID` | Yes (frontend checkout) | `rzp_test_...` | Public Razorpay key ID for browser checkout. Never expose the secret. |
| `VIEW_ONCE_TTL_HOURS` | No | `24` | View-once audio lifetime before purge. |
| `RETENTION_DAYS` | No | `60` | Grace window before irreversible erasure of deleted accounts/voice models. |
| `EMAIL_VERIFICATION_REQUIRED` | Legacy | `true` | Retained from the pre-Clerk email auth flow. Clerk now owns verification. |
| `EMAIL_VERIFY_TTL_HOURS` | Legacy | `24` | Legacy verification-token TTL. |
| `PASSWORD_RESET_TTL_HOURS` | Legacy | `1` | Legacy reset-token TTL. |
| `UNVERIFIED_ACCOUNT_TTL_DAYS` | Legacy | `7` | Sweep window for legacy unverified accounts. |
| `DISPOSABLE_API_ENABLED` | Legacy | `true` | Enables the validator.pizza disposable-email check used by the legacy signup flow. |
| `DISPOSABLE_API_COOLDOWN_SECONDS` | Legacy | `600` | Cooldown after the disposable-email API rate-limits the app. |

### Observability, CORS, and frontend

| Variable | Required | Default / example | Purpose |
|---|---|---|---|
| `SERVICE_VERSION` | No | `0.1.0` | Version reported in startup logs. |
| `LOG_LEVEL` | No | `INFO` | `DEBUG`, `INFO`, `WARNING`, or `ERROR`. |
| `LOG_JSON` | No | `true` | JSON logs for aggregation; `false` for human-readable dev logs. |
| `METRICS_ENABLED` | No | `true` | Exposes `/metrics` and starts the worker metrics server. |
| `WORKER_METRICS_PORT` | No | `9100` | Worker Prometheus scrape port. |
| `SENTRY_DSN` | No | empty | Empty disables Sentry. |
| `SENTRY_TRACES_SAMPLE_RATE` | No | `0.0` | `0` means errors only, no tracing. |
| `CORS_ORIGINS` | Yes | `http://localhost:3000` | Comma-separated explicit origins. `*` is rejected in production with credentials. |
| `TRUST_PROXY_HEADERS` | No | `false` | Set `true` only behind a trusted reverse proxy/load balancer that sets `X-Forwarded-For`. |
| `NEXT_PUBLIC_API_URL` | Yes (frontend) | `http://localhost:8000` | Browser-reachable API base URL. |
| `INTERNAL_API_URL` | No | `http://api:8000` | Server-side Next.js → FastAPI URL inside the Docker network. |

### Configuration files and defaults

- `.env.example` — safe local template.
- `backend/app/core/config.py` — authoritative setting names, defaults, and validation.
- Treat env-file values as literal: do not append a `# comment` after a value on the same line. Recreate containers after changing `.env`, because environment variables are read at process start.
- `docker-compose.yml` — Compose service wiring and the local Postgres/Redis/MinIO defaults.
- `backend/alembic.ini` / `backend/migrations/env.py` — migration configuration; the database URL comes from `DATABASE_URL`, not the INI file.
- `frontend/.env.local` — local frontend values when not using Compose (ignored by Git).
- `infra/k8s/configmap.yaml` and `infra/k8s/secret.example.yaml` — production templates. Real secrets must be created out-of-band.

---

## Usage Guide

### Main UI routes

| Route | Purpose |
|---|---|
| `/` | Public landing page. Signed-in users are redirected to `/companions`. |
| `/login` | Clerk sign-in. |
| `/signup` | Clerk sign-up. |
| `/dob` | Post-signup 18+ date-of-birth gate. |
| `/companions` | List the signed-in user's companions. |
| `/companions/new` | Create a companion, then store consent. |
| `/companions/[id]` | Voice conversation screen (push-to-talk, greeting, reply playback). |
| `/companions/[id]/voice` | Upload/replace a 30–50 second voice recording. |
| `/pricing` | Premium plans and Razorpay checkout. |
| `/settings` | Account, password/security (Clerk), plan, export, and account deletion. |
| `/support` | Abuse report, DPDP grievance, and suspension appeal forms. |
| `/object` | Public heir/third-party objection form; no account required. |
| `/legal` | Disclaimer, privacy policy, terms, and refund/cancellation information. |
| `/admin/login` | Admin password + TOTP login. |
| `/admin` | Internal operations dashboard (requires an admin session). |

### Typical end-to-end workflow

1. Sign up or sign in through Clerk.
2. Confirm your date of birth on `/dob`; the backend enforces 18+.
3. Create a companion on `/companions/new`. Only deceased subjects are accepted in the self-serve flow.
4. Store an estate/next-of-kin/self-legacy consent record. This activates the companion and seeds persona memories.
5. Upload one 30–50 second voice recording on the voice page. Wait for the `training` voice model to become `ready`.
6. Open the conversation. If eligible, the one-time cloned-voice greeting plays.
7. Hold the microphone button to record, then send. The reply arrives asynchronously as a voice note.
8. On the free tier, replies are view-once. Premium rows use standard delivery at the API level, but the current conversation UI does not keep history or provide a replay screen.
9. Use `/settings` to export data, delete the account, or manage premium.
10. Use `/support` for abuse reports, grievances, or appeals. Use `/object` if you are a third party objecting to a companion.

### Tier behavior (current code)

| Capability | Free | Premium |
|---|---|---|
| Voice messages per day | 5 | 200 |
| Companions | up to 3 | up to 3 |
| Live voice models per companion | 1 | 3 |
| Reply model | Groq Llama 3.1 8B Instant | DeepSeek V4 Flash (default) |
| Reply delivery | View-once (`plays once · no in-app download`) | Standard/replayable at the API level; the current UI is live-only |
| Memory retrieval | Shared memory service; semantic only when embeddings are configured | Same |
| Rights/safety | Free | Free |

Prices are defined in `backend/app/services/billing_plans.py`: ₹499/month and ₹4,990/year. Both grant the same premium tier.

### Local billing simulator

With `BILLING_PROVIDER=fake` and `APP_ENV` not equal to `production`:

1. Open `/pricing` and choose a plan.
2. The app creates a fake subscription and points checkout at `/api/billing/dev/checkout/{subscription_id}`.
3. Click “Pay & activate Premium.” The simulator applies an activation event and writes a synthetic payment-history row.
4. The dev endpoints return `404` automatically when the real Razorpay provider or production mode is active.

### Admin console

Provision an admin:

```bash
docker compose exec api python scripts/create_admin.py admin@example.com super_admin
```

Then sign in at `/admin/login` with the temporary password and the TOTP code from your authenticator app. The console includes:

- Metrics and queue depth.
- Provider budget and alert acknowledgement.
- User list, detail, suspension, and unban.
- Held-message review (including support-only self-harm holds).
- Consent disputes and abuse reports.
- DPDP rights requests and grievances.
- Suspension appeals.
- Audit log.
- Runtime config.

Admin actions are permission-checked by role and written to the audit log.

---

## API and Integration Reference

The API is served under `/api`; interactive OpenAPI docs are at `/docs` when the API is running. All user endpoints require a Clerk session token in the `Authorization: Bearer <token>` header (or the project’s auth cookie), plus ownership and entitlement checks. The summary below covers the primary contract; use `/docs` for the generated schema.

### Health and operations

| Method | Path | Auth | Purpose |
|---|---|---|---|
| `GET` | `/health` | Public | Liveness only; the process is up. |
| `GET` | `/ready` | Public | Readiness; checks Postgres and Redis. Returns `503` when degraded. |
| `GET` | `/metrics` | Public (network-restrict in production) | Prometheus exposition; `404` when metrics are disabled. |

### Authentication and users

| Method | Path | Auth | Purpose |
|---|---|---|---|
| `GET` | `/api/auth/me` | Clerk bearer | Return the local user, tier, and `needs_dob`; provisions the local row on first sight. |
| `POST` | `/api/auth/onboarding/dob` | Clerk bearer | Capture the date of birth and enforce 18+. First write only. |
| `POST` | `/api/auth/clerk/webhook` | Svix signature | Sync Clerk `user.created`, `user.updated`, and `user.deleted`. |

### Companions, consent, and voice

| Method | Path | Auth | Purpose |
|---|---|---|---|
| `POST` | `/api/companions` | User bearer | Create a deceased-subject companion in `pending_verification`. |
| `GET` | `/api/companions` | User bearer | List owned companions. |
| `GET` | `/api/companions/{id}` | User bearer | Get one owned companion. |
| `POST` | `/api/companions/{id}/consent` | User bearer | Store consent and activate the companion; seeds persona memories. |
| `POST` | `/api/companions/{id}/voice` | User bearer | Upload exactly one 30–50 s audio file and enqueue voice cloning. |
| `DELETE` | `/api/companions/{id}/voice` | User bearer | Mark the live voice model deleted and schedule purge. |
| `GET` | `/api/companions/{id}/greeting` | User bearer | Report greeting eligibility and return a short-lived stream token. |
| `GET` | `/api/companions/{id}/greeting-stream` | Stream token | Stream the one-time greeting audio. |

### Messages and conversation events

| Method | Path | Auth | Purpose |
|---|---|---|---|
| `POST` | `/api/messages/upload-url` | User bearer | Validate consent/quota, create the user message, and return a presigned S3 PUT URL. |
| `POST` | `/api/messages/{id}/submit` | User bearer | Finalize the upload and enqueue `stt_job`. Returns `409` if already submitted. |
| `GET` | `/api/messages/{id}` | User bearer | Poll job state, transcript, reply ID, delivery mode, token, and moderation metadata. |
| `GET` | `/api/messages/{id}/stream` | User bearer + optional view-once token | Stream reply audio inline. View-once tokens are single-use. |
| `GET` | `/api/messages/{id}/reply-stream` | Short-lived stream token | Stream the live LLM → TTS reply. Returns `404` when streaming is disabled. |
| `WS` | `/api/conversations/{id}/events?token=...` | Clerk session token | Receive job-state, `stream_ready`, `delivered`, and failure events. |

### DPDP rights, safety, and disputes

| Method | Path | Auth | Purpose |
|---|---|---|---|
| `GET` | `/api/account/export` | User bearer | Export the caller’s data bundle. |
| `DELETE` | `/api/account` | User bearer | Soft-delete the account, close the Clerk identity, and schedule purge. |
| `POST` | `/api/report` | User bearer | File an abuse/impersonation/fraud report for human review. |
| `POST` | `/api/disputes` | Public | File an heir/third-party objection; rate-limited per IP. |
| `GET` | `/api/grievance/contact` | Public | Return the configured grievance-officer contact. |
| `POST` | `/api/grievance` | User bearer | File a DPDP grievance. |
| `POST` | `/api/appeals` | User bearer (suspended allowed) | Appeal an account suspension. |

### Billing

| Method | Path | Auth | Purpose |
|---|---|---|---|
| `GET` | `/api/billing/plans` | Public | Return the monthly/annual plan catalog and savings percentage. |
| `POST` | `/api/billing/order` | User bearer | Create a Razorpay order for Standard Checkout. |
| `POST` | `/api/billing/verify` | User bearer | Verify a payment signature and grant one-time premium. |
| `POST` | `/api/billing/subscribe` | User bearer | Create a recurring subscription with explicit consent. |
| `POST` | `/api/billing/switch` | User bearer | Schedule a monthly ↔ annual switch at period end. |
| `GET` | `/api/billing/history` | User bearer | Payment/lifecycle history. |
| `POST` | `/api/billing/cancel` | User bearer | One-click cancellation at period end. |
| `GET` | `/api/billing/status` | User bearer | Current tier, subscription state, period end, grace, and pending switch. |
| `POST` | `/api/billing/webhook` | Razorpay signature | Verify and apply `subscription.*` events idempotently. |
| `GET` | `/api/billing/dev/checkout/{id}` | Dev only | Fake-provider checkout page; `404` in production/real provider. |
| `POST` | `/api/billing/dev/activate/{id}` | Dev only | Simulate activation for the fake provider. |

### Admin API

All admin routes require a fully authenticated admin (password + TOTP), except the login/MFA steps. Admin tokens are separate from user tokens.

| Group | Representative paths |
|---|---|
| Auth | `POST /api/admin/auth/login`, `POST /api/admin/auth/mfa`, `POST /api/admin/auth/logout`, `GET /api/admin/auth/me` |
| Metrics | `GET /api/admin/metrics/users`, `GET /api/admin/metrics/system`, `GET /api/admin/metrics/budget` |
| Budget alerts | `GET /api/admin/alerts`, `POST /api/admin/alerts/{id}/ack` |
| Users | `GET /api/admin/users`, `GET /api/admin/users/{id}`, `POST /api/admin/users/{id}/suspend`, `POST /api/admin/users/{id}/unban` |
| Appeals | `GET /api/admin/appeals`, `POST /api/admin/appeals/{id}/resolve` |
| Review | `GET /api/admin/review`, `GET /api/admin/review/{message_id}`, `POST /api/admin/review/{message_id}/resolve` |
| Disputes and reports | `GET /api/admin/disputes`, `POST /api/admin/disputes/{id}/resolve`, `GET /api/admin/reports`, `POST /api/admin/reports/{id}/resolve` |
| Rights | `GET /api/admin/rights`, `POST /api/admin/rights/{id}/resolve` |
| Audit | `GET /api/admin/audit` |
| Config | `GET /api/admin/config`, `PUT /api/admin/config/{key}` |
| Conversations | `GET /api/admin/users/{user_id}/conversations`, transcript endpoints |

### Example: create an upload URL

```http
POST /api/messages/upload-url
Authorization: Bearer <clerk-session-token>
Content-Type: application/json

{
  "companion_id": "00000000-0000-0000-0000-000000000000",
  "content_type": "audio/webm",
  "response_language": "hi"
}
```

```json
{
  "message_id": "11111111-1111-1111-1111-111111111111",
  "upload_url": "http://localhost:9000/reconnect-audio/...presigned...",
  "conversation_id": "22222222-2222-2222-2222-222222222222"
}
```

Then `PUT` the audio bytes to `upload_url` with the same `Content-Type`, and call `POST /api/messages/{message_id}/submit`.

### Example: store consent

```http
POST /api/companions/{companion_id}/consent
Authorization: Bearer <clerk-session-token>
Content-Type: application/json

{
  "type": "estate_auth",
  "attestation": {
    "authority": "executor",
    "declared_legal_authority": true
  },
  "doc_url": null
}
```

```json
{
  "consent_id": "33333333-3333-3333-3333-333333333333",
  "companion_status": "active"
}
```

### WebSocket events

Connect with a fresh Clerk session token:

```text
ws://localhost:8000/api/conversations/{conversation_id}/events?token=<clerk-session-token>
```

Common event payloads:

```json
{ "event": "state_change", "message_id": "...", "job_state": "transcribed" }
{ "event": "stream_ready", "message_id": "...", "stream_token": "...", "delivery_mode": "view_once", "eta_seconds": 4 }
{ "event": "delivered", "message_id": "...", "reply_message_id": "...", "delivery_mode": "standard", "playback_token": null }
{ "event": "held_for_review", "message_id": "...", "job_state": "held_for_review", "categories": ["self_harm"], "break_character_response": "..." }
```

> The backend emits `break_character_response` and `categories` for self-harm holds. The current frontend does not render that field; see [Limitations and Roadmap](#limitations-and-roadmap).

### Error handling and retries

| Status | Typical meaning |
|---|---|
| `401` | Missing/invalid Clerk session, stale token, or revoked session. The frontend signs out and redirects to login. |
| `403` | Ownership failure, consent not stored, tier/voice cap exceeded, living-person block, or insufficient admin permission. |
| `404` | Resource not found or not owned by the caller; also used to hide disabled streaming/dev billing endpoints. |
| `409` | Invalid job-state transition (for example, submitting the same message twice) or voice model not ready. |
| `410` | View-once audio already consumed or expired. |
| `413` / `415` / `422` | Voice sample too large, unsupported format, or outside the 30–50 second range. |
| `429` | Daily message quota or rate limit exceeded. |
| `502` / `503` | Upstream payment/AI provider failure, or no TTS slot became available. `503` responses may include `Retry-After`. |

STT retries transient provider errors with exponential backoff up to five attempts. Provider errors are metered into `provider_usage_ledger` where applicable. Streaming replies hold a distributed TTS slot; callers queue up to `TTS_SLOT_WAIT_SECONDS` before receiving `503`.

### Third-party integrations

| Provider | Used for | Configuration |
|---|---|---|
| Clerk | Authentication, sessions, MFA, webhook sync | `CLERK_*` + `NEXT_PUBLIC_CLERK_*` |
| Groq | Whisper STT, free-tier/greeting Llama, moderation classifier | `GROQ_API_KEY`, `STT_*`, `GROQ_LLM_MODELS`, `MODERATION_*` |
| DeepSeek | Premium LLM replies | `DEEPSEEK_API_KEY`, `LLM_MODEL_PREMIUM` |
| Google Gemini | Optional LLM fallback | `GEMINI_API_KEY`, `LLM_FALLBACK_MODEL`, `ALLOW_EXTERNAL_LLM` |
| Fish Audio | Voice cloning and TTS | `FISH_AUDIO_API_KEY`, `TTS_*` |
| Embeddings provider | Semantic memory | `EMBEDDING_*` |
| Razorpay | Payments and subscriptions | `RAZORPAY_*`, `NEXT_PUBLIC_RAZORPAY_KEY_ID` |
| SMTP or HTTP email | Transactional mail | `NOTIFICATIONS_*` / `SMTP_*` |
| Sentry | Error reporting | `SENTRY_DSN` |

---

## Database and Data Flow

### Database technology

- **PostgreSQL 16 + pgvector** (HNSW vector index, `pg_trgm` for lexical fallback).
- **Redis 7** for transient state, jobs, pub/sub, and distributed limits.
- **S3-compatible object storage** for source voice samples, user audio, and generated replies. The bucket is private; the API issues presigned PUT URLs and streams audio through authenticated endpoints.

### Main entities

| Table | Purpose |
|---|---|
| `users` | Canonical local identity mapped to Clerk; tier, DOB, status, session epoch, deletion timestamp. |
| `auth_tokens` | Legacy single-use email verification/password reset tokens (hashed). |
| `companions` | Memorial companion profile, relationship, deceased flag, status, bio, traits, greeting timestamp. |
| `consents` | Estate/next-of-kin/self-legacy authorization, attestation, optional document URL, IP, signature/revocation timestamps. |
| `consent_disputes` | Heir/third-party objections and review resolution. |
| `abuse_reports` | User-filed abuse/impersonation/fraud reports and resolution. |
| `voice_models` | Provider voice ID, status, deletion/purge timestamps. |
| `voice_samples` | Source sample S3 keys and duration. |
| `conversations` | A user ↔ companion conversation. |
| `messages` | User/companion turns, transcript, audio key, job state, delivery mode, view-once token/consumption/purge, moderation metadata, reply language, watermark ID. |
| `memories` | Profile/semantic/episodic/summary content, salience, source, and `vector(1536)` embedding. |
| `safety_events` | Self-harm, impersonation, fraud, or abuse signals with severity/detail. |
| `subscriptions` | Provider subscription mirror, plan, cycle, amount, consent, grace, period end, cancellation. |
| `billing_events` | Idempotent provider events and payment/lifecycle history (no card data). |
| `app_config` | Runtime feature/config values editable by super admins. |
| `account_sanctions`, `appeals` | Suspension history and user appeals. |
| `user_flags`, `user_warnings` | Moderation flags/warnings derived from non-self-harm safety events. |
| `review_actions` | Terminal review decisions for held messages. |
| `dpdp_requests` | Export/erasure/grievance request tracking. |
| `provider_usage_ledger` | Per-call STT/TTS/LLM usage for budget estimates. |
| `budget_alerts` | De-duplicated provider budget/low-balance alerts. |
| `admin_users` | Separate admin identity, role, encrypted TOTP secret, session epoch. |
| `audit_log` | Actor/action/target/metadata audit trail. |

### Relationships and ownership

- A `users` row owns `companions`, `conversations`, `subscriptions`, and related rows.
- A `companion` owns `consents`, `voice_models`, `voice_samples`, `memories`, and `conversations`.
- A `conversation` owns `messages`.
- Messages and memories are indirectly owned through their conversation/companion.
- `users` and `companions` are RLS roots; indirect tables use ownership subqueries.
- Deleting an account cascades through owned rows; external blobs and provider voices are deleted by the purge worker. Consent, safety, and audit records are retained as disclosed.

### Row-Level Security

Migrations `021` and `022` enable and force RLS on:

- `users`
- `companions`
- `conversations`
- `messages`
- `memories`
- `voice_models`
- `voice_samples`

Each transaction sets either `app.current_user_id` (authenticated user request) or `app.bypass_rls='on'` (workers, admin, auth bootstrap, public endpoints, migrations). The runtime should connect as the non-superuser `reconnect_app` role; table owners and superusers bypass RLS even with `FORCE` in some contexts, so `APP_DATABASE_URL` matters.

### Migrations

Alembic migrations are in `backend/migrations/versions/` (25 files at the time of writing). They include the base schema, memory indexes, admin tables, RLS scaffolding and enforcement, billing, Clerk identity, retention columns, and session epochs. Apply them with:

```bash
cd backend
alembic upgrade head
```

The API container does this automatically in Compose. In Kubernetes, the current API deployment uses an init container; the manifest notes that a one-shot Job is preferable for multiple replicas.

### Storage and retrieval

- Upload flow: the client asks the API for a presigned PUT URL, uploads directly to S3/MinIO, then tells the API to submit the message.
- Audio is never exposed via a public bucket URL. Playback uses authenticated stream endpoints with `no-store`, `inline`, and no-referrer headers.
- Memory retrieval uses `EMBEDDING_API_KEY` to enable pgvector cosine search; without it, `pg_trgm` lexical similarity is used. Both are re-ranked by similarity × salience × recency.
- `backend/scripts/backfill_embeddings.py` can populate embeddings for existing memories when a provider is configured.
- Voice samples and reply audio live under separate S3 prefixes (`voice-samples/...`, `audio/user/...`, `audio/companion/...`).

### Retention and deletion

| Data | Behavior |
|---|---|
| View-once audio | Purged when consumed or after `VIEW_ONCE_TTL_HOURS` (default 24h); sweep runs every 5 minutes. |
| Deleted voice models | Retained for `RETENTION_DAYS` (default 60), then provider voice and samples are erased. |
| Deleted accounts | Soft-deleted immediately; user content erased after `RETENTION_DAYS`; subscriptions cancelled; Clerk identity deleted. |
| Consent, safety events, audit log | Retained beyond the grace window as disclosed compliance records. |
| Cache/queue state | Redis is transient; job results are kept for one hour by default. |

---

## Development and Testing

### Backend

```bash
cd backend
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements-dev.txt

# Requires Postgres/Redis/MinIO and configuration
alembic upgrade head
uvicorn app.main:app --reload --port 8000
```

Run the worker separately:

```bash
cd backend
source .venv/bin/activate
python -m arq app.workers.settings.WorkerSettings
```

Run the tests:

```bash
cd backend
pytest
```

The test suite is designed to run without live provider keys. `backend/tests/conftest.py` sets `APP_ENV=test`, which forces fake STT/TTS/LLM/moderation/billing providers. The suite contains 47 test modules and 367 test functions covering consent, age gating, entitlements, moderation, memory, billing compliance, view-once behavior, RLS SQL, jobs, admin operations, and provider routing.

**Backend verification status:** not executed in this environment because the host Python environment does not have the backend dependencies installed and the built API image does not include `pytest`. The commands above are the project’s documented path.

### Frontend

```bash
cd frontend
npm ci
npm run dev          # http://localhost:3000
npm test             # Vitest
npm run type-check   # tsc --noEmit
npm run build        # production Next.js build
```

**Frontend verification status (this environment):**

- `npm test` — passed: 2 test files, 12 tests.
- `npm run type-check` — passed with no diagnostics.
- `docker compose config -q` — passed.
- `npm run lint` — did not complete: `next lint` is deprecated and prompts to configure ESLint because no ESLint config is committed. Use `npm run type-check` and `npm test` until ESLint is set up.

There is no CI workflow in the repository. Adding GitHub Actions to run pytest, the frontend tests/type-check, and a Postgres-backed migration/RLS integration test is a recommended next step.

### Adding or changing a feature

1. Read the relevant section of [`docs/spec.md`](docs/spec.md) and the golden rules in [`CLAUDE.md`](CLAUDE.md).
2. Keep provider integrations behind the `backend/app/workers/providers` Protocols and add a fake when appropriate.
3. Add a migration for any schema change; do not edit old migrations.
4. Enforce authentication, ownership, tier entitlement, and audit logging at the API layer.
5. Consider RLS implications for every new owned table or query path.
6. Add tests for safety/compliance constraints before calling the change complete.
7. Run `pytest`, `npm test`, and `npm run type-check`.
8. Use Conventional Commits (`feat:`, `fix:`, `chore:`, `docs:`, `test:`, `refactor:`).

### Debugging

- API docs: `http://localhost:8000/docs`.
- Health/readiness: `/health`, `/ready`.
- Prometheus metrics: API `/metrics`, worker `:9100/metrics`.
- Structured JSON logs: set `LOG_JSON=false` for readable local logs; use `LOG_LEVEL=DEBUG` for more detail.
- Sentry: set `SENTRY_DSN` to enable error reporting.
- Redis queue: inspect arq jobs and `provider_usage_ledger`; worker jobs have structured completion/failure logs.
- MinIO console: inspect private buckets and object prefixes.
- Clerk issues: verify publishable/secret/JWT public key values, sign-in URLs, and webhook signing secret.
- RLS issues: verify `APP_DATABASE_URL` uses the non-superuser role and that the RLS bootstrap SQL was applied.

---

## Deployment

### Supported paths in the repository

1. **Docker Compose** — the primary local/dev stack. `docker-compose.yml` defines Postgres, Redis, MinIO, API, worker, and frontend. The API runs migrations before serving. The frontend image runs `npm run dev`, so Compose is not a production frontend deployment recipe.
2. **Kubernetes templates** — `infra/k8s/` contains API/worker Deployments, Services, HPA, Ingress, ConfigMap, Secret template, namespace, and a migration init container. These are **templates**: replace the image registry, hostnames, ingress class, cluster issuer, and all `REPLACE_ME` secret values.
3. **Manual local development** — run services separately as described above.

### Production considerations

- **Managed data plane in India where available.** Use managed Postgres 16 + pgvector, Redis, and S3-compatible storage in an Indian region. `infra/k8s/README.md` explicitly leaves these out of cluster.
- **Migrations.** For many replicas, run `alembic upgrade head` as a one-shot Job or Helm pre-install/upgrade hook instead of a per-pod init container.
- **Secrets.** Create the Kubernetes Secret out-of-band (`kubectl create secret ... --from-env-file`), never from a committed file. Rotate any key that has ever been exposed.
- **Startup guards.** In production, the API refuses to start without `JWT_SECRET`/`NEXTAUTH_SECRET`, Clerk JWT/secret/webhook configuration, a non-`fake` moderation provider with `GROQ_API_KEY`, and explicit CORS origins.
- **Reverse proxy.** Set `TRUST_PROXY_HEADERS=true` only behind a trusted proxy/load balancer that overwrites `X-Forwarded-For`.
- **Metrics.** Network-restrict `/metrics` to the scrape target; it is unauthenticated by design.
- **Health checks.** Use `/health` for liveness and `/ready` for readiness. The worker exposes metrics on `WORKER_METRICS_PORT`.
- **Scaling caveats.** The TTS concurrency gate, Redis single-instance topology, Postgres connection pool sizing, synchronous S3 calls, and per-pod Uvicorn worker count are known scale risks. See [`LAUNCH_READINESS.md`](LAUNCH_READINESS.md) before a high-traffic launch.
- **Frontend.** Build a production Next.js image/artifact for deployment; the committed frontend Dockerfile is a dev-server image.
- **CDN.** Audio is proxied through the API today. A CDN with signed URLs for standard/premium playback is a recommended future optimization.
- **Data residency/legal.** Clerk is US-hosted. The cross-border transfer record and required privacy-notice disclosure are in [`docs/dpdp-transfers.md`](docs/dpdp-transfers.md); legal sign-off is still a TODO.

---

## Security and Privacy

### Security controls implemented

- **Secrets only via environment variables.** `.env` is ignored by Git. No real keys belong in source, fixtures, docs, or tests.
- **Clerk-owned authentication.** The backend verifies Clerk RS256 session JWTs and maps them to local users. Passwords are never stored in the local database.
- **Separate admin identity.** Admin users live in `admin_users`, use password + TOTP MFA, and have role-based permissions. Admin logout bumps a session epoch to revoke tokens.
- **Session revocation.** User and admin tokens carry a session epoch; a mismatch is rejected. This supports logout-everywhere and password-change revocation.
- **Ownership and RLS.** API handlers check ownership explicitly; Postgres RLS is a second layer for owned tables. Workers/admin/public paths use an explicit bypass scope.
- **Signed media access.** Private S3 bucket, short-lived presigned PUT/GET, no public object URLs. Stream responses set `no-store`, `inline`, `X-AI-Generated`, and `no-referrer`.
- **Input validation.** Pydantic schemas validate lengths, enums, UUIDs, dates, and email fields. Free-text API fields are stripped of HTML/control characters. Voice uploads validate size, MIME/extension, and duration.
- **Rate limiting and lockout.** Auth, admin, MFA, disputes, webhooks, and onboarding have per-IP or per-account limits. Admin accounts lock after repeated password failures.
- **Production boot guards.** Missing secrets, disabled moderation, and wildcard credentialed CORS fail startup.
- **Audit logging.** Sensitive actions write `audit_log` rows with actor type and metadata.
- **Webhook verification.** Razorpay HMAC signatures and Clerk/Svix signatures are verified; billing events are deduplicated on provider event IDs.
- **Safety routing.** Self-harm disclosures break character and return the configured Indian helpline; self-harm events are deliberately not converted into user flags. Living-person companion creation is blocked in the self-serve flow.
- **AI labeling.** Generated audio carries an AI label through object metadata, the stream header, and the UI, plus a stored watermark ID.


