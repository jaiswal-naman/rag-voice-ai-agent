<p align="center">
  <img src="https://img.shields.io/badge/Python-3.12+-3776AB?style=for-the-badge&logo=python&logoColor=white" />
  <img src="https://img.shields.io/badge/TypeScript-5.x-3178C6?style=for-the-badge&logo=typescript&logoColor=white" />
  <img src="https://img.shields.io/badge/FastAPI-0.128+-009688?style=for-the-badge&logo=fastapi&logoColor=white" />
  <img src="https://img.shields.io/badge/React-18-61DAFB?style=for-the-badge&logo=react&logoColor=black" />
  <img src="https://img.shields.io/badge/Docker-Ready-2496ED?style=for-the-badge&logo=docker&logoColor=white" />
  <img src="https://img.shields.io/badge/AWS-ECS%20Fargate-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white" />
</p>

<p align="center">
  <a href="https://github.com/jaiswal-naman/rag-voice-ai-agent/stargazers"><img src="https://img.shields.io/github/stars/jaiswal-naman/rag-voice-ai-agent?style=social" alt="GitHub Stars" /></a>
  <a href="https://github.com/jaiswal-naman/rag-voice-ai-agent/network/members"><img src="https://img.shields.io/github/forks/jaiswal-naman/rag-voice-ai-agent?style=social" alt="GitHub Forks" /></a>
  <a href="https://github.com/jaiswal-naman/rag-voice-ai-agent/blob/main/LICENSE"><img src="https://img.shields.io/github/license/jaiswal-naman/rag-voice-ai-agent" alt="License: MIT" /></a>
  <a href="https://github.com/jaiswal-naman/rag-voice-ai-agent/issues"><img src="https://img.shields.io/github/issues/jaiswal-naman/rag-voice-ai-agent" alt="Open Issues" /></a>
  <a href="https://github.com/jaiswal-naman/rag-voice-ai-agent/commits/main"><img src="https://img.shields.io/github/last-commit/jaiswal-naman/rag-voice-ai-agent" alt="Last Commit" /></a>
</p>

<h1 align="center">🎙️ RAG Voice AI Agent</h1>

<p align="center">
  <strong>An intelligent, real-time voice assistant powered by Retrieval-Augmented Generation (RAG) for industrial equipment support.</strong>
</p>

<p align="center">
  Built by <a href="https://github.com/jaiswal-naman"><strong>Naman Jaiswal</strong></a>
</p>

---

## 📌 Overview

**RAG Voice AI Agent** is a production-ready, full-stack voice AI system that enables real-time, speech-based interaction with a knowledge base. Designed for industrial use cases, it allows operators and support agents to query equipment documentation hands-free using natural voice commands and receive instant, contextually accurate spoken responses.

The system combines **real-time speech processing**, **vector-based document retrieval**, and **large language model inference** into a seamless conversational pipeline — all deployable with a single `docker-compose up`.

> 💼 **Portfolio Project** — This project showcases end-to-end AI system design: from vector database architecture and LLM tool-calling to real-time WebSocket audio streaming and cloud-native deployment on AWS ECS Fargate. It demonstrates the ability to integrate multiple cutting-edge AI services into a cohesive, production-grade product.

---

## ✨ Features

