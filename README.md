<p align="center">
  <img src="https://img.shields.io/badge/Python-3.12+-3776AB?style=for-the-badge&logo=python&logoColor=white" />
  <img src="https://img.shields.io/badge/TypeScript-5.x-3178C6?style=for-the-badge&logo=typescript&logoColor=white" />
  <img src="https://img.shields.io/badge/FastAPI-0.128+-009688?style=for-the-badge&logo=fastapi&logoColor=white" />
  <img src="https://img.shields.io/badge/React-18-61DAFB?style=for-the-badge&logo=react&logoColor=black" />
  <img src="https://img.shields.io/badge/Docker-Ready-2496ED?style=for-the-badge&logo=docker&logoColor=white" />
  <img src="https://img.shields.io/badge/AWS-ECS%20Fargate-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white" />
</p>

# 🎙️ RAG Voice AI Agent

> **An intelligent, real-time voice assistant powered by Retrieval-Augmented Generation (RAG) for industrial equipment support.**

Built by [**Naman Jaiswal**](https://github.com/jaiswal-naman)

---

## 📌 Overview

**RAG Voice AI Agent** is a production-ready, full-stack voice AI system that enables real-time, speech-based interaction with a knowledge base. Designed for industrial use cases, it allows operators and support agents to query equipment documentation hands-free using natural voice commands and receive instant, contextually accurate spoken responses.

The system combines **real-time speech processing**, **vector-based document retrieval**, and **large language model inference** into a seamless conversational pipeline — all deployable with a single `docker-compose up`.

### 🎯 Key Highlights

- **Voice-First Interface** — Speak naturally, get instant spoken answers
- **RAG-Powered Accuracy** — Responses grounded in your uploaded documents, zero hallucination
- **Real-Time Streaming** — Sub-second latency via WebSocket-based audio pipeline
- **Production-Ready** — Dockerized, AWS-deployable, with full CI/CD support
- **Multi-Document Support** — Upload PDFs, DOCX, and text files per equipment

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

### Data Flow

1. **User speaks** → Microphone captures audio via browser
2. **Deepgram STT** → Converts speech to text in real-time
3. **LLM (Groq)** → Processes query, triggers RAG tool call when needed
4. **RAG Engine** → Generates embeddings (Google Gemini) → MongoDB Atlas Vector Search
5. **LLM Response** → Formulates answer from retrieved context
6. **ElevenLabs TTS** → Converts response to natural speech
7. **User hears response** → Audio streamed back via WebSocket

---

## ⚡ Tech Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| **Frontend** | React 18, TypeScript, Tailwind CSS | Real-time voice chat UI |
| **Voice SDK** | Pipecat AI Client SDK | WebSocket audio transport |
| **Backend** | FastAPI, Python 3.12+ | REST API + WebSocket server |
| **Voice Pipeline** | Pipecat AI Framework | Orchestrates STT → LLM → TTS |
| **Speech-to-Text** | Deepgram | Real-time audio transcription |
| **LLM** | Groq (Llama 3 / GPT-oss) | Fast inference for responses |
| **Text-to-Speech** | ElevenLabs | Natural voice synthesis |
| **Embeddings** | Google Gemini (text-embedding-004) | Document vectorization |
| **Database** | MongoDB Atlas | Document storage + Vector Search |
| **DevOps** | Docker, Docker Compose | Containerized deployment |
| **Cloud** | AWS ECS Fargate, CloudFormation | Production infrastructure |

---

## 🚀 Quick Start

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- API keys for: [Deepgram](https://console.deepgram.com/), [ElevenLabs](https://elevenlabs.io/), [Groq](https://console.groq.com/), [Google AI](https://aistudio.google.com/)
- [MongoDB Atlas](https://www.mongodb.com/atlas) cluster with Vector Search index

### 1. Clone the Repository

```bash
git clone https://github.com/jaiswal-naman/rag-voice-ai-agent.git
cd rag-voice-ai-agent
```

### 2. Configure Environment

Create `backend/.env`:

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
| Frontend | http://localhost:3000 |
| Backend API | http://localhost:8000 |
| API Documentation | http://localhost:8000/docs |

### 5. Usage

1. Open the frontend at `http://localhost:3000`
2. Create equipment via the API (`POST /api/v1/equipment/`)
3. Upload documentation files (PDF/DOCX) for the equipment
4. Select the equipment in the UI and click **Connect**
5. Start speaking — the AI will answer based on your uploaded documents

---

## 📁 Project Structure

```
rag-voice-ai-agent/
├── backend/                    # FastAPI backend service
│   ├── main.py                 # Application entry point
│   ├── Dockerfile              # Backend container config
│   ├── pyproject.toml          # Python dependencies (uv)
│   └── app/
│       ├── bot.py              # Pipecat voice pipeline definition
│       ├── config.py           # Environment configuration
│       ├── database.py         # MongoDB connection manager
│       ├── models/             # Pydantic data models
│       │   ├── document.py     # Document schema
│       │   ├── equipment.py    # Equipment schema
│       │   └── rag.py          # RAG retrieval schemas
│       ├── routers/            # API route handlers
│       │   ├── equipment.py    # Equipment CRUD + doc upload
│       │   └── stream.py       # WebSocket streaming endpoint
│       └── services/           # Business logic
│           ├── embeddings.py   # Google Gemini embeddings
│           ├── rag.py          # Vector search & retrieval
│           └── text_extraction.py  # PDF/DOCX text parser
│
├── frontend/                   # React frontend application
│   ├── Dockerfile              # Frontend container config
│   ├── nginx.conf              # Nginx reverse proxy config
│   ├── package.json            # Node.js dependencies
│   └── src/
│       ├── App.tsx             # Router setup
│       ├── components/         # UI components
│       │   ├── RealTimeChatPanel.tsx  # Main voice chat interface
│       │   ├── BotMessageBubble.tsx   # Bot response display
│       │   └── UserMessageBubble.tsx  # User message display
│       ├── hooks/              # Custom React hooks
│       │   └── pipecat-chat-events.ts # Voice event handlers
│       ├── pages/
│       │   └── Stream.tsx      # Main streaming page
│       └── utils/              # Helper utilities
│
├── infrastructure/             # AWS deployment configs
│   ├── cloudformation.yaml     # Full AWS infrastructure (VPC, ECS, ALB)
│   ├── setup-aws.sh            # Automated AWS provisioning
│   └── destroy-aws.sh          # Clean teardown script
│
├── scripts/                    # CI/CD & deployment scripts
│   ├── build-and-push-ecr.sh   # Docker → ECR push
│   ├── create-services.sh      # ECS service creation
│   └── deploy_aws.sh           # Full deployment pipeline
│
├── docker-compose.yml          # Local development orchestration
├── DEPLOYMENT.md               # Detailed deployment guide
├── CONTRIBUTING.md             # Contribution guidelines
└── LICENSE                     # MIT License
```

---

## 🔌 API Reference

### Equipment Management

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/v1/equipment/` | Create new equipment |
| `GET` | `/api/v1/equipment/` | List all equipment |
| `GET` | `/api/v1/equipment/{id}` | Get equipment details |
| `POST` | `/api/v1/equipment/{id}/documents` | Upload documentation files |

### Voice Streaming

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/v1/stream/connect` | Initialize voice session |
| `WS` | `/api/v1/stream/ws/{equipment_id}` | WebSocket audio stream |

### Health Check

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/health` | Service health status |

Full interactive docs available at `/docs` (Swagger UI) when the backend is running.

---

## ☁️ Production Deployment (AWS)

The project includes a complete **AWS ECS Fargate** deployment setup with:

- **VPC** with public/private subnets across 2 AZs
- **Application Load Balancer** with health checks
- **ECS Cluster** with Fargate tasks
- **ECR** repositories for container images
- **AWS Secrets Manager** for API key management
- **NAT Gateway** for private subnet internet access
- **CloudFormation** for infrastructure-as-code

```bash
# One-command AWS deployment
cd infrastructure
./setup-aws.sh
```

See [DEPLOYMENT.md](DEPLOYMENT.md) for the complete step-by-step deployment guide.

---

## 🧩 Key Design Decisions

| Decision | Rationale |
|----------|-----------|
| **Pipecat AI Framework** | Production-grade voice pipeline with smart turn detection, VAD, and interruptibility |
| **MongoDB Atlas Vector Search** | Native vector search eliminates need for a separate vector DB (Pinecone, Weaviate) |
| **Groq for LLM** | Ultra-low latency inference (~200ms) critical for real-time voice conversations |
| **Google Gemini Embeddings** | High-quality 768-dim embeddings with generous free tier |
| **ElevenLabs TTS** | Most natural-sounding voice synthesis for production use |
| **WebSocket Transport** | Full-duplex audio streaming with minimal overhead |
| **uv Package Manager** | 10-100x faster than pip for Python dependency resolution |

---

## 🛠️ Development

### Backend (without Docker)

```bash
cd backend
uv sync                    # Install dependencies
cp .env.example .env       # Configure environment
uv run uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

### Frontend (without Docker)

```bash
cd frontend
npm install                # Install dependencies
npm run dev                # Start dev server on :5173
```

### Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `MONGO_URL` | ✅ | MongoDB Atlas connection string |
| `DB_NAME` | ✅ | Database name |
| `DEEPGRAM_API_KEY` | ✅ | Deepgram STT API key |
| `GROQ_API_KEY` | ✅ | Groq LLM API key |
| `GOOGLE_API_KEY` | ✅ | Google AI embeddings key |
| `ELEVENLABS_API_KEY` | ✅ | ElevenLabs TTS API key |
| `ELEVENLABS_VOICE_ID` | ❌ | Custom voice ID (default: Adam) |
| `GROQ_MODEL` | ❌ | Model name (default: `openai/gpt-oss-20b`) |
| `CHUNK_SIZE` | ❌ | Text chunk size (default: 1000) |
| `CHUNK_OVERLAP` | ❌ | Chunk overlap (default: 250) |
| `ALLOWED_ORIGINS` | ❌ | CORS origins (comma-separated) |

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## 👤 Author

**Naman Jaiswal**

- GitHub: [@jaiswal-naman](https://github.com/jaiswal-naman)

---

<p align="center">
  <sub>Built with ❤️ by Naman Jaiswal</sub>
</p>
