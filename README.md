# Sourcepoint

## The problem

Finding a reliable answer across many documents takes time. Important details can be spread across files, and a search result alone does not show whether the answer is supported by evidence.

## The solution

Sourcepoint searches a document collection, brings together relevant passages, and uses an AI model to form an answer. It shows source citations so you can check the evidence. When the documents do not support an answer, Sourcepoint can say what information is missing.

You can investigate questions across the HotpotQA Wikipedia dataset or search and upload your own documents.

## How it works

1. **Prepare documents** — Sourcepoint extracts text and metadata from supported files and splits long documents into passages.
2. **Index the passages** — It stores semantic vectors and a BM25 keyword index in Qdrant.
3. **Retrieve evidence with RAG** — Retrieval-Augmented Generation (RAG) searches by meaning and keywords, combines the results, and reranks them. The best passages are sent to the language model as context for its answer.
4. **Investigate with the AI agent** — In agent mode, the model can search again, follow references, and read more documents before it submits an answer. Single-shot mode answers from one retrieval pass.
5. **Verify and present** — Sourcepoint checks claims and citations against retrieved passages. It shows the answer, sources, and investigation steps, or reports when evidence is insufficient.

## API and architecture

The application uses a Python **FastAPI** backend and a browser interface served by the same app. The API connects document ingestion, Qdrant search, the AI agent, and answer verification.

- `POST /api/ask` runs an investigation and returns the answer, citations, and sources.
- `POST /api/ask/stream` streams agent steps and the final answer as they are produced.
- `GET /api/search` returns ranked passages for a query.
- `/api/documents` supports listing, uploading, replacing, and deleting documents.
- `GET /api/health` reports backend and model-provider status.

Qdrant can run locally or as a hosted database. Groq and Gemini are supported as language-model providers; add their keys to `.env`.

## Run on Windows

Install Python 3.11 and `uv`, then run these commands in PowerShell:

```powershell
uv venv --python 3.11 .venv
uv pip install --python .venv\Scripts\python.exe -e ".[dev]"
Copy-Item .env.example .env
```

Open `.env` and add at least one provider key, such as `GROQ_API_KEY` or `GEMINI_API_KEY`. Keep real keys in `.env`; do not commit that file.

## Prepare HotpotQA

```powershell
.\.venv\Scripts\python.exe -m scripts.prepare_hotpotqa
.\.venv\Scripts\python.exe -m scripts.index_corpus --corpus hotpotqa --subset 100
```

The subset includes supporting paragraphs for the sample questions and 100 additional documents. To index the full dataset, omit `--subset 100`; the full index takes longer and uses more disk space.

## Start Sourcepoint

```powershell
.\.venv\Scripts\python.exe -m uvicorn app.api.main:app --port 8000
```

Open <http://127.0.0.1:8000>.
