# JW Marriott RAG Concierge Assistant

A Retrieval-Augmented Generation (RAG) chatbot that answers hotel questions
(rooms, dining, amenities, spa, events, policies) using a local hotel data
CSV, ChromaDB for vector search, and an optional Hugging Face model for
natural-language answers.

## How it works

- **Embeddings**: `sentence-transformers/all-MiniLM-L6-v2` (runs locally,
  no API key needed) turns hotel data and user questions into vectors.
- **Vector store**: [Chroma](https://www.trychroma.com/), persisted to
  `chroma_db/`.
- **Answer generation**: `google/flan-t5-base` via the free
  [Hugging Face Inference API](https://huggingface.co/docs/api-inference).
  If no `HF_TOKEN` is set, the app falls back to a built-in
  retrieval-based answer formatter — no API key is required to run it.
- **Off-topic detection**: questions unrelated to the hotel are detected
  via similarity-score thresholding and get a polite redirect instead of
  a hallucinated answer.

## Requirements

- Python 3.10+
- (Optional) A free [Hugging Face account](https://huggingface.co/join)
  and [access token](https://huggingface.co/settings/tokens) for
  LLM-generated answers.

## Setup & run

### Windows

Double-click **`setup.bat`** once (creates a virtual environment,
installs dependencies, and builds the vector database), then double-click
**`run.bat`** any time to start the app. Double-click **`stop.bat`** to
stop it.

If Python isn't installed or isn't on your `PATH`, `setup.bat` will offer
to fix that automatically via `add_python_to_path.bat`.

### macOS / Linux

```bash
./setup.sh   # first-time setup: venv, dependencies, vector DB
./run.sh     # start the app
./stop.sh    # stop the app
```

Both `run` scripts open the app in your browser automatically at
`http://localhost:8501`.

### Providing a Hugging Face token (optional)

Either:
- Set the `HF_TOKEN` environment variable before running, or
- Enter your token directly in the app's sidebar once it's running.

Without a token, the app still works fully, using retrieval-based answers
instead of LLM-generated ones.

## Project structure

```
app.py              Streamlit chat UI
ingest.py            Builds the Chroma vector store from data/hotel_data.csv
rag_chain.py          Core RAG logic: retrieval, off-topic detection, answer generation
data/hotel_data.csv   Hotel knowledge base (rooms, dining, amenities, policies, etc.)
requirements.txt      Python dependencies
setup.bat / setup.sh  First-time environment setup
run.bat / run.sh      Launch the app
stop.bat / stop.sh    Stop the app
```

## Updating the hotel data

Edit `data/hotel_data.csv`, then re-run `python ingest.py` (or delete the
`chroma_db/` folder and restart the app — it will rebuild automatically).
