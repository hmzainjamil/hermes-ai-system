# hermes-ai-system

NousResearch Hermes 3 agent with 30+ tools, persistent memory, 8 providers, 80+ skills — fully autonomous local AI system.

![Hermes](https://img.shields.io/badge/Hermes-3-blue?style=flat&labelColor=555) ![Tools](https://img.shields.io/badge/Tools-30+-green?style=flat&labelColor=555) ![Providers](https://img.shields.io/badge/Providers-8-orange?style=flat&labelColor=555) ![License](https://img.shields.io/badge/License-MIT-yellow?style=flat&labelColor=555)

[Concepts](#-concepts) · [How It Works](#-how-it-works) · [Install](#-install) · [Usage](#-usage) · [Config](#-configuration) · [Tips](#-tips-and-tricks-12) · [Troubleshooting](#-troubleshooting) · [Architecture](#-architecture) · [Startups](#️-startups--businesses)

---

## 🧠 CONCEPTS

| Feature | Location | Description |
|---|---|---|
| Hermes Agent Core | `core/agent.py` | NousResearch Hermes 3 with function-calling in chatml format |
| Tool Registry | `tools/registry.py` | 30+ tools: web search, code exec, file ops, APIs |
| Memory System | `memory/` | Persistent facts + semantic search via ChromaDB |
| Provider Router | `routing/router.py` | 8 providers: Ollama, Groq, DeepSeek, Gemini, OpenRouter, etc. |
| Skill Library | `skills/` | 80+ specialized skills loadable on demand |
| Function Calling | `core/functions.py` | OpenAI-format tool definitions for Hermes |
| Web Search | `tools/web_search.py` | Tavily + DuckDuckGo + SerpAPI |
| Code Executor | `tools/code_exec.py` | Sandboxed Python execution with output capture |
| File Manager | `tools/file_ops.py` | Read, write, search files from agent context |
| API Tools | `tools/apis/` | Google Ads, Meta, Slack, Gmail, Calendar integrations |
| Chat Interface | `ui/chat.py` | Gradio chat with tool visibility and memory display |
| Session Manager | `core/sessions.py` | Multi-session support with independent memory scopes |

### 🔥 Hot

| Feature | Location | Description |
|---|---|---|
| Hermes Function Calling | `core/functions.py` | Hermes 3 has best-in-class tool use at local model quality |
| Persistent Memory | `memory/` | Agent remembers across sessions — never re-explains context |
| 30+ Tools | `tools/registry.py` | Web → code → file → API in single agent loop |
| Skill Injection | `skills/` | 80+ skills dynamically injected into system prompt |
| 8 Providers | `routing/router.py` | Never locked into one provider — instant failover |

---

## ⚙️ HOW IT WORKS

```
User Message
    │
    ▼
Hermes 3 (via Ollama / Groq)
    │
    ├── Tool Call? → Execute Tool → Inject Result → Continue
    │
    ├── Memory Lookup → Semantic Search → Inject Context
    │
    ├── Skill Active? → Inject Skill Prompt
    │
    └── Final Response → Save to Memory

Tools available:
    web_search | code_exec | file_read | file_write
    google_ads | meta_ads | slack | gmail
    calendar | notion | airtable | browser
    image_gen | pdf_read | csv_analyze | sql_query
```

---

## 🚀 INSTALL

```bash
git clone https://github.com/hmzainjamil/hermes-ai-system
cd hermes-ai-system

# Hermes model
ollama pull hf.co/NousResearch/Hermes-3-Llama-3.1-8B-GGUF:Q5_K_M

# Python deps
pip install -r requirements.txt
# chromadb, tavily-python, gradio, openai, anthropic

cp .env.example .env
# Fill: TAVILY_API_KEY, GROQ_API_KEY, SLACK_BOT_TOKEN, etc.

# Init memory
python3 memory/init.py

# Test agent
python3 core/agent.py --test "What tools do you have?"

# Launch UI
python3 ui/chat.py
```

---

## 📟 USAGE

```bash
# Chat via CLI
python3 core/agent.py

# Chat with specific skill
python3 core/agent.py --skill ads-strategy

# Chat via web UI
python3 ui/chat.py  # http://localhost:7860

# Add memory
python3 memory/add.py "Client X uses GA4 with cross-domain tracking"

# List active tools
python3 tools/registry.py --list

# Enable tool for session
python3 tools/registry.py --enable google_ads

# Run autonomous task
python3 core/agent.py --task "Research top 5 competitors for DigiMinds and save report"

# Multi-session
python3 core/sessions.py --create "project-alpha"
python3 core/agent.py --session "project-alpha"
```

---

## ⚙️ CONFIGURATION

| Variable | Default | Description |
|---|---|---|
| `HERMES_MODEL` | `hermes-3-llama3.1:8b` | Ollama model tag for Hermes |
| `HERMES_TEMPERATURE` | `0.3` | Lower = more deterministic tool calls |
| `MAX_TOOL_ITERATIONS` | `10` | Max tool calls per agent turn |
| `MEMORY_TOP_K` | `5` | Memories to inject per turn |
| `MEMORY_SIMILARITY_THRESHOLD` | `0.75` | Min cosine similarity for memory retrieval |
| `TAVILY_API_KEY` | — | Tavily web search API key |
| `GROQ_API_KEY` | — | Groq for cloud Hermes fallback |
| `CODE_EXEC_TIMEOUT_S` | `30` | Sandboxed code execution timeout |
| `SKILL_AUTO_LOAD` | `true` | Auto-load skills by conversation topic |
| `SESSION_MEMORY_ISOLATION` | `true` | Separate memory per session |

---

## 💡 TIPS AND TRICKS (12)

[Hermes](#tips-hermes) · [Tools](#tips-tools) · [Memory](#tips-memory) · [Skills](#tips-skills)

<a id="tips-hermes"></a>■ **Hermes Model (3)**

| Tip | Source |
|---|---|
| Use Q5_K_M quant — best quality/speed balance on 16GB Mac for function calling | Ollama benchmarks |
| Set `temperature=0.1` for tool calls — Hermes is more reliable with low temp | NousResearch docs |
| Hermes follows chatml format — tool definitions must use `<tool_call>` format | Hermes docs |

<a id="tips-tools"></a>■ **Tool Use (3)**

| Tip | Source |
|---|---|
| Enable only needed tools per session — fewer tools = less confusion for model | Tool registry |
| `code_exec` tool runs in sandbox — safe for untrusted code from web search | Code executor |
| Chain tools naturally: web_search → code_exec → file_write for research tasks | Agent examples |

<a id="tips-memory"></a>■ **Memory System (3)**

| Tip | Source |
|---|---|
| Add project-specific facts via `memory/add.py` before starting complex sessions | Memory guide |
| Memory retrieval uses nomic-embed-text — must have Ollama running | Memory config |
| `memory/search.py "query"` to check what agent knows before asking | Memory search |

<a id="tips-skills"></a>■ **Skills (3)**

| Tip | Source |
|---|---|
| 80+ skills — only activate relevant ones to keep system prompt under 4k tokens | Skill library |
| Skills inject specialized knowledge — ads-strategy skill makes Hermes a PPC expert | Skill design |
| Create custom skills: `skills/my_skill/SKILL.md` with domain knowledge | Skill creation |

---

## 🔧 TROUBLESHOOTING

| Issue | Fix |
|---|---|
| Hermes model not found | `ollama pull hf.co/NousResearch/Hermes-3-Llama-3.1-8B-GGUF:Q5_K_M` |
| Tool calls malformed | Lower temperature: `HERMES_TEMPERATURE=0.1` |
| Memory not persisting | Check ChromaDB path: `memory/chroma_db/` must be writable |
| Code exec timeout | Increase `CODE_EXEC_TIMEOUT_S` or optimize code being executed |
| Web search returns nothing | Check TAVILY_API_KEY — free tier has 1000 req/month |
| Agent loops on tool | Check `MAX_TOOL_ITERATIONS` — increase if complex task |
| Gradio UI crashes | `pip install gradio>=4.0` |

---

## 📊 ARCHITECTURE

```
hermes-ai-system/
├── core/
│   ├── agent.py                # Main agent loop
│   ├── functions.py            # Tool definitions (OpenAI format)
│   └── sessions.py             # Session management
├── tools/
│   ├── registry.py             # Tool catalog
│   ├── web_search.py           # Tavily + DDG
│   ├── code_exec.py            # Sandboxed Python
│   ├── file_ops.py             # File read/write
│   └── apis/
│       ├── google_ads.py
│       ├── meta_ads.py
│       ├── slack.py
│       └── gmail.py
├── memory/
│   ├── init.py
│   ├── add.py
│   ├── search.py
│   └── chroma_db/              # Vector store
├── routing/
│   └── router.py               # 8 provider routing
├── skills/                     # 80+ skill SKILL.md files
├── ui/
│   └── chat.py                 # Gradio interface
├── config/
│   └── providers.yaml          # Provider definitions
└── .env.example
```

---

## 🛠️ TOOL CATALOG

| Category | Tools |
|---|---|
| Web | web_search, browser_fetch, screenshot |
| Code | python_exec, bash_exec, code_review |
| Files | file_read, file_write, file_search, pdf_read |
| Data | csv_analyze, sql_query, json_parse |
| Ads | google_ads_pull, meta_ads_pull |
| Comms | slack_send, gmail_send, calendar_create |
| AI | image_gen, text_embed, summarize |
| Utils | time_get, calculator, url_shorten |

---

## ☠️ STARTUPS / BUSINESSES

| This Repo / Feature | Replaced |
|---|---|
| Hermes 3 local agent | Paying Claude $0.015/1k tokens for every subtask |
| 30+ tool ecosystem | Separate specialized apps for each task |
| Persistent memory | Re-briefing agent every session |
| 8-provider routing | Single provider dependency and outage risk |
| 80+ skills | Generic LLM with no domain specialization |
| Session isolation | Memory bleed between client projects |
| Gradio UI | Terminal-only interface |
| Autonomous task mode | Human supervision for every agent step |

---

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=hmzainjamil/hermes-ai-system&type=Date)](https://star-history.com/#hmzainjamil/hermes-ai-system&Date)

---
<div align="center">Built by <a href="https://github.com/hmzainjamil">HMZ</a> · Part of HMZ Claude AI System</div>

---

## 🔄 CONTRIBUTING

PRs welcome. Please:
1. Fork the repo
2. Create a feature branch
3. Add tests for new functionality
4. Submit PR with description of changes

---

## 🔬 HERMES VS OTHER LOCAL MODELS

| Model | Tool Calling | Context | Speed (tok/s) | Ollama Tag |
|---|---|---|---|---|
| Hermes 3 8B | Excellent | 128K | ~45 | hermes-3-llama3.1:8b |
| Llama 3.1 8B | Good | 128K | ~50 | llama3.1:8b |
| Mistral 7B | Basic | 32K | ~55 | mistral:7b |
| Phi-3 Mini | Limited | 4K | ~70 | phi3:mini |
| DeepSeek-R1 7B | Good | 64K | ~40 | deepseek-r1:7b |

Hermes 3 recommended for agentic tool-use workloads. Llama 3.1 for pure chat.

---

## 🧪 FUNCTION CALLING FORMAT (HERMES)

```xml
<tool_call>
{"name": "web_search", "arguments": {"query": "Claude Code best practices 2025"}}
</tool_call>

<tool_response>
{"results": [{"title": "...", "url": "...", "snippet": "..."}]}
</tool_response>
```
