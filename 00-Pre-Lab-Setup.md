# Pre-Lab Setup — Advanced Dynamic Memory Architecture: Agent Memory

**Duration:** 15 minutes  
**Level:** Beginner to Intermediate

---

## Objective

Collect pre-deployed Azure credentials and verify your lab environment before Exercise 1.

> Azure resources are **already provisioned** by your instructor. You do **not** run `azd provision`.

---

## Task 0.1 — Open the Lab Credentials Sheet (5 min)

### What we are doing

- Locate the instructor handout with Azure OpenAI and Cosmos DB values
- Confirm you have endpoint, API key, deployment names, and Cosmos connection details

### Steps

1. Open the handout titled **Agent Memory Lab — Credentials** (PDF, portal link, or shared file).
2. Confirm it contains:
   - Azure OpenAI endpoint and API key
   - Chat and processing deployment names
   - Embedding deployment name (`text-embedding-ada-002`)
   - Cosmos DB endpoint or connection string
3. If anything is missing, ask your instructor before continuing.

### Expected result

You have all credential fields available for the next task.

### ✅ Complete when

- Lab Credentials Sheet is open and complete

---

## Task 0.2 — Verify the Pre-Configured `.env` File (5 min)

### What we are doing

- Open the project folder on the lab VM
- Confirm `.env` is pre-filled with Azure OpenAI and Cosmos settings

### Steps

1. Open a terminal and go to the project folder:

```bash
cd C:\Lab\agent-memory
```

> Linux: `cd /home/lab/agent-memory`

2. Open `.env`:

```bash
code .env
```

3. Verify these variables are set (non-empty):

   - `AZURE_OPENAI_ENDPOINT`
   - `AZURE_OPENAI_API_KEY`
   - `AZURE_OPENAI_REASONING_MODEL`
   - `AZURE_OPENAI_PROCESSING_MODEL`
   - `AZURE_OPENAI_EMB_DEPLOYMENT`
   - `COSMOS_ENDPOINT` or `AZURE_COSMOS_CONNECTION_STRING`

### Expected result

All required variables have values supplied by the instructor.

### ✅ Complete when

- `.env` opens without errors and all required keys are populated

---

## Task 0.3 — Find Values in Azure Portal (Optional Reference) (5 min)

### What we are doing

- Learn where credentials live in Azure Portal if you need to look them up later

### Steps

1. Sign in to [https://portal.azure.com](https://portal.azure.com) if your instructor granted access.
2. Use this reference:

| Value | Portal path |
|-------|-------------|
| OpenAI endpoint & key | **Azure OpenAI** → **Keys and Endpoint** |
| Deployment names | **Azure OpenAI** → **Model deployments** |
| Cosmos endpoint | **Cosmos DB** → **Overview** → URI |
| Cosmos connection string | **Cosmos DB** → **Keys** → Primary Connection String |

### Expected result

You know where each lab value comes from in Azure.

### ✅ Complete when

- You can explain where to find OpenAI keys and Cosmos connection string in the portal

---

## Next step

Proceed to **Exercise 1 — Environment Setup & Local Memory**.
