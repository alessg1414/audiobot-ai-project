# AudioBot AI — Call Center Audio Analysis & RAG Platform

> **Automated Speech-to-Text Pipeline & Retrieval-Augmented Generation Platform for Conversational Intelligence**

> [!NOTE]  
> **Academic Project Status**: This repository represents a fully functional prototype designed for automated speech transcription and document-based Retrieval-Augmented Generation (RAG). The platform natively processes Spanish-language telecommunications audio, enforcing strict system guardrails to prevent out-of-context model hallucinations.

---

## Executive Summary

**AudioBot AI** is an end-to-end conversational intelligence platform designed to transcribe, index, and analyze call center audio interactions. The system combines GPU-accelerated local Automatic Speech Recognition (ASR) via **OpenAI Whisper (large-v3)** with a high-performance **Retrieval-Augmented Generation (RAG)** pipeline powered by **LlamaIndex** and **Google Gemini 2.5 Flash**.

By leveraging localized vector embeddings and strict system prompt guardrails, the platform enables domain-specific querying over call transcripts. This ensures that analytical insights, compliance checks, and agent performance evaluations are derived strictly from grounded transcript data rather than generic LLM pre-training.

---

## Technical Features & Core Capabilities

* **Accelerated ASR Pipeline**: Utilizes OpenAI Whisper `large-v3` with beam search optimization, supporting hardware acceleration via PyTorch CUDA on modern GPU architectures.
* **Grounded RAG Architecture**: Implements LlamaIndex `VectorStoreIndex` coupled with Google Gemini embeddings to construct context-aware transcript vector spaces.
* **Strict Context Guardrails**: Enforces system-level prompts that prevent the LLM from answering queries unrelated to the provided transcript context.
* **Granular Timestamping**: Formats ASR outputs into timestamped transcript segments for precise chronological tracking of customer-agent exchanges.
* **Multilingual Localization**: Native Spanish language configuration (`es`), covering transcription heuristics, system guardrails, and user interface feedback.
* **Reactive User Interface**: Single-Page Application (SPA) built with React 19 and Vite, featuring typing status indicators and real-time query rendering.

---

## Technology Stack

| Architecture Layer | Technology / Framework | Function / Role |
| :--- | :--- | :--- |
| **Speech Recognition** | OpenAI Whisper (`large-v3`) | Localized ASR with PyTorch CUDA acceleration |
| **Language Model (LLM)** | Google Gemini 2.5 Flash | Contextual reasoning and question answering |
| **Vector Embeddings** | Gemini Embedding 001 | High-dimensional semantic text representations |
| **Orchestration Framework**| LlamaIndex (`VectorStoreIndex`) | Ingestion, chunking, and vector index retrieval |
| **Backend API Service** | Python 3.10+ / Flask / Flask-CORS | REST API exposure and indexing pipeline |
| **Frontend UI** | React 19 / Vite / Node.js 18+ | Single-Page Application interface |
| **Audio Processing** | FFmpeg | Audio decoding and media format normalization |

---

## System Architecture Pipeline

The platform processes unstructured audio files through a two-stage pipeline: batch ASR processing followed by vector indexing for real-time querying.


```

+-----------------------------------------------------------------------------------+
|                              Audio Ingestion Pipeline                             |
|                                                                                   |
|  +--------------------+      +-----------------------+      +------------------+  |
|  | Call Audio (.mp3)  | ===> | OpenAI Whisper v3     | ===> | Timestamped Text |  |
|  |  (Telephony Input) |      | (CUDA GPU Engine)     |      |  (call.txt)      |  |
|  +--------------------+      +-----------------------+      +--------+---------+  |
+----------------------------------------------------------------------|------------+
|
+----------------------------------------------------------------------v------------+
|                              RAG Query Engine                                     |
|                                                                                   |
|  +--------------------+      +-----------------------+      +------------------+  |
|  | LlamaIndex Vector  | <=== | Gemini Embedding 001  | <=== | Transcript File  |  |
|  | Store Index        |      | Vectorization         |      | Parsing          |  |
|  +---------+----------+      +-----------------------+      +------------------+  |
|            |                                                                      |
|            v                                                                      |
|  +--------------------+      +-----------------------+      +------------------+  |
|  | User REST Query    | ===> | Gemini 2.5 Flash      | ===> | Grounded Context |  |
|  |  (/ask endpoint)   |      | LLM Synthesizer       |      | Response         |  |
|  +--------------------+      +-----------------------+      +------------------+  |
+-----------------------------------------------------------------------------------+

```

