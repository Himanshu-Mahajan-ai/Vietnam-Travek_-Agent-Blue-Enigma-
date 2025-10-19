# Vietnam Travel Agent (Gemini + Pinecone + Neo4j + Streamlit)

A fast, structured trip planner for Vietnam. It retrieves relevant places via Pinecone, enriches with Neo4j graph facts, and asks Gemini 2.5 to generate a clear day-by-day itinerary. A deterministic fallback ensures users always get a usable plan. Exports to TXT and PDF.

## Features

- Clean Streamlit web UI
- Preferences: budget, duration, travelers, origin, hotels, beaches, family-friendly, travel modes
- Quick suggestion buttons
- Vector search (Pinecone) + Knowledge Graph (Neo4j)
- Structured system prompt for well-formatted itineraries
- Deterministic fallback itinerary when the model response is blocked/empty
- TXT and PDF download (robust Unicode sanitization and wrapping)

## Project layout

```
hybrid_chat_test/
├─ hybrid_chat_test/
│  ├─ config.py              # API keys & service config
│  ├─ hybrid_chat.py         # Core logic (embed, search, graph, prompt, chat)
│  ├─ web_streamlit.py       # Streamlit UI (preferences, results, download)
│  ├─ pinecone_upload.py     # (optional) data upload to Pinecone
│  ├─ load_to_neo4j.py       # (optional) data load into Neo4j
│  ├─ visualize_graph.py     # (optional) graph visualization helpers
│  ├─ requirements.txt       # dependencies
│  └─ vietnam_travel_dataset.json  # sample dataset
└─ README.md
```

## Prerequisites

- Python 3.10+
- Accounts/keys for:
  - Google Generative AI (Gemini)
  - Pinecone (serverless index recommended)
  - Neo4j (local or Aura)

Configure `config.py` with:

```python
# config.py (example)
GEMINI_API_KEY = "..."
PINECONE_API_KEY = "..."
PINECONE_INDEX_NAME = "vietnam-travel"
PINECONE_VECTOR_DIM = 768  # or your embedding dimension
NEO4J_URI = "neo4j://localhost:7687"
NEO4J_USER = "neo4j"
NEO4J_PASSWORD = "password"
```

## Install

```powershell
# From the repo root
python -m pip install -r hybrid_chat_test\hybrid_chat_test\requirements.txt
```

If you want PDF downloads and they’re disabled, ensure `fpdf2` is installed (already in requirements).

## Run the web app

```powershell
streamlit run hybrid_chat_test\hybrid_chat_test\web_streamlit.py
```

- Set preferences in the sidebar
- Click a quick-suggestion or enter your own query
- Click “Generate Itinerary”
- Expand details as needed
- Download TXT or PDF

## CLI (optional)

```powershell
python hybrid_chat_test\hybrid_chat_test\hybrid_chat.py
```

## How it works (short)

1. The UI builds a concise user request including preferences (budget, duration, hotels, beaches, etc.).
2. The app embeds the query and searches Pinecone for top matches (places).
3. It fetches related facts from Neo4j.
4. It builds a structured system prompt + context summaries and calls Gemini 2.5.
5. If Gemini returns nothing/blocked, a fast fallback itinerary is generated from the retrieved data.

## Troubleshooting

- Pinecone errors: confirm index name and vector dimension in `config.py`, and that data was uploaded.
- Neo4j connection: check `NEO4J_URI`, username/password, and network.
- PDF errors: the app sanitizes Unicode and wraps long text. For full Unicode (Vietnamese diacritics), embed a TTF font via fpdf2 (ask us to enable).
- Slow runs: reduce Top-K in the UI, ensure Streamlit cache is warm.

## Roadmap ideas

- Seasonal hints (monsoon windows, best months per region)
- Maps and distance tables
- More hotel filters and price tiers
- Pin favorites and regenerate with constraints
- Full Unicode font embedding for PDF