| Feature | Description |
|---------|-------------|
| 🎤 **Voice-First Interface** | Speak naturally; the assistant listens, understands, and responds in real-time |
| 🔍 **RAG-Powered Accuracy** | Every answer is grounded in your uploaded documents — zero hallucination |
| ⚡ **Sub-Second Latency** | Full-duplex WebSocket audio pipeline with smart turn detection & VAD |
| 📄 **Multi-Format Documents** | Upload PDF, DOCX, or plain text files per equipment unit |
| 🤖 **LLM Tool Calling** | Groq LLM autonomously calls the vector search tool when context is needed |
| 🌐 **Multi-Tenant Ready** | Tenant and equipment isolation at the database and retrieval layer |
| ☁️ **Production-Ready** | Dockerized microservices, AWS ECS Fargate, full CI/CD via GitHub Actions |
| 🔒 **Secrets Management** | API keys stored securely in AWS Secrets Manager (never in code) |
| 📊 **Pipeline Metrics** | Built-in Pipecat metrics & usage tracking per voice session |
| 🔄 **Auto-Reconnect** | Idle timeout, graceful disconnection, and session cleanup |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        React Frontend                           │
│          (Pipecat Client SDK · WebSocket · Tailwind CSS)        │
└──────────────────────────┬──────────────────────────────────────┘
                           │ WebSocket (Audio + Text)
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                       FastAPI Backend                            │
│                                                                 │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌───────────────┐  │
│  │ Deepgram │  │  Groq    │  │ElevenLabs│  │   Pipecat     │  │
│  │  (STT)   │→ │  (LLM)   │→ │  (TTS)   │  │  Pipeline     │  │
│  └──────────┘  └────┬─────┘  └──────────┘  └───────────────┘  │
│                     │                                           │
│               ┌─────▼─────┐                                    │
│               │ RAG Engine│                                    │
│               │ (Vector   │                                    │
│               │  Search)  │                                    │
│               └─────┬─────┘                                    │
│                     │                                           │
└─────────────────────┼───────────────────────────────────────────┘
                      │
              ┌───────▼────────┐
              │  MongoDB Atlas  │
              │ (Vector Store + │
              │   Documents)    │
              └────────────────┘
```

### 🔄 Data Flow

```
User speaks
    │
    ▼
[Browser Mic] ──WebSocket──▶ [FastAPI + Pipecat Pipeline]
                                        │
                              ┌─────────▼──────────┐
                              │   Deepgram STT      │  Speech → Text
                              └─────────┬──────────┘
                                        │
                              ┌─────────▼──────────┐
                              │   Groq LLM          │  Understands intent
                              │   (Tool Calling)    │──▶ search_knowledge_base()
                              └─────────┬──────────┘         │
                                        │            ┌────────▼────────┐
                                        │            │  Gemini Embeds  │
                                        │            │  + MongoDB      │
                                        │            │  Vector Search  │
                                        │            └────────┬────────┘
                                        │◀──── Context ───────┘
                              ┌─────────▼──────────┐
                              │   ElevenLabs TTS    │  Text → Speech
                              └─────────┬──────────┘
                                        │
                              ◀──WebSocket──── Audio Response
                                        │
                                   User hears answer
```

---

## ⚡ Tech Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| **Frontend** | React 18, TypeScript, Tailwind CSS | Real-time voice chat UI |
| **Voice SDK** | Pipecat AI Client SDK | WebSocket audio transport |
| **Backend** | FastAPI, Python 3.12+ | REST API + WebSocket server |
| **Voice Pipeline** | Pipecat AI Framework | Orchestrates STT → LLM → TTS |
| **Speech-to-Text** | Deepgram Nova | Real-time audio transcription with diarization |
| **LLM** | Groq (Llama 3 / GPT-oss) | Ultra-fast inference for responses (~200 ms) |
| **Text-to-Speech** | ElevenLabs | Natural voice synthesis |
| **Embeddings** | Google Gemini (`text-embedding-004`) | 768-dim document vectorization |
| **Vector Database** | MongoDB Atlas Vector Search | Cosine similarity retrieval with metadata filtering |
| **Text Splitting** | LangChain | Intelligent chunking (size 1000, overlap 250) |
| **DevOps** | Docker, Docker Compose | Containerized deployment |
| **CI/CD** | GitHub Actions | Automated build, push, and deploy |
| **Cloud** | AWS ECS Fargate, ALB, ECR, Secrets Manager | Production-grade serverless containers |
| **IaC** | AWS CloudFormation | Full infrastructure as code |
| **Package Manager** | uv | 10–100× faster than pip |

---

## 🚀 Quick Start

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- API keys for: [Deepgram](https://console.deepgram.com/), [ElevenLabs](https://elevenlabs.io/), [Groq](https://console.groq.com/), [Google AI](https://aistudio.google.com/)
- [MongoDB Atlas](https://www.mongodb.com/atlas) cluster with Vector Search enabled

### 1. Clone the Repository

```bash
git clone https://github.com/jaiswal-naman/rag-voice-ai-agent.git
cd rag-voice-ai-agent
```

### 2. Configure Environment

```bash
cp backend/.env.example backend/.env
```

Edit `backend/.env`:

```env
# MongoDB
MONGO_URL=mongodb+srv://<user>:<password>@<cluster>.mongodb.net
DB_NAME=rag_voice_agent_db

