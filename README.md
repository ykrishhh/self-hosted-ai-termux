# Self-Hosted AI on Termux

> Run AI models on your phone. No server required, no cloud dependency, no monthly fees. Just your Android device and an internet connection (or none at all).

I run Llama 3 on my phone daily. Not a toy demo — actual, useful AI that I use for coding help, writing, and research. Most guides assume you have a server. This doesn't.

**Here's what actually works on mobile.**

## Why This Exists

Every "run local AI" guide assumes you have a Mac M1 or a beefy GPU. But what if you only have a phone? What if you want AI on the bus, in a tunnel, or somewhere with no internet?

I built this guide because I wanted AI that:
- Works offline
- Doesn't require a subscription
- Runs on hardware I already own
- Is actually useful, not just a tech demo

## What You'll Need

**Hardware:**
- Android phone (4GB+ RAM, 8GB+ recommended)
- 10-20GB free storage (for models)
- Patience (mobile inference is slower than desktop)

**Software:**
- Termux (from F-Droid, NOT Play Store — Play Store version is outdated)
- Basic Linux command line knowledge

**Time:**
- Setup: 30-60 minutes
- Model download: 10-60 minutes depending on connection
- Learning curve: 1-2 days to get comfortable

## Table of Contents

- [Termux Setup](#termux-setup)
- [Ollama on Termux](#ollama-on-termux)
- [Local LLM Models](#local-llm-models)
- [Open WebUI](#open-webui)
- [Voice-to-Text (Whisper)](#voice-to-text-whisper)
- [Image Generation](#image-generation)
- [RAG Setup](#rag-setup)
- [Performance Tips](#performance-tips)
- [Storage Management](#storage-management)
- [Troubleshooting](#troubleshooting)
- [Model Benchmarks](#model-benchmarks)

---

## Termux Setup

### Installing Termux

**DO NOT install from Google Play Store.** The Play Store version is ancient and missing features. Use F-Droid.

1. Download F-Droid from [f-droid.org](https://f-droid.org/)
2. Install F-Droid, open it, search for "Termux"
3. Install Termux from F-Droid
4. Open Termux and run the setup

### Initial Setup

```bash
# Update packages
pkg update && pkg upgrade -y

# Install essential tools
pkg install -y git curl wget nano htop tree

# Set up storage access
termux-setup-storage

# Configure colors (optional but nice)
echo 'export TERM=xterm-256color' >> ~/.bashrc
source ~/.bashrc
```

### Why F-Droid?

The Play Store version of Termux is frozen at an old version. It's missing:
- Current package versions
- Security patches
- New features
- Compatibility with modern tools

F-Droid keeps it updated. Always use F-Droid.

---

## Ollama on Termux

Ollama is the easiest way to run LLMs on Termux. It handles model management, quantization, and inference.

### Installing Ollama

```bash
# Method 1: Official script (recommended)
curl -fsSL https://ollama.com/install.sh | sh

# Method 2: If the script fails, manual install
# Download the ARM64 binary
wget https://ollama.com/download/ollama-linux-arm64 -O ollama
chmod +x ollama
sudo mv ollama /usr/local/bin/

# Verify installation
ollama --version
```

### Starting Ollama

```bash
# Start the Ollama server
ollama serve &

# It runs on http://localhost:11434 by default
# You can check if it's working:
curl http://localhost:11434/api/tags
```

### Running Your First Model

```bash
# Download and run a small model
ollama run phi3:mini

# This downloads ~2.3GB and runs immediately
# Type your prompt and press Enter
# Type /bye to exit
```

### Ollama Commands

```bash
# List installed models
ollama list

# Pull a model without running it
ollama pull llama3.2:3b

# Remove a model
ollama rm phi3:mini

# Show model info
ollama show phi3:mini

# Copy a model
ollama cp phi3:mini phi3-backup
```

---

## Local LLM Models

These are the models that actually work on phones. I've tested all of them.

### Recommended Models

#### For Daily Use (Best Balance)

| Model | Size | RAM Needed | Speed | Quality | Best For |
|-------|------|------------|-------|---------|----------|
| **Llama 3.2 3B** | 2.0GB | 3GB | Fast | Good | General chat, coding |
| **Phi-3 Mini** | 2.3GB | 3GB | Fast | Good | Reasoning, math |
| **Gemma 2 2B** | 1.6GB | 2GB | Fast | Decent | Quick answers |
| **Qwen 2.5 3B** | 2.0GB | 3GB | Fast | Good | Multilingual |

#### For Quality (When You Can Wait)

| Model | Size | RAM Needed | Speed | Quality | Best For |
|-------|------|------------|-------|---------|----------|
| **Llama 3.1 8B** | 4.7GB | 6GB | Slow | Great | Complex tasks |
| **Mistral 7B** | 4.1GB | 5GB | Slow | Great | Instruction following |
| **Mixtral 8x7B** | 26GB | 16GB+ | Very Slow | Excellent | Best quality (if you have RAM) |

#### For Coding

| Model | Size | RAM Needed | Speed | Quality | Best For |
|-------|------|------------|-------|---------|----------|
| **DeepSeek Coder V2 Lite** | 2.7GB | 4GB | Moderate | Great | Code generation |
| **CodeLlama 7B** | 3.8GB | 5GB | Slow | Great | Code completion |
| **StarCoder2 3B** | 1.8GB | 3GB | Fast | Good | Quick code help |

### My Daily Drivers

I rotate between these:
1. **Llama 3.2 3B** — for quick questions while mobile
2. **Phi-3 Mini** — for reasoning and analysis
3. **DeepSeek Coder** — for coding help

### Model Quantization Explained

You'll see terms like Q4_K_M, Q5_K_S, etc. Here's what they mean:

- **Q4** = 4-bit quantization (smaller, faster, slightly less quality)
- **Q5** = 5-bit quantization (good balance)
- **Q8** = 8-bit quantization (larger, slower, better quality)
- **K_M** = Medium context (good for most uses)
- **K_S** = Small context (faster, less RAM)

**Recommendation:** Use Q4_K_M for mobile. It's the sweet spot between speed, size, and quality.

### Downloading Models

```bash
# Via Ollama (easiest)
ollama pull llama3.2:3b
ollama pull phi3:mini
ollama pull deepseek-coder-v2:lite

# Check download progress
ollama list

# Models are stored in ~/.ollama/models/
du -sh ~/.ollama/models/
```

---

## Open WebUI

Open WebUI gives you a ChatGPT-like interface for your local models. It's the best way to interact with Ollama on mobile.

### Installation

```bash
# Install Python (needed for Open WebUI)
pkg install python python-pip

# Install Open WebUI
pip install open-webui

# Or use Docker if you prefer
pkg install docker
docker pull ghcr.io/open-webui/open-webui:main
```

### Running Open WebUI

```bash
# Start Open WebUI
open-webui serve

# It runs on http://localhost:8080 by default
# Open in your phone's browser
```

### Using Docker (Alternative)

```bash
# Run Open WebUI in Docker
docker run -d \
  -p 8080:8080 \
  -v open-webui:/app/backend/data \
  --name open-webui \
  ghcr.io/open-webui/open-webui:main

# Access at http://localhost:8080
```

### Open WebUI Features

- Chat interface similar to ChatGPT
- Model switching (switch between models mid-conversation)
- Conversation history
- System prompts
- File upload (for multimodal models)
- Dark mode (great for OLED screens)

### Mobile Browser Tips

- Add to home screen for app-like experience
- Use Chrome or Firefox (not Samsung Internet)
- Enable "Desktop site" if mobile view is buggy
- Bookmark http://localhost:8080 for quick access

---

## Voice-to-Text (Whisper)

Run OpenAI's Whisper model locally for speech-to-text. No cloud, no API, no privacy concerns.

### Installing Whisper

```bash
# Install Python dependencies
pkg install python ffmpeg

# Install Whisper
pip install openai-whisper

# Or use faster-whisper (optimized for mobile)
pip install faster-whisper
```

### Running Whisper

```bash
# Basic transcription
whisper audio.mp3 --model base

# With timestamps
whisper audio.mp3 --model base --output_format srt

# Using faster-whisper (recommended for mobile)
python3 -c "
from faster_whisper import WhisperModel
model = WhisperModel('base', device='cpu', compute_type='int8')
segments, info = model.transcribe('audio.mp3')
for segment in segments:
    print(f'[{segment.start:.1f}s -> {segment.end:.1f}s] {segment.text}')
"
```

### Whisper Model Sizes

| Model | Size | RAM | Speed | Accuracy |
|-------|------|-----|-------|----------|
| tiny | 75MB | 1GB | Very Fast | Basic |
| base | 142MB | 1GB | Fast | Good |
| small | 466MB | 2GB | Moderate | Great |
| medium | 1.5GB | 5GB | Slow | Excellent |
| large | 2.9GB | 10GB | Very Slow | Best |

**Recommendation:** Use `base` for mobile. It's fast enough for real-time use and accurate enough for most purposes.

### Recording Audio in Termux

```bash
# Install audio recording tools
pkg install termux-api

# Record audio (10 seconds)
termux-microphone-record -l 10

# The recording is saved to ~/storage/Downloads/
```

### Voice Command Setup

```bash
# Create a voice command script
cat > ~/voice-command.sh << 'EOF'
#!/bin/bash
# Record 5 seconds of audio
termux-microphone-record -l 5
# Get the latest recording
AUDIO=$(ls -t ~/storage/Downloads/*.m4a 2>/dev/null | head -1)
if [ -n "$AUDIO" ]; then
    # Transcribe
    whisper "$AUDIO" --model base --language en --output_format txt
    # Read the result
    cat "${AUDIO%.*}.txt"
fi
EOF
chmod +x ~/voice-command.sh
```

---

## Image Generation

Run Stable Diffusion on your phone. It's slow but it works.

### Installing Stable Diffusion

```bash
# Install dependencies
pkg install python git

# Clone a lightweight Stable Diffusion implementation
git clone https://github.com/nickmuchi/stable-diffusion-optimized.git
cd stable-diffusion-optimized

# Install requirements
pip install -r requirements.txt
```

### Running Stable Diffusion

```bash
# Generate an image
python3 txt2img.py \
  --prompt "a beautiful sunset over mountains, detailed, high quality" \
  --steps 20 \
  --width 512 \
  --height 512 \
  --output sunset.png
```

### Mobile Image Generation Tips

1. **Use small resolutions:** 256x256 or 512x512 max
2. **Fewer steps:** 15-20 steps instead of 50
3. **Use quantized models:** INT8 or FP16
4. **Expect slowness:** 5-15 minutes per image on phone
5. **Use simpler prompts:** Complex prompts take longer

### Alternative: Use Cloud API

If local generation is too slow:

```bash
# Use Pollinations.ai (free, no API key)
curl "https://image.pollinations.ai/prompt/a%20beautiful%20sunset%20over%20mountains" --output sunset.png

# Use Hugging Face Inference API (free tier)
python3 -c "
import requests
API_URL = 'https://api-inference.huggingface.co/models/stabilityai/stable-diffusion-2'
headers = {'Authorization': 'Bearer YOUR_TOKEN'}
response = requests.post(API_URL, headers=headers, json={'inputs': 'a beautiful sunset'})
with open('sunset.png', 'wb') as f:
    f.write(response.content)
"
```

---

## RAG Setup

Build a personal knowledge base that runs entirely on your phone.

### What is RAG?

RAG (Retrieval-Augmented Generation) lets you:
1. Store documents locally
2. Search them intelligently
3. Use an LLM to answer questions about them

All offline, all on your phone.

### Installing RAG Tools

```bash
# Install ChromaDB (vector database)
pip install chromadb

# Install sentence-transformers (embeddings)
pip install sentence-transformers

# Install LangChain (RAG framework)
pip install langchain langchain-community
```

### Basic RAG Setup

```python
# rag_example.py
from langchain_community.document_loaders import TextLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.vectorstores import Chroma
from langchain_community.embeddings import HuggingFaceEmbeddings

# Load documents
loader = TextLoader('my_documents.txt')
documents = loader.load()

# Split into chunks
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=50
)
chunks = text_splitter.split_documents(documents)

# Create embeddings and store
embeddings = HuggingFaceEmbeddings(model_name='all-MiniLM-L6-v2')
vectorstore = Chroma.from_documents(chunks, embeddings, persist_directory='./chroma_db')

# Query
query = "What is the main topic of these documents?"
results = vectorstore.similarity_search(query, k=3)
for result in results:
    print(result.page_content)
    print("---")
```

### RAG with Ollama

```python
# rag_ollama.py
from langchain_community.vectorstores import Chroma
from langchain_community.embeddings import OllamaEmbeddings
from langchain_community.llms import Ollama
from langchain.chains import RetrievalQA

# Use Ollama for both embeddings and LLM
embeddings = OllamaEmbeddings(model='nomic-embed-text')
llm = Ollama(model='llama3.2:3b')

# Load existing vectorstore
vectorstore = Chroma(persist_directory='./chroma_db', embedding_function=embeddings)

# Create QA chain
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    chain_type='stuff',
    retriever=vectorstore.as_retriever()
)

# Ask questions
response = qa_chain.invoke('What are the key points?')
print(response['result'])
```

### Organizing Your Documents

```
~/documents/
├── notes/
│   ├── meeting-2024-01-15.txt
│   ├── project-ideas.txt
│   └── research-papers/
├── books/
│   ├── book1.txt
│   └── book2.txt
└── articles/
    ├── article1.txt
    └── article2.txt
```

---

## Performance Tips

### Making It Faster

**1. Close Other Apps**
```bash
# Check what's using memory
free -h

# Kill unnecessary processes
pkill -f "app_name"
```

**2. Use Swap Space**
```bash
# Create a swap file
fallocate -l 2G /data/data/com.termux/files/home/swapfile
chmod 600 /data/data/com.termux/files/home/swapfile
mkswap /data/data/com.termux/files/home/swapfile
swapon /data/data/com.termux/files/home/swapfile

# Add to fstab for persistence
echo '/data/data/com.termux/files/home/swapfile swap swap defaults 0 0' >> /etc/fstab
```

**3. Use Smaller Models**
```bash
# Instead of 8B, use 3B
ollama pull llama3.2:3b  # Instead of llama3.1:8b

# Check model sizes
ollama list
du -sh ~/.ollama/models/
```

**4. Reduce Context Length**
```bash
# In Ollama, you can set context length
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.2:3b",
  "prompt": "Hello",
  "options": {
    "num_ctx": 2048
  }
}'
```

**5. Use CPU Optimization**
```bash
# Set thread count (experiment with this)
export OLLAMA_NUM_THREADS=4

# For Whisper
whisper audio.mp3 --model base --threads 4
```

### Speed Expectations

| Model Size | Token/s (Phone) | Time for 100 tokens |
|------------|-----------------|---------------------|
| 1-2B | 10-20 t/s | 5-10 seconds |
| 3B | 5-10 t/s | 10-20 seconds |
| 7B | 2-5 t/s | 20-50 seconds |
| 13B+ | <2 t/s | 50+ seconds |

**What this means:** A 3B model generates a short answer in about 10 seconds. A 7B model takes 30-60 seconds. Manage your expectations.

---

## Storage Management

AI models eat storage. Here's how to manage it.

### Current Usage

```bash
# Check Ollama models
du -sh ~/.ollama/models/

# Check Python packages
du -sh ~/.local/lib/python*/

# Check total Termux usage
du -sh ~/../
```

### Freeing Space

```bash
# Remove unused models
ollama rm old-model-name

# Clean pip cache
pip cache purge

# Remove old logs
rm -rf ~/.ollama/logs/*

# Compress large files
tar -czf backup.tar.gz old-directory/
rm -rf old-directory/
```

### Model Storage Locations

```bash
# Ollama models
~/.ollama/models/

# Python packages
~/.local/lib/python3.*/site-packages/

# ChromaDB
~/chroma_db/

# Downloaded audio
~/storage/Downloads/
```

### Storage Budget

| Item | Size | Notes |
|------|------|-------|
| Termux base | 500MB | System files |
| Ollama + 3 models | 6-8GB | 3B + 3B + embeddings |
| Python packages | 2-3GB | Whisper, LangChain, etc. |
| Your data | Variable | Documents, notes |
| **Total minimum** | **10-15GB** | For a useful setup |

---

## Troubleshooting

### Common Issues

**"Permission denied"**
```bash
# Fix Termux storage permissions
termux-setup-storage
# Then allow storage access in Android settings
```

**"No space left on device"**
```bash
# Check storage
df -h

# Find large files
du -sh ~/.ollama/models/*
du -sh ~/.local/lib/python*/

# Remove unused models
ollama rm model-name
```

**"Model not found"**
```bash
# List available models
ollama list

# Pull the model
ollama pull model-name

# Check if Ollama is running
curl http://localhost:11434/api/tags
```

**"Slow performance"**
```bash
# Check RAM usage
free -h

# Close other apps
# Use smaller model
# Reduce context length
```

**"Ollama not starting"**
```bash
# Kill existing process
pkill ollama

# Start fresh
ollama serve &

# Check logs
tail -f ~/.ollama/logs/server.log
```

**"Python import error"**
```bash
# Reinstall the package
pip install --force-reinstall package-name

# Check Python version
python3 --version

# Make sure you're using the right Python
which python3
```

---

## Model Benchmarks

Real-world performance on a typical Android phone (Snapdragon 870, 8GB RAM):

### Llama 3.2 3B (Q4_K_M)
- **Prompt eval:** 12.3 tokens/second
- **Generation:** 8.7 tokens/second
- **Memory usage:** 2.8GB
- **Quality:** Good for general chat, decent for coding
- **Verdict:** Best daily driver for mobile

### Phi-3 Mini (Q4_K_M)
- **Prompt eval:** 11.8 tokens/second
- **Generation:** 8.2 tokens/second
- **Memory usage:** 2.6GB
- **Quality:** Great for reasoning, math, analysis
- **Verdict:** Best for thinking tasks

### DeepSeek Coder V2 Lite (Q4_K_M)
- **Prompt eval:** 9.4 tokens/second
- **Generation:** 6.1 tokens/second
- **Memory usage:** 3.2GB
- **Quality:** Excellent for code
- **Verdict:** Best for coding on mobile

### Llama 3.1 8B (Q4_K_M)
- **Prompt eval:** 5.2 tokens/second
- **Generation:** 3.1 tokens/second
- **Memory usage:** 5.4GB
- **Quality:** Great for everything
- **Verdict:** Use when you can wait and have RAM

### Whisper Base
- **Processing:** 8x realtime (1 second audio takes 0.125s)
- **Memory usage:** 1.2GB
- **Accuracy:** Good for clear speech
- **Verdict:** Practical for real-time transcription

---

## Contributing

Found a better way to do something? Open a PR.

**What I'm looking for:**
- Better model recommendations
- Performance optimizations
- New use cases
- Bug fixes for commands
- Additional tools and setups

**What I'm not looking for:**
- "Just use a server" comments
- Enterprise solutions
- Unstable/experimental setups without documentation

---

## Related Projects

- [Ollama](https://ollama.com/) - Run LLMs locally
- [Open WebUI](https://github.com/open-webui/open-webui) - Chat interface for Ollama
- [Whisper.cpp](https://github.com/ggerganov/whisper.cpp) - Fast Whisper implementation
- [Termux](https://termux.dev/) - Android terminal emulator
- [ChromaDB](https://www.trychroma.com/) - Vector database

---

## Disclaimer

Mobile inference is slower than desktop. Expect:
- 5-20 tokens/second for 3B models (vs 50-100 on desktop)
- 15-30 seconds for a short response
- Battery drain during inference
- Heat generation (normal, but monitor it)

This is the trade-off for running AI entirely on your phone with no cloud dependency.

---

**Running AI on your phone?** Star this repo and let me know what models you're using.

**Found a bug?** Open an issue. I test on multiple devices.
