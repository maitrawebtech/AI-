# VectorDB — Build a Vector Database from Scratch in C++

A fully working Vector Database built from scratch in modern C++ with a web UI.

This project implements:

- HNSW (Hierarchical Navigable Small World)
- KD-Tree
- Brute Force Search
- RAG (Retrieval-Augmented Generation) pipeline using Ollama

Built as an educational project to demonstrate how production vector databases like Pinecone, Weaviate, Chroma, and Milvus work internally.

---

# Features

| Feature | Description |
|---|---|
| 3 Search Algorithms | HNSW, KD-Tree, and Brute Force side-by-side |
| 3 Distance Metrics | Cosine Similarity, Euclidean Distance, Manhattan Distance |
| 16D Demo Dataset | 20 semantic vectors across CS, Math, Food, and Sports |
| PCA Visualization | Live 2D semantic scatter plot |
| Real Embeddings | Ollama-powered 768D embeddings |
| RAG Pipeline | Ask questions about your documents |
| REST API | Full CRUD + search endpoints |
| Local AI | Fully offline LLM workflow |

---

# How It Works

```text
Your Text
    │
    ▼
Ollama (nomic-embed-text)
    │
    ▼
768D Embedding Vector
    │
    ▼
HNSW Index (C++)
    │
    ▼
Semantic Search
    │
    ▼
Retrieved Context Chunks
    │
    ▼
Ollama (llama3.2)
    │
    ▼
Generated Answer
```

---

# What is HNSW?

HNSW (Hierarchical Navigable Small World) is a graph-based Approximate Nearest Neighbor (ANN) algorithm used in modern vector databases.

It creates multiple graph layers:

- Upper layers → sparse long-range connections
- Lower layers → dense local connections

Search begins at the top sparse layers and progressively zooms into the nearest neighborhood.

This enables near logarithmic complexity:

```text
O(log N)
```

instead of brute-force complexity:

```text
O(N)
```

HNSW is widely used in:

- Pinecone
- Weaviate
- Milvus
- Chroma

---

# Prerequisites

Install the following on your Windows machine:

- MSYS2
- Git
- Ollama

---

# Step 1 — Install MSYS2 (C++ Compiler)

Download:

https://www.msys2.org

Install using default settings.

Open:

```text
MSYS2 UCRT64
```

Run:

```bash
pacman -Syu
```

Restart the terminal if requested, then run:

```bash
pacman -S mingw-w64-ucrt-x86_64-gcc
```

---

## Add g++ to Windows PATH

Add this directory to your system PATH:

```text
C:\msys64\ucrt64\bin
```

Verify installation:

```bash
g++ --version
```

Expected output:

```text
g++ (GCC) 15.x.x
```

---

# Step 2 — Install Git

Download:

https://git-scm.com/download/win

Verify installation:

```bash
git --version
```

---

# Step 3 — Install Ollama

Download:

https://ollama.com

Install Ollama and pull the required models:

```bash
ollama pull nomic-embed-text
```

```bash
ollama pull llama3.2
```

Verify:

```bash
ollama list
```

Expected models:

- nomic-embed-text
- llama3.2

---

# Minimum System Requirements

| Component | Recommended |
|---|---|
| RAM | 8 GB |
| Storage | 5 GB free |
| OS | Windows 10/11 |
| CPU | Modern multi-core processor |

---

# Step 4 — Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/VectorDB.git
cd VectorDB
```

Replace:

```text
YOUR_USERNAME
```

with your GitHub username.

---

# Step 5 — Compile the Server

Inside the project folder:

```bash
g++ -std=c++17 -O2 main.cpp -o db -lws2_32
```

This generates:

```text
db.exe
```

---

# Compilation Troubleshooting

| Problem | Fix |
|---|---|
| g++ not found | Add MSYS2 path correctly |
| undefined reference to WSA | Add `-lws2_32` |
| Slow compile | Remove `-O2` |

---

# Step 6 — Run the Project

## Terminal 1 — Start Ollama

```bash
ollama serve
```

Skip if already running.

---

## Terminal 2 — Start VectorDB

```bash
./db
```

Expected output:

```text
=== VectorDB Engine ===
http://localhost:8080

20 demo vectors | 16 dims | HNSW + KD-Tree + BruteForce