# AI Services
DEEPGRAM_API_KEY=your_deepgram_key
ELEVENLABS_API_KEY=your_elevenlabs_key
GROQ_API_KEY=your_groq_key
GOOGLE_API_KEY=your_google_ai_key
```

### 3. Set Up MongoDB Atlas Vector Search

Create a Vector Search index named `vector_index` on your `document_chunks` collection:

```json
{
  "fields": [
    {
      "type": "vector",
      "path": "embedding",
      "numDimensions": 768,
      "similarity": "cosine"
    },
    {
      "type": "filter",
      "path": "tenant_id"
    },
    {
      "type": "filter",
      "path": "equipment_id"
    },
    {
      "type": "filter",
      "path": "is_disabled"
    }
  ]
}
```

### 4. Launch

```bash
docker-compose up --build
```

| Service | URL |
|---------|-----|
| 🖥️ Frontend | http://localhost:3000 |
| ⚙️ Backend API | http://localhost:8000 |
| 📖 Swagger Docs | http://localhost:8000/docs |

### 5. First Run Walkthrough

```
1. Open http://localhost:3000 in your browser
2. Create equipment via the API:
   POST http://localhost:8000/api/v1/equipment/
   Body: { "name": "Hydraulic Press A1", "description": "..." }

3. Upload its documentation (PDF/DOCX):
   POST http://localhost:8000/api/v1/equipment/{id}/documents
   Form: files=<your-pdf>

4. In the UI, select the equipment and click Connect
5. Allow microphone access and start speaking!
```

---

## 📁 Project Structure

```
rag-voice-ai-agent/
├── backend/                         # FastAPI backend service
│   ├── main.py                      # App entry point, CORS & middleware setup
│   ├── Dockerfile                   # Multi-stage backend container
│   ├── pyproject.toml               # Python dependencies (managed by uv)
│   ├── .env.example                 # Environment variable template
│   └── app/
│       ├── bot.py                   # Pipecat voice pipeline
│       │                            #   (Deepgram STT → Groq LLM → ElevenLabs TTS)
│       ├── config.py                # Pydantic Settings (reads from .env)
│       ├── database.py              # Async MongoDB connection (Motor)
│       ├── models/                  # Pydantic data models
│       │   ├── document.py          # Document upload schema
│       │   ├── equipment.py         # Equipment CRUD schema
│       │   └── rag.py               # RAG retrieval result schemas
│       ├── routers/                 # API route handlers
│       │   ├── equipment.py         # Equipment CRUD + document ingestion pipeline
│       │   └── stream.py            # WebSocket voice streaming endpoint
│       └── services/                # Core business logic
│           ├── embeddings.py        # Google Gemini text-embedding-004 wrapper
│           ├── rag.py               # MongoDB Atlas Vector Search + cosine retrieval
│           └── text_extraction.py   # PDF / DOCX → plain text extractor
│
├── frontend/                        # React + TypeScript UI
│   ├── Dockerfile                   # Nginx-based frontend container
│   ├── nginx.conf                   # Reverse proxy + SPA routing config
│   ├── package.json                 # Node.js dependencies
│   └── src/
│       ├── App.tsx                  # React Router setup
│       ├── components/
│       │   ├── RealTimeChatPanel.tsx # Main voice chat interface with equipment selector
│       │   ├── BotMessageBubble.tsx  # Bot response message bubble
│       │   ├── BotJsonCard.tsx       # Renders RAG chunk metadata cards
│       │   └── UserMessageBubble.tsx # User transcription message bubble
│       ├── hooks/
│       │   └── pipecat-chat-events.ts # Pipecat SDK event → chat state bridge
│       ├── pages/
│       │   └── Stream.tsx            # Top-level streaming page with Pipecat provider
│       ├── types/                    # TypeScript interfaces
│       │   ├── BotJson.ts            # RAG metadata card type
│       │   ├── ChatMessage.ts        # Chat message union type
│       │   ├── Chunk.ts              # Retrieved chunk type
│       │   └── ServerMessage.ts      # RTVI server message type
│       └── utils/
│           ├── api.ts                # REST API helper functions
│           └── chat.ts               # Chat message processing utilities
│
├── infrastructure/                  # AWS infrastructure as code
│   ├── cloudformation.yaml          # Full stack: VPC, ECS, ALB, ECR, Secrets Manager
│   ├── setup-aws.sh                 # One-command AWS provisioning script
│   └── destroy-aws.sh               # Teardown & cleanup script
│
├── scripts/                         # CI/CD & deployment automation
│   ├── build-and-push-ecr.sh        # Build Docker images → push to ECR
│   ├── create-services.sh           # Create ECS services (run once after infra setup)
│   └── deploy_aws.sh                # Force new ECS deployment
│
├── .github/workflows/
│   └── deploy.yml                   # GitHub Actions CI/CD pipeline
│
├── docker-compose.yml               # Local development orchestration
├── DEPLOYMENT.md                    # Step-by-step AWS deployment guide
├── CONTRIBUTING.md                  # Contribution guidelines
└── LICENSE                          # MIT License
```

---

## 🔌 API Reference

### Equipment Management

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/v1/equipment/` | Create new equipment |
| `GET` | `/api/v1/equipment/` | List all equipment |
| `GET` | `/api/v1/equipment/{id}` | Get equipment details |
| `POST` | `/api/v1/equipment/{id}/documents` | Upload & embed documentation files |
| `GET` | `/api/v1/equipment/{id}/documents` | List documents for an equipment |

