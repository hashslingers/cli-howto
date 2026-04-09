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

## Requirements

- Python 3.7+
- A Google Gemini API key (free tier available)

## Installation

### 1. Clone the repo

```bash
git clone git@github.com:hashslingers/cli-howto.git
cd cli-howto
```

### 2. Install dependencies

```bash
pip3 install google-generativeai rich
```

### 3. Get a Gemini API key

1. Go to [aistudio.google.com/apikey](https://aistudio.google.com/apikey)
2. Click "Create API Key"
3. Copy the key and add it to your shell config:

```bash
echo 'export GEMINI_API_KEY="your_key_here"' >> ~/.zshrc
source ~/.zshrc
```

> **Note:** If you use `bash` instead of `zsh`, replace `~/.zshrc` with `~/.bash_profile`

### 4. Make it available system-wide

```bash
mkdir -p ~/bin
ln -s "$(pwd)/howto" ~/bin/howto
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### 5. Verify installation

```bash
howto --help
```

You should see the help text. Open a new terminal tab and you're ready to go.

## Usage

```bash
howto "<your question>"
```

### Examples

```bash
howto "how do I find large files"
howto "show me how to grep for a pattern"
howto "what's the command to check disk usage"
```

## Troubleshooting

**"command not found: howto"**
- Make sure you ran `source ~/.zshrc` after the install steps
- Or open a new terminal tab

**"GEMINI_API_KEY not set"**
- Verify you added the export to `~/.zshrc` (or `~/.bash_profile` for bash)
- Run `echo $GEMINI_API_KEY` to check it's set
- If still missing, run `source ~/.zshrc` or restart your terminal

**API errors**
- Check your API key is valid at [aistudio.google.com/apikey](https://aistudio.google.com/apikey)
- Ensure you have internet connectivity
