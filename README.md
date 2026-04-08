# SAIM — SLIET Academic Intelligence Model

![Status](https://img.shields.io/badge/Status-In%20Development-yellow?style=for-the-badge)
![Model](https://img.shields.io/badge/Base%20Model-Llama%203.1%208B-orange?style=for-the-badge)
![Backend](https://img.shields.io/badge/Backend-FastAPI-green?style=for-the-badge)
![License](https://img.shields.io/badge/License-Open%20Source-blue?style=for-the-badge)

An AI-powered academic assistant fine-tuned on SLIET's own PYQs and study material. Built for students. Trained on real college data.

---

## What is SAIM?

SAIM (SLIET Academic Intelligence Model) is an AI assistant designed specifically for students at SLIET. It is fine-tuned on college-specific data — previous year questions (PYQs), notes, and exam material — to act as a personalized academic companion that can answer questions, generate mock tests, and help students study smarter.

---

## Final Technology Stack

| Category | Tool | Reason |
|---|---|---|
| Base LLM | Llama 3.1 8B | Best quality + fine-tuning support |
| Fine-tuning Method | QLoRA (4-bit) | Runs on single GPU, near full fine-tune quality |
| Vector Database | ChromaDB | Zero config, local, free |
| Embedding Model | bge-small-en-v1.5 | Fast, free, no internet dependency |
| Backend | FastAPI (Python) | Native ML integration, async support |
| Frontend — Phase 1 | React.js | Fast to build, web dashboard |
| Frontend — Phase 2 | React Native | Shared codebase, mobile extension |
| Primary Database | PostgreSQL | Structured data, excellent Python support |
| GPU / Compute | RunPod + Google Colab | Best price-performance for training |
| OCR | Tesseract + Google Vision API | Printed and handwritten notes coverage |
| Local Dev LLM | Ollama | Single command, OpenAI-compatible API |

---

## Stack Decisions

### Base LLM — Llama 3.1 8B

Best balance of quality, community support, and fine-tuning resources. QLoRA works excellently with this model. Widely used in production EdTech systems.

### Fine-Tuning Method — QLoRA (4-bit quantization)

Runs on a single rented A100 for under Rs. 2,000 per training run. Only trains small adapter weights, not the full model. Output quality is near-identical to full fine-tuning for this use case.

### Vector Database — ChromaDB

Zero configuration, works locally, free, and perfectly sized for a college dataset of a few thousand documents. Can switch to Qdrant or FAISS later if scale requires.

### Embedding Model — bge-small-en-v1.5

Better quality than MiniLM with similar speed. Completely free and runs locally. No internet dependency at query time — important for on-premise deployment.

### Backend Framework — FastAPI (Python)

Industry standard for the ML model serving. Async support, automatic API documentation, and direct access to allthe Python ML libraries.

### Frontend — React.js (Phase 1), React Native (Phase 2)

Start with a React web app for speed of development. Once the core system works, extend to React Native for mobile. Shared codebase reduces effort.

### Primary Database — PostgreSQL

Battle-tested, free, excellent Python support via SQLAlchemy, and works perfectly with FastAPI. Handles all structured data needs for years of growth.

### GPU / Compute — RunPod + Google Colab Free

RunPod gives the best price-to-performance ratio for training runs. Colab free tier is sufficient for initial experiments and testing before committing to a full training run.

### OCR — Tesseract (printed) + Google Vision API (handwritten)

Use free Tesseract for typed/printed notes. For handwritten notes, Google Vision API gives the best accuracy at very low cost — approximately Rs. 1.20 per 1000 pages.

### Local Dev LLM — Ollama

Single command install, runs any model, has a simple API identical to OpenAI format so code written against it works with minimal changes against the real model later. Industry standard for local LLM development.

---

## System Architecture

```
Student Query
     |
     v
React.js Frontend
     |
     v
FastAPI Backend
     |
     |---> ChromaDB (Vector Search) ---> bge-small-en-v1.5 Embeddings
     |              |
     |              └---> Relevant Chunks (RAG Context)
     |
     |---> PostgreSQL (User Data, Sessions, Exams)
     |
     └---> Fine-tuned Llama 3.1 8B (QLoRA Adapter)
                    |
                    v
               AI Response --> Student
```

---

## Estimated Training Cost

| Run Type | Platform | GPU | Est. Time | Est. Cost |
|---|---|---|---|---|
| Experiment / Test | Google Colab Free | T4 | 1–2 hrs | Rs. 0 |
| Full Training Run | RunPod | A100 80GB | 4–8 hrs | Rs. 320–2,000 |
| Re-training (updates) | RunPod | A100 | 2–4 hrs | Rs. 160–1,000 |

---

## Repository Structure

```
saim/
├── data/
│   ├── raw/                # Original uploads (PDFs, images)
│   └── processed/          # Cleaned and chunked text
├── training/               # QLoRA fine-tuning scripts
├── rag/                    # RAG pipeline (ChromaDB + embeddings)
├── backend/                # FastAPI application
│   ├── routes/
│   ├── models/
│   └── main.py
├── frontend/               # React.js web app
├── ocr/                    # Tesseract + Google Vision scripts
├── docs/                   # Research documents
└── README.md
```

---

## Getting Started

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

### 3. Pull the local model

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

## Contributing

This is a student research project at SLIET. Contributions from team members are welcome. Please open an issue before starting on major changes.

---

*Built at SLIET — SAIM Team*