### Voice Streaming

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/v1/stream/connect` | Initialize a new voice session |
| `WS` | `/api/v1/stream/ws/{equipment_id}` | WebSocket audio stream (Protobuf serialized) |

### Health Check

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/health` | Service health status |
| `GET` | `/` | API root with version info |

> 📖 Full interactive Swagger UI available at **`http://localhost:8000/docs`** when the backend is running.

---

## ☁️ Production Deployment (AWS)

The project includes a complete **AWS ECS Fargate** deployment setup provisioned entirely via **CloudFormation**:

| AWS Resource | Purpose |
|---|---|
| **VPC** | Isolated network with public/private subnets across 2 AZs |
| **Application Load Balancer** | HTTPS termination, health checks, request routing |
| **ECS Cluster (Fargate)** | Serverless container execution — no EC2 to manage |
| **ECR** | Private Docker image registry for backend & frontend |
| **Secrets Manager** | Secure storage for all API keys |
| **NAT Gateway** | Private subnet internet access for ECS tasks |
| **CloudWatch Logs** | Centralized container log aggregation |

```bash
# One-command AWS infrastructure provisioning
cd infrastructure
./setup-aws.sh
```

See **[DEPLOYMENT.md](DEPLOYMENT.md)** for the complete step-by-step guide including:
- IAM permissions setup
- GitHub Actions CI/CD configuration
- ECS service creation
- Secrets management
- Troubleshooting common issues

---

## 🧩 Key Design Decisions

| Decision | Rationale |
|----------|-----------|
| **Pipecat AI Framework** | Production-grade voice pipeline with smart turn detection (LocalSmartTurnV3), VAD (Silero), and interruption support |
| **MongoDB Atlas Vector Search** | Native vector search eliminates a separate vector DB (Pinecone, Weaviate) while keeping documents and vectors co-located |
| **Groq for LLM** | Ultra-low latency inference (~200 ms) — critical for real-time voice conversations where every millisecond matters |
| **LLM Tool Calling** | The LLM decides *when* to search the knowledge base, making responses more natural rather than always retrieving context |
| **Google Gemini Embeddings** | High-quality 768-dim embeddings with cosine similarity, plus a generous free tier for cost-effective prototyping |
| **ElevenLabs TTS** | Most natural-sounding voice synthesis available, essential for a polished voice experience |
| **WebSocket Transport** | Full-duplex audio streaming with Protobuf serialization for minimal overhead |
| **uv Package Manager** | 10–100× faster than pip — dramatically speeds up Docker image builds in CI/CD |
| **Tenant Isolation** | All queries filter by `tenant_id` and `equipment_id`, enabling multi-tenant SaaS extension |

