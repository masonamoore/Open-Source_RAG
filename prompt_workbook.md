# Local Llama + Open‑Source RAG: Hands‑On Prompt Workbook

*A copy‑paste friendly set of prompts and mini‑exercises to learn the open‑source stack (Llama → Embeddings → Vector DB → Retrieval → App). Work through in order; each section takes ~15–30 minutes.*

> Tip: Everything here assumes **Ollama** is installed and running. If not: [https://ollama.com](https://ollama.com) → install → `ollama run llama3.1:8b` once.

---

## 0) Quick System Prompts for Better Results

**Paste as your system/instruction message:**

```
You are a precise, fast local AI assistant. Role: teacher + pair‑programmer.
Guidelines: reason step‑by‑step; show minimal commands first, advanced options second; when unsure, ask one clarifying question. Output in concise bullet points with copy‑paste blocks.
```

**Follow‑up style:**

```
When I say “explain”, give me a 3‑level explanation:
1‑liner → practical cheatsheet → deeper background links or keywords.
```

---

## 1) Llama Basics (Ollama)

### 1.1 Run & sanity‑check

**Terminal**

```
ollama run llama3.1:8b
```

**Prompt inside the REPL**

```
Act as a CLI tutor. In 6 bullets, show: model path, quantization, context window, and how to stream responses via the REST API.
```

**Exit tip:** `Ctrl+C` twice.

### 1.2 Use the HTTP API

**Terminal (one‑liner curl)**

```
curl -s http://localhost:11434/api/generate \
  -d '{"model":"llama3.1:8b","prompt":"List 5 things you can do for local dev.","stream":false}' | jq -r .response
```

**Exercise prompt**

```
Give me a JSON schema for a /chat endpoint with fields: model, messages[], temperature, top_p.
```

### 1.3 Prompt patterns

**Try these inside your UI:**

```
You are my code reviewer. First: checklist for a minimal Node/Express API that proxies Ollama. Then: show a 25‑line example.
```

```
You are my Linux coach. Given Mac M‑series hardware, what GGUF quant (Q4_K_M vs Q5_K_M) should I try for 8B and 13B? Include RAM/VRAM estimates and expected tokens/sec.
```

---

## 2) Add a Web UI (Open WebUI or LM Studio)

### 2.1 Open WebUI via Docker

**Terminal**

```
docker run -d --name open-webui -p 3000:8080 \
  -e OLLAMA_BASE_URL=http://host.docker.internal:11434 \
  ghcr.io/open-webui/open-webui:main
```

**Prompt to paste in the WebUI**

```
Create a short “starter pack” of 8 reusable prompts: (a) code helper, (b) regex builder, (c) shell generator, (d) commit message writer, (e) bug triage, (f) doc explainer, (g) tldr summarizer, (h) test case writer. Output as a markdown list I can save as presets.
```

### 2.2 LM Studio alternative

**Prompt**

```
Compare Open WebUI vs LM Studio for my use case (local dev on Mac, occasional Windows/NVIDIA). Summarize: model catalog, extensions, token streaming, API compatibility, and offline behavior. Finish with a recommendation.
```

---

## 3) Embeddings 101 (Sentence‑Transformers / Nomic / BGE)

> Goal: turn text into vectors you can search. We’ll start in plain Python with `sentence-transformers`.

### 3.1 Minimal embeddings script

**Prompt to generate code (ask Llama):**

```
Write a Python script that:
1) installs sentence-transformers if missing
2) loads "all-MiniLM-L6-v2" (or nomic-embed-text if GPU)
3) embeds a small list of docs
4) saves to embeddings.npy and texts.json
5) prints cosine similarity matrix
Use only standard libs + numpy; keep it <60 lines.
```

### 3.2 Embedding hygiene

**Prompt**

```
I have mixed content: code, FAQs, long PDFs. Give chunking rules (size/overlap), normalization (lowercasing, punctuation), and when to prefer domain‑specific models like BGE‑small‑en. Provide a table.
```

---

## 4) Vector Stores (Chroma / Qdrant / pgvector / Weaviate)

### 4.1 Quickstart with Chroma (local, zero‑ops)

**Prompt to generate code**

```
Write a Python snippet that loads texts.json + embeddings.npy and inserts them into a local Chroma collection. Then run a similarity search for the query: "reset a lost SSH key" and print the top 3 with scores.
```

### 4.2 Switch to Qdrant (Docker)

**Terminal**

```
docker run -d --name qdrant -p 6333:6333 qdrant/qdrant
```

**Prompt**

```
Provide a Python client example to upsert embeddings into Qdrant, create an index with cosine metric, and perform a filtered search on a `doc_type` payload field.
```

### 4.3 pgvector option

**Prompt**

```
Generate the SQL to enable pgvector, create a table `docs(id serial, text text, embedding vector(384))`, and a query that returns the top‑5 most similar rows for a given embedding parameter.
```

---

## 5) Retrieval & Ranking (FAISS / Haystack / Rerankers)

### 5.1 Baseline retriever

**Prompt**

```
Using my embeddings store, build a retriever function `retrieve(query, k=5)` that returns (text, score). Describe how to choose k and when to enable Maximal Marginal Relevance (MMR).
```

### 5.2 Add a reranker (bge‑reranker / jina‑reranker‑v2)

**Prompt**

```
Show a Python example that takes the top‑10 retrieved passages and re‑ranks them with a cross‑encoder reranker model, returning top‑3. Explain pros/cons and latency.
```

---

## 6) Chat + RAG (LlamaIndex or LangChain)

### 6.1 LlamaIndex minimal RAG

**Prompt**

```
Give me a 40‑line LlamaIndex example that:
- loads a folder of .md files
- builds embeddings (sentence-transformers)
- answers questions with Llama via Ollama (local endpoint)
- prints source attributions
Assume Python 3.11 and show pip requirements.
```

### 6.2 LangChain alternative

**Prompt**

```
Provide an equivalent LangChain pipeline using `Ollama` LLM, `Chroma` vectorstore, and `Runnable` chains. Include: prompt template, history support, and streaming tokens to stdout.
```

### 6.3 Guardrails

**Prompt**

```
Design a system prompt + output schema for a RAG answerer that MUST: cite sources, say "I don't know" when confidence < threshold, and return JSON with fields {answer, sources[], confidence}. Show the pydantic model and validator.
```

---

## 7) Evaluation (Ragas / golden‑set)

### 7.1 Quick eval with Ragas

**Prompt**

```
Generate code to evaluate a RAG chain with Ragas on 20 Q/A pairs. Compute answer‑faithfulness and context‑precision. Output a markdown report.
```

### 7.2 DIY golden set

**Prompt**

```
Help me craft 15 ground‑truth Q/A pairs from my docs folder. Ask me 5 clarifying questions, then propose the set with difficulty labels (easy/medium/hard).
```

---

## 8) Frontend (Next.js or SvelteKit) + Simple Backend

### 8.1 Tiny Express proxy (Node)

**Prompt**

```
Write a 30‑line Express server with `/api/chat` that forwards to `http://localhost:11434/api/generate` and supports streaming (Server‑Sent Events). Include CORS.
```

### 8.2 Next.js chat page

**Prompt**

```
Create a Next.js 14 page with a textarea, send button, temperature slider, and a model dropdown. On submit, stream tokens from `/api/chat` and render markdown with code blocks.
```

### 8.3 Upload + index UI

**Prompt**

```
Add an "Upload Docs" panel that accepts .pdf/.md/.txt, chunks them client‑side, sends to a `/api/index` route, and shows indexing progress. Include a toast on completion.
```

---

## 9) Data & Orchestration (Airflow / NiFi / LangChain loaders)

**Prompt**

```
Show me an Airflow DAG that nightly: pulls a docs folder from S3, runs a chunk+embed step, updates a Qdrant collection, and posts metrics to Slack. Use PythonOperators; keep it readable.
```

---

## 10) Optional: Images & Multimodal

**Prompt**

```
Outline a pipeline where a vision model (e.g., llava) extracts captions from images, stores them alongside text chunks, and enables hybrid search (text + image captions). Provide the data schema and retrieval query.
```

---

## 11) Performance Tuning Cheatsheet

**Prompt**

```
Given my hardware (fill in: CPU/GPU/VRAM), recommend: quant level, batch size, context window, rope scaling, and caching strategy. Provide expected tokens/sec for 7B vs 13B.
```

---

## 12) Your Project Plan (fill‑in prompts)

**Prompt**

```
Act as my tech lead. Build a 2‑week plan to ship an MVP: local chat + RAG over my notes. Include milestones, risks, and a de‑risk spike. Output as a checklist with time estimates.
```

**Prompt**

```
Turn the plan into GitHub issues with titles, acceptance criteria, and labels. Output as CSV I can import.
```

---

### Appendix A: Model Shortlist

* **Llama 3.1 8B Instruct (GGUF)** — great default.
* **Qwen2.5‑7B Instruct** — strong coding.
* **Mistral 7B Instruct** — light + fast.
* **Rerankers:** `bge-reranker-v2-m3`, `jina-reranker-v2`.
* **Embeddings:** `all-MiniLM-L6-v2` (fast), `bge-small-en-v1.5` (quality), `nomic-embed-text` (GPU‑friendly).

### Appendix B: Common Gotchas

* Mismatch of embedding dims between model and DB schema
* Over‑chunking (losing semantics) vs under‑chunking (missed recall)
* Not normalizing text consistently at index and query time
* Forgetting to surface **sources** and **confidence** to users
* RAG ≠ fine‑tuning; start with retrieval quality first
