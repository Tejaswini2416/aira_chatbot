# Aira

A polished React and TypeScript Aira assistant UI paired with an Express API for auth, personalized memory, streamed AI completions, and speech synthesis.

## Quick start

1. Install dependencies: `npm install`
2. Copy `.env.example` to `.env`, set a unique `JWT_SECRET` (32+ random characters), and configure `DATABASE_URL`.
3. Start PostgreSQL, then generate the Prisma client and apply migrations: `npm run db:generate` and `npm run db:migrate`.
4. Set at least one provider key (`GROQ_API_KEY`, `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, or `GOOGLE_AI_API_KEY`). The Groq option defaults to `openai/gpt-oss-20b` and uses Groq's OpenAI-compatible endpoint.
5. Run `npm run dev:api` and `npm run dev` in separate terminals. For production, build the frontend with `npm run build` and the API with `npm run build:api`, then run `npm run start:api`.

Vite proxies `/api` to `http://localhost:3001`. Register using `POST /api/v1/auth/register`, then pass the returned token as `Authorization: Bearer <token>` to protected routes. The static UI can be opened without registration; authenticated API requests require a token.

## API

- `POST /api/v1/auth/register`, `POST /api/v1/auth/login`
- `GET /api/v1/me`
- `GET /api/v1/memory`, `POST /api/v1/memory`, `PATCH /api/v1/memory/:id`, `DELETE /api/v1/memory/:id`
- `PUT /api/v1/profile`
- `GET /api/v1/conversations`, `GET /api/v1/conversations/:id`
- `POST /api/v1/chat/completions` (SSE; send bearer token, `message`, optional `model`, `memoryEnabled`, and `conversationId`)
- `POST /api/v1/speech/synthesize` (returns `audio/mpeg`)
- `GET /health`

## Deployment notes

Set an explicit production CORS allowlist, a high-entropy JWT secret, a durable PostgreSQL database with TLS, trusted proxy configuration when applicable, and provider keys in a secret manager. The Groq chat option uses `GROQ_API_KEY` and optionally `GROQ_MODEL`; the frontend selects it by default when the health endpoint reports Groq configured. The vector retrieval path uses PostgreSQL `pgvector` with OpenAI embeddings when available; apply the included migration to enable it. Without embeddings, memory uses a small per-user lexical ranking fallback. Agent tools are deliberately deny-by-default until a separately sandboxed code runner or vetted search adapter is configured. Set a distributed rate-limit store before using multiple API instances.
