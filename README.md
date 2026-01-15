# LiveVision

Real-time AI vision understanding using your webcam with SmolVLM and llama.cpp. Everything runs locally - no cloud, no API keys.

## TL;DR (For the Pros)

```bash
# Install llama.cpp from releases, add to PATH
# https://github.com/ggerganov/llama.cpp/releases

# Start server
llama-server -hf ggml-org/SmolVLM-500M-Instruct-GGUF

# Open index.html in browser, click Start
```

That's it. Model auto-downloads (~300MB), runs on GPU automatically.

---

## Setup

**1. Install llama.cpp**

Download from [releases](https://github.com/ggerganov/llama.cpp/releases), extract, add to PATH.

**Mac:**
```bash
cd ~/Downloads
tar -xzf llama-*-bin-macos-*.tar.gz
mkdir -p ~/tools && mv llama-b* ~/tools/llama.cpp
echo 'export PATH="$HOME/tools/llama.cpp:$PATH"' >> ~/.zshrc && source ~/.zshrc
xattr -dr com.apple.quarantine ~/tools/llama.cpp/
```

**Windows:** Extract to `C:\Program Files\llama.cpp\`, add to PATH via Environment Variables.

**2. Start the server**

```bash
llama-server -hf ggml-org/SmolVLM-500M-Instruct-GGUF
```

First run downloads the model (~300MB). Keep terminal open.

**3. Open LiveVision**

```bash
open index.html  # or just double-click it
```

Grant camera permissions, click "Start Processing".

---

## Usage

- **Instruction field:** `What do you see?`, `Count fingers`, `Read text`, etc.
- **Update interval:** 500ms recommended (balance of speed/smoothness)
- **JSON output:** Just ask for it: `Return JSON with {objects, colors, count}`

---

## Models

- **SmolVLM-500M** (default): Fast, real-time capable, ~300MB
- **SmolVLM-2.2B**: Better quality, slower, ~1.5GB
  ```bash
  llama-server -hf ggml-org/SmolVLM-Instruct-GGUF
  ```

---

## Troubleshooting

**Camera not working?** Grant permissions, close other apps using camera  
**Server error?** Check llama-server is running on `localhost:8080`  
**Slow?** Use 500M model, increase interval to 1-2s  
**Mac quarantine error?** Run `xattr -dr com.apple.quarantine ~/tools/llama.cpp/`

More help: See [GUIDE.md](GUIDE.md) for detailed setup, performance tips, and troubleshooting.

---

## What This Is

Point your webcam at anything, get real-time AI descriptions. Runs 100% locally on your machine. Perfect for:
- Accessibility (describe surroundings)
- Object detection and counting
- OCR (read text from camera)
- Automation (JSON outputs)
- Privacy-focused vision AI

Built with [llama.cpp](https://github.com/ggerganov/llama.cpp) and [SmolVLM](https://huggingface.co/HuggingFaceTB/SmolVLM-Instruct).

---

**Performance:** M1/M2 Mac or RTX GPU = smooth real-time. CPU-only = usable but slower.

**License:** MIT (SmolVLM is Apache 2.0)

**Issues?** Check [GUIDE.md](GUIDE.md) or open an issue.