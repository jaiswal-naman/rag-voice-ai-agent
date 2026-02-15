# Contributing to RAG Voice AI Agent

Thank you for your interest in contributing to the **RAG Voice AI Agent** project! This document provides guidelines and instructions for contributing.

## 🚀 Getting Started

### Prerequisites

- Python 3.12+
- Node.js 18+
- Docker Desktop
- Git

### Development Setup

1. **Fork & Clone**
   ```bash
   git clone https://github.com/jaiswal-naman/rag-voice-ai-agent.git
   cd rag-voice-ai-agent
   ```

2. **Backend Setup**
   ```bash
   cd backend
   cp .env.example .env     # Fill in your API keys
   uv sync                  # Install Python dependencies
   uv run uvicorn main:app --host 0.0.0.0 --port 8000 --reload
   ```

3. **Frontend Setup**
   ```bash
   cd frontend
   npm install
   npm run dev
   ```

4. **Full Stack (Docker)**
   ```bash
   docker-compose up --build
   ```

## 📝 How to Contribute

### Reporting Bugs

- Use [GitHub Issues](https://github.com/jaiswal-naman/rag-voice-ai-agent/issues)
- Include steps to reproduce, expected behavior, and actual behavior
- Add relevant logs or screenshots

### Suggesting Features

- Open a GitHub Issue with the `enhancement` label
- Describe the use case and proposed solution

### Submitting Code

1. Create a feature branch from `main`:
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. Make your changes following the code style guidelines below

3. Test your changes locally (both with and without Docker)

4. Commit with clear, descriptive messages:
   ```bash
   git commit -m "feat: add support for XLSX document upload"
   ```

5. Push and open a Pull Request against `main`

## 🎨 Code Style

### Python (Backend)
- Follow PEP 8 conventions
- Use type hints for function signatures
- Use `loguru` for logging (not `print()`)
- Pydantic models for data validation

### TypeScript (Frontend)
- Use functional components with hooks
- TypeScript strict mode — no `any` unless absolutely necessary
- Tailwind CSS for styling (no inline styles)
- Component files in PascalCase

### Commits
Follow [Conventional Commits](https://www.conventionalcommits.org/):
- `feat:` — New feature
- `fix:` — Bug fix
- `docs:` — Documentation changes
- `refactor:` — Code refactoring
- `chore:` — Maintenance tasks

## 📂 Project Structure

- `backend/` — FastAPI application with Pipecat voice pipeline
- `frontend/` — React + TypeScript UI
- `infrastructure/` — AWS CloudFormation templates
- `scripts/` — CI/CD and deployment automation

## 📄 License

By contributing, you agree that your contributions will be licensed under the [MIT License](LICENSE).

---

**Maintained by [Naman Jaiswal](https://github.com/jaiswal-naman)**
