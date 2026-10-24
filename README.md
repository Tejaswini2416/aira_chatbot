# Aira — Personalized AI Chatbot

Aira is an intelligent, personalized AI chatbot and conversational assistant featuring a sleek glassmorphic interface, long-term memory, voice mode, multi-provider AI streaming, and secure authentication.

---

## 🌟 All Features

### 1. Multi-Provider AI Engine
- **Multiple LLM Support**: Switch seamlessly between leading AI providers:
  - **Groq**: Ultra-fast inference (default model: `openai/gpt-oss-20b` or Llama models).
  - **OpenAI**: Supports `GPT-4o`, `gpt-4o-mini`, and custom OpenAI-compatible endpoints via `OPENAI_BASE_URL`.
  - **Google Gemini**: Powered by `Gemini 1.5 Pro` via Google Generative AI SDK.
  - **Anthropic Claude**: Powered by `Claude 3.5 Sonnet`.
- **Runtime Provider Detection**: Checks active keys dynamically on startup (`/api/health`) and automatically selects the available configured provider.
- **Model Switcher**: On-the-fly model switching directly from the composer toolbar in the UI.

### 2. Personalized Memory & Custom Profiles
- **Long-Term Memory Retention**: Stores facts, user details, and context across sessions in PostgreSQL.
- **User Profile Customization**:
  - Set a custom display name and professional role.
  - Choose conversational tone (e.g., Casual, Direct, Formal, Creative).
  - Custom system instructions injected into every model prompt.
- **Memory Toggle**: Enable or disable personalized memory retention with a single click in user preferences.
- **Memory Management API**: Full CRUD capabilities for stored facts (`/api/v1/memory`).

### 3. Real-Time Streaming Chat
- **Token-by-Token Streaming**: Responses stream in real-time using Server-Sent Events (SSE).
- **Interactive Message Actions**:
  - **Copy**: One-click copy with animated visual confirmation.
  - **Regenerate**: Instantly regenerate responses with alternative phrasing.
  - **Fork Branch**: Branch out from any point in the chat history to explore a new direction.
- **Ambient Status Indicators**: Pulsing orb, typing cursor, and status messages reflecting agent state.

### 4. Interactive Voice & Speech Mode
- **Dedicated Voice View**: Fullscreen immersive voice mode with a glowing, floating ambient orb visualizer.
- **Live Waveform Dock**: Animated audio waveform reflecting microphone activity.
- **Text-to-Speech (TTS)**: Dedicated speech synthesis endpoint (`/api/v1/speech/synthesize`) using OpenAI audio models.

### 5. Glassmorphism UI & User Experience
- **Modern Aesthetic**: Dark palette with glowing radial gradients, frosted glass panels, and smooth micro-animations.
- **Home View Quick Prompts**: Categorized prompt cards for quick start (Creative, Tech, Productivity, Analysis).
- **Collapsible Sidebar**: Easily navigate between chats, workspaces, profile settings, and new sessions.
- **Keyboard Shortcuts**: Press `Enter` to send, `Shift + Enter` for multi-line inputs.

### 6. Security & Infrastructure
- **Authentication**: JWT token-based authentication with 7-day expiration.
- **Password Security**: Passwords hashed securely using `bcryptjs` (12 salt rounds).
- **Rate Limiting**: Protects against abuse using `express-rate-limit`.
- **Security Headers & CORS**: Hardened with `helmet` and custom allowed origin policies.
- **Database ORM**: PostgreSQL database powered by **Prisma**, with automated migrations and relation schemas.

---

## 🛠️ Tech Stack

- **Frontend**: React 18, TypeScript, Vite, TailwindCSS, Lucide Icons, Framer Motion
- **Backend**: Node.js, Express, TypeScript, TSX, Zod validation
- **Database**: PostgreSQL (supports Neon Cloud Postgres), Prisma ORM
- **AI Integrations**: Groq SDK, OpenAI SDK, Anthropic SDK, Google Generative AI SDK

---

## 🚀 How to Run the Application

Follow these steps to run the application locally on your machine.

### Step 1: Clone and Install Dependencies

```bash
# Clone the repository
git clone https://github.com/Tejaswini2416/personalized_chatbot.git
cd personalized_chatbot

# Install dependencies
npm install
```