---

## 🛠️ Local Development (Without Docker)

### Backend

```bash
cd backend
uv sync                    # Install dependencies (requires uv: pip install uv)
cp .env.example .env       # Configure environment variables
uv run uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

> **Note:** On first run, the Pipecat bot will download the Silero VAD model and LocalSmartTurn model (~20 seconds). Subsequent starts are instant.

### Frontend

```bash
cd frontend
npm install                # Install dependencies
npm run dev                # Start dev server at http://localhost:5173
```

> **Note:** Set `VITE_API_URL=http://localhost:8000` if running frontend separately from Docker.

---

## 🔧 Environment Variables Reference

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `MONGO_URL` | ✅ | — | MongoDB Atlas connection string |
| `DB_NAME` | ✅ | `live_db` | Target database name |
| `DEEPGRAM_API_KEY` | ✅ | — | Deepgram Speech-to-Text API key |
| `GROQ_API_KEY` | ✅ | — | Groq LLM inference API key |
| `GOOGLE_API_KEY` | ✅ | — | Google AI (Gemini) embeddings key |
| `ELEVENLABS_API_KEY` | ✅ | — | ElevenLabs Text-to-Speech API key |
| `ELEVENLABS_VOICE_ID` | ❌ | `pNInz6obpgDQGcFmaJgB` (Adam) | Custom ElevenLabs voice ID |
| `GROQ_MODEL` | ❌ | `openai/gpt-oss-20b` | Groq model name |
| `EMBEDDING_MODEL` | ❌ | `models/text-embedding-004` | Google Gemini embedding model |
| `CHUNK_SIZE` | ❌ | `1000` | Document chunk size (characters) |
| `CHUNK_OVERLAP` | ❌ | `250` | Overlap between adjacent chunks |
| `VECTOR_INDEX_NAME` | ❌ | `vector_index` | MongoDB Atlas vector index name |
| `ALLOWED_ORIGINS` | ❌ | `*` (dev) | Comma-separated CORS allowed origins |
| `ENVIRONMENT` | ❌ | — | Set to `production` to enforce CORS |

---

## 🗺️ Roadmap

- [ ] **HTTPS / SSL** — Add Let's Encrypt or ACM certificate to the ALB listener
- [ ] **Authentication** — JWT-based user auth and per-user session history
- [ ] **Conversation Memory** — Persist session transcripts to MongoDB for context continuity
- [ ] **Multi-Language Support** — Configurable STT/TTS language per equipment
- [ ] **Admin Dashboard** — Web UI for equipment and document management (currently API-only)
- [ ] **Streaming LLM Responses** — Token-by-token TTS for even lower perceived latency
- [ ] **Analytics** — Session duration, query count, and retrieval score dashboards
- [ ] **XLSX / CSV Support** — Extend text extraction to spreadsheet formats

---

## 🤝 Contributing

Contributions are welcome! Please read [CONTRIBUTING.md](CONTRIBUTING.md) for:
- Development setup instructions
- Code style guidelines (PEP 8 for Python, strict TypeScript)
- Commit message conventions (Conventional Commits)
- How to open issues and pull requests

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## 👤 Author

<p align="left">
  <strong>Naman Jaiswal</strong> — Full-Stack & AI Engineer
</p>

- 🐙 GitHub: [@jaiswal-naman](https://github.com/jaiswal-naman)

---

<p align="center">
  <sub>Built with ❤️ by <a href="https://github.com/jaiswal-naman">Naman Jaiswal</a></sub>
  <br/>
  <sub>⭐ Star this repo if you find it useful!</sub>
</p>
