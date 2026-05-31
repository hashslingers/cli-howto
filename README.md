# howto

An AI-powered command line learning utility. Ask how to do anything in the terminal and get a clear, detailed, and formatted answer — so you learn as you work, rather than blindly copying from StackOverflow.

This tool utilizes a "Dual-Brain" architecture: it defaults to a **Local Small Language Model (Gemma 2B)** for speed and privacy, with an optional fallback to a **Cloud LLM (Gemini 2.5 Flash)** for complex reasoning.

## The Mission: Learning via CLI

`howto` is not just a command generator; it's a teacher. By strictly formatting its output to provide the *Primary Command*, relevant *Variations*, and *Descriptions* of what specific flags do, the goal is to build the user's "muscle memory" and fundamental understanding of Unix systems.

### Setting the Baseline for Fine-Tuning
Currently, this project relies on **Prompt Engineering** (Specifically, Few-Shot In-Context Learning) to force the models to output the exact teaching format required. 

This repository serves as a **baseline and playground**. The ultimate goal is to establish this workflow, identify the edge cases where the 2B local model struggles with ambiguity, and use that data to **automate a LoRA fine-tuning pipeline**. By eventually fine-tuning a small model purely on Unix command generation and teaching formats, we aim to make a 2B parameter model punch far above its weight class without relying on heavy prompt context.

---

## Local vs. Cloud Architecture

You can run this tool entirely locally (default), or hit the cloud via an API key (`--remote`). Here is an honest comparison of the two approaches:

### 1. The Default: Local Gemma 2B (via Ollama)
**What it is:** Google's Gemma 2B, running entirely on your machine's CPU/GPU via Ollama.
*   **User Experience:** 100% private. Works offline. Zero latency waiting for a network request. It feels like a native Unix tool.
*   **Hardware Impact:** Moderate. On modern Apple Silicon (M1/M2/M3) or dedicated GPUs, it requires about 2GB-4GB of RAM to load the model into memory. Generation is practically instant. On older Intel hardware, it may spin up your fans and take a few seconds to generate.
*   **The Downside (The Ambiguity Problem):** Small Language Models (SLMs) are highly capable but easily distracted. If you ask a slightly ambiguous question (e.g., "set remote git default"), a 2B model might focus on the word "default" and give you global config commands, ignoring the word "remote". We mitigate this with strict prompt engineering, but it lacks the deep semantic reasoning of a massive cloud model.

### 2. The Fallback: Cloud Gemini 2.5 Flash (`--remote`)
**What it is:** An API call to Google's massive 100B+ parameter models.
*   **User Experience:** Incredibly smart. Handles highly ambiguous or poorly phrased questions perfectly. Understands complex, multi-step pipeline requests.
*   **Hardware Impact:** Zero. Your computer does no heavy lifting.
*   **The Downside:** Requires an internet connection and an API key. Your query leaves your machine. There is a slight network latency (~1-3 seconds) before the answer appears.

---

## Installation

### 1. Requirements
- Python 3.7+
- **For Local:** [Ollama](https://ollama.com/) installed on your machine.
- **For Remote (Optional):** A Google Gemini API key ([Get one here](https://aistudio.google.com/apikey)).

### 2. Clone & Install

```bash
git clone git@github.com:hashslingers/cli-howto.git
cd cli-howto

# Install python dependencies
pip3 install -r requirements.txt

# Make the script executable
chmod +x howto
```

### 3. Setup Local Inference (Ollama)
If you are on macOS and have Homebrew:
```bash
# Install Ollama
brew install ollama
brew services start ollama

# Pull the lightweight Gemma model
ollama pull gemma2:2b
```

### 4. Setup Remote Inference (Optional)
If you want the ability to use the `--remote` flag, add your API key to your shell config (`~/.zshrc` or `~/.bash_profile`):

```bash
echo 'export GEMINI_API_KEY="your_key_here"' >> ~/.zshrc
source ~/.zshrc
```

### 5. Make it System-Wide
```bash
mkdir -p ~/bin
ln -s "$(pwd)/howto" ~/bin/howto
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

---

## Usage

**Default (Local Gemma Model):**
```bash
howto "find all pdf files"
howto "kill process on port 8080"
```

**Remote Fallback (Gemini Cloud API):**
```bash
howto --remote "how do I delete a directory"
howto --remote "what is the command to check disk usage"
```