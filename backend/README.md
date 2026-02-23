# RAG Voice AI Agent — Backend

FastAPI backend service for the RAG Voice AI Agent. Handles real-time voice streaming, document ingestion, embedding generation, and RAG-powered retrieval.

## Tech Stack

| Library | Purpose |
|---------|---------|
| **FastAPI** | Async web framework with automatic OpenAPI docs |
| **Pipecat AI** | Voice pipeline (STT → LLM → TTS) orchestration |
| **Motor** | Async MongoDB driver for non-blocking DB operations |
| **LangChain** | Text splitting (`RecursiveCharacterTextSplitter`) |
| **Google Generative AI** | `text-embedding-004` — 768-dim embeddings |
| **Deepgram** | Real-time Speech-to-Text with diarization |
| **Groq** | Ultra-low-latency LLM inference |
| **ElevenLabs** | Natural-sounding Text-to-Speech synthesis |
| **PyPDF / python-docx** | PDF and DOCX text extraction |
| **Loguru** | Structured, colorized logging |
| **uv** | Fast Python package manager (10–100× faster than pip) |

## Quick Start

```bash
# Install uv (if not already installed)
pip install uv

# Install dependencies
uv sync

# Configure environment
cp .env.example .env   # Fill in your API keys

# Run development server (with hot reload)
uv run uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

The API will be available at `http://localhost:8000`.
Interactive Swagger UI: `http://localhost:8000/docs`

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/` | API root with version info |
| `GET` | `/health` | Health check |
| `POST` | `/api/v1/equipment/` | Create equipment |
| `GET` | `/api/v1/equipment/` | List all equipment |
| `GET` | `/api/v1/equipment/{id}` | Get equipment by ID |
| `POST` | `/api/v1/equipment/{id}/documents` | Upload & embed documents |
| `GET` | `/api/v1/equipment/{id}/documents` | List documents |
| `POST` | `/api/v1/stream/connect` | Initialize voice session |
| `WS` | `/api/v1/stream/ws/{equipment_id}` | WebSocket audio stream |

## Module Structure

```
main.py              → FastAPI app, CORS, middleware, router registration
app/
├── bot.py           → Pipecat pipeline definition
│                        Deepgram STT → Groq LLM (tool calling) → ElevenLabs TTS
├── config.py        → Pydantic Settings loaded from .env
├── database.py      → Async Motor MongoDB connection (singleton)
├── models/
│   ├── document.py  → Document upload/storage Pydantic schema
│   ├── equipment.py → Equipment CRUD Pydantic schema
│   └── rag.py       → ChunkContent, ChunkMetadata, RetrievalResult schemas
├── routers/
│   ├── equipment.py → Equipment CRUD + full document ingestion pipeline
│   │                    (upload → extract → chunk → embed → store)
│   └── stream.py    → WebSocket voice session endpoint
└── services/
    ├── embeddings.py       → Google Gemini embed_text() + split_text()
    ├── rag.py              → MongoDB Atlas $vectorSearch aggregation pipeline
    └── text_extraction.py  → PDF (pypdf) and DOCX (python-docx) extractors
```

## Document Ingestion Pipeline

When a file is uploaded to `POST /api/v1/equipment/{id}/documents`:

1. **Upload** — File bytes received via multipart form
2. **Validate** — Check MIME type and file extension are supported
3. **Extract** — PDF/DOCX → plain text via `TextExtractionService`
4. **Split** — Text → chunks via `RecursiveCharacterTextSplitter` (size=1000, overlap=250)
5. **Embed** — Each chunk → 768-dim vector via Google Gemini `text-embedding-004`
6. **Store** — Vectors stored in MongoDB `document_chunks` collection with metadata
7. **Index** — MongoDB Atlas Vector Search index enables cosine similarity queries

## Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `MONGO_URL` | ✅ | — | MongoDB Atlas connection string |
| `DB_NAME` | ✅ | `live_db` | Target database name |
| `DEEPGRAM_API_KEY` | ✅ | — | Deepgram STT API key |
| `GROQ_API_KEY` | ✅ | — | Groq LLM API key |
| `GOOGLE_API_KEY` | ✅ | — | Google AI Gemini embeddings key |
| `ELEVENLABS_API_KEY` | ✅ | — | ElevenLabs TTS API key |
| `ELEVENLABS_VOICE_ID` | ❌ | `pNInz6obpgDQGcFmaJgB` | Custom ElevenLabs voice ID |
| `GROQ_MODEL` | ❌ | `openai/gpt-oss-20b` | Groq model name |
| `CHUNK_SIZE` | ❌ | `1000` | Text chunk size (characters) |
| `CHUNK_OVERLAP` | ❌ | `250` | Overlap between adjacent chunks |
| `VECTOR_INDEX_NAME` | ❌ | `vector_index` | MongoDB Atlas vector index name |
| `ALLOWED_ORIGINS` | ❌ | `*` | CORS allowed origins (comma-separated) |
| `ENVIRONMENT` | ❌ | — | Set to `production` to enforce strict CORS |

---

**Author: [Naman Jaiswal](https://github.com/jaiswal-naman)**
