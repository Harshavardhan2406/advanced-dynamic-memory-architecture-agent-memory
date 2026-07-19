# Exercise 2 — Agent Framework Integration

**Lab:** Advanced Dynamic Memory Architecture: Agent Memory  
**Duration:** 60 minutes

---

## Objective

Connect Agent Memory to **Microsoft Agent Framework** so memory is injected and stored **automatically** — no manual `add_turn()` calls.

---

## Task 2.1 — Study the Integration Pattern (15 min)

### What we are doing

- Read how `AgentMemory` plugs into Agent Framework as a `ContextProvider`
- Trace the before/after run hooks that manage memory each turn

### Steps

1. Open the demo file:

```bash
code demo/02_agent_framework.py
```

2. Locate these key lines:

   - `context_providers=[memory]` on the `Agent` constructor
   - `AgentMemory(..., db_path=DB_PATH)` for SQLite backend
   - Pre-written `queries` lists in `run_session()`

3. Note this flow in your lab notebook:

```
User query → Agent.run()
  → before_run(): memory injects context
  → LLM responds
  → after_run(): memory stores turn
```

### Expected understanding

Memory is a **ContextProvider** — the framework calls it automatically every turn.

### ✅ Complete when

- You can point to `context_providers=[memory]` in the code

---

## Task 2.2 — Run the Financial Advisor Demo (15 min)

### What we are doing

- Run a multi-session financial advisor scenario
- Observe cross-session recall without manual memory calls

### Steps

1. Run the demo:

```bash
uv run python demo/02_agent_framework.py
```

2. Follow three sessions in the console:

   - Initial Consultation
   - Investment Strategy
   - Tax Planning

3. In Session 2, check whether the advisor references Sarah's profile from Session 1.

### Expected output (excerpt)

```
🧠 Agent Memory Demo: Financial Advisor
  Memory: Automatic (no manual add_turn calls)
  Backend: SQLite (zero-config)

SESSION: Initial Consultation
👤 User: Hi! I'm Sarah, 35 years old...
🤖 Advisor: ...

SESSION: Investment Strategy
👤 User: Based on what we discussed before...
🤖 Advisor: ...
```

### ✅ Complete when

- Session 2 response references Sarah's age, income, or risk tolerance from Session 1

---

## Task 2.3 — Compare Agent-Driven Memory Retrieval (15 min)

### What we are doing

- Contrast automatic memory injection vs explicit memory search by the agent

### Steps

1. Run the agent-driven demo:

```bash
uv run python demo/03_agent_driven.py
```

2. Compare with Task 2.2:

   - **Exercise 2 demo:** memory injected automatically via hooks
   - **Exercise 3 demo:** agent explicitly searches memory when needed

3. Write one sentence in your notes describing the difference.

### Expected output

Demo completes with visible memory search/recall steps in the console.

### ✅ Complete when

- You can state one difference between automatic injection and explicit memory tools

---

## Task 2.4 — Long-Term Insight Extraction (15 min)

### What we are doing

- Run the insight curation demo
- Observe how sessions produce summaries and long-term insights

### Steps

1. Run:

```bash
uv run python demo/06_insight_curation.py
```

2. Watch for:

   - Session summaries after `end_session()`
   - Long-term insights extracted across sessions

3. Note the progression: **turns → session summary → long-term insights**

### Expected output (excerpt)

```
Session complete
 Summary: ...
 Insights extracted: N
Extracted Insights:
 • ...
```

### ✅ Complete when

- You can explain the three-tier memory progression

---

## Exercise 2 checklist

| Task | Done |
|------|------|
| 2.1 Integration pattern studied | ☐ |
| 2.2 Financial advisor demo run | ☐ |
| 2.3 Agent-driven comparison done | ☐ |
| 2.4 Insight curation demo run | ☐ |

**Next:** Exercise 3 — Azure Cosmos DB Backend
