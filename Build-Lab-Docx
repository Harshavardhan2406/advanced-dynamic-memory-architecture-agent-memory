# Build Agent Memory Lab DOCX
$ErrorActionPreference = "Stop"
$outDir = Split-Path -Parent $MyInvocation.MyCommand.Path
$docxPath = Join-Path $outDir "Agent-Memory-Lab-Guide.docx"

function Escape-Xml([string]$text) {
    if ($null -eq $text) { return "" }
    return [System.Security.SecurityElement]::Escape($text)
}

function New-Paragraph([string]$text, [string]$style = $null, [bool]$bold = $false) {
    $escaped = Escape-Xml $text
    $pPr = if ($style) { "<w:pPr><w:pStyle w:val=`"$style`"/></w:pPr>" } else { "" }
    $rPr = if ($bold) { "<w:rPr><w:b/></w:rPr>" } else { "" }
    return "<w:p>${pPr}<w:r>${rPr}<w:t xml:space=`"preserve`">$escaped</w:t></w:r></w:p>"
}

function New-Bullet([string]$text, [int]$level = 0) {
    $escaped = Escape-Xml $text
    $indent = 720 + ($level * 720)
    return @"
<w:p>
  <w:pPr>
    <w:numPr><w:ilvl w:val="$level"/><w:numId w:val="1"/></w:numPr>
    <w:ind w:left="$indent" w:hanging="360"/>
  </w:pPr>
  <w:r><w:t xml:space="preserve">$escaped</w:t></w:r>
</w:p>
"@
}

function New-PageBreak {
    return '<w:p><w:r><w:br w:type="page"/></w:r></w:p>'
}

$tasks = @(
    @{
        Exercise = "Pre-Lab Setup"
        Duration = "15 min"
        Items = @(
            @{ Id = "0.1"; Name = "Open the Lab Credentials Sheet"; What = "Collect Azure OpenAI and Cosmos DB credentials from the instructor handout."; Steps = @(
                "Open the handout titled Agent Memory Lab — Credentials.",
                "Confirm it lists OpenAI endpoint, API key, deployment names, and Cosmos connection string.",
                "Ask the instructor for any missing values before continuing."
            ) }
            @{ Id = "0.2"; Name = "Verify the Pre-Configured .env File"; What = "Confirm the project .env file is pre-filled on the lab VM."; Steps = @(
                "Open a terminal and run: cd C:\Lab\agent-memory",
                "Open .env in VS Code: code .env",
                "Verify AZURE_OPENAI_* and COSMOS_* variables are populated."
            ) }
            @{ Id = "0.3"; Name = "Find Values in Azure Portal (Optional)"; What = "Learn where credentials live in Azure Portal for reference."; Steps = @(
                "Sign in to https://portal.azure.com if access is granted.",
                "OpenAI keys: Azure OpenAI → Keys and Endpoint.",
                "Cosmos string: Cosmos DB → Keys → Primary Connection String."
            ) }
        )
    }
    @{
        Exercise = "Exercise 1 — Environment Setup & Local Memory (SQLite)"
        Duration = "45 min"
        Items = @(
            @{ Id = "1.1"; Name = "Verify Tools and Open the Project"; What = "Confirm Python, uv, and Git are installed and open the agent-memory repo."; Steps = @(
                "Open PowerShell or bash terminal.",
                "Run: cd C:\Lab\agent-memory",
                "Run: python --version, uv --version, git --version",
                "List folders: demo, memory, server"
            ) }
            @{ Id = "1.2"; Name = "Review Environment Configuration"; What = "Inspect .env for Azure OpenAI settings and review architecture components."; Steps = @(
                "Run: code .env",
                "Confirm AZURE_OPENAI_ENDPOINT, API key, and deployment names are set.",
                "Optionally open README.md and review the architecture diagram.",
                "Note three components: AgentMemory, MemoryOrchestrator, Backend."
            ) }
            @{ Id = "1.3"; Name = "Install Dependencies and Run Basic Demo"; What = "Sync packages and run demo/01_basic_memory.py to see SQLite memory in action."; Steps = @(
                "Run: uv sync --extra dev",
                "Run: uv run python demo/01_basic_memory.py",
                "Watch for SESSION 1, BUFFER MANAGEMENT, SESSION 2, SEMANTIC SEARCH.",
                "Confirm output ends with DEMO COMPLETE!"
            ) }
            @{ Id = "1.4"; Name = "Observe Memory Behavior"; What = "Analyze buffer pruning and cross-session recall from the demo output."; Steps = @(
                "Answer: How many turns in Session 1? (8)",
                "Answer: What happens when the buffer fills? (Older turns summarized)",
                "Answer: Does Session 2 load Session 1 context? (Yes)",
                "Optional: locate demo_basic.db file created by the demo."
            ) }
        )
    }
    @{
        Exercise = "Exercise 2 — Agent Framework Integration"
        Duration = "60 min"
        Items = @(
            @{ Id = "2.1"; Name = "Study the Integration Pattern"; What = "Learn how AgentMemory plugs in as a ContextProvider via context_providers=[memory]."; Steps = @(
                "Run: code demo/02_agent_framework.py",
                "Find context_providers=[memory] on the Agent constructor.",
                "Trace flow: before_run injects context, after_run stores turns.",
                "Draw or note the hook flow in your lab notebook."
            ) }
            @{ Id = "2.2"; Name = "Run the Financial Advisor Demo"; What = "Run a multi-session advisor demo with automatic memory management."; Steps = @(
                "Run: uv run python demo/02_agent_framework.py",
                "Follow Initial Consultation, Investment Strategy, and Tax Planning sessions.",
                "Verify Session 2 recalls Sarah's profile from Session 1."
            ) }
            @{ Id = "2.3"; Name = "Compare Agent-Driven Memory Retrieval"; What = "Contrast automatic injection vs explicit memory search."; Steps = @(
                "Run: uv run python demo/03_agent_driven.py",
                "Compare with Task 2.2: automatic vs explicit memory use.",
                "Write one sentence describing the difference."
            ) }
            @{ Id = "2.4"; Name = "Long-Term Insight Extraction"; What = "Observe session summaries and long-term insights across sessions."; Steps = @(
                "Run: uv run python demo/06_insight_curation.py",
                "Watch for session summaries after end_session().",
                "Note extracted insights in the console output.",
                "Document progression: turns → summary → insights."
            ) }
        )
    }
    @{
        Exercise = "Exercise 3 — Azure Cosmos DB Backend"
        Duration = "60 min"
        Items = @(
            @{ Id = "3.1"; Name = "Understand Backend Options"; What = "Review four backends and confirm Cosmos credentials in .env."; Steps = @(
                "Review backend table: sqlite, cosmosdb, azure_ai_search, postgresql.",
                "Note this lab uses SQLite + Cosmos only.",
                "Run: Select-String -Path .env -Pattern COSMOS (Windows) or grep COSMOS .env (Linux).",
                "Confirm COSMOS_ENDPOINT or AZURE_COSMOS_CONNECTION_STRING is set."
            ) }
            @{ Id = "3.2"; Name = "Run the Cosmos DB Demo"; What = "Execute demo/04_cosmosdb.py with cloud persistence."; Steps = @(
                "Run: uv run python demo/04_cosmosdb.py",
                "Confirm header shows Backend: Azure CosmosDB.",
                "Watch all three Sarah sessions complete.",
                "Verify ending message: Data persisted to CosmosDB for future sessions."
            ) }
            @{ Id = "3.3"; Name = "Verify Data Persisted in Cosmos DB"; What = "Prove cloud data survives beyond the local machine."; Steps = @(
                "Option A: Re-run 04_cosmosdb.py and check cross-session recall.",
                "Option B: Azure Portal → Cosmos DB → Data Explorer → agent_memory_db.",
                "Answer: How is Cosmos different from SQLite?",
                "Answer: Why use Cosmos DB in production?"
            ) }
            @{ Id = "3.4"; Name = "Concept Check — Other Backends"; What = "Summarize backend usage across all exercises."; Steps = @(
                "Open README.md and find AGENT_MEMORY_DB_TYPE.",
                "List supported values: sqlite, cosmosdb, azure_ai_search, postgresql.",
                "Complete table: Exercises 1–2 = sqlite, Exercise 3 = cosmosdb, Exercise 4 server = cosmosdb."
            ) }
        )
    }
    @{
        Exercise = "Exercise 4 — Live Server Mode and Streamlit UI"
        Duration = "75 min"
        Items = @(
            @{ Id = "4.1"; Name = "Start the Memory Server"; What = "Launch FastAPI memory service on port 8000 with Cosmos backend."; Steps = @(
                "Terminal 1: cd C:\Lab\agent-memory",
                "Set AGENT_MEMORY_DB_TYPE=cosmosdb (PowerShell or export).",
                "Run: uv run uvicorn server.main:app --host 127.0.0.1 --port 8000",
                "Terminal 2: curl http://127.0.0.1:8000/health — expect healthy status."
            ) }
            @{ Id = "4.2"; Name = "Interactive Terminal Chat"; What = "Chat live via demo/05_server_mode.py connected to the memory server."; Steps = @(
                "Terminal 2: uv run python demo/05_server_mode.py",
                "Send at least two custom messages (e.g. name, age, goal).",
                "Try /context, /search, or /insights command.",
                "Type /quit to end the session."
            ) }
            @{ Id = "4.3"; Name = "Launch the Streamlit Live UI"; What = "Open browser chat UI and test multi-turn memory recall."; Steps = @(
                "Keep server running in Terminal 1.",
                "Terminal 2: uv run streamlit run demo/07_interactive_ui.py",
                "Open http://localhost:8501 in your browser.",
                "Enter User ID lab-user-01 and click Start Session.",
                "Send 3+ messages and ask a recall question about earlier messages."
            ) }
            @{ Id = "4.4"; Name = "End Session and Review Memory Tiers"; What = "Test cross-session recall and document memory tiers."; Steps = @(
                "Click End Session in Streamlit.",
                "Start new session with same User ID lab-user-01.",
                "Ask: What do you remember about me from before?",
                "Document tiers: active turns, session summary, long-term insights.",
                "Stop Streamlit and uvicorn with Ctrl+C."
            ) }
        )
    }
)

$body = New-Object System.Text.StringBuilder
[void]$body.Append(New-Paragraph "Advanced Dynamic Memory Architecture: Agent Memory" "Title")
[void]$body.Append(New-Paragraph "Lab Guide — Step-by-Step" "Subtitle")
[void]$body.Append(New-Paragraph "Duration: ~4 hours | Level: Beginner to Intermediate")
[void]$body.Append(New-Paragraph "Repository: github.com/james-tn/agent-memory")
[void]$body.Append(New-PageBreak)

[void]$body.Append(New-Paragraph "Table of Contents" "Heading1")
[void]$body.Append(New-Paragraph "")

foreach ($ex in $tasks) {
    [void]$body.Append(New-Paragraph $ex.Exercise "Heading2")
    foreach ($item in $ex.Items) {
        [void]$body.Append(New-Paragraph ("Task {0} — {1}" -f $item.Id, $item.Name))
    }
    [void]$body.Append(New-Paragraph "")
}

[void]$body.Append(New-PageBreak)

foreach ($ex in $tasks) {
    [void]$body.Append(New-Paragraph $ex.Exercise "Heading1")
    [void]$body.Append(New-Paragraph ("Duration: {0}" -f $ex.Duration))
    [void]$body.Append(New-Paragraph "")

    foreach ($item in $ex.Items) {
        [void]$body.Append(New-Paragraph ("Task {0} — {1}" -f $item.Id, $item.Name) "Heading2")
        [void]$body.Append(New-Paragraph "What we are doing" "Heading3")
        [void]$body.Append(New-Paragraph $item.What)
        [void]$body.Append(New-Paragraph "Steps" "Heading3")
        foreach ($step in $item.Steps) {
            [void]$body.Append(New-Bullet $step)
        }
        [void]$body.Append(New-PageBreak)
    }
}

$documentXml = @"
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<w:document xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main">
  <w:body>
    $($body.ToString())
    <w:sectPr>
      <w:pgSz w:w="12240" w:h="15840"/>
      <w:pgMar w:top="1440" w:right="1440" w:bottom="1440" w:left="1440" w:header="720" w:footer="720" w:gutter="0"/>
    </w:sectPr>
  </w:body>
</w:document>
"@

$stylesXml = @"
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<w:styles xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main">
  <w:style w:type="paragraph" w:default="1" w:styleId="Normal">
    <w:name w:val="Normal"/>
    <w:qFormat/>
    <w:rPr><w:sz w:val="22"/><w:szCs w:val="22"/></w:rPr>
  </w:style>
  <w:style w:type="paragraph" w:styleId="Title">
    <w:name w:val="Title"/>
    <w:basedOn w:val="Normal"/>
    <w:qFormat/>
    <w:pPr><w:jc w:val="center"/></w:pPr>
    <w:rPr><w:b/><w:sz w:val="48"/><w:szCs w:val="48"/></w:rPr>
  </w:style>
  <w:style w:type="paragraph" w:styleId="Subtitle">
    <w:name w:val="Subtitle"/>
    <w:basedOn w:val="Normal"/>
    <w:qFormat/>
    <w:pPr><w:jc w:val="center"/></w:pPr>
    <w:rPr><w:sz w:val="28"/><w:szCs w:val="28"/></w:rPr>
  </w:style>
  <w:style w:type="paragraph" w:styleId="Heading1">
    <w:name w:val="heading 1"/>
    <w:basedOn w:val="Normal"/>
    <w:qFormat/>
    <w:pPr><w:spacing w:before="240" w:after="120"/></w:pPr>
    <w:rPr><w:b/><w:sz w:val="32"/><w:szCs w:val="32"/></w:rPr>
  </w:style>
  <w:style w:type="paragraph" w:styleId="Heading2">
    <w:name w:val="heading 2"/>
    <w:basedOn w:val="Normal"/>
    <w:qFormat/>
    <w:pPr><w:spacing w:before="200" w:after="80"/></w:pPr>
    <w:rPr><w:b/><w:sz w:val="26"/><w:szCs w:val="26"/></w:rPr>
  </w:style>
  <w:style w:type="paragraph" w:styleId="Heading3">
    <w:name w:val="heading 3"/>
    <w:basedOn w:val="Normal"/>
    <w:qFormat/>
    <w:pPr><w:spacing w:before="160" w:after="60"/></w:pPr>
    <w:rPr><w:b/><w:sz w:val="24"/><w:szCs w:val="24"/></w:rPr>
  </w:style>
</w:styles>
"@

$numberingXml = @"
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<w:numbering xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main">
  <w:abstractNum w:abstractNumId="0">
    <w:multiLevelType w:val="hybridMultilevel"/>
    <w:lvl w:ilvl="0">
      <w:start w:val="1"/>
      <w:numFmt w:val="bullet"/>
      <w:lvlText w:val="•"/>
      <w:lvlJc w:val="left"/>
      <w:pPr><w:ind w:left="720" w:hanging="360"/></w:pPr>
    </w:lvl>
    <w:lvl w:ilvl="1">
      <w:start w:val="1"/>
      <w:numFmt w:val="bullet"/>
      <w:lvlText w:val="◦"/>
      <w:lvlJc w:val="left"/>
      <w:pPr><w:ind w:left="1440" w:hanging="360"/></w:pPr>
    </w:lvl>
  </w:abstractNum>
  <w:num w:numId="1">
    <w:abstractNumId w:val="0"/>
  </w:num>
</w:numbering>
"@

$contentTypes = @"
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
  <Default Extension="rels" ContentType="application/vnd.openxmlformats-package.relationships+xml"/>
  <Default Extension="xml" ContentType="application/xml"/>
  <Override PartName="/word/document.xml" ContentType="application/vnd.openxmlformats-officedocument.wordprocessingml.document.main+xml"/>
  <Override PartName="/word/styles.xml" ContentType="application/vnd.openxmlformats-officedocument.wordprocessingml.styles+xml"/>
  <Override PartName="/word/numbering.xml" ContentType="application/vnd.openxmlformats-officedocument.wordprocessingml.numbering+xml"/>
</Types>
"@

$rels = @"
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<Relationships xmlns="http://schemas.openxmlformats.org/package/2006/relationships">
  <Relationship Id="rId1" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/officeDocument" Target="word/document.xml"/>
</Relationships>
"@

$docRels = @"
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<Relationships xmlns="http://schemas.openxmlformats.org/package/2006/relationships">
  <Relationship Id="rId1" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/styles" Target="styles.xml"/>
  <Relationship Id="rId2" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/numbering" Target="numbering.xml"/>
</Relationships>
"@

$tempRoot = Join-Path $env:TEMP ("agent-memory-docx-" + [guid]::NewGuid().ToString())
$wordDir = Join-Path $tempRoot "word"
$relsDir = Join-Path $tempRoot "_rels"
New-Item -ItemType Directory -Path $wordDir -Force | Out-Null
New-Item -ItemType Directory -Path $relsDir -Force | Out-Null

Set-Content -Path (Join-Path $tempRoot "[Content_Types].xml") -Value $contentTypes -Encoding UTF8
Set-Content -Path (Join-Path $relsDir ".rels") -Value $rels -Encoding UTF8
Set-Content -Path (Join-Path $wordDir "document.xml") -Value $documentXml -Encoding UTF8
Set-Content -Path (Join-Path $wordDir "styles.xml") -Value $stylesXml -Encoding UTF8
Set-Content -Path (Join-Path $wordDir "numbering.xml") -Value $numberingXml -Encoding UTF8
Set-Content -Path (Join-Path $wordDir "_rels\document.xml.rels") -Value $docRels -Encoding UTF8

if (Test-Path $docxPath) { Remove-Item $docxPath -Force }
Compress-Archive -Path (Join-Path $tempRoot "*") -DestinationPath ($docxPath + ".zip") -Force
Move-Item -Path ($docxPath + ".zip") -Destination $docxPath -Force
Remove-Item $tempRoot -Recurse -Force

Write-Output "Created: $docxPath"
