# 🧠 SAIM — SLIET Academic Intelligence Model

<div align="center">

![Status](https://img.shields.io/badge/Status-In%20Development-yellow?style=for-the-badge)
![License](https://img.shields.io/badge/License-Open%20Source-blue?style=for-the-badge)
![Model](https://img.shields.io/badge/Base%20Model-Llama%203.1%208B-orange?style=for-the-badge)
![Framework](https://img.shields.io/badge/Backend-FastAPI-green?style=for-the-badge)

**An AI-powered academic assistant fine-tuned on SLIET's own PYQs and study material.**

*Built for students. Trained on real college data. Runs locally.*

</div>

---

## 📌 What is SAIM?

SAIM (SLIET Academic Intelligence Model) is an AI assistant designed specifically for students at SLIET. It is fine-tuned on college-specific data — previous year questions (PYQs), notes, and exam material — to act as a personalized academic companion that can answer questions, generate mock tests, and help students study smarter.

> This repository contains the technical research, stack decisions, and implementation for the SAIM project.

---

## 🏗️ Final Technology Stack

After thorough evaluation of all alternatives, the following stack was finalized:

| Category | Chosen Tool | Reason |
|---|---|---|
| 🤖 Base LLM | **Llama 3.1 8B** | Best quality + fine-tuning support |
| 🎯 Fine-tuning Method | **QLoRA (4-bit)** | Runs on single GPU, near full-FT quality |
| 🗃️ Vector Database | **ChromaDB** | Zero config, local, free |
| 🔢 Embedding Model | **bge-small-en-v1.5** | Fast, free, no internet dependency |
| ⚡ Backend | **FastAPI (Python)** | Native ML integration, async support |
| 🖥️ Frontend Phase 1 | **React.js** | Fast to build, web dashboard |
| 📱 Frontend Phase 2 | **React Native** | Shared codebase → mobile extension |
| 🗄️ Primary Database | **PostgreSQL** | Structured data, excellent Python support |
| ☁️ GPU / Compute | **RunPod + Google Colab** | Best price-performance for training |
| 🔍 OCR | **Tesseract + Google Vision API** | Printed + handwritten notes coverage |
| 💻 Local Dev LLM | **Ollama** | Single command, OpenAI-compatible API |

---

## 🔬 Technology Evaluation Details

Each tool category was researched and debated before a final verdict was recorded. Full comparisons are below.

---

### 1. Core LLM — Base Model

The base model is the foundation of everything. It must be open-source, run on affordable hardware, and be strong at instruction-following tasks like Q&A generation.

| Model | Size | Strengths | Weaknesses | License |
|---|---|---|---|---|
| **Llama 3.1 8B** ✅ | 8B | Best quality, strong reasoning, huge community | Needs 8GB+ VRAM for training | Open (Meta) |
| Mistral 7B | 7B | Fast, efficient, great instruction following | Slightly weaker than Llama 3 | Apache 2.0 |
| Phi-3 Mini | 3.8B | Runs on low-end hardware, fast inference | Less capable on complex tasks | MIT |
| Gemma 2B | 2B | Very lightweight, Google-backed | Weak on long-form answers | Open (Google) |
| OLMo 7B | 7B | Fully open weights + data, research-grade | Complex setup, less community support | Apache 2.0 |

> **✅ VERDICT → Llama 3.1 8B**
> Best balance of quality, community support, and fine-tuning resources. QLoRA works excellently with this model. Widely used in production EdTech systems.

---

### 2. Fine-Tuning Method

Fine-tuning teaches the base model to behave like a personalized academic assistant. We need a method that works on limited hardware (rented GPU) without retraining the entire model from scratch.

| Method | Hardware Needed | Training Time | Quality | Complexity |
|---|---|---|---|---|
| **QLoRA (4-bit)** ✅ | 1× A100 / RTX 4090 | 4–8 hours | Excellent | Low — well documented |
| LoRA (16-bit) | 2× A100 | 6–12 hours | Excellent | Low |
| Full Fine-tune | 8× A100 | 2–5 days | Best possible | Very High |
| Prompt Tuning | CPU only | Minutes | Moderate | Very Low |
| RLHF | Multiple GPUs | Days–weeks | Best for chat | Very High |

> **✅ VERDICT → QLoRA (4-bit quantization)**
> Runs on a single rented A100 for under ₹2,000 per training run. Only trains small adapter weights, not the full model. Output quality is near-identical to full fine-tuning for our use case.

---

### 3. Vector Database (for RAG)

The vector database stores embeddings of all notes and PYQ chunks. When a student asks a question, we search this database for the most relevant content and pass it to the model.

| Tool | Type | Setup | Speed | Best For |
|---|---|---|---|---|
| **ChromaDB** ✅ | Local / Cloud | `pip install` | Fast for <1M docs | Small to medium college dataset |
| FAISS | Local only | `pip install` | Extremely fast | Large scale, no metadata needed |
| Weaviate | Cloud / Self-host | Docker setup needed | Fast | Production scale with filtering |
| Pinecone | Cloud only | API signup required | Very fast | Production SaaS — costs money |
| Qdrant | Local / Cloud | Docker setup needed | Very fast | Production with rich filtering |

> **✅ VERDICT → ChromaDB**
> Zero configuration, works locally, free, and perfectly sized for a college dataset of a few thousand documents. Can switch to Qdrant or FAISS later if scale requires.

---

### 4. Embedding Model

The embedding model converts text (questions, notes, PYQs) into numerical vectors for semantic search. Runs at query time — every time a student asks something.

| Model | Size | Speed | Quality | Cost |
|---|---|---|---|---|
| all-MiniLM-L6-v2 | 80MB | Very fast | Good | Free (local) |
| **bge-small-en-v1.5** ✅ | 120MB | Fast | Very good | Free (local) |
| bge-large-en-v1.5 | 1.3GB | Moderate | Excellent | Free (local) |
| OpenAI text-embedding-3 | Cloud | Fast | Excellent | Paid per token |
| Cohere Embed | Cloud | Fast | Excellent | Paid per token |

> **✅ VERDICT → bge-small-en-v1.5**
> Better quality than MiniLM with similar speed. Completely free and runs locally. No internet dependency at query time — critical for on-premise deployment.

---

### 5. Backend Framework

The backend serves the model to the frontend, handles authentication, manages the database, and runs the RAG pipeline. Must be Python-based to work directly with ML libraries.

| Framework | Language | Speed | ML Integration | Learning Curve |
|---|---|---|---|---|
| **FastAPI** ✅ | Python | Very fast (async) | Native — perfect | Low |
| Flask | Python | Moderate | Good | Very Low |
| Django | Python | Moderate | Good | Medium |
| Express.js | Node.js | Fast | Poor — separate process needed | Low |
| Spring Boot | Java | Very fast | Poor | High |

> **✅ VERDICT → FastAPI (Python)**
> Industry standard for ML model serving. Async support, automatic API documentation, and direct access to all Python ML libraries. The entire team is already using Python for ML work.

---

### 6. Frontend Framework

The student-facing interface for chatting with the AI, taking mock tests, browsing notes, and viewing the academic calendar. Needs to be fast, responsive, and work on both desktop and mobile.

| Framework | Type | Mobile Support | Complexity | Best For |
|---|---|---|---|---|
| **React.js** ✅ Phase 1 | Web only | Responsive design | Medium | Web dashboard — fast to build |
| **React Native** ✅ Phase 2 | Mobile app | Native iOS + Android | High | Dedicated mobile app |
| Next.js | Web + SSR | Responsive design | Medium | Web with SEO + performance |
| Flutter | Mobile + Web | Native iOS + Android | High | Cross-platform app |
| Vue.js | Web only | Responsive design | Low | Simpler alternative to React |

> **✅ VERDICT → React.js (Phase 1) → React Native (Phase 2)**
> Start with a React web app for speed of development. Once the core system works, extend to React Native for mobile. Shared codebase reduces effort.

---

### 7. Primary Database

Stores structured data — user accounts, teacher profiles, calendar events, session history, and exam schedules. Separate from the vector database.

| Database | Type | Setup | Scalability | Best For |
|---|---|---|---|---|
| **PostgreSQL** ✅ | Relational (SQL) | Easy | Excellent | Structured data — users, teachers, exams |
| MySQL | Relational (SQL) | Easy | Good | Simple structured data |
| MongoDB | NoSQL (document) | Easy | Excellent | Flexible schema, JSON documents |
| SQLite | Relational (file) | Zero setup | Poor (single file) | Development only |
| Supabase | Postgres + cloud | Easy (hosted) | Good | Quick start with auth built in |

> **✅ VERDICT → PostgreSQL**
> Battle-tested, free, excellent Python support via SQLAlchemy, and works perfectly with FastAPI. Handles all structured data needs for years of growth.

---

### 8. GPU / Compute for Training

We need a GPU to run QLoRA fine-tuning. We do not need to own hardware — renting cloud GPUs by the hour is the standard approach.

| Platform | GPU Available | Cost/hr (approx) | Ease of Use | Recommended For |
|---|---|---|---|---|
| **RunPod** ✅ | A100, H100, RTX 4090 | ₹80–250/hr | Easy | Fine-tuning runs |
| Lambda Labs | A100, H100 | ₹100–200/hr | Easy | Fine-tuning runs |
| **Google Colab Free** ✅ | T4 | Free | Very Easy | Experiments, testing |
| Google Colab Pro | T4, A100 | ₹900/month flat | Very Easy | Consistent experiments |
| AWS / GCP / Azure | All types | ₹200–500/hr | Complex | Production deployment |
| Kaggle Notebooks | T4 (free) | Free (30hr/week) | Easy | Small experiments only |

> **✅ VERDICT → RunPod for training + Google Colab Free for experiments**
> RunPod gives the best price-to-performance ratio for training runs. Colab free tier is sufficient for initial experiments and testing before committing to a full training run.

---

### 9. OCR Tool (for Handwritten Notes)

Many college notes are handwritten or scanned PDFs. We need an OCR tool to extract text from these before using them for training or RAG.

| Tool | Type | Accuracy | Cost | Handles Handwriting |
|---|---|---|---|---|
| **Tesseract** ✅ | Local / Free | Good for printed | Free | Poor |
| **Google Vision API** ✅ | Cloud API | Excellent | Paid (low cost) | Good |
| Azure Document Intelligence | Cloud API | Excellent | Paid | Very Good |
| PaddleOCR | Local / Free | Very Good | Free | Moderate |
| EasyOCR | Local / Free | Good | Free | Moderate |

> **✅ VERDICT → Tesseract (printed) + Google Vision API (handwritten)**
> Use free Tesseract for typed/printed notes. For handwritten notes, Google Vision API gives the best accuracy at very low cost — approximately ₹1.20 per 1000 pages.

---

### 10. Local Development LLM Tool

For day-to-day development and testing on laptops — before the actual fine-tuning runs. We need a tool that lets us run open LLMs locally without a GPU.

| Tool | Models Supported | Setup | Performance | Best For |
|---|---|---|---|---|
| **Ollama** ✅ | Llama, Mistral, Phi, Gemma, 100+ | One command install | Excellent | Daily development |
| LM Studio | Most GGUF models | GUI app — very easy | Excellent | Non-technical team members |
| llama.cpp | GGUF models | Compile from source | Best performance | Advanced users only |
| GPT4All | Curated selection | GUI app | Good | Beginners |
| Jan.ai | Most GGUF models | GUI app | Good | Easy local chat UI |

> **✅ VERDICT → Ollama**
> Single command install, runs any model, has a simple API identical to OpenAI format — so code written against it works with minimal changes against the real model later. Industry standard for local LLM development.

---

## 🗺️ System Architecture (Overview)

```
Student Query
     │
     ▼
 React.js Frontend
     │
     ▼
 FastAPI Backend
     │
     ├──► ChromaDB (Vector Search) ──► bge-small-en-v1.5 Embeddings
     │         │
     │         └──► Relevant Chunks (RAG Context)
     │
     ├──► PostgreSQL (User Data, Sessions, Exams)
     │
     └──► Fine-tuned Llama 3.1 8B (QLoRA Adapter)
               │
               ▼
          AI Response → Student
```

---

## 💸 Estimated Training Cost

| Run Type | Platform | GPU | Est. Time | Est. Cost |
|---|---|---|---|---|
| Experiment / Test | Google Colab Free | T4 | 1–2 hrs | ₹0 |
| Full Training Run | RunPod | A100 80GB | 4–8 hrs | ₹320–₹2,000 |
| Re-training (updates) | RunPod | A100 | 2–4 hrs | ₹160–₹1,000 |

---

## 📁 Repository Structure

```
saim/
├── data/                   # PYQs, notes, raw documents
│   ├── raw/                # Original uploads (PDFs, images)
│   └── processed/          # Cleaned & chunked text
├── training/               # Fine-tuning scripts (QLoRA)
├── rag/                    # RAG pipeline (ChromaDB + bge embeddings)
├── backend/                # FastAPI application
│   ├── routes/
│   ├── models/
│   └── main.py
├── frontend/               # React.js web app
├── ocr/                    # Tesseract + Google Vision scripts
├── docs/                   # Research documents & notes
│   └── SAIM_tech_research.docx
└── README.md
```

---

## 🚀 Getting Started (Local Development)

### Prerequisites

- Python 3.10+
- Node.js 18+
- [Ollama](https://ollama.ai) installed

### 1. Clone the repository

```bash
git clone https://github.com/your-org/saim.git
cd saim
```

### 2. Set up the backend

```bash
cd backend
pip install -r requirements.txt
uvicorn main:app --reload
```

### 3. Pull the local model (for dev testing)

```bash
ollama pull llama3.1:8b
```

### 4. Set up the frontend

```bash
cd frontend
npm install
npm run dev
```

### 5. Initialize ChromaDB

```bash
cd rag
python setup_chroma.py
```

---

## 🤝 Contributing

This is a student research project at SLIET. Contributions from team members are welcome. Please open an issue before starting on major changes.

---

## 📄 License

This project uses open-source tools and models. The codebase is developed internally by the SAIM team. The base model (Llama 3.1) is used under Meta's open license terms.

---

<div align="center">
  <sub>Built with ❤️ at SLIET · SAIM Team</sub>
</div>
