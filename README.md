# howto

An LLM-powered command line learning tool. Ask it how to do anything in the terminal and get a clear, detailed answer — so you learn as you work.

Powered by Google Gemini 2.5 Flash.

## Example

```bash
howto "how do I delete a directory"
howto "move a file to another folder"
howto "find all pdf files on my computer"
howto "check what is using port 8080"
```

## Installation

### 1. Clone the repo

```bash
git clone git@github.com:hashslingers/howto.git
cd howto
```

### 2. Install dependencies

```bash
pip3 install google-generativeai rich
```

### 3. Get a Gemini API key

Go to [aistudio.google.com/apikey](https://aistudio.google.com/apikey) and create a key, then add it to your shell config:

```bash
echo 'export GEMINI_API_KEY="your_key_here"' >> ~/.zshrc
source ~/.zshrc
```

### 4. Make it available system-wide

```bash
mkdir -p ~/bin
ln -s "$(pwd)/howto" ~/bin/howto
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

## Usage

```bash
howto "<your question>"
```

Open a new terminal tab after setup and you're good to go.
