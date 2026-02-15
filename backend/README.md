# RAG Voice AI Agent — Backend

FastAPI backend service for the RAG Voice AI Agent. Handles real-time voice streaming, document management, embeddings generation, and RAG-powered retrieval.

## Tech Stack

- **FastAPI** — Async web framework
- **Pipecat AI** — Voice pipeline (STT → LLM → TTS)
- **Motor** — Async MongoDB driver
- **LangChain** — Text splitting & Google Gemini embeddings
- **uv** — Fast Python package manager

## Quick Start

```bash
# Install dependencies
uv sync

# Configure environment
cp .env.example .env   # Fill in your API keys

# Run development server
uv run uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/health` | Health check |
| `POST` | `/api/v1/equipment/` | Create equipment |
| `GET` | `/api/v1/equipment/` | List equipment |
| `POST` | `/api/v1/equipment/{id}/documents` | Upload docs |
| `POST` | `/api/v1/stream/connect` | Start voice session |
| `WS` | `/api/v1/stream/ws/{id}` | Audio WebSocket |

## Architecture

```
main.py → FastAPI app setup
app/
├── bot.py          → Pipecat pipeline (Deepgram STT → Groq LLM → ElevenLabs TTS)
├── config.py       → Pydantic settings from .env
├── database.py     → MongoDB connection
├── models/         → Data schemas
├── routers/        → API endpoints
└── services/       → RAG, embeddings, text extraction
```

---

**Author: Naman Jaiswal**
