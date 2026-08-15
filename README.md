<div align="center">
  <img src="website/logo.jpg" alt="Memoria Logo" width="120" height="120" style="border-radius: 20px;">
  <h1>⚡ Memoria Engine</h1>
  <p><b>High-Performance Semantic Caching & LLM Cost-Optimization Gateway</b></p>
  <p>
    Tri-Engine Architecture (Go + Rust + C) • Local-First Embedding • OpenAI-Compatible Proxy
  </p>
</div>

---

## 🚀 Overview

**Memoria** is a high-performance reverse proxy middleware that intercepts LLM API calls (OpenAI, Anthropic, DeepSeek, etc.), executes semantic embeddings and cosine similarity evaluations entirely locally, and serves instant cached responses for prompts with similar intent. 

By operating entirely within your infrastructure (Zero-Config Integration), Memoria reduces Operational Expenditure (OpEx) and slashes LLM latency to practically zero for recurring conversational patterns.

```text
[ Client / SDK ] ──► [ Memoria Gateway ] ──┬──► (Cache HIT: <8ms, $0)  ──► [ Instant Return ]
                                           └──► (Cache MISS) ──────────► [ Upstream LLM API ]
```

---

## ✨ Features

- **Semantic Caching:** Identifies contextually similar prompts using local INT8 ONNX embeddings (`all-MiniLM-L6-v2`) via a highly optimized C/Rust backend.
- **Drop-in Replacement:** 100% compatible with the OpenAI SDK. Simply change the `baseURL` to point to the Memoria Gateway. No code refactoring required.
- **Zero-Latency Overhead:** Local vector processing powered by CPU AVX2 acceleration with highly concurrent Rust state management ensures minimum routing delays.
- **Memoria Studio Dashboard:** A real-time web UI to monitor hit rates, calculate token cost savings, and inspect semantic similarities via live SSE streams.
- **Enterprise-Grade Privacy:** Your prompt data and embeddings never leave your local infrastructure. Upstream API keys are passed securely or stored using Row Level Security (RLS).

---

## ⚡ Quickstart

The easiest way to run Memoria is via Docker. For a more detailed guide, see [QUICKSTART.md](QUICKSTART.md).

### 1. Start the Cluster (Docker)

```bash
docker-compose up --build -d
```

Once running, the following services will be available:
- 🚀 **Memoria Gateway (API Proxy):** `http://localhost:8080`
- 📊 **Memoria Studio (Dashboard):** `http://localhost:5173`

### 2. Connect Your SDK

Configure your existing LLM application to point to the Memoria Gateway instead of the upstream provider.

**Node.js / TypeScript Example:**
```javascript
import OpenAI from "openai";

const openai = new OpenAI({
  baseURL: "http://localhost:8080/v1", // Route traffic through Memoria
  apiKey: process.env.OPENAI_API_KEY,  // Your upstream key is securely forwarded
});
```

**Python Example:**
```python
from openai import OpenAI
import os

client = OpenAI(
    base_url="http://localhost:8080/v1",
    api_key=os.environ.get("OPENAI_API_KEY")
)
```

**Bypassing the Cache:**
To force a fresh, non-cached response directly from the upstream provider, inject the following HTTP header:
```json
{ "x-memoria-skip": "true" }
```

---

## 🏗️ Architecture

Memoria utilizes a highly specialized Tri-Engine architecture designed for maximum throughput and minimum memory footprint:

1. **Go Gateway (Layer 3):** Handles concurrent HTTP ingestion, Server-Sent Events (SSE) telemetry streaming, and asynchronous proxy forwarding.
2. **Rust Core (Layer 2):** Manages Thread-Safe state, RwLock concurrency, caching evictions (TTL/LRU), and FFI bridging to the C-kernel.
3. **C SIMD Kernel (Layer 1):** Executes the heavy math operations (Dot Product, Magnitude, Cosine Similarity) using AVX2 SIMD instructions directly on the CPU.

---

## 🛠️ Manual Build (Native Compilation)

If you prefer running the binaries natively without Docker, follow these steps (Requires GCC and Go 1.21+):

### Linux / macOS
1. **Build the Rust Core:**
   ```bash
   cd src/rust_core
   cargo build --release
   cp target/release/libmemoria_core.so ../../build/
   ```
2. **Build the Go Gateway:**
   ```bash
   cd src/go_gateway
   go build -o ../../bin/memoria main.go
   ```
3. **Run the Gateway:**
   ```bash
   LD_LIBRARY_PATH=$(pwd)/build ./bin/memoria
   ```

### Windows (Requires MinGW-w64)
1. **Build the Rust Core:**
   ```bash
   cd src\rust_core
   cargo build --release
   copy target\release\memoria_core.dll ..\..\bin\
   ```
2. **Build the Go Gateway:**
   ```bash
   cd src\go_gateway
   set CGO_ENABLED=1
   go build -o ..\..\bin\memoria.exe main.go
   ```
3. **Run:** Execute `memoria.exe` in the `bin/` directory alongside the DLL.

---

## 📄 Documentation & Links
- [Memoria Documentation Website](https://memoria-proxy.vercel.app)
- [Terms & Conditions](https://memoria-proxy.vercel.app/tnc.html)
- [Privacy Policy](https://memoria-proxy.vercel.app/privacy.html)

## ⚖️ License
Memoria is built for performance. See the [LICENSE](LICENSE) file for more information.
