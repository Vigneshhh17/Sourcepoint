# Sourcepoint

Sourcepoint is a document research app. Ask a question, search your documents, and review the sources behind an answer.

## What it does

- Searches the HotpotQA Wikipedia dataset and documents you upload.
- Gives answers with citations and shows the supporting passages.
- Lets you upload, browse, replace, and remove your own documents.
- Supports multi-step investigations and direct search.

## Run on Windows

You need Python 3.11 and `uv` installed.

```powershell
uv venv --python 3.11 .venv
uv pip install --python .venv\Scripts\python.exe -e ".[dev]"
Copy-Item .env.example .env
```

Open `.env` and add at least one provider key, such as `GROQ_API_KEY` or `GEMINI_API_KEY`. Keep `.env` private; it should not be committed.

Prepare and index the HotpotQA documents:

```powershell
.\.venv\Scripts\python.exe -m scripts.prepare_hotpotqa
.\.venv\Scripts\python.exe -m scripts.index_corpus --corpus hotpotqa --subset 100
```

The subset includes the supporting paragraphs for the sample evaluation questions plus 100 distractor documents. To index the full dataset instead, omit `--subset 100`; the full index is much larger and takes longer.

Start Sourcepoint:

```powershell
.\.venv\Scripts\python.exe -m uvicorn app.api.main:app --port 8000
```

Open <http://127.0.0.1:8000>.

## API keys

Set keys in your local `.env` file. Available providers and model defaults are listed in `app/config.py`. Never put a real key in `.env.example` or commit it to Git.
