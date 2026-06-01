# Next Steps: Refactoring to Thin Client

**Status:** ON HOLD
**Prerequisite:** The `coda-llm` background daemon must be fully implemented and running locally on `http://127.0.0.1:8000`.

## Context
We are migrating from a monolithic script to a client-server architecture. The `cli-howto` repository will become a "dumb" UI adapter (The Client), while the heavy lifting (LLM API calls, Prompt Engineering, Routing) moves to the `coda-llm` repository (The Daemon).

## Refactoring Checklist

When `coda-llm` is ready, execute the following refactoring steps in `howto`:

### 1. Remove Heavy Dependencies
- [ ] Remove `google-generativeai` from `requirements.txt`.
- [ ] Remove `import google.generativeai as genai` from `howto`.
- [ ] Remove direct HTTP calls to `localhost:11434` (Ollama).

### 2. Remove Domain Knowledge
- [ ] Delete the `SYSTEM_PROMPT` entirely. The prompt engineering is now the responsibility of `coda-llm`.

### 3. Implement the API Seam
- [ ] Replace the `ask_local` and `ask` functions with a single `ask_daemon(question: str, use_remote: bool)` function.
- [ ] This function should send a JSON POST request to `http://127.0.0.1:8000/api/ask`:
  ```json
  {
    "query": "<user's question>"
  }
  ```
  *(Note: The `--remote` flag might translate to sending a specific parameter in the JSON payload, or the daemon might handle fallbacks automatically depending on how it's implemented).*

### 4. Update the UI Presentation
- [ ] The daemon will return strict JSON rather than raw markdown:
  ```json
  {
    "primary_command": "...",
    "explanation": "...",
    "source_engine": "gemma2:2b"
  }
  ```
- [ ] Update the `rich` Console rendering to parse this JSON and format it into the nice panels, rather than relying on the LLM to output markdown headers. This guarantees a perfect UI every time.

## Objective
By the end of this refactor, `howto` should be under 100 lines of code, incredibly fast, and completely ignorant of whether it's talking to an 11B parameter local model or a 100B parameter cloud model.