# Farhan Fitness — RAG Knowledge Worker

A small **retrieval-augmented generation (RAG)** demo for a fictional gym, **Farhan Fitness LLC**. Markdown files under `knowledge-base/` act as the company’s “internal docs.” The notebook loads them, chunks the text, embeds them into **Chroma**, and answers questions with **OpenAI** chat + embeddings—optionally through a **Gradio** chat UI.

## What’s in this repo

| Path | Purpose |
|------|--------|
| `farhanFitness.ipynb` | End-to-end notebook: setup → ingest → RAG → Gradio app (includes short comments on the important steps) |
| `knowledge-base/` | Mock markdown content, split by **topic folders** (see below) |

### Knowledge base layout

Each **top-level folder** under `knowledge-base/` is one **category**. The notebook only loads folders listed in `KNOWLEDGE_SUBFOLDERS` inside the config cell.

| Folder | Contents (illustrative) |
|--------|-------------------------|
| `company/` | About, services, awards |
| `employees/` | Careers, coaches (“pilots” filename is legacy structure), management |
| `membership/` | Locations / schedule-style info, passes, booking |
| `classes/` | Schedules, program descriptions |
| `equipment/` | Floor equipment, safety rules |
| `policies/` | Membership terms, privacy (mock) |

All business details are **fabricated for coursework**—not a real gym’s operational data.

## How it works (high level)

1. **Ingestion** — For each category folder, `DirectoryLoader` reads every `.md` file. Documents are split into overlapping chunks (`RecursiveCharacterTextSplitter`), embedded with `text-embedding-3-large`, and stored in a persistent Chroma DB directory (`vector_db/`).
2. **Retrieval** — A user question (plus prior user turns, concatenated) is passed to the retriever; the top‑`k` chunks are pulled by similarity.
3. **Generation** — Those chunks are pasted into `SYSTEM_PROMPT` as `{context}`, and the chat model answers using that context (and admits uncertainty when needed).
4. **UI** — Gradio shows the conversation (`type="messages"`) and a side panel of retrieved sources for transparency.

## Prerequisites

- Python 3.10+ recommended (notebook uses `list[str]` style hints).
- Environment variables (e.g. in `.env` at your project root or next to the notebook):

  ```bash
  OPENAI_API_KEY=sk-...
  ```

- Installed packages (typical for this stack):

  ```text
  langchain-openai langchain-chroma langchain-community langchain-text-splitters
  langchain-core gradio python-dotenv chromadb
  ```

  Exact versions depend on your course environment.

## How to run

1. Open `farhanFitness.ipynb` in Jupyter, VS Code, or Cursor.
2. Run cells **top to bottom** at least once:
   - Imports and config  
   - **Ingestion** cell (builds / refreshes `vector_db/`)  
   - **RAG** cell (reloads Chroma + defines `answer_question`)  
   - Optional: **test** cell to sanity-check answers  
   - **Gradio** cell to open the browser UI  

3. Run the notebook with the **working directory** set to this folder (`farhan/`) so paths `knowledge-base` and `vector_db` resolve correctly.

## What not to commit

- **`.env`** with real API keys  
- **`vector_db/`** if you prefer to rebuild the index locally (add to `.gitignore` if you like)  

## Credits

Structure inspired by community contributions in the LLM engineering course (e.g. markdown knowledge base + Chroma + Gradio pattern). Branding and copy are original mock data for **Farhan Fitness**.

Inspired by Ed Donner's LLM Engineering course on Udemy
