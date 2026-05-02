# hallucination_detection_system_RAG
# hallucination_detection_sys 🔍

> Production-grade RAG system with real-time LLM hallucination detection.
> Built with LangGraph · ChromaDB · Claude · FastAPI · Streamlit.

## Architecture

User Query → LangGraph Pipeline → FastAPI → Streamlit UI
                 ↓
     [retrieve] → [check_relevance] → [generate] → [detect_hallucination] → [finalize]

## Setup

### 1. Clone and install

git clone https://github.com/YOUR_USERNAME/hallucination_detection_sys.git
cd hallucination_detection_sys
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt

### 2. Configure environment

cp .env.example .env
# Edit .env and set API_KEY

### 3. Add documents

Place PDF/TXT/MD files in the `data/` directory.

### 4. Run the API

uvicorn app.main:app --reload --port 8000

### 5. Run the frontend (new terminal)

streamlit run frontend/app.py

### 6. Ingest documents via CLI (optional)

python -c "
from ingestion.ingest import DocumentIngestionPipeline
p = DocumentIngestionPipeline()
p.ingest_directory('./data')
"

## API Reference

| Method | Endpoint              | Description                        |
|--------|-----------------------|------------------------------------|
| POST   | /query                | Run RAG + hallucination detection  |
| POST   | /ingest/file          | Upload and ingest a single file    |
| POST   | /ingest/directory     | Ingest all files in a directory    |
| GET    | /stats                | Vector store statistics            |
| DELETE | /vectorstore/reset    | Wipe vector store                  |
| GET    | /health               | Liveness check                     |
| GET    | /docs                 | Swagger UI                         |

## Hallucination Detection

The system uses an LLM-as-Judge pattern:
- After generating an answer, a second Claude call evaluates faithfulness.
- Every factual claim in the answer is checked against the retrieved context.
- Faithfulness Score: 0.0 (fully hallucinated) → 1.0 (fully grounded).
- Answers below 0.7 receive a hallucination warning in the response.
