# Reconnect (Starecho)

**Starecho is an AI remembrance companion that lets adults send voice messages and receive spoken replies inspired by the memories, personality, and voice of a deceased loved one.** It is designed as an AI companion—not as the real person.

> **Status: Pre-production MVP.** The project is not publicly launched and has no hosted demo. Some features and production safeguards are still incomplete. Use test data while developing.

## Features

- Create a remembrance companion using biographical details and personality traits.
- Create a voice model from an uploaded voice sample, subject to consent requirements.
- Send voice notes and receive AI-generated spoken replies.
- Support for English and Hindi conversations.
- Use stored memories to help personalize replies.
- User accounts, premium billing, and an internal admin dashboard.
- Privacy and safety features, including consent records, data export and deletion, moderation, and abuse reporting.

**Important:** The app must not claim that the AI is the deceased person, is conscious, or is communicating from an afterlife. Consent, privacy, and safety requirements must be followed.

## How it works

1. A user signs in and creates a companion.
2. The user records the required consent information and uploads a voice sample.
3. The user sends a voice note.
4. The app converts the audio to text, checks it for safety, and uses relevant memories to generate a reply.
5. The reply is converted to speech and returned to the user.

## Tech stack

- **Frontend:** Next.js, React, TypeScript, Tailwind CSS
- **Backend:** Python, FastAPI
- **Database:** PostgreSQL with pgvector
- **Background jobs and cache:** Redis and arq
- **Local object storage:** MinIO (S3-compatible)
- **AI and voice:** Groq, DeepSeek, Fish Audio, with optional Gemini and embedding providers
- **Authentication:** Clerk
- **Payments:** Razorpay
- **Local development:** Docker Compose

## Run locally

### Requirements

- Docker Engine and Docker Compose v2
- API keys for the external services you want to test
- A Clerk development application for sign-in

### Setup

1. Clone the repository and enter the project directory:

   ```bash
   git clone <repository-url>
   cd reconnect-2
   ```

2. Create your local environment file:

   ```bash
   cp .env.example .env
   ```

3. Edit `.env` and add the required credentials. See `.env.example` and `backend/app/core/config.py` for configuration details. Never commit real API keys or secrets.

4. Build and start the services:

   ```bash
   docker compose up --build
   ```

5. Open the app:

   - **Web app:** http://localhost:3000
   - **API:** http://localhost:8000
   - **API documentation:** http://localhost:8000/docs
   - **Health check:** http://localhost:8000/health

For detailed configuration and troubleshooting, check the project files and documentation in `docs/`.

## Project structure

```text
backend/    FastAPI API, AI pipeline, workers, database models, tests
frontend/   Next.js web application
infra/      Database security and deployment templates
docs/       Technical specifications and operational guides
.env.example
Dockerfile / docker-compose.yml
```

## Current limitations

This is still a pre-production project. Known gaps include incomplete conversation-history features, limited consent verification, missing voiceprint detection, and additional privacy, security, and launch-readiness work. The self-harm support response also needs a frontend fix before production use.

Do not treat the project as production-ready until the remaining items in the audit and roadmap documents have been reviewed and resolved.

## Contributing

Contributions and feedback are welcome. Please review the existing project documentation before making changes, and do not commit secrets, private user data, or real voice recordings.

## License

No license information is specified here yet. Check the repository for a `LICENSE` file before reusing or distributing the code.