Ollama: ONLINE
embed model: nomic-embed-text
gen model: llama3.2
```

Open:

```text
http://localhost:8080
```

in your browser.

---

# Using the Application

# Tab 1 — Semantic Search

Search concepts like:

- binary tree
- sushi
- basketball
- calculus

Choose:

- Search algorithm
- Distance metric

Supported algorithms:

- HNSW
- KD-Tree
- Brute Force

Supported metrics:

- Cosine Similarity
- Euclidean Distance
- Manhattan Distance

The scatter plot visualizes semantic clusters in vector space.

---

# Tab 2 — Document Embeddings

Insert real documents using Ollama embeddings.

Workflow:

1. Enter title
2. Paste text
3. Click **Embed & Insert**

The system automatically:

- Splits long documents into chunks
- Generates 768D embeddings
- Stores chunks inside an HNSW index

---

# Tab 3 — Ask AI (RAG Pipeline)

Ask questions about inserted documents.

Example:

```text
What is dynamic programming?
```

Pipeline flow:

1. Question embedding generated
2. HNSW retrieves nearest chunks
3. Context sent to llama3.2
4. AI generates answer

Features:

- Streaming responses
- Retrieved source chunks
- Semantic context references

---

# REST API

Base URL:

```text
http://localhost:8080
```

---

# Demo Vector Endpoints

| Method | Endpoint | Description |
|---|---|---|
| GET | `/search` | K-NN vector search |
| POST | `/insert` | Insert vector |
| DELETE | `/delete/:id` | Delete vector |
| GET | `/items` | List vectors |
| GET | `/benchmark` | Compare algorithms |
| GET | `/hnsw-info` | HNSW graph stats |
| GET | `/stats` | Database statistics |

---

# Document & RAG Endpoints

| Method | Endpoint | Description |
|---|---|---|
| POST | `/doc/insert` | Embed and store document |
| GET | `/doc/list` | List stored chunks |
| DELETE | `/doc/delete/:id` | Delete chunk |
| POST | `/doc/ask` | RAG question answering |
| GET | `/status` | Ollama status |

---

# Example API Usage

## Vector Search

```bash
curl "http://localhost:8080/search?v=0.9,0.8,0.7,0.6,0.1,0.1,0.1,0.1,0.1,0.1,0.1,0.1,0.1,0.1,0.1,0.1&k=3&metric=cosine&algo=hnsw"
```

---

## Ask AI

```bash
curl -X POST http://localhost:8080/doc/ask ^
  -H "Content-Type: application/json" ^
  -d "{\"question\":\"What is dynamic programming?\",\"k\":3}"
```

---

# Project Structure

```text
VectorDB/
│
├── main.cpp        # Backend engine
├── httplib.h       # cpp-httplib library
├── index.html      # Frontend UI
└── README.md       # Documentation
```

---

# Internal Architecture

| Component | Complexity | Purpose |
|---|---|---|
| BruteForce | O(N·d) | Exact baseline |
| KDTree | O(log N) | Exact low-dimensional search |
| HNSW | O(log N) | Approximate ANN search |

---

# Why KD-Tree Struggles at High Dimensions

KD-Tree works well in low dimensions.

However, in very high-dimensional spaces:

- Distance bounds become ineffective
- Most branches cannot be pruned
- Search approaches brute-force complexity

This is called:

```text
Curse of Dimensionality
```

HNSW avoids this issue using graph navigation instead of geometric partitioning.

---

# Algorithm Deep Dive

# HNSW

Each node is inserted into a multilayer graph.

- Layer 0 contains all nodes
- Higher layers contain progressively fewer nodes

Search process:

1. Start at top layer
2. Greedily navigate toward nearest node
3. Descend layers
4. Expand nearest candidates at layer 0

Why it is fast:

- Upper layers behave like highways
- Lower layers refine local search

---

# KD-Tree

KD-Tree uses binary space partitioning.

Each node splits space along one dimension.

Search prunes subtrees that cannot contain better candidates.

Weakness:

- Degrades badly in high dimensions
- Approaches brute-force behavior at 768D

---

# Common Issues

| Problem | Fix |
|---|---|
| Ollama OFFLINE | Run `ollama serve` |
| Slow embeddings | Models downloading initially |
| Port 8080 busy | Kill existing process |
| Slow responses | Use smaller LLM |
| g++ missing | Fix PATH variable |

---

# Use a Smaller Faster Model

Install:

```bash
ollama pull llama3.2:1b
```

Update in `main.cpp`:

```cpp
std::string genModel = "llama3.2:1b";
```

Recompile:

```bash
g++ -std=c++17 -O2 main.cpp -o db -lws2_32
```

---

# Educational Goals

This project teaches:

- Vector search internals
- ANN algorithms
- Semantic embeddings
- RAG pipelines
- Graph indexing
- AI infrastructure engineering
- Local LLM integration
- REST API development

---

# Future Improvements

Potential upgrades:

- Disk persistence
- IVF indexing
- Product quantization
- GPU acceleration
- Distributed indexing
- Hybrid BM25 + vector search
- SIMD optimization
- Authentication system

---

# License

MIT License

Use, modify, and distribute freely.
