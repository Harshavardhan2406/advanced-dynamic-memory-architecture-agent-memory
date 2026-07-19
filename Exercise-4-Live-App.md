# Exercise 4 — Live Server Mode & Streamlit UI

**Lab:** Advanced Dynamic Memory Architecture: Agent Memory  
**Duration:** 75 minutes

---

## Objective

Run a **live interactive chat application** — type your own messages in a browser while a FastAPI memory server persists data to Cosmos DB.

---

## Task 4.1 — Start the Memory Server (15 min)

### What we are doing

- Start the FastAPI memory service on port 8000
- Point it at the Cosmos DB backend
- Verify the service is healthy

### Steps

1. Open **Terminal 1** and run:

```bash
cd C:\Lab\agent-memory

# Windows PowerShell
$env:AGENT_MEMORY_DB_TYPE = "cosmosdb"
uv run uvicorn server.main:app --host 127.0.0.1 --port 8000

# Linux
export AGENT_MEMORY_DB_TYPE=cosmosdb
uv run uvicorn server.main:app --host 127.0.0.1 --port 8000
```

2. Wait for:

```
INFO:     Uvicorn running on http://127.0.0.1:8000
INFO:     Application startup complete.
```

3. In **Terminal 2**, health check:

```bash
curl http://127.0.0.1:8000/health
```

### Expected output

```json
{"status":"healthy","active_sessions":0}
```

### ✅ Complete when

- Server is running and health check succeeds

---

## Task 4.2 — Interactive Terminal Chat (15 min)

### What we are doing

- Connect to the memory server from a terminal chat client
- Send custom messages and use memory commands

### Steps

1. In **Terminal 2** (server still running):

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

3. Try at least one command: `/context`, `/search`, or `/insights`

### Command reference

| Command | Action |
|---------|--------|
| `/context` | Show current memory context |
| `/search <topic>` | Semantic memory search |
| `/insights` | List long-term insights |
| `/quit` | End session and exit |

### Expected output (excerpt)

```
✓ Memory service: healthy
You: Hi, I'm Alex...
Advisor: ...
--- Memory Context (N turns) ---
```

### ✅ Complete when

- You sent ≥2 custom messages and used one command

---

## Task 4.3 — Launch the Streamlit Live UI (20 min)

### What we are doing

- Open the browser-based live chat UI
- Have a multi-turn conversation with memory recall

### Steps

1. Keep the server running in **Terminal 1**.

2. In **Terminal 2**, start Streamlit:

```bash
uv run streamlit run demo/07_interactive_ui.py
```

3. Open the URL shown (usually `http://localhost:8501`).

4. In the UI:

   - Enter User ID: `lab-user-01`
   - Click **Start Session**
   - Send 3+ messages on two topics (e.g. food preferences, travel plans)
   - Ask: *"What did I say about food?"*
   - Explore the memory panel (context, insights, search)

### Expected behavior

- Real-time chat responses
- Memory context updates after each turn
- Recall question answered from earlier messages

### ✅ Complete when

- Multi-turn live chat with successful recall in the same session

---

## Task 4.4 — End Session & Review Memory Tiers (25 min)

### What we are doing

- End the session and test cross-session memory with the same User ID
- Document the three memory tiers you observed

### Steps

1. In Streamlit, click **End Session**.

2. Start a **new session** with the **same User ID** (`lab-user-01`).

3. Ask: *"What do you remember about me from before?"*

4. Document memory tiers:

| Tier | What it stores | Where you saw it |
|------|----------------|------------------|
| Active turns | Recent verbatim messages | Chat / context panel |
| Session summary | Compressed session history | After end session |
| Long-term insights | Facts across sessions | Insights panel |

5. Stop services:

   - Terminal 2: `Ctrl+C` (Streamlit)
   - Terminal 1: `Ctrl+C` (uvicorn)

### Expected behavior

- New session with same User ID recalls prior insights/summaries

### ✅ Complete when

- All three memory tiers documented
- Server and UI stopped cleanly

---

## Exercise 4 checklist

| Task | Done |
|------|------|
| 4.1 Memory server started | ☐ |
| 4.2 Terminal chat completed | ☐ |
| 4.3 Streamlit UI live chat | ☐ |
| 4.4 Cross-session recall tested | ☐ |

---

## Lab complete

Congratulations — you have completed **Advanced Dynamic Memory Architecture: Agent Memory**.
