<div align="center">
  <img src="https://raw.githubusercontent.com/daffaanan11/memoria/main/logo.jpg" alt="Memoria Logo" width="120" height="120" style="border-radius: 20px;">
  <h1>Memoria Gateway</h1>
  <p><strong>The High-Performance Semantic Caching Proxy for LLMs</strong></p>

  [![License](https://img.shields.io/badge/License-Proprietary-blue.svg)](https://memoria-proxy.vercel.app)
  [![Version](https://img.shields.io/badge/Version-1.0.0-success.svg)](#)
  [![Platform](https://img.shields.io/badge/Platform-Linux%20AMD64-lightgrey.svg)](#)
  [![C++](https://img.shields.io/badge/Built_with-C++%20%7C%20Go-00599C.svg)](#)

  <p>
    <a href="https://memoria-proxy.vercel.app">Website</a> •
    <a href="https://memoria-proxy.vercel.app/docs.html">Documentation</a> •
    <a href="https://memoria-proxy.vercel.app/dashboard.html">Dashboard</a>
  </p>
</div>

<br/>

**Memoria** is an ultra-fast, drop-in reverse proxy gateway designed to reduce your OpenAI API costs by up to 60%. By intercepting your LLM requests, Memoria performs semantic vector similarity search in memory using local ONNX embeddings, instantly returning cached responses for similar queries with zero upstream latency.

---

## ✨ Key Features

- ⚡ **Zero-Latency Caching**: Millisecond-level cache hits using high-speed C++ in-memory semantic search.
- 🧠 **Local ONNX Embeddings**: Computes vector embeddings locally using `all-MiniLM-L6-v2` without sending data to third-party embedding APIs.
- 🔌 **Drop-in Replacement**: No code changes required. Just change your OpenAI SDK's `baseURL`.
- 📊 **Telemetry & Rate Limiting**: Built-in request quotas (Free/Pro tiers) and real-time dashboard analytics.
- 🛡️ **Enterprise Security**: Backed by Supabase Row Level Security (RLS) for isolated, secure API key management.

## 🚀 Installation

Memoria is packaged as a pre-compiled Linux binary with an automated installation script. 

1. **Download the Release**
   Download the latest `memoria-linux-amd64.tar.gz` from the [Releases](https://github.com/daffaanan11/memoria/releases) page.

2. **Extract & Install**
   ```bash
   tar -xzvf memoria-linux-amd64.tar.gz
   cd release
   sudo ./install.sh
   ```

3. **Verify Installation**
   ```bash
   memoria status
   ```

*The installation script will automatically install the binaries, set up the AI models, and launch the Gateway as a background Systemd service (`memoria-gateway.service`).*

## 💻 Quick Start (Usage)

Once the gateway is running on your server (`localhost:8080`), you can immediately point your existing AI applications to it.

Generate your **Memoria API Key** from the [Memoria Dashboard](https://memoria-proxy.vercel.app/dashboard.html), and update your code:

### Python (OpenAI SDK)
```python
from openai import OpenAI

client = OpenAI(
    api_key="sk-your-openai-key", # Will be passed securely to OpenAI on cache miss
    base_url="http://localhost:8080/v1",
    default_headers={"Authorization": "Bearer MEM-YOUR-API-KEY"}
)

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "What is semantic caching?"}]
)

print(response.choices[0].message.content)
```

### Node.js / TypeScript
```typescript
import OpenAI from 'openai';

const openai = new OpenAI({
  apiKey: 'sk-your-openai-key', 
  baseURL: 'http://localhost:8080/v1',
  defaultHeaders: { 'Authorization': 'Bearer MEM-YOUR-API-KEY' }
});

const chatCompletion = await openai.chat.completions.create({
  messages: [{ role: 'user', content: 'What is semantic caching?' }],
  model: 'gpt-4o',
});
```

## 🛠️ Service Management

Memoria runs as a native Linux service. You can manage it using standard `systemctl` commands:

- **View real-time logs (Cache Hits/Misses)**:
  ```bash
  sudo journalctl -fu memoria-gateway
  ```
- **Restart the Gateway**:
  ```bash
  sudo systemctl restart memoria-gateway
  ```
- **Stop the Gateway**:
  ```bash
  sudo systemctl stop memoria-gateway
  ```

## 🔐 Privacy & Security

Memoria respects your data privacy.
- **Upstream Keys**: Your upstream LLM API keys (e.g., OpenAI) are never stored on disk. They are held in memory during the proxy request and discarded immediately.
- **Local Embeddings**: User prompts are embedded entirely locally on your CPU/GPU. No prompt data is sent to external embedding services.

---

<div align="center">
  Crafted with ❤️ by <a href="https://github.com/daffaanan11">daffaanan11</a>.
</div>
