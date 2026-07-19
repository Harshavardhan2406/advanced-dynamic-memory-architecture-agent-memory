# Exercise 3 — Azure Cosmos DB Backend

**Lab:** Advanced Dynamic Memory Architecture: Agent Memory  
**Duration:** 60 minutes

---

## Objective

Switch from local SQLite to **Azure Cosmos DB** using the same Agent Memory API — demonstrating pluggable cloud persistence.

> This exercise uses a **scripted demo**. Live interaction is in Exercise 4.

---

## Task 3.1 — Understand Backend Options (15 min)

### What we are doing

- Compare the four supported backends and understand why this lab uses SQLite + Cosmos only
- Verify Cosmos credentials in `.env`

### Steps

1. Review the backend table:

| Backend | Best for | In this lab? |
|---------|----------|--------------|
| `sqlite` | Local dev | ✅ Exercises 1–2 |
| `cosmosdb` | Production, global scale | ✅ This exercise |
| `azure_ai_search` | Managed search indexes | ❌ Not used |
| `postgresql` | pgvector relational store | ❌ Not used |

2. Check Cosmos variables in `.env`:

```bash
# Windows
Select-String -Path .env -Pattern "COSMOS"

# Linux
grep COSMOS .env
```

3. Confirm `COSMOS_ENDPOINT` or `AZURE_COSMOS_CONNECTION_STRING` is set.

### Expected output

```
COSMOS_ENDPOINT=https://....documents.azure.com:443/
AZURE_COSMOS_CONNECTION_STRING=AccountEndpoint=...
```

### ✅ Complete when

- You can explain why Cosmos DB is used instead of AI Search or PostgreSQL in this lab

---

## Task 3.2 — Run the Cosmos DB Demo (20 min)

### What we are doing

- Run the financial advisor demo with Cosmos DB as the persistence backend
- Confirm data is written to the cloud, not a local `.db` file

### Steps

1. Run:

```bash
uv run python demo/04_cosmosdb.py
```

2. Confirm the header shows Cosmos DB:

```
Backend: Azure CosmosDB (enterprise-grade)
✅ CosmosDB: Endpoint configured
```

3. Watch all three Sarah sessions complete.

### Expected output (excerpt)

```
🧠 Agent Memory Demo: Financial Advisor (CosmosDB)
✅ CosmosDB: Endpoint configured
...
✅ Demo Complete!
 Data persisted to CosmosDB for future sessions
```

### Troubleshooting

| Error | Fix |
|-------|-----|
| Cosmos credentials not found | Check `.env`; ask instructor |
| Forbidden / firewall | Instructor must allow lab VM IP on Cosmos firewall |
| `CosmosHttpResponseError` | Contact instructor |

### ✅ Complete when

- Demo ends with `Data persisted to CosmosDB for future sessions`

---

## Task 3.3 — Verify Data Persisted in Cosmos DB (15 min)

### What we are doing

- Prove cloud persistence survives beyond the local machine
- Compare SQLite vs Cosmos DB trade-offs

### Steps

1. **Option A — Re-run demo:** Run `04_cosmosdb.py` again. With the same `USER_ID`, Session 2/3 should recall Sarah without re-introduction.

2. **Option B — Azure Portal** (if access granted):

   - **Azure Portal** → **Cosmos DB** → your lab account
   - **Data Explorer** → database `agent_memory_db`
   - Browse containers for session/insight data

3. Answer in your notes:

   - What changed vs SQLite?
   - Why pick Cosmos DB for production?

### Expected answers

- SQLite = local file on one machine; Cosmos = cloud, durable, scalable
- Production benefits: global distribution, managed vector search, multi-user persistence

### ✅ Complete when

- You can explain SQLite vs Cosmos DB trade-off

---

## Task 3.4 — Concept Check: Other Backends (10 min)

### What we are doing

- Understand how backend selection works via environment variable
- Summarize which backend is used in each exercise

### Steps

1. Open `README.md` and find `AGENT_MEMORY_DB_TYPE`.

2. Supported values: `sqlite`, `cosmosdb`, `azure_ai_search`, `postgresql`

3. Complete this table:

| Question | Your answer |
|----------|-------------|
| Backend in Exercises 1–2? | sqlite |
| Backend in Exercise 3? | cosmosdb |
| Backend for server in Exercise 4? | cosmosdb |

### ✅ Complete when

- Table is complete; you understand one API, multiple backends

---

## Exercise 3 checklist

| Task | Done |
|------|------|
| 3.1 Backend options reviewed | ☐ |
| 3.2 Cosmos demo run | ☐ |
| 3.3 Persistence verified | ☐ |
| 3.4 Concept check completed | ☐ |

**Next:** Exercise 4 — Live Server Mode & Streamlit UI