---

## Environment & System Prerequisites

* **Operating System**: Linux (Ubuntu 22.04 LTS recommended) or Windows 11
* **Runtime Environments**:
  * **Python**: Version 3.10 or higher
  * **Node.js**: Version 18.0 or higher
* **Media Dependencies**: **FFmpeg** installed and accessible via system `PATH`
* **Hardware Requirements**: NVIDIA GPU (e.g., RTX 3070/4070 series) with CUDA drivers configured for accelerated Whisper inference.
* **API Credentials**: Active **Google Gemini API Key** generated via Google AI Studio.

---

## Local Setup & Deployment Guide

### 1. Repository Setup & Environment Configuration
Clone the repository and prepare backend environment variables:

```bash
git clone [https://github.com/your-username/audiobot-ai.git](https://github.com/your-username/audiobot-ai.git)
cd audiobot-ai

```

Create a `.env` file inside the `backend/` directory:

```env
GEMINI_API_KEY=YOUR_GEMINI_API_KEY
TRANSCRIPTION_PATH=C:\audio\call.txt

```

### 2. Backend Environment Setup

Navigate to the `backend` directory, install required Python dependencies, and configure PyTorch with CUDA support:

```bash
cd backend
pip install -r requirements.txt

# (Required for NVIDIA CUDA GPU Acceleration)
pip install torch --index-url [https://download.pytorch.org/whl/cu126](https://download.pytorch.org/whl/cu126) --force-reinstall

```

### 3. Frontend Setup

Navigate to the `frontend` directory and install dependencies:

```bash
cd ../frontend
npm install

```

---

## Execution Runbook

The application operates in three sequential execution phases:

### Phase 1: Audio Transcription Execution

Place the target audio recording (e.g., `Grabacion.mp3`) in the designated directory and run the ASR extraction script:

```bash
cd backend
python LoadAudio.py

```

*Output: Generates `call.txt` containing timestamped speech-to-text segments.*

### Phase 2: Launch Backend Service

Start the Flask application server to initialize the vector index and expose REST endpoints:

```bash
cd backend
python app.py

```

*The API service will bind to `http://localhost:8090`.*

### Phase 3: Launch Frontend Application

In a separate terminal, launch the Vite development server:

```bash
cd frontend
npm run dev

```

*Access the interface by navigating to `http://localhost:5173`.*

---

## Production API Specification

The backend server exposes the following RESTful endpoints:

| Endpoint | Method | Query Parameters | Response Payload | Description |
| --- | --- | --- | --- | --- |
| `/status` | `GET` | *None* | `{"ready": true}` | Returns vector store index readiness state. |
| `/ask` | `GET` | `q` (string) | `{"answer": "..."}` | Queries the RAG index and returns grounded responses. |

---

## Localization & Security Guardrails

* **Language Specification**: The transcription pipeline is explicitly restricted to Spanish via `language="es"` within Whisper parameters to optimize accuracy and prevent code-switching errors.
* **System Prompt Isolation**: The LLM system prompt enforces strict bounds. If a query falls outside the semantic context of the call transcript, the service returns a standardized rejection:
> *"No puedo responder a esta pregunta porque la información no se encuentra en la transcripción de la llamada."*



---

## License & Attribution

* **License**: Open-source under the terms of the [MIT License](https://www.google.com/search?q=LICENSE).
* **Attribution**:
* [OpenAI Whisper](https://github.com/openai/whisper) — Automatic Speech Recognition
* [LlamaIndex](https://github.com/run-llama/llama_index) — Data Framework for LLM Applications
* [Google Gemini API](https://ai.google.dev/) — Multimodal Generative AI Framework



```

```
