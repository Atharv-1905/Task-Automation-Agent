# 🚀 Experiment 09: Automated Task Agent (Web Search & Summarization Workflow)

![Workflow & Tech Stack Banner](banner.png)

An automated two-stage agentic workflow built with **Python**, **LangChain (LCEL)**, and **DuckDuckGo Search (`ddgs`)** that retrieves live information from the web on any target topic and condenses the findings into a structured executive brief—with **zero manual intervention** between steps.

---

## 📌 1. Aim & Objective
To design, implement, and test a deterministic, sequential agentic pipeline that chains together:
1. **Stage 1 (Information Retrieval):** Queries the live web for real-time articles and snippets on a user-provided topic.
2. **Stage 2 (Condensation & Synthesis):** Passes the retrieved web context into a Large Language Model (LLM) to generate a structured executive summary and key bullet points automatically.

Unlike dynamic ReAct agents that autonomously loop and select tools at runtime, this project demonstrates how **fixed sequential workflows** provide fast, predictable, and repeatable task automation.

---

## 🛠️ 2. Tech Stack & Environment
* **OS:** Linux (Ubuntu)
* **Language:** Python 3.12
* **Platform:** Jupyter Notebook (`.ipynb`)
* **Framework & Libraries:** 
  * `langchain`, `langchain-core`, `langchain-community`
  * `langchain-openai` (`ChatOpenAI` client)
  * `ddgs` / `duckduckgo-search` (Keyless live web search)
* **Model Endpoint:** OpenAI-compatible LLM inference via Groq API (`https://api.groq.com/openai/v1`)

---

## ⚙️ 3. Pipeline Architecture

```text
[User Input: Topic] 
        │
        ▼
[Stage 1: DuckDuckGo Search Tool (Multi-Backend Fallback)]
        │
        ▼
[Data Bridge: RunnablePassthrough.assign(search_results=...)]
        │
        ▼
[Stage 2: ChatPromptTemplate ──► ChatOpenAI (LLM) ──► StrOutputParser]
        │
        ▼
[Output: Structured Executive Brief (2-Sentence Summary + Bullet Points)]
```

---

## 🧩 4. Problem-Solving Approach & Engineering Fixes
During implementation on Ubuntu (Python 3.12), four real-world engineering challenges were resolved:

1. **Environment Isolation:** Used a dedicated Python virtual environment (`venv`) and exported dependencies to `requirements.txt` to avoid conflicts with Ubuntu system Python packages.
2. **API Quota & Model Discovery Mitigation:** Standard OpenAI free-tier accounts throw `HTTP 429 (insufficient_quota)` errors, and hardcoded model names can cause `HTTP 404 (model_not_found)` errors. We configured `ChatOpenAI` to connect to Groq's free OpenAI-compatible endpoint and dynamically queried `client.models.list()` at runtime to auto-select an active LLM.
3. **Search Package Migration (`duckduckgo-search` ➔ `ddgs`):** Updated `langchain-community` utilities require the renamed `ddgs` package. Installing `ddgs` directly resolved `ModuleNotFoundError`.
4. **ISP/Firewall Timeout Resilience:** Default search routing (`backend="auto"`) timed out on throttled search mirrors (such as Mojeek). We implemented a resilient custom search wrapper (`reliable_web_search`) that iterates across multiple fallback backends (`["duckduckgo", "lite", "html", "bing", "brave"]`) with a 15-second timeout.

---

## 📂 5. Repository Structure

```text
Experiment-09-Task-Automation-Agent/
├── Experiment_09_Task_Automation_Agent.ipynb   # Main Jupyter Notebook (Code + Outputs + Documentation)
├── requirements.txt                            # Python package dependencies
├── banner.png                                  # Project workflow & tech stack infographic
├── .gitignore                                  # Excludes heavy venv/ and cache files
└── README.md                                   # Project documentation
```

---

## 🚀 6. How to Run This Project Locally

### 1. Clone the repository
```bash
git clone https://github.com/YOUR_GITHUB_USERNAME/Experiment-09-Task-Automation-Agent.git
cd Experiment-09-Task-Automation-Agent
```

### 2. Create a virtual environment & install dependencies
```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### 3. Launch Jupyter Notebook
```bash
jupyter notebook Experiment_09_Task_Automation_Agent.ipynb
```
*(When prompted in Cell 1, enter your free API key from [Groq Console](https://console.groq.com/keys) or OpenAI).*

---

## 📝 7. Key Observations (Task 6)
Testing the end-to-end sequential workflow across multiple topics (*"Quantum Computing breakthroughs in healthcare"* and *"Autonomous AI Agents in software engineering"*) confirmed that chaining `RunnablePassthrough.assign()` with LangChain Expression Language (`|`) completely eliminates manual copy-pasting between web retrieval and LLM summarization. Grounding the prompt in live DuckDuckGo search snippets enabled the LLM to accurately summarize recent developments beyond its static training cutoff with low latency and consistent formatting.
