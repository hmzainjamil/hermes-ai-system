# Hermes Local Integration Scripts

This repository contains shell and Python helpers that connect a local workstation to an externally installed Hermes CLI, Ollama, and optional services. It is not the Hermes Agent distribution and does not include the Hermes runtime.

## Repository map

| File | Role |
|---|---|
| `hermes-activate` | Checks a local Ollama endpoint and model, may pull `qwen2.5:7b`, sends a keep-alive request, writes a readiness sentinel, and optionally invokes the Hermes CLI with a task |
| `hermes-deactivate` | Kills processes matching `hermes chat` and `hermes-agent`, then removes the readiness sentinel |
| `hermes-autodetect` | Searches for a Hermes binary; when found, may create `~/.hermes/config.yaml`, edit `~/.zshrc`, update a Claude agent file, and create the readiness sentinel |
| `hermes-fc` | Sends a prompt to a local Ollama-compatible endpoint; if that call fails, it attempts the configured OpenRouter model. `--remote` forces the remote path |
| `hermes-orchestrator` | Logs a task, applies simple keyword routing, and invokes local Hermes, TCC, MAE, or bridge commands depending on matches |
| `hermes-audit` | Runs workstation checks and live probes against local tools, files, services, and remote endpoints |
| `README.md` | Scope, side effects, and review guidance |

## Relationship to Hermes Agent

The scripts expect a separately installed `hermes` command. The upstream project is [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent). This repository is a local integration/helper collection, not an upstream fork or release. Its scripts also reference local Claude, MAE, TCC, and Paperclip paths that are not included here.

## Data and operational effects

Review each script and its target paths before use.

- `hermes-activate` contacts Ollama at `localhost:11434`. It can download the hard-coded `qwen2.5:7b` model with `ollama pull`, sends a keep-alive request, creates `~/.hermes/.hermes_ready`, and passes any supplied task to the local Hermes CLI.
- `hermes-autodetect` can modify files under the home directory, including `~/.hermes/config.yaml`, `~/.zshrc`, and `~/.claude/agents/hermes-nous-agent.md`.
- `hermes-deactivate` uses broad `pkill -f` patterns. It can terminate matching Hermes chat or agent processes, then deletes the readiness sentinel.
- `hermes-fc` sends prompts to the local Ollama-compatible API first. On failure, it sends the prompt to OpenRouter if the key is configured. The OpenRouter model and endpoint are specified in source. The script reads `GROQ_API_KEY` but does not use it.
- `hermes-orchestrator` writes the supplied task text to timestamped logs under `~/.claude/tcc-logs`. Depending on keyword matches, it may pass that task to Hermes or invoke external local routing commands. Hermes may itself be configured to use a remote provider.
- `hermes-audit` sources `~/.zshrc` and `~/.hermes/.env`, evaluates command strings, checks local paths and services, makes network requests, and issues live model prompts. Treat it as an active workstation probe, not a read-only or isolated unit-test suite. Do not run it before reviewing its commands and data exposure.

## Validation and limitations

The files document the behavior above; they were not executed as part of this review. The audit script labels 100 checks, but its results depend on local state and external services, and it includes placeholder logic checks. No results are claimed here.

The repository has no dependency manifest, installation procedure, automated test suite, or license file. GitHub metadata reports no declared license. Do not infer permission to reuse or redistribute these scripts. Paths, model identifiers, provider behavior, and upstream Hermes interfaces can change.

## Maintainer

[hmzainjamil](https://github.com/hmzainjamil)
