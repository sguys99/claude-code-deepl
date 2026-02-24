# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

**Run the application:**
```bash
./run.sh
# Or manually:
cd backend && uv run uvicorn app:app --reload --port 8000
```

**Install dependencies:**
```bash
uv sync
```

**Access points:**
- Web interface: http://localhost:8000
- API docs: http://localhost:8000/docs

## Architecture

This is a RAG (Retrieval-Augmented Generation) chatbot for course materials with a FastAPI backend and vanilla JS frontend.

### Core Components (backend/)

- **RAGSystem** (`rag_system.py`) - Main orchestrator that coordinates all components
- **VectorStore** (`vector_store.py`) - ChromaDB wrapper with two collections:
  - `course_catalog`: Course metadata for semantic course name resolution
  - `course_content`: Chunked course content for retrieval
- **AIGenerator** (`ai_generator.py`) - Claude API integration with tool execution loop
- **DocumentProcessor** (`document_processor.py`) - Parses course documents into chunks
- **ToolManager/CourseSearchTool** (`search_tools.py`) - Tool-based search exposed to Claude

### Data Flow

1. Documents in `docs/` are loaded at startup and chunked
2. Chunks are embedded and stored in ChromaDB (persisted in `backend/chroma_db/`)
3. User queries go through `/api/query` endpoint
4. Claude uses the search tool to retrieve relevant chunks
5. Claude generates response based on retrieved context

### Configuration (`config.py`)

Key settings: `CHUNK_SIZE=800`, `CHUNK_OVERLAP=100`, `MAX_RESULTS=5`, embedding model `all-MiniLM-L6-v2`

## Development Rules

- Always use `uv` for all dependency management and Python execution (never use `pip` or `python` directly)
  - Install dependencies: `uv sync`
  - Add packages: `uv add <package>`
  - Run Python scripts: `uv run <script>`
  - Run server: `uv run uvicorn ...`

## Environment

Requires `ANTHROPIC_API_KEY` in `.env` file at project root.
