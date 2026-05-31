# Local AI Learning Path & Architecture Guide

This document outlines a practical, project-based curriculum for mastering open-source Large Language Models (LLMs). It transitions from building basic local tools to fine-tuning custom models, and eventually deploying enterprise-grade architectures.

## Phase 1: Supervised Fine-Tuning (SFT) with LoRA (Your Next Step)

**Goal:** Teach a small model (like Gemma 2B) a specific *behavior* and *format* so it no longer requires a massive system prompt. You are baking the behavior into the model's weights.

**Why it matters:** In enterprise scenarios, you don't want to waste context window space (which costs money and compute) on 1,000 words of instructions. A fine-tuned model knows *how* to act natively.

### The Project Plan (`howto` v2)

1.  **Synthetic Data Generation (The "Distillation" approach):**
    *   Write a Python script that loops through 1,000 common Unix concepts (e.g., "tar", "grep", "lsof", "find").
    *   For each concept, use the **Google Gemini Cloud API** to generate a realistic user query (e.g., "how do I zip a folder?") and the *perfectly formatted* Markdown response matching your exact `howto` style.
    *   Save these pairs into a `dataset.jsonl` file.
2.  **Environment Setup:**
    *   Create a Google Colab notebook (free GPU access is sufficient for a 2B model).
    *   Use the **Unsloth** library (the fastest, most efficient library for fine-tuning open models).
3.  **The Fine-Tuning Loop (LoRA):**
    *   Load the base `gemma-2b` model.
    *   Apply a **LoRA (Low-Rank Adaptation)** adapter. This freezes the 2 billion parameters and only trains a tiny, attached layer (e.g., 20 million parameters). This is why you can do it on a single GPU in under an hour.
    *   Feed your `dataset.jsonl` through the training loop.
4.  **Quantization & Export:**
    *   Export your trained LoRA weights merged with the base model into a `.GGUF` file format. (GGUF is optimized for CPU/Mac inference).
5.  **Local Inference:**
    *   Import your custom `.GGUF` file into your local Ollama instance (`ollama create my-howto-model -f Modelfile`).
    *   Update your `howto` Python script to query `my-howto-model` with *zero* system prompt. Watch it perfectly format the output natively.

**Trade-offs at Scale:**
*   **Benefits:** Extremely fast inference. Consistent formatting. Zero prompt tokens wasted.
*   **Downsides:** The model cannot learn *new facts* this way. If a new Unix command is invented tomorrow, the model won't know it unless you train it again.

---

## Phase 2: Agentic Workflows & Tool Calling (The "Action" Phase)

**Goal:** Bridge the gap between a chatbot that talks, and an agent that *does*.

**The Project:** Upgrade `howto` to execute the commands it suggests.
1.  Prompt the model to output strict JSON: `{"command": "kill -9 8080", "safe_to_run": true}`.
2.  Use Python to parse the JSON. If `safe_to_run` is true, prompt the user: `Execute 'kill -9 8080'? (y/n)`.

**Trade-offs at Scale:**
*   **Benefits:** Transforms AI from a search engine into an automated worker.
*   **Downsides:** Requires rigorous error handling. If the model hallucinates a destructive command (`rm -rf /`), your code must catch it before execution.

---

## Phase 3: Retrieval-Augmented Generation (RAG) (The "Knowledge" Phase)

**Goal:** Give the model access to private, real-time data without retraining it.

**The Project:** Make `howto` aware of your personal infrastructure.
1.  Set up a local Vector Database (like **ChromaDB**).
2.  Write a script to embed your personal `.ssh/config`, `~/.bash_history`, or local dev server IP markdown notes into the database.
3.  When a user asks: "How do I connect to the staging server?", your Python script first queries ChromaDB for the phrase "staging server", retrieves the exact IP address, and invisibly feeds it into the model's prompt.

**Trade-offs at Scale:**
*   **Benefits:** This is the standard for Enterprise AI. It prevents hallucinations because the model is forced to cite the retrieved documents. You can update facts instantly by updating the database (no retraining required).
*   **Downsides:** Complex architecture. "Retrieval" is hard—if the search engine finds the wrong document, the AI will give the wrong answer.

---

## Phase 4: MLOps & Production Serving (The "Enterprise" Phase)

**Goal:** Move from a single-user laptop setup to a robust server architecture capable of handling thousands of requests.

**The Curriculum:**
1.  **Drop Ollama for vLLM:** Ollama is an excellent developer tool, but it processes requests linearly. **vLLM** (or TGI) uses techniques like *Continuous Batching* and *PagedAttention* to process dozens of requests simultaneously on a single GPU.
2.  **API Gateways:** Wrap vLLM in a FastAPI server to handle API keys, rate limiting, and logging.
3.  **Observability:** Implement tools like Langfuse or MLflow to track which prompts fail, how long generation takes, and what users are asking.

**Trade-offs at Scale:**
*   **Benefits:** Unlocks commercial viability. Maximizes GPU utilization (saving money).
*   **Downsides:** High infrastructure costs (renting cloud GPUs like A100s or H100s). Requires deep DevOps and Kubernetes knowledge.