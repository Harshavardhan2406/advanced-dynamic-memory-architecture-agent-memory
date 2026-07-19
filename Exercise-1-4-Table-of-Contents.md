# Exercises 1–4 — Table of Contents & Task Summaries

This file consolidates the Table of Contents for Exercises 1–4 and provides short, actionable bullet-point summaries for each task.

---

## Table of Contents

- Exercise 1 — Environment Setup & Local Memory
  - Task 1.1: Verify tools and open the project
  - Task 1.2: Review environment configuration
  - Task 1.3: Install dependencies & run basic demo
  - Task 1.4: Observe memory behavior

- Exercise 2 — Agent Framework Integration
  - Task 2.1: Study the integration pattern
  - Task 2.2: Run the financial advisor demo
  - Task 2.3: Compare agent-driven memory retrieval
  - Task 2.4: Long-term insight extraction

- Exercise 3 — Azure Cosmos DB Backend
  - Task 3.1: Understand backend options
  - Task 3.2: Run the Cosmos DB demo
  - Task 3.3: Verify data persisted in Cosmos DB
  - Task 3.4: Concept check: other backends

- Exercise 4 — Live Server Mode & Streamlit UI
  - Task 4.1: Start the memory server
  - Task 4.2: Interactive terminal chat (optional warm-up)
  - Task 4.3: Launch the Streamlit live UI
  - Task 4.4: End session & review memory tiers

---

## Exercise 1 — Environment Setup & Local Memory

- Task 1.1: Verify tools and open the project
  - What we are doing:
    - Confirm Python, uv, and Git versions
    - Inspect repo folders (demo/, memory/, server/)
  - Key steps:
    - Run `python --version`, `uv --version`, `git --version`
    - `cd` into project and list top-level demo/memory/server directories

- Task 1.2: Review environment configuration
  - What we are doing:
    - Open and verify the `.env` file contains Azure OpenAI and Cosmos values
    - Know the main agent memory components (AgentMemory, MemoryOrchestrator, Backend)
  - Key steps:
    - `code .env` (or open in your editor)
    - Confirm `AZURE_OPENAI_ENDPOINT`, `AZURE_OPENAI_API_KEY`, `AZURE_OPENAI_EMB_DEPLOYMENT` are present

- Task 1.3: Install dependencies & run basic demo
  - What we are doing:
    - Install required Python/dev dependencies and run the SQLite-based memory demo
  - Key steps:
    - `uv sync --extra dev`
    - `uv run python demo/01_basic_memory.py`
    - Watch for sections: Session 1, Buffer management result, Session 2, Semantic search

- Task 1.4: Observe memory behavior
  - What we are doing:
    - Inspect demo output to understand active turns, summarization, and cross-session recall
  - Key steps:
    - Answer: how many turns, what happened when buffer filled, did Session 2 recall Session 1
    - Optionally inspect `demo_basic.db` (SQLite file)

---

## Exercise 2 — Agent Framework Integration

- Task 2.1: Study the integration pattern
  - What we are doing:
    - Open the agent demo and locate how AgentMemory is plugged into the Agent as a ContextProvider
  - Key steps:
    - `code demo/02_agent_framework.py`
    - Find `context_providers=[memory]` and construction of `AgentMemory`

- Task 2.2: Run the financial advisor demo
  - What we are doing:
    - Execute the agent framework demo to see automatic context injection and storage
  - Key steps:
    - `uv run python demo/02_agent_framework.py`
    - Confirm Session 2 references details from Session 1 (e.g., user profile)

- Task 2.3: Compare agent-driven memory retrieval
  - What we are doing:
    - Compare automatic context injection (Exercise 2) vs explicit memory searches (agent-driven demo)
  - Key steps:
    - `uv run python demo/03_agent_driven.py`
    - Note differences: automatic before_run/after_run hooks vs explicit memory tool calls

- Task 2.4: Long-term insight extraction
  - What we are doing:
    - Run insight curation to see summarization and long-term insight extraction after sessions
  - Key steps:
    - `uv run python demo/06_insight_curation.py`
    - Observe summaries and extracted insights across sessions

---

## Exercise 3 — Azure Cosmos DB Backend

- Task 3.1: Understand backend options
  - What we are doing:
    - Review backend trade-offs and confirm Cosmos credentials in `.env`
  - Key steps:
    - Check `.env` for `COSMOS_ENDPOINT` or `AZURE_COSMOS_CONNECTION_STRING`
    - Recall why sqlite vs cosmosdb is chosen here

- Task 3.2: Run the Cosmos DB demo
  - What we are doing:
    - Run the Cosmos-backed demo to persist memory in the cloud
  - Key steps:
    - `uv run python demo/04_cosmosdb.py`
    - Watch for header indicating CosmosDB backend and successful persistence messages

- Task 3.3: Verify data persisted in Cosmos DB
  - What we are doing:
    - Confirm that data survives beyond the run, either by re-running demo or inspecting Azure Portal
  - Key steps:
    - Re-run `04_cosmosdb.py` and confirm Session 2 recalls earlier data
    - (Optional) Use Azure Portal Data Explorer to inspect database `agent_memory_db`

- Task 3.4: Concept check: other backends
  - What we are doing:
    - Read README or code to see supported backends and answer quick conceptual questions
  - Key steps:
    - Search for `AGENT_MEMORY_DB_TYPE` values: `sqlite`, `cosmosdb`, `azure_ai_search`, `postgresql`

---

## Exercise 4 — Live Server Mode & Streamlit UI

- Task 4.1: Start the memory server
  - What we are doing:
    - Run the FastAPI memory server (backed by Cosmos if configured) and verify health
  - Key steps:
    - `uv run uvicorn server.main:app --host 127.0.0.1 --port 8000`
    - `curl http://127.0.0.1:8000/health`

- Task 4.2: Interactive terminal chat (optional)
  - What we are doing:
    - Use the terminal client to interact with the running memory server and try commands
  - Key steps:
    - `uv run python demo/05_server_mode.py`
    - Try commands: `/context`, `/search <topic>`, `/insights`, `/quit`

- Task 4.3: Launch the Streamlit live UI
  - What we are doing:
    - Start Streamlit UI that talks to the memory server for a live chat experience and visual memory panel
  - Key steps:
    - `uv run streamlit run demo/07_interactive_ui.py`
    - Open `http://localhost:8501`, start a session with a user ID, send multi-turn messages, test recall

- Task 4.4: End session & review memory tiers
  - What we are doing:
    - End the session to trigger summary/insight generation, then start a new session with same user ID to validate cross-session recall
  - Key steps:
    - Click End Session in UI (or send `/quit` in terminal), start new session with same user ID, ask what the agent remembers

---

## Notes

- This consolidated TOC is designed to be a quick reference. Each task in the original lab guide contains more detail, commands, and troubleshooting notes.
- If you want, I can also:
  - Add direct links to the demo scripts in the repo for each task
  - Break this file into per-exercise markdown files

---
