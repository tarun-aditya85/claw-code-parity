# 🧠 Local LLM Setup Guide (Ollama + LiteLLM + Roo)

This guide helps you **start everything from scratch after a restart** and get your **DeepSeek local model working with Roo (unlimited usage setup)**.

---

# ✅ 1. Start Ollama

### Check if Ollama is installed

```bash
ollama --version
```

### Start Ollama server

```bash
ollama serve
```

👉 Keep this running in a terminal

---

# ✅ 2. Verify / Pull Model

### Check installed models

```bash
ollama list
```

### If missing, pull model

```bash
ollama pull deepseek-coder:6.7b
```

---

# ✅ 3. Test Ollama directly

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "deepseek-coder:6.7b",
  "prompt": "Write a python function to add two numbers"
}'
```

👉 If this works, Ollama is healthy

---

# ✅ 4. Start LiteLLM Proxy

### Activate environment (if using conda)

```bash
conda activate litellm
```

### Start proxy

```bash
litellm --config config.yaml --port 4000 --drop_params
```

👉 This does:

* Removes `tools` (fixes Roo issue)
* Converts requests → Ollama compatible

---

# ✅ 5. Verify LiteLLM

```bash
curl http://localhost:4000/v1/models
```

Expected output:

```json
{
  "data": [
    {
      "id": "deepseek-local"
    }
  ]
}
```

---

# ✅ 6. Roo Configuration

Set:

| Field        | Value                                                |
| ------------ | ---------------------------------------------------- |
| API Provider | OpenAI Compatible                                    |
| Base URL     | [http://localhost:4000/v1](http://localhost:4000/v1) |
| API Key      | sk-local                                             |
| Model        | deepseek-local                                       |

---

# ✅ 7. Test in Roo

Try prompt:

> Write a Python function to reverse a linked list

If working:

* Roo → LiteLLM → Ollama → Response ✅

---

# ⚙️ 8. Recommended Settings (Roo)

* Temperature: `0.2`
* Max tokens: `-1`
* Reasoning effort: `LOW`

---

# 🧹 9. Disk Cleanup (important)

To avoid disk full issues:

### Remove unused models

```bash
ollama rm roo-mini
ollama rm roo-agent-3b
ollama rm qwen2.5-coder:3b
```

### Clean cache

```bash
rm -rf ~/.ollama/models/blobs/*
conda clean --all -y
pip cache purge
```

---

# 🚀 10. Optional: Run in background

```bash
nohup ollama serve > ollama.log 2>&1 &
nohup litellm --config config.yaml --port 4000 --drop_params > litellm.log 2>&1 &
```

---

# 🧠 Architecture Overview

```
Roo (Agent UI)
     ↓
LiteLLM (proxy strips tools)
     ↓
Ollama (local LLM)
     ↓
DeepSeek Coder 6.7B
```

---

# ⚠️ Known Limitations

* No real tool calling (intentionally disabled)
* Weaker reasoning vs GPT-4/5
* No browsing or external actions

---

# 💡 Next Steps (Optional)

* Upgrade model → `deepseek-coder:33b`
* Add Redis for caching
* Build custom agent (LangGraph)

---

# ✅ One-command startup (quick workflow)

```bash
# Terminal 1
ollama serve

# Terminal 2
litellm --config config.yaml --port 4000 --drop_params
```

---

You're now running a **fully local, unlimited LLM system** 🚀
