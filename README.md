# Azure AI Foundry Multi-Tool Agent (Python SDK & REST)

A production-ready multi-tool AI agent built using **Microsoft Azure AI Foundry**, demonstrating tool integrations (**Code Interpreter** and **Web Search**), streaming responses via the **OpenAI Protocol**, and direct invocation using **REST API / cURL**.

---

## Architecture & Features

* **Multi-Tool Integration**:
  * **Code Interpreter**: Processes structured data files (`Sales.xlsx`) directly in a sandboxed Python execution container.
  * **Web Search**: Retrieves real-time information and external context with automatic source citation.
* **SDK & Programmatic Orchestration**: Uses `azure-ai-projects` (v2.3.0) and `AIProjectClient` to dynamically create agent versions and multi-turn conversations.
* **OpenAI Protocol Streaming**: Interacts with the published agent endpoint via `openai` Python SDK with token delta streaming.
* **REST API Invocation**: Verified direct execution using Entra ID Bearer tokens via `curl.exe` and PowerShell `Invoke-RestMethod`.

---

## Project Structure

```text
├── agentenv/                 # Virtual environment (git-ignored)
├── .env                      # Azure credentials & endpoints (git-ignored)
├── .gitignore                # Ignored files & folders
├── agent.py                  # Azure AI Projects SDK script (multi-tool setup)
├── call_published_agent.py   # OpenAI client consuming published agent
├── Sales.xlsx                # Sample data analyzed by Code Interpreter
└── README.md                 # Project documentation
```

---

## Setup & Installation

### 1. Prerequisites
* Python 3.9+ installed
* Azure CLI (`az`) installed
* Microsoft Azure subscription with Azure AI Foundry access

### 2. Environment Configuration
Create and activate the virtual environment:
```powershell
python -m venv agentenv
.\agentenv\Scripts\Activate.ps1
```

Install required dependencies:
```powershell
pip install "azure-ai-projects==2.3.0" azure-identity openai python-dotenv httpx
```

### 3. Configure Environment Variables
Create a `.env` file in the project root:
```env
PROJECT_ENDPOINT="https://<resource-name>.services.ai.azure.com/api/projects/<project-name>"
MODEL_DEPLOYMENT_NAME="gpt-4.1-mini"
BASE_URL="https://<resource-name>.services.ai.azure.com/api/projects/<project-name>/agents/python-multitool-agent/endpoint/protocols/openai"
```

### 4. Azure Authentication
Log in via Azure CLI:
```powershell
az login --use-device-code
```

---

## Usage

### Run Multi-Turn Agent with Tools
Executes agent provisioning, uploads `Sales.xlsx`, and runs a multi-turn calculation and analysis conversation:
```powershell
python agent.py
```

### Call Published Agent via Streaming SDK
Streams live responses from the deployed agent:
```powershell
python call_published_agent.py
```

### Direct REST Call via cURL
Obtain an Entra ID bearer token and invoke the endpoint directly:
```powershell
$TOKEN = az account get-access-token --resource https://ai.azure.com --query accessToken -o tsv

curl.exe -i --fail-with-body -X POST "$BASE_URL/responses?api-version=2025-11-15-preview" `
  -H "Authorization: Bearer $TOKEN" `
  -H "Content-Type: application/json" `
  -H "Foundry-Features: AgentEndpoints=V1Preview" `
  --data-raw '{\"input\": \"What are the benefits of using Microsoft Foundry?\"}'
```