# hermes-ai-system
Local Ollama AI orchestrator for Claude Code — auto-detect, audit, function-calling, task routing via qwen2.5:7b

![macOS](https://img.shields.io/badge/macOS-Apple_Silicon-black?style=flat&labelColor=555&logo=apple)
![Shell](https://img.shields.io/badge/Shell-Bash-4EAA25?style=flat&labelColor=555&logo=gnubash)
![Ollama](https://img.shields.io/badge/Ollama-qwen2.5:7b-white?style=flat&labelColor=555)
![Claude](https://img.shields.io/badge/Claude-Code-cc785c?style=flat&labelColor=555)
![Model](https://img.shields.io/badge/Model-Local_LLM-blue?style=flat&labelColor=555)
![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=flat&labelColor=555)

[Concepts](#-concepts) · [How It Works](#️-how-it-works) · [Install](#-install) · [Commands](#-commands) · [Tips](#-tips-and-tricks-9) · [Startups](#️-startups--businesses)

---

## 🧠 CONCEPTS

| Feature | Location | Description |
|---------|----------|-------------|
| [**hermes-activate**](hermes-activate) | `hermes-activate` | Warms up qwen2.5:7b via Ollama, confirms model ready, sets sentinel flag |
| [**hermes-deactivate**](hermes-deactivate) | `hermes-deactivate` | Cleanly unloads model, removes sentinel, frees VRAM |
| [**hermes-autodetect**](hermes-autodetect) | `hermes-autodetect` | Scans available Ollama models, picks best for current task |
| [**hermes-audit**](hermes-audit) | `hermes-audit` | 441-line health check — model status, latency, memory, tool availability |
| [**hermes-fc**](hermes-fc) | `hermes-fc` | Function-calling wrapper — structured JSON outputs from local model |
| [**hermes-orchestrator**](hermes-orchestrator) | `hermes-orchestrator` | Routes tasks to Hermes vs Claude vs Tier0 based on complexity + cost |

### 🔥 Hot

| Feature | Location | Description |
|---------|----------|-------------|
| [**Zero API Cost Routing**](hermes-orchestrator) | `hermes-orchestrator` | Simple tasks → local qwen2.5:7b, complex → Claude. Saves Claude quota |
| [**Function Calling Local**](hermes-fc) | `hermes-fc` | Structured JSON tool-use from local model — no OpenAI/Anthropic API needed |
| [**441-line Health Audit**](hermes-audit) | `hermes-audit` | Full system audit: model latency, memory, tool chain, Ollama version |

---

## ⚙️ HOW IT WORKS

```
User task arrives
      ↓
hermes-autodetect → scans loaded models → picks best fit
      ↓
hermes-orchestrator → complexity check
      ├── Simple/local → hermes-activate → qwen2.5:7b (FREE)
      │                        ↓
      │                  hermes-fc (structured output)
      └── Complex → Claude Code / Tier0 LLMs
```

**Sentinel system:** `hermes-activate` writes `~/.hermes/.hermes_ready` — orchestrator checks this before routing. No cold starts mid-task.

**Model priority:** qwen2.5:7b → llama3 → fallback to Claude Haiku

---

## 🚀 INSTALL

```bash
git clone https://github.com/hmzainjamil/hermes-ai-system
cd hermes-ai-system
cp hermes-* ~/.claude/bin/
chmod +x ~/.claude/bin/hermes-*
```

**Requires:**
- [Ollama](https://ollama.ai) running locally
- `ollama pull qwen2.5:7b`
- Claude Code installed

---

## 📟 COMMANDS

| Command | Description |
|---------|-------------|
| `hermes-activate` | Warm up + confirm model ready |
| `hermes-activate "task"` | Warm up with task context hint |
| `hermes-deactivate` | Unload model, free memory |
| `hermes-autodetect` | List + score available models |
| `hermes-audit` | Full 441-point system health check |
| `hermes-fc "prompt" "schema"` | Function call with JSON schema output |
| `hermes-orchestrator "task"` | Auto-route task to best model |

---

## 💡 TIPS AND TRICKS (9)

[routing](#tips-routing) · [models](#tips-models) · [debug](#tips-debug) · [integration](#tips-integration)

<a id="tips-routing"></a>■ **Routing (3)**

| Tip | Source |
|-----|--------|
| Run `hermes-autodetect` first — it scores models by speed+size fit for your task type | [HMZ](https://github.com/hmzainjamil) |
| Sentinel at `~/.hermes/.hermes_ready` — check it before task to avoid cold-start latency | [HMZ](https://github.com/hmzainjamil) |
| `hermes-orchestrator` checks task word count: <50 words → local, >50 → Claude | [DigiMinds](https://github.com/hmzainjamil) |

<a id="tips-models"></a>■ **Models (3)**

| Tip | Source |
|-----|--------|
| qwen2.5:7b outperforms llama3 on structured output tasks — prefer it for hermes-fc | [Ollama Docs](https://ollama.ai) |
| `ollama stop model-name` between tasks frees ~2GB VRAM — use hermes-deactivate | [HMZ](https://github.com/hmzainjamil) |
| Keep `ollama serve` running as LaunchAgent — hermes won't start without it | [Ollama Docs](https://ollama.ai) |

<a id="tips-debug"></a>■ **Debug (2)**

| Tip | Source |
|-----|--------|
| `hermes-audit` output: look for `LATENCY > 2000ms` — means model needs reload | [HMZ](https://github.com/hmzainjamil) |
| If hermes-fc returns malformed JSON, add `--retry 3` flag — it retries with stricter prompt | [HMZ](https://github.com/hmzainjamil) |

<a id="tips-integration"></a>■ **Integration (1)**

| Tip | Source |
|-----|--------|
| Wire hermes-orchestrator into MAE via `mae-task-intercept` — auto-routes before hitting Claude API | [DigiMinds](https://github.com/hmzainjamil) |

---

## ☠️ STARTUPS / BUSINESSES

| This Repo / Feature | Replaced |
|-|-|
| **hermes-orchestrator** | [LangChain](https://langchain.com), [LlamaIndex](https://llamaindex.ai), [Haystack](https://haystack.deepset.ai) |
| **hermes-fc (local function calling)** | [OpenAI Function Calling API](https://openai.com), [Anthropic Tool Use](https://anthropic.com) — zero cost |
| **hermes-autodetect** | [LM Studio](https://lmstudio.ai), [Jan.ai](https://jan.ai) model selection UI |
| **hermes-audit** | [Datadog LLM Observability](https://datadoghq.com), [Langfuse](https://langfuse.com), [HelixML](https://helix.ml) |
| **Whole Hermes System** | [Replicate](https://replicate.com), [Together AI](https://together.ai), [Fireworks AI](https://fireworks.ai) — runs 100% local |

---

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=hmzainjamil/hermes-ai-system&type=Date)](https://star-history.com/#hmzainjamil/hermes-ai-system&Date)

---

<div align="center">
Built by <a href="https://github.com/hmzainjamil">HMZ</a> · Part of the <a href="https://github.com/hmzainjamil/claude-ai-system">HMZ Claude AI System</a> · Runs 100% local
</div>
