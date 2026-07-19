# Agent Memory Lab Guide

**Duration:** ~4 hours  
**Level:** Beginner to Intermediate  
**Repository:** [github.com/james-tn/agent-memory](https://github.com/james-tn/agent-memory)

---

## Lab Overview

In this lab you will learn how to add **persistent memory** to AI agents using the **Agent Memory** library, **Microsoft Agent Framework**, and **Azure OpenAI**. You will progress from local SQLite storage to Azure Cosmos DB, then finish with a **live interactive chat UI**.

### What You Will Build

| Exercise | Focus | Interaction |
|----------|-------|-------------|
| 1 | Local memory with SQLite | Scripted demo |
| 2 | Agent Framework integration | Scripted demo |
| 3 | Cloud memory with Cosmos DB | Scripted demo |
| 4 | FastAPI server + Streamlit UI | **Live interactive app** |

### Backend Decision (Instructor Note)

This lab uses **SQLite** (local) and **Azure Cosmos DB** (cloud) only.

**We intentionally exclude Azure AI Search and PostgreSQL** because:

- They add significant fixed Azure cost with little benefit at beginner level
- The repo’s clearest teaching demos use SQLite and Cosmos DB
- Server mode and Streamlit work with Cosmos DB — no need for extra backends
- AI Search and PostgreSQL are mentioned conceptually in Exercise 3 Task 1 only

---

## Pre-Lab: Azure Resources (Instructor / Platform Team)

All Azure resources are **pre-provisioned** before learners start. Learners do **not** run `azd provision`.

### Required Pre-Deployed Resources

| Resource | Purpose |
|----------|---------|
| **Azure OpenAI** | Chat models + embeddings for memory orchestration |
| **Azure Cosmos DB for NoSQL** | Cloud persistence with vector search |
| **Lab VM** | Windows or Linux VM with repo and tools pre-installed |

### Azure OpenAI Deployments (Minimum)

| Deployment name | Type | Used for |
|-----------------|------|----------|
| `<your-chat-deployment>` | Chat (e.g. gpt-4o-mini) | `AZURE_OPENAI_REASONING_MODEL` |
| `<your-processing-deployment>` | Chat (can be same model) | `AZURE_OPENAI_PROCESSING_MODEL` |
| `text-embedding-ada-002` | Embedding | `AZURE_OPENAI_EMB_DEPLOYMENT` |

### Post-Provision Checklist (Instructor Only)

- [ ] Cosmos DB account created with vector search enabled
- [ ] Post-provision script run: `infra/scripts/setup-cosmos-vectors.*`
- [ ] Lab VM has repo cloned and `uv sync --extra dev` completed
- [ ] `.env` file placed in repo root (see template below)
- [ ] Smoke test passed: `uv run python demo/01_basic_memory.py`
- [ ] Smoke test passed: `uv run python demo/04_cosmosdb.py`

---

## Learner Handout: Collect Your Credentials

Your instructor will provide a **Lab Credentials Sheet** (PDF, portal link, or shared file). Use it to fill in values below.

### Step 0.1 — Open the Lab Credentials Sheet

Locate the handout titled **"Agent Memory Lab — Credentials"**. It contains:

1. Azure OpenAI endpoint and API key  
2. Model deployment names  
3. Cosmos DB endpoint and connection string  
4. Lab VM login (if using remote VM)

### Step 0.2 — Verify the Pre-Configured `.env` File

On the lab VM, open the project folder (default: `C:\Lab\agent-memory` or `/home/lab/agent-memory`).

Confirm a `.env` file exists with these variables **already filled in** by the instructor:

```bash
# Azure OpenAI (provided by instructor)
AZURE_OPENAI_ENDPOINT=https://<your-resource>.openai.azure.com/
AZURE_OPENAI_API_KEY=<provided-key>
AZURE_OPENAI_API_VERSION=2025-04-01-preview
AZURE_OPENAI_REASONING_MODEL=<chat-deployment-name>
AZURE_OPENAI_PROCESSING_MODEL=<processing-deployment-name>
AZURE_OPENAI_EMB_DEPLOYMENT=text-embedding-ada-002

# Cosmos DB (provided by instructor)
COSMOS_ENDPOINT=https://<your-cosmos-account>.documents.azure.com:443/
AZURE_COSMOS_CONNECTION_STRING=<provided-connection-string>
COSMOS_DATABASE_NAME=agent_memory_db

# Optional — used in Exercise 4
MEMORY_SERVICE_URL=http://127.0.0.1:8000
```

> **If any value is missing:** Ask your instructor for the Lab Credentials Sheet. Do not create new Azure resources yourself.

### Step 0.3 — Optional: Find Values in Azure Portal (Reference)

If your instructor points you to the portal instead of a sheet:

| Value | Azure Portal Path |
|-------|-------------------|
| OpenAI endpoint & key | **Azure OpenAI** → your resource → **Keys and Endpoint** |
| Deployment names | **Azure OpenAI** → **Model deployments** |
| Cosmos endpoint | **Cosmos DB** → your account → **Overview** → URI |
| Cosmos connection string | **Cosmos DB** → **Keys** → Primary Connection String |

---

## Lab VM Prerequisites (Pre-Installed)

These should already be installed. You will verify them in Exercise 1.

| Software | Minimum Version |
|----------|-----------------|
| Python | 3.12+ |
| uv | Latest |
| Git | Latest |
| VS Code | Latest (recommended) |

---

# Exercise 1: Environment Setup & Local Memory (45 min)

**Objective:** Verify your environment and run the simplest Agent Memory flow using **SQLite** (local, zero-config storage).

---

## Task 1.1 — Verify Tools and Open the Project (10 min)

### Steps

1. Open a terminal (PowerShell on Windows, bash on Linux).

2. Go to the project folder:

```bash
cd C:\Lab\agent-memory
```

> Linux path example: `cd /home/lab/agent-memory`

3. Verify tools:

```bash
python --version
uv --version
git --version
```

4. Confirm the repo structure:

```bash
# Windows PowerShell
Get-ChildItem demo, memory, server

# Linux/macOS
ls demo memory server
```

### Expected Output

```
Python 3.12.x
uv x.x.x
git version x.x.x
```

You should see folders including `demo/`, `memory/`, and `server/`.

### ✅ Task 1.1 Complete When

- Python 3.12+ is reported
- Project folders are visible

---

## Task 1.2 — Review Environment Configuration (10 min)

### Steps

1. Open `.env` in VS Code:

```bash
code .env
```

2. Confirm these variables are set (values hidden on screen is fine):

   - `AZURE_OPENAI_ENDPOINT`
   - `AZURE_OPENAI_API_KEY`
   - `AZURE_OPENAI_REASONING_MODEL`
   - `AZURE_OPENAI_EMB_DEPLOYMENT`

3. Read the architecture comment in the README (optional):

```bash
code README.md
```

### Expected Output

No errors opening the file. All required variables have non-empty values.

### ✅ Task 1.2 Complete When

- Learner can name the three main components: **AgentMemory**, **MemoryOrchestrator**, **Backend (SQLite/Cosmos)**

---

## Task 1.3 — Install Dependencies & Run Basic Demo (15 min)

### Steps

1. Sync dependencies (skip if instructor pre-ran this; takes ~1–2 min):

```bash
uv sync --extra dev
```

2. Run the basic memory demo:

```bash
uv run python demo/01_basic_memory.py
```

3. Watch the console for these sections:

   - `SESSION 1: Long Conversation`
   - `BUFFER MANAGEMENT RESULT`
   - `SESSION 2: Cross-Session Memory Recall`
   - `SEMANTIC SEARCH`
   - `DEMO COMPLETE!`

### Expected Output (Excerpt)

```
======================================================================
Basic Memory Demo
  Pattern: Manual add_turn() + await get_context()
  Backend: SQLite (zero configuration)
======================================================================

--- Turn 1 ---
User: Hi! I'm looking for book recommendations....
Assistant: Hello! I'd be happy to help...

...

BUFFER MANAGEMENT RESULT:
----------------------------------------------------------------------

Final context (... chars):
----------------------------------------
### Conversation Summary
...

======================================================================
DEMO COMPLETE!
======================================================================
```

### Troubleshooting

| Error | Fix |
|-------|-----|
| `AZURE_OPENAI_ENDPOINT` not set | Check `.env` file path; re-open terminal |
| 401 Unauthorized | Verify API key from Lab Credentials Sheet |
| Deployment not found | Confirm deployment names match portal |

### ✅ Task 1.3 Complete When

- Demo runs to `DEMO COMPLETE!` without errors

---

## Task 1.4 — Observe Memory Behavior (10 min)

### Steps

1. Re-read the demo output and answer in your notes:

   - How many turns were in Session 1?
   - What happened when the buffer filled up?
   - Did Session 2 load context from Session 1?

2. Optional — inspect the SQLite database created:

```bash
# Windows
dir demo_basic.db

# Linux
ls -la demo_basic.db
```

### Expected Answers (Learner Notes)

- Session 1 has **8 turns**
- Older turns are **summarized**; recent turns stay verbatim
- Session 2 **loads prior session summary** into context

### ✅ Task 1.4 Complete When

- Learner can explain: `add_turn()` stores, `get_context()` retrieves, `search()` finds facts semantically

---

# Exercise 2: Agent Framework Integration (60 min)

**Objective:** Connect Agent Memory to **Microsoft Agent Framework** so memory is injected and stored **automatically** — no manual `add_turn()` calls.

---

## Task 2.1 — Study the Integration Pattern (15 min)

### Steps

1. Open the demo file:

```bash
code demo/02_agent_framework.py
```

2. Find these key lines:

   - `context_providers=[memory]` on the `Agent` constructor
   - `AgentMemory` created with `db_path` (SQLite)
   - Pre-written `queries` lists passed to `run_session()`

3. Draw or note the flow:

```
User query → Agent.run()
  → before_run(): memory injects context
  → LLM responds
  → after_run(): memory stores turn
```

### Expected Understanding

Memory is a **ContextProvider** — the agent calls it automatically each turn.

### ✅ Task 2.1 Complete When

- Learner can point to `context_providers=[memory]` in the code

---

## Task 2.2 — Run the Financial Advisor Demo (15 min)

### Steps

1. Run the demo:

```bash
uv run python demo/02_agent_framework.py
```

2. Follow three scripted sessions in the output:

   - Initial Consultation
   - Investment Strategy
   - Tax Planning

### Expected Output (Excerpt)

```
======================================================================
🧠 Agent Memory Demo: Financial Advisor
  Integration: Agent Framework ContextProvider
  Memory: Automatic (no manual add_turn calls)
  Backend: SQLite (zero-config)
======================================================================

SESSION: Initial Consultation
...
👤 User: Hi! I'm Sarah, 35 years old...
🤖 Advisor: ...

SESSION: Investment Strategy
...
👤 User: Based on what we discussed before, what asset allocation...
🤖 Advisor: ...
```

### ✅ Task 2.2 Complete When

- Session 2 advisor response references Sarah's profile from Session 1

---

## Task 2.3 — Compare Agent-Driven Memory Retrieval (15 min)

### Steps

1. Run the agent-driven demo:

```bash
uv run python demo/03_agent_driven.py
```

2. Compare with Exercise 2:

   - Exercise 2: memory injected **automatically**
   - Exercise 3 demo: agent can **explicitly search** memory when needed

### Expected Output

Demo completes with memory search/recall steps visible in console output.

### ✅ Task 2.3 Complete When

- Learner can state one difference between automatic context injection vs explicit memory tools

---

## Task 2.4 — Long-Term Insight Extraction (15 min)

### Steps

1. Run the insight curation demo:

```bash
uv run python demo/06_insight_curation.py
```

2. Watch for:

   - Session summaries after `end_session()`
   - Long-term insights extracted across sessions

### Expected Output (Excerpt)

```
Session complete
 Summary: ...
 Insights extracted: N
...
Extracted Insights:
 • ...
```

### ✅ Task 2.4 Complete When

- Learner can explain: sessions → summaries → long-term insights

---

# Exercise 3: Azure Cosmos DB Backend (60 min)

**Objective:** Switch from local SQLite to **Azure Cosmos DB** — the same agent memory API, different cloud backend.

> **Note:** This exercise uses a **scripted demo** (pre-written prompts), not a live chat UI. Live interaction comes in Exercise 4.

---

## Task 3.1 — Understand Backend Options (15 min)

### Steps

1. Read the backend table in `README.md` or recall:

| Backend | Best for | In this lab? |
|---------|----------|--------------|
| `sqlite` | Local dev, learning | ✅ Exercises 1–2 |
| `cosmosdb` | Production, global scale | ✅ This exercise |
| `azure_ai_search` | Managed search indexes | ❌ Not used (cost/complexity) |
| `postgresql` | pgvector relational store | ❌ Not used (cost/complexity) |

2. Verify Cosmos credentials in `.env`:

```bash
# Windows PowerShell
Select-String -Path .env -Pattern "COSMOS"

# Linux
grep COSMOS .env
```

3. Confirm you see `COSMOS_ENDPOINT` or `AZURE_COSMOS_CONNECTION_STRING` set.

### Expected Output

```
COSMOS_ENDPOINT=https://....documents.azure.com:443/
AZURE_COSMOS_CONNECTION_STRING=AccountEndpoint=...
```

### ✅ Task 3.1 Complete When

- Learner knows why this lab uses Cosmos DB instead of AI Search or PostgreSQL

---

## Task 3.2 — Run the Cosmos DB Demo (20 min)

### Steps

1. Run the Cosmos-backed financial advisor demo:

```bash
uv run python demo/04_cosmosdb.py
```

2. Confirm the header shows **CosmosDB** backend:

```
Backend: Azure CosmosDB (enterprise-grade)
✅ CosmosDB: Endpoint configured
```

3. Watch all three sessions complete (same Sarah scenario as Exercise 2).

### Expected Output (Excerpt)

```
======================================================================
🧠 Agent Memory Demo: Financial Advisor (CosmosDB)
...
✅ CosmosDB: Endpoint configured
...
SESSION: Initial Consultation
...
✅ Demo Complete!
 Data persisted to CosmosDB for future sessions
======================================================================
```

### Troubleshooting

| Error | Fix |
|-------|-----|
| Cosmos credentials not found | Check `.env`; ask instructor for connection string |
| Forbidden / firewall | Instructor must allow lab VM IP in Cosmos firewall |
| `CosmosHttpResponseError` | Network or RBAC issue — contact instructor |

### ✅ Task 3.2 Complete When

- Demo ends with `Data persisted to CosmosDB for future sessions`

---

## Task 3.3 — Verify Data Persisted in Cosmos DB (15 min)

### Steps

1. **Option A — Re-run the demo** (simplest): Run `04_cosmosdb.py` again. Session 2 should still recall Sarah's profile if the same `USER_ID` is used and data was not cleared.

2. **Option B — Azure Portal** (if instructor enabled access):

   - Go to **Azure Portal** → **Cosmos DB** → your lab account
   - Open **Data Explorer**
   - Browse database `agent_memory_db` for containers with session/insight data

3. In your notes, answer:

   - What changed vs SQLite? (Data survives beyond local `.db` file)
   - Why would you pick Cosmos DB for production?

### Expected Answer

- SQLite = single machine file; Cosmos DB = cloud, scalable, multi-session persistence
- Production: durability, global distribution, managed vector search

### ✅ Task 3.3 Complete When

- Learner can explain SQLite vs Cosmos DB trade-off

---

## Task 3.4 — Concept Check: Other Backends (10 min)

### Steps

1. Read (no need to run) how backend selection works:

```bash
code README.md
```

Search for `AGENT_MEMORY_DB_TYPE`. Supported values:

- `sqlite`
- `cosmosdb`
- `azure_ai_search`
- `postgresql`

2. Complete this table in your notes:

| Question | Your answer |
|----------|-------------|
| Which backend did we use in Exercises 1–2? | sqlite |
| Which backend in Exercise 3? | cosmosdb |
| Which backend powers the server in Exercise 4? | cosmosdb (instructor pre-configured) |

### ✅ Task 3.4 Complete When

- Table completed; learner understands one API, multiple backends

---

# Exercise 4: Live Server Mode & Streamlit UI (75 min)

**Objective:** Run a **live interactive chat application** — users type messages in a browser; memory is served by a FastAPI backend backed by Cosmos DB.

---

## Task 4.1 — Start the Memory Server (15 min)

### Steps

1. Open **Terminal 1** and start the FastAPI server:

```bash
cd C:\Lab\agent-memory

# Use Cosmos DB backend (instructor may pre-set in .env)
# Windows PowerShell:
$env:AGENT_MEMORY_DB_TYPE = "cosmosdb"
uv run uvicorn server.main:app --host 127.0.0.1 --port 8000

# Linux/macOS:
export AGENT_MEMORY_DB_TYPE=cosmosdb
uv run uvicorn server.main:app --host 127.0.0.1 --port 8000
```

2. Wait for startup message:

```
INFO:     Uvicorn running on http://127.0.0.1:8000
INFO:     Application startup complete.
```

3. Open **Terminal 2** and verify health:

```bash
curl http://127.0.0.1:8000/health
```

### Expected Output

```json
{"status":"healthy","active_sessions":0}
```

(or similar JSON with `"status"` field)

### ✅ Task 4.1 Complete When

- Server running; health check returns success

---

## Task 4.2 — Interactive Terminal Chat (Optional Warm-Up) (15 min)

### Steps

1. In **Terminal 2** (server still running in Terminal 1):

```bash
uv run python demo/05_server_mode.py
```

2. Type your own messages, for example:

```
You: Hi, I'm Alex and I want to save for retirement. I'm 28.
You: What did I tell you about my age?
You: /context
You: /search retirement
You: /quit
```

### Expected Output (Excerpt)

```
✓ Memory service: healthy (0 active sessions)
...
You: Hi, I'm Alex...
Advisor: ...
...
You: /context
--- Memory Context (N turns) ---
...
```

### ✅ Task 4.2 Complete When

- Learner sent at least 2 custom messages and used one command (`/context`, `/search`, or `/insights`)

---

## Task 4.3 — Launch the Streamlit Live UI (20 min)

### Steps

1. Keep the server running in **Terminal 1**.

2. In **Terminal 2**, start Streamlit:

```bash
streamlit run demo/07_interactive_ui.py
```

   Or via uv:

```bash
uv run streamlit run demo/07_interactive_ui.py
```

3. Open the URL shown (typically `http://localhost:8501`).

4. In the UI:

   - Enter a **User ID** (e.g. `lab-user-01`)
   - Click **Start Session**
   - Type a custom message in the chat box
   - Send 3+ messages across two topics (e.g. food preferences, then travel)
   - Ask a recall question: *"What did I say about food?"*
   - Explore the memory panel (context, insights, search)

### Expected Behavior

- Chat responds in real time
- Memory context updates after turns
- Recall question uses stored memory from earlier in the session

### ✅ Task 4.3 Complete When

- Learner completed a multi-turn live chat with successful recall

---

## Task 4.4 — End Session & Review Memory Tiers (25 min)

### Steps

1. In the Streamlit UI, click **End Session** (or equivalent control in sidebar).

2. Start a **new session** with the **same User ID**.

3. Ask: *"What do you remember about me from before?"*

4. In your lab notes, document the memory tiers you observed:

| Tier | What it stores | Where you saw it |
|------|----------------|------------------|
| Active turns | Recent verbatim messages | Chat / context panel |
| Session summary | Compressed session history | After end session |
| Long-term insights | Facts across sessions | Insights panel |

5. Stop services when done:

   - Terminal 2: `Ctrl+C` (Streamlit)
   - Terminal 1: `Ctrl+C` (uvicorn)

### Expected Behavior

- New session with same User ID loads prior insights/summaries
- Agent answers recall question using persisted memory

### ✅ Task 4.4 Complete When

- Learner documented all three memory tiers
- Both server and UI stopped cleanly

---

# Lab Completion Checklist

| # | Item | Done |
|---|------|------|
| 1 | Ran `01_basic_memory.py` — SQLite buffer management | ☐ |
| 2 | Ran `02_agent_framework.py` — automatic memory via ContextProvider | ☐ |
| 3 | Ran `06_insight_curation.py` — long-term insights | ☐ |
| 4 | Ran `04_cosmosdb.py` — Cosmos DB persistence | ☐ |
| 5 | Started FastAPI server on port 8000 | ☐ |
| 6 | Used live Streamlit UI with custom messages | ☐ |
| 7 | Demonstrated cross-turn memory recall in live UI | ☐ |

---

# Instructor Solution Keys

> **For instructors only.** Do not distribute to learners before lab completion.

---

## Exercise 1 — Solution Key

### Task 1.3 Expected Sections (in order)

1. `KEY CONCEPT: Automatic Buffer Management`
2. `SESSION 1: Long Conversation` — 8 turns
3. `BUFFER MANAGEMENT RESULT` — shows `### Conversation Summary`
4. `SESSION 2: Cross-Session Memory Recall` — non-empty context
5. `SEMANTIC SEARCH` — 3 queries with results
6. `DEMO COMPLETE!`

### Task 1.4 Answers

| Question | Answer |
|----------|--------|
| Turns in Session 1 | 8 |
| Buffer behavior | At 6 turns, older turns summarized; last 4 kept active (`buffer_size=6`, `active_turns=4`) |
| Session 2 behavior | Loads session summary + insights from Session 1 |

### Common Failures

- **Missing `.env`:** Copy from instructor template
- **Wrong API version:** Use `2025-04-01-preview` or value on credentials sheet
- **Embedding deployment missing:** Deploy `text-embedding-ada-002` in Azure OpenAI

---

## Exercise 2 — Solution Key

### Key Code Locations (`02_agent_framework.py`)

```python
memory = AgentMemory(user_id=USER_ID, openai_client=openai_client, db_path=DB_PATH, ...)
agent = Agent(..., context_providers=[memory])
```

### Task 2.2 — Proof of Memory Recall

In **Session 2**, query *"Based on what we discussed before, what asset allocation..."* — advisor should mention:

- Sarah, age 35
- Moderate-to-high risk tolerance
- ~30 years to retirement

### Task 2.3 — Comparison Answer

| Pattern | When memory is used |
|---------|---------------------|
| `02_agent_framework.py` | Automatic: `before_run` / `after_run` hooks |
| `03_agent_driven.py` | Explicit: agent invokes memory search tools on demand |

### Task 2.4 — Insights

After `end_session()`, expect `insights_extracted` count ≥ 1 by Session 3. Insights should mention retirement, 401k, or risk tolerance.

---

## Exercise 3 — Solution Key

### Task 3.2 Success Indicators

```
✅ CosmosDB: Endpoint configured
...
✅ Demo Complete!
 Data persisted to CosmosDB for future sessions
```

### Task 3.3 — Persistence Verification

**Re-run test:** If `USER_ID = "sarah_demo_cosmos"` is unchanged and DB not wiped, Session 1 context should appear non-empty on second run's Session 2/3 without re-introducing Sarah.

**Portal:** Data Explorer → `agent_memory_db` → containers (names vary by schema version; look for sessions, insights, or interactions containers).

### Task 3.4 — Concept Answers

| Backend | Used in lab? | Reason |
|---------|--------------|--------|
| sqlite | Yes | Free, local, zero setup |
| cosmosdb | Yes | Cloud production path |
| azure_ai_search | No | Higher cost; redundant for intro lab |
| postgresql | No | Extra infra; demos don't require it |

### Cosmos Firewall Script (Instructor Pre-Lab)

Ensure lab VM public IP is allowed on Cosmos DB → **Networking** → **Firewall and virtual networks**.

---

## Exercise 4 — Solution Key

### Task 4.1 — Server Startup

Correct startup shows no traceback. Misconfiguration fails **at startup** (by design).

**Verify:**

```bash
curl http://127.0.0.1:8000/health
curl http://127.0.0.1:8000/docs   # optional — FastAPI Swagger UI
```

### Task 4.2 — Terminal Chat Commands

| Command | Behavior |
|---------|----------|
| `/context` | Prints current memory context string |
| `/search <topic>` | Semantic search (default query if blank: "retirement savings") |
| `/insights` | Lists up to 5 long-term insights |
| `/quit` | Ends session, triggers reflection |

### Task 4.3 — Streamlit UI Success Criteria

Learner should demonstrate:

1. Custom user ID entered
2. ≥ 3 user-typed messages (not auto-play scenario only)
3. Recall question answered correctly from same session
4. Memory visualization panel shows non-empty context

**Sample test script for instructor spot-check:**

| Turn | User says | Expected agent behavior |
|------|-----------|-------------------------|
| 1 | "I'm vegetarian and allergic to peanuts." | Acknowledges dietary restriction |
| 2 | "I'm planning a trip to Japan." | Talks about travel |
| 3 | "What dietary restrictions did I mention?" | Mentions vegetarian + peanut allergy |

### Task 4.4 — Memory Tier Answers

| Tier | Mechanism |
|------|-----------|
| Active turns | Stored via `store_turn`; recent verbatim in context |
| Session summary | Generated on `end_session()` |
| Long-term insights | Reflection pipeline after session ends; persisted to Cosmos |

### Common Exercise 4 Failures

| Symptom | Cause | Fix |
|---------|-------|-----|
| `Memory service unavailable` | Server not running | Start uvicorn in Terminal 1 |
| Port 8000 in use | Previous server still running | Kill process or use different port |
| Streamlit blank/errors | Missing streamlit | `uv sync --extra dev` |
| No cross-session recall | Different User ID in new session | Use same User ID |
| Cosmos errors on server start | Missing `AGENT_MEMORY_DB_TYPE=cosmosdb` or bad connection string | Check `.env` |

---

## Instructor Pre-Lab `.env` Template

Copy to lab VM as `C:\Lab\agent-memory\.env`:

```bash
# === Azure OpenAI ===
AZURE_OPENAI_ENDPOINT=https://REPLACE.openai.azure.com/
AZURE_OPENAI_API_KEY=REPLACE
AZURE_OPENAI_API_VERSION=2025-04-01-preview
AZURE_OPENAI_REASONING_MODEL=REPLACE-chat-deployment
AZURE_OPENAI_PROCESSING_MODEL=REPLACE-processing-deployment
AZURE_OPENAI_EMB_DEPLOYMENT=text-embedding-ada-002

# === Cosmos DB ===
AGENT_MEMORY_DB_TYPE=cosmosdb
COSMOS_ENDPOINT=https://REPLACE.documents.azure.com:443/
AZURE_COSMOS_CONNECTION_STRING=REPLACE
COSMOS_DATABASE_NAME=agent_memory_db

# === Server (Exercise 4) ===
MEMORY_SERVICE_URL=http://127.0.0.1:8000
```

---

## Instructor Timing Guide

| Exercise | Duration | Cumulative |
|----------|----------|------------|
| 0 — Credentials & setup | 15 min | 0:15 |
| 1 — SQLite basics | 45 min | 1:00 |
| 2 — Agent Framework | 60 min | 2:00 |
| 3 — Cosmos DB | 60 min | 3:00 |
| 4 — Live UI | 75 min | 4:15 |
| Buffer / Q&A | 15 min | 4:30 |

> Trim Task 4.2 (terminal chat) to 5 min if running short on time.

---

## Azure Cost Notes (Instructor)

**Included in lab:** Azure OpenAI (pay-per-token), Cosmos DB (serverless or minimal RU)

**Excluded to save cost:** Azure AI Search (~$75+/mo minimum), PostgreSQL Flexible Server (~$15–40+/mo)

**Post-lab:** Delete or deallocate the lab resource group if not reused within 24 hours.

---

## Appendix: Quick Command Reference

```bash
# Exercise 1
uv sync --extra dev
uv run python demo/01_basic_memory.py

# Exercise 2
uv run python demo/02_agent_framework.py
uv run python demo/03_agent_driven.py
uv run python demo/06_insight_curation.py

# Exercise 3
uv run python demo/04_cosmosdb.py

# Exercise 4 — Terminal 1
uv run uvicorn server.main:app --host 127.0.0.1 --port 8000

# Exercise 4 — Terminal 2
uv run python demo/05_server_mode.py
uv run streamlit run demo/07_interactive_ui.py
```

---

*Lab guide version 1.0 — Agent Memory (SQLite + Cosmos DB path)*
