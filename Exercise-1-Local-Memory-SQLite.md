# Exercise 1 — Environment Setup & Local Memory (SQLite)

**Lab:** Advanced Dynamic Memory Architecture: Agent Memory  
**Duration:** 45 minutes  
**Repository:** [github.com/james-tn/agent-memory](https://github.com/james-tn/agent-memory)

---

## Objective

Verify your lab environment and run the simplest Agent Memory flow using **SQLite** (local, zero-config storage).

---

## Task 1.1 — Verify Tools and Open the Project (10 min)

### What we are doing

- Confirm Python, uv, and Git are installed
- Navigate to the agent-memory project and inspect key folders

### Steps

1. Open a terminal (PowerShell or bash).
2. Change to the project directory:

```bash
cd C:\Lab\agent-memory
```

3. Check tool versions:

```bash
python --version
uv --version
git --version
```

4. List project folders:

```bash
# Windows
Get-ChildItem demo, memory, server

# Linux
ls demo memory server
```

### Expected output

```
Python 3.12.x
uv x.x.x
git version x.x.x
```

Folders `demo/`, `memory/`, and `server/` are visible.

### ✅ Complete when

- Python 3.12+ is reported
- Project structure is visible

---

## Task 1.2 — Review Environment Configuration (10 min)

### What we are doing

- Inspect the `.env` file for Azure OpenAI settings
- Identify the three main architecture components

### Steps

1. Open `.env`:

```bash
code .env
```

2. Confirm these variables are set:

   - `AZURE_OPENAI_ENDPOINT`
   - `AZURE_OPENAI_API_KEY`
   - `AZURE_OPENAI_REASONING_MODEL`
   - `AZURE_OPENAI_EMB_DEPLOYMENT`

3. Optionally open `README.md` and review the architecture diagram.

### Expected output

`.env` opens with all required variables populated.

### Key concepts to note

- **AgentMemory** — unified memory API
- **MemoryOrchestrator** — manages tiers and retrieval
- **Backend** — SQLite (this exercise) or Cosmos DB (later)

### ✅ Complete when

- You can name AgentMemory, MemoryOrchestrator, and Backend

---

## Task 1.3 — Install Dependencies & Run Basic Demo (15 min)

### What we are doing

- Install project dependencies with `uv`
- Run the basic memory demo and observe buffer management

### Steps

1. Sync dependencies (skip if pre-installed by instructor):

```bash
uv sync --extra dev
```

2. Run the demo:

```bash
uv run python demo/01_basic_memory.py
```

3. Watch for these console sections:

   - `SESSION 1: Long Conversation`
   - `BUFFER MANAGEMENT RESULT`
   - `SESSION 2: Cross-Session Memory Recall`
   - `SEMANTIC SEARCH`
   - `DEMO COMPLETE!`

### Expected output (excerpt)

```
Basic Memory Demo
  Backend: SQLite (zero configuration)
...
### Conversation Summary
...
DEMO COMPLETE!
```

### Troubleshooting

| Error | Fix |
|-------|-----|
| Missing `AZURE_OPENAI_ENDPOINT` | Re-open terminal; check `.env` location |
| 401 Unauthorized | Verify API key on Credentials Sheet |
| Deployment not found | Match deployment names to Azure portal |

### ✅ Complete when

- Demo finishes with `DEMO COMPLETE!` and no errors

---

## Task 1.4 — Observe Memory Behavior (10 min)

### What we are doing

- Analyze demo output to understand how memory tiers and buffer pruning work
- Optionally locate the SQLite database file created by the demo

### Steps

1. Answer in your notes:

   - How many turns were in Session 1?
   - What happened when the buffer filled up?
   - Did Session 2 load context from Session 1?

2. Optional — find the database file:

```bash
# Windows
dir demo_basic.db

# Linux
ls -la demo_basic.db
```

### Expected answers

- Session 1 has **8 turns**
- Older turns are **summarized**; recent turns stay verbatim
- Session 2 **loads the prior session summary**

### API summary

| Method | Purpose |
|--------|---------|
| `add_turn()` | Store user/assistant message pairs |
| `get_context()` | Retrieve formatted memory for the LLM |
| `search()` | Semantic search across memory |

### ✅ Complete when

- You can explain `add_turn`, `get_context`, and `search`

---

## Exercise 1 checklist

| Task | Done |
|------|------|
| 1.1 Tools verified | ☐ |
| 1.2 `.env` reviewed | ☐ |
| 1.3 Basic demo run | ☐ |
| 1.4 Memory behavior documented | ☐ |

**Next:** Exercise 2 — Agent Framework Integration
