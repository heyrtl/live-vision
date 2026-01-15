# LiveVision Complete Guide

Comprehensive documentation for LiveVision - real-time AI vision with your webcam.

## Table of Contents

- [Installation Details](#installation-details)
- [Performance Guide](#performance-guide)
- [Advanced Usage](#advanced-usage)
- [Troubleshooting](#troubleshooting)
- [FAQ](#faq)
- [Technical Details](#technical-details)

---

## Installation Details

### Mac (Detailed)

1. **Download llama.cpp:**
   - Go to [llama.cpp releases](https://github.com/ggerganov/llama.cpp/releases)
   - Download `llama-XXXXX-bin-macos-arm64.tar.gz` (M1/M2/M3)
   - Or `llama-XXXXX-bin-macos-x64.tar.gz` (Intel Mac)

2. **Extract and install:**
   ```bash
   cd ~/Downloads
   tar -xzf llama-*-bin-macos-*.tar.gz
   mkdir -p ~/tools
   mv llama-b* ~/tools/llama.cpp
   ```

3. **Add to PATH:**
   ```bash
   echo 'export PATH="$HOME/tools/llama.cpp:$PATH"' >> ~/.zshrc
   source ~/.zshrc
   ```

4. **Remove security warning:**
   ```bash
   xattr -dr com.apple.quarantine ~/tools/llama.cpp/
   ```

5. **Verify:**
   ```bash
   llama-server --version
   ```

### Windows (Detailed)

1. **Download llama.cpp:**
   - Go to [llama.cpp releases](https://github.com/ggerganov/llama.cpp/releases)
   - Download `llama-XXXXX-bin-win-cuda-cuXX.X-x64.zip` (NVIDIA GPU)
   - Or `llama-XXXXX-bin-win-noavx-x64.zip` (CPU only)

2. **Extract:**
   - Extract to `C:\Program Files\llama.cpp\`

3. **Add to PATH:**
   - Press Win+R, type `sysdm.cpl`, press Enter
   - Go to "Advanced" tab → "Environment Variables"
   - Under "System variables", find "Path", click "Edit"
   - Click "New", add `C:\Program Files\llama.cpp`
   - Click OK on all dialogs

4. **Verify:**
   - Open new Command Prompt or PowerShell
   ```cmd
   llama-server --version
   ```

### Linux

```bash
# Download from releases or build from source
wget https://github.com/ggerganov/llama.cpp/releases/download/XXX/llama-XXX-bin-linux-x64.tar.gz
tar -xzf llama-*-bin-linux-*.tar.gz
sudo mv llama-b* /usr/local/bin/llama.cpp
echo 'export PATH="/usr/local/bin/llama.cpp:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

---

## Performance Guide

### Expected Performance by Hardware

**Mac M1/M2/M3 (8GB+ RAM):**
- SmolVLM-500M: ~100-200ms per frame ✅ Smooth real-time
- SmolVLM-2.2B: ~300-500ms per frame ✅ Real-time capable
- Metal acceleration automatic

**Windows NVIDIA RTX 3060/3070/3080:**
- SmolVLM-500M: ~50-100ms per frame ⚡ Very fast
- SmolVLM-2.2B: ~150-300ms per frame ✅ Smooth real-time
- CUDA acceleration automatic

**Windows NVIDIA RTX 4060/4070/4090:**
- SmolVLM-500M: ~30-70ms per frame ⚡⚡ Blazing fast
- SmolVLM-2.2B: ~100-200ms per frame ⚡ Very smooth

**AMD GPU (ROCm):**
- Performance similar to NVIDIA with proper drivers
- May require manual ROCm setup

**CPU Only (Modern i7/Ryzen 7):**
- SmolVLM-500M: ~1-3 seconds per frame 🐢 Usable but slow
- SmolVLM-2.2B: ~5-10 seconds per frame ❌ Too slow for real-time

### Model Selection

| Model | Download Size | VRAM Usage | Speed | Quality | Recommended For |
|-------|---------------|------------|-------|---------|-----------------|
| SmolVLM-500M-Q4 | ~300MB | ~600MB | ⚡⚡⚡ | ⭐⭐⭐ | Real-time webcam (default) |
| SmolVLM-500M-Q6 | ~400MB | ~700MB | ⚡⚡ | ⭐⭐⭐⭐ | Better quality, still fast |
| SmolVLM-2.2B-Q4 | ~1.5GB | ~2.5GB | ⚡ | ⭐⭐⭐⭐⭐ | Best quality, slower |

**Quantization explained:**
- **Q4** (4-bit): Best speed/quality balance, 99% of people should use this
- **Q5/Q6**: Marginally better quality, minimal difference for webcam use
- **Q8/F16**: Overkill for real-time, not recommended

### Optimizing Performance

**For maximum speed:**
```bash
# Use Q4 quantization (default)
llama-server -hf ggml-org/SmolVLM-500M-Instruct-GGUF

# Set update interval to 500ms-1s in the app
```

**For better quality:**
```bash
# Use 2.2B model with Q4 quantization
llama-server -hf ggml-org/SmolVLM-Instruct-GGUF

# Set update interval to 1-2s for smoother experience
```

**For battery life (laptops):**
- Use 500M model
- Increase interval to 2-5 seconds
- Stop processing when not needed
- Close server when done

---

## Advanced Usage

### JSON Output Examples

**Basic object detection:**
```
Return JSON: {"objects": ["list of objects"], "count": number}
```

Response:
```json
{"objects": ["laptop", "mouse", "coffee mug", "notebook"], "count": 4}
```

**Scene analysis:**
```
Return JSON: {"scene": "description", "lighting": "bright/dim/dark", "people_count": number, "objects": ["list"]}
```

**OCR (text extraction):**
```
Read all visible text and return JSON: {"text": "extracted text", "language": "detected language"}
```

**Color detection:**
```
Return JSON: {"dominant_colors": ["color1", "color2"], "objects_with_colors": [{"object": "name", "color": "color"}]}
```

### Custom Update Intervals

Edit the code for non-standard intervals:

```javascript
// In index.html, find intervalSelect and add:
<option value="100">100ms (Very Fast)</option>
<option value="3000">3s (Battery Saver)</option>
<option value="10000">10s (Slow Motion)</option>
```

### Running Multiple Models

```bash
# Terminal 1: 500M on default port
llama-server -hf ggml-org/SmolVLM-500M-Instruct-GGUF

# Terminal 2: 2.2B on port 8081
llama-server -hf ggml-org/SmolVLM-Instruct-GGUF --port 8081
```

In the app, change the API URL to switch between them.

### Remote Access

```bash
# Allow access from other devices on your network
llama-server -hf ggml-org/SmolVLM-500M-Instruct-GGUF --host 0.0.0.0

# Then access from phone/tablet using your computer's IP:
# http://192.168.1.XXX:8080
```

Update the API URL in LiveVision to your computer's local IP.

---

## Troubleshooting

### Camera Issues

**"Camera not working" or black screen:**
1. Check browser permissions (lock icon in address bar)
2. Ensure no other app is using camera (Zoom, Teams, etc.)
3. On Mac: System Settings > Privacy & Security > Camera
4. On Windows: Settings > Privacy > Camera
5. Try different browser (Chrome usually works best)
6. Restart browser completely

**"Permission denied" error:**
- You must grant camera access when prompted
- If you denied it, click the lock icon in address bar to change
- On Firefox: about:preferences#privacy > Permissions > Camera

### Server Connection Issues

**"Failed to fetch" or connection errors:**

1. **Check server is running:**
   ```bash
   curl http://localhost:8080/health
   # Should return: {"status":"ok"}
   ```

2. **Verify URL in app matches server:**
   - Default: `http://localhost:8080`
   - Check server output for actual port

3. **Firewall blocking:**
   - Windows: Allow llama-server through Windows Defender
   - Mac: System Settings > Network > Firewall > Options
   - Add llama-server to allowed apps

4. **Port already in use:**
   ```bash
   # Use different port
   llama-server -hf ggml-org/SmolVLM-500M-Instruct-GGUF --port 8081
   # Update URL in app to localhost:8081
   ```

### Performance Issues

**Slow/laggy processing:**
1. Check GPU is being used (server output shows Metal/CUDA)
2. Switch to 500M model if using 2.2B
3. Increase update interval to 1-2 seconds
4. Close other heavy applications
5. Check system resources (Activity Monitor/Task Manager)

**High CPU usage:**
- Normal if no GPU available
- Consider getting a GPU or using longer intervals

**Out of memory errors:**
```bash
# Use smaller model
llama-server -hf ggml-org/SmolVLM-500M-Instruct-GGUF

# Or reduce context size
llama-server -hf ggml-org/SmolVLM-500M-Instruct-GGUF --ctx-size 1024
```

### Installation Issues

**"llama-server: command not found" (Mac/Linux):**
```bash
# Check PATH
echo $PATH | grep llama

# Re-add to PATH
echo 'export PATH="$HOME/tools/llama.cpp:$PATH"' >> ~/.zshrc
source ~/.zshrc

# Or use full path
~/tools/llama.cpp/llama-server -hf ggml-org/SmolVLM-500M-Instruct-GGUF
```

**"Apple could not verify" (Mac):**
```bash
xattr -dr com.apple.quarantine ~/tools/llama.cpp/
```

**Model download stuck:**
- Download is from HuggingFace, can be slow
- Cancel (Ctrl+C) and restart if stuck
- Check internet connection
- Try different network if university/corporate firewall blocks HF

### Response Quality Issues

**Nonsense or incorrect responses:**
1. Improve lighting in room
2. Position objects clearly in frame
3. Be more specific in instruction
4. Try 2.2B model for complex scenes
5. Adjust camera angle/distance

**"I cannot see" or "No image provided":**
- Might be a model loading issue
- Restart server
- Verify image capture is working (check browser console with F12)

---

## FAQ

**Q: Do I need internet after initial setup?**  
A: No. Only for first-time model download. After that, 100% offline.

**Q: Is my webcam data sent anywhere?**  
A: Absolutely not. Everything processes locally. Zero data leaves your machine.

**Q: Can I use this commercially?**  
A: Yes. MIT license (this project) + Apache 2.0 (SmolVLM) = commercial use allowed.

**Q: Mobile support?**  
A: HTML works on mobile, but you need llama.cpp server running on a computer. Could access remotely though.

**Q: Multiple cameras?**  
A: Edit the code to select specific camera. Check browser's `getUserMedia` API docs.

**Q: How does this compare to GPT-4 Vision?**  
A: Less accurate, but runs locally, no cost, no rate limits, privacy-first. Great for prototyping or offline use.

**Q: Can I fine-tune the model?**  
A: Yes, but beyond this project. Check llama.cpp and HuggingFace docs for training.

**Q: Why not just use Python?**  
A: llama.cpp is faster, uses less RAM, single binary, no dependency hell. Perfect for this use case.

**Q: Battery impact?**  
A: Moderate. GPU usage increases battery drain. Use longer intervals or stop when not needed.

**Q: Can this identify specific people?**  
A: No. It describes people but doesn't do facial recognition. By design.

**Q: Ollama vs llama.cpp?**  
A: Both work. llama.cpp gives more control over quantizations and models. Ollama is simpler. Pick your preference.

---

## Technical Details

### Architecture

**Frontend:**
- Single HTML file with vanilla JavaScript
- No frameworks, no build process
- Uses `getUserMedia()` for webcam access
- Canvas API for frame capture
- Fetch API for server communication

**Backend:**
- llama.cpp server (C++ with Metal/CUDA support)
- OpenAI-compatible API endpoint
- Loads SmolVLM GGUF quantized models
- Handles vision + text processing

**Data Flow:**
1. Browser captures frame via `getUserMedia()`
2. Draw to canvas, convert to JPEG base64
3. POST to `/v1/chat/completions` with image + text
4. llama.cpp processes with SmolVLM
5. Streams response back to browser
6. Display in overlay on video feed

### Why This Stack?

- **No dependencies:** Works anywhere with a browser
- **Privacy-first:** Local processing only
- **Cross-platform:** Mac/Windows/Linux identical setup
- **Fast enough:** Real-time with GPU
- **Simple deployment:** 2 files total

### Model Details

**SmolVLM** is a vision-language model by HuggingFace:
- 500M and 2.2B parameter variants
- Trained on vision-text pairs
- Understands images + natural language
- Apache 2.0 licensed

**GGUF** is a quantized format:
- Reduces model size and memory usage
- Enables GPU acceleration
- Created by Georgi Gerganov (llama.cpp author)

### API Compatibility

LiveVision uses OpenAI's API format:
```javascript
POST /v1/chat/completions
{
  "messages": [{
    "role": "user",
    "content": [
      {"type": "text", "text": "instruction"},
      {"type": "image_url", "image_url": {"url": "data:image/jpeg;base64,..."}}
    ]
  }],
  "max_tokens": 100
}
```

This means you can swap backends (OpenAI API, other llama.cpp servers) with minimal code changes.

---

## Use Cases

### Accessibility
- Describe surroundings for visually impaired
- Read signs, menus, labels aloud
- Navigate unfamiliar environments
- Identify objects and obstacles

### Education
- Real-time science experiment documentation
- Object recognition teaching tool
- Language learning (describe in target language)
- Interactive demonstrations

### Development
- Prototype CV features before cloud deployment
- Test AI models locally
- Build offline-first applications
- Create privacy-focused tools

### Automation
- Monitor manufacturing for defects
- Automate data entry from documents
- Track inventory with JSON outputs
- Quality control systems

### Creative
- Generate alt text for images
- Interactive art installations
- AI-powered games and experiences
- Real-time video analysis

### Security/Monitoring
- Private security cameras
- Describe visitors without cloud
- Monitor spaces locally
- No privacy concerns from cloud services

---

## Contributing

Contributions welcome! Here's how:

**Report bugs:**
- Open GitHub issue with detailed description
- Include OS, hardware, model used
- Share terminal and browser console errors

**Suggest features:**
- Open GitHub issue with use case
- Explain why it would be useful
- Consider if it fits project scope (simplicity)

**Submit PRs:**
```bash
git clone https://github.com/yourusername/livevision.git
cd livevision
# Make changes
git commit -m "Description"
git push
# Open PR on GitHub
```

**Improve docs:**
- Fix typos, clarify instructions
- Add examples or troubleshooting tips
- Translate to other languages

---

## Resources

**Official Documentation:**
- [llama.cpp GitHub](https://github.com/ggerganov/llama.cpp)
- [llama.cpp server docs](https://github.com/ggerganov/llama.cpp/blob/master/examples/server/README.md)
- [SmolVLM on HuggingFace](https://huggingface.co/HuggingFaceTB/SmolVLM-Instruct)
- [GGUF format spec](https://github.com/ggerganov/ggml/blob/master/docs/gguf.md)

**Community:**
- [llama.cpp Discord](https://discord.gg/llamacpp)
- [r/LocalLLaMA](https://reddit.com/r/LocalLLaMA)

**Similar Projects:**
- [llava-webui](https://github.com/pkunz96/llava-webui)
- [ollama](https://github.com/ollama/ollama)

---

## Command Reference

```bash
# Basic startup
llama-server -hf ggml-org/SmolVLM-500M-Instruct-GGUF

# Use 2.2B model
llama-server -hf ggml-org/SmolVLM-Instruct-GGUF

# Specific quantization
llama-server -hf ggml-org/SmolVLM-500M-Instruct-GGUF \
  --file SmolVLM-500M-Instruct-Q6_K.gguf

# Different port
llama-server -hf ggml-org/SmolVLM-500M-Instruct-GGUF --port 8081

# Allow remote access
llama-server -hf ggml-org/SmolVLM-500M-Instruct-GGUF --host 0.0.0.0

# Custom context size (reduce memory usage)
llama-server -hf ggml-org/SmolVLM-500M-Instruct-GGUF --ctx-size 1024

# Check server health
curl http://localhost:8080/health

# List available models (cached)
ls ~/.cache/llama.cpp/
```

---

Made with ❤️ for the open-source AI community.

**If this helped you, consider:**
- ⭐ Star the repo
- 🐦 Share it
- 🤝 Contribute
- 💬 Give feedback

Happy hacking! 🎥✨