---

### Step 2: Configure Environment Variables

Create your local `.env` file from `.env.example`:

```bash
# On Windows (PowerShell)
Copy-Item .env.example .env

# On macOS / Linux
cp .env.example .env
```

Open `.env` in your editor and configure the values:

```env
# Server & Client Configuration
NODE_ENV=development
PORT=3001
CORS_ORIGIN=http://localhost:5173

# Authentication (enter any random 32+ character string)
JWT_SECRET=development-only-secret-change-before-production-12345
JWT_EXPIRES_IN=7d

# Database Connection (PostgreSQL or Neon Postgres)
DATABASE_URL="postgresql://username:password@localhost:5432/aira?schema=public"

# AI Provider Keys (configure at least one)
GROQ_API_KEY="your_groq_api_key_here"
GROQ_MODEL="openai/gpt-oss-20b"

OPENAI_API_KEY="your_openai_api_key_here"
GOOGLE_AI_API_KEY="your_gemini_api_key_here"
ANTHROPIC_API_KEY="your_anthropic_api_key_here"
```

> **Note on AI Keys**: You only need **one** working provider key to run the chatbot (e.g., Groq has a free tier at [console.groq.com](https://console.groq.com), or Google Gemini at [aistudio.google.com](https://aistudio.google.com)).

---

### Step 3: Initialize the Database

Generate the Prisma client and apply database migrations to create the required tables:

```bash
# Generate the Prisma Client
npm run db:generate

# Apply migrations to your PostgreSQL database
npm run db:migrate
```

---

### Step 4: Start the Application

You need to run the backend API server and the frontend client. Open **two terminal tabs**:

#### Terminal 1 — Start the Backend API:
```bash
npm run dev:api
```
- The API server will start on: **`http://localhost:3001`**
- Verifies database connection and active AI provider keys.

#### Terminal 2 — Start the Frontend UI:
```bash
npm run dev
```
- The Vite dev server will start on: **`http://localhost:5173`**
- Proxies `/api` requests automatically to the backend on port 3001.

---

### Step 5: Open in Your Browser

1. Navigate to: **[http://localhost:5173](http://localhost:5173)**
2. The UI will automatically detect your configured provider (e.g. Groq, OpenAI, Gemini, or Claude).
3. Type your prompt in the composer and press **Enter** to chat!

---

## 📋 Available NPM Scripts

| Command | Description |
| :--- | :--- |
| `npm run dev` | Starts the Vite React frontend dev server on port `5173` |
| `npm run dev:api` | Starts the Express API server with hot-reload via `tsx watch` on port `3001` |
| `npm run db:generate` | Generates TypeScript types and client from `prisma/schema.prisma` |
| `npm run db:migrate` | Applies pending Prisma migrations to the PostgreSQL database |
| `npm run build` | Typechecks and compiles production build of frontend assets |
| `npm run build:api` | Compiles the Express TypeScript server into `dist-server/` |
| `npm run start:api` | Runs the compiled production API server (`node dist-server/index.js`) |

---

## 📡 API Endpoints Overview

| Method | Route | Description |
| :--- | :--- | :--- |
| `GET` | `/health` | Basic server health check |
| `GET` | `/api/health` | Status of database and configured AI providers |
| `POST` | `/api/v1/auth/register` | Register new user account |
| `POST` | `/api/v1/auth/login` | Login and receive JWT bearer token |
| `GET` | `/api/v1/me` | Retrieve active user profile and preferences |
| `PUT` | `/api/v1/profile` | Update user instructions, tone, and memory settings |
| `GET` | `/api/v1/memory` | Fetch all saved memory items for user |
| `POST` | `/api/v1/memory` | Create a new memory item |
| `DELETE` | `/api/v1/memory/:id` | Delete a specific memory item |
| `GET` | `/api/v1/conversations` | List user conversation history |
| `GET` | `/api/v1/conversations/:id` | Get messages for a conversation |
| `POST` | `/api/v1/chat/completions` | Stream AI chat response (SSE) |
| `POST` | `/api/v1/speech/synthesize` | Generate speech audio from text (`audio/mpeg`) |
