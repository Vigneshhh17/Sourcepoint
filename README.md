# Sourcepoint

## The problem

Finding a reliable answer across many documents takes time. Important details can be spread across files, and a search result alone does not show whether the answer is supported by evidence.

## The solution

Sourcepoint searches a document collection, brings together relevant passages, and uses an AI model to form an answer. It shows source citations so you can check the evidence. When the documents do not support an answer, Sourcepoint can say what information is missing.

You can investigate questions across the HotpotQA Wikipedia dataset or search and upload your own documents.

## How it works

1. **Load documents** — Sourcepoint reads supported files, extracts text and useful metadata, then splits long documents into smaller passages.
2. **Build the search index** — It creates semantic vectors for meaning-based search and a BM25 index for keyword search. The indexes are stored in Qdrant.
3. **Find evidence** — A question searches the selected collection. The results are combined and reranked to bring useful passages forward.
4. **Write and check an answer** — The AI investigator uses the passages to answer. A verification step checks claims and citations against the retrieved evidence.
5. **Show the result** — The interface displays the answer, citations, source passages, and investigation trace.

The backend uses Python and FastAPI. The browser interface is served by the same app. Local Qdrant works without a separate database service. Optional LLM providers include Groq and Gemini.

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
