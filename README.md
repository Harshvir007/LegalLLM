# LegalLLM ⚖️

**RAG Application for College Law Department**

[![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://www.python.org/)
[![OpenAI](https://img.shields.io/badge/OpenAI-Assistants%20API-green.svg)](https://openai.com/)
[![Streamlit](https://img.shields.io/badge/Frontend-Streamlit-red.svg)](https://streamlit.io/)
[![Vector Store](https://img.shields.io/badge/Vector%20Store-Pinecone%20%2F%20Chroma-orange.svg)](https://www.pinecone.io/)

## 📖 Overview

**LegalLLM** is a Retrieval-Augmented Generation (RAG) application designed to assist law students and faculty. It leverages **Option A: OpenAI Assistants API + Vector Store** to provide reliable, citation-grounded legal answers.

The primary goal of this project is rapid development without compromising on accuracy. The system retrieves information strictly from a curated knowledge base of legal documents, ensuring no hallucinations and providing precise source citations.

---

## 🏗 Architecture

### System Design
The architecture focuses on using the OpenAI Assistants API for core reasoning and generation, paired with an external vector store for high-fidelity retrieval.

**Model Architecture Diagrams:**

<img width="680" alt="Model Architecture" src="https://github.com/user-attachments/assets/a69a633d-fd71-4a0d-bb4a-115de581780c" />

<br>

<img width="100%" alt="Detailed Flow" src="https://github.com/user-attachments/assets/3df484f6-fc56-428e-a324-4042b5253938" />

### Technical Stack 

| Component | Technology Used |
| :--- | :--- |
| **LLM** | GPT-4.1, GPT-5.1, or GPT-4o |
| **Embeddings** | `text-embedding-3-large` |
| **Vector Store** | Chroma (Local) or Pinecone (Managed) |
| **Orchestration** | OpenAI Assistants API (Retrieval Tool) |
| **Frontend** | Streamlit |
| **Deployment** | Railway / Render / Vercel |

---

## ✨ Key Features

The application solves specific academic needs by enabling:
* **Section-based Lookup:** Query specific IPC, CrPC, Constitution, and Acts.
* **Concept Definitions:** Clear explanations of legal terms and doctrines.
* **Case Retrieval:** Access to precedents and case laws.
* **Summarization:** Summaries of complex judgments and long documents.
* **Q&A:** Interactive queries over study materials, circulars, and notes.
* **Strict Grounding:** Answers are always based on uploaded documents with citations.

---

## 📂 Knowledge Base Preparation

To ensure high-quality retrieval, the document ingestion pipeline follows these steps:

### 1. Supported Document Types
* Bare Acts
* Case Briefs & Judgments
* Lecture Notes & PPTs
* Research Papers & Handbooks
* Past Question Papers & Circulars

### 2. Ingestion Pipeline
1.  **Extraction:** Text is extracted using `PyMuPDF` (or `Unstructured`). OCR via Tesseract is used only when necessary.
2.  **Normalization:** Removal of headers, footers, and page numbers. Cleaning of OCR noise.
3.  **Chunking:**
    * **Strategy:** Section-aware splitting (keeping "Section 24" or "Article 14" intact).
    * **Size:** 512–1024 tokens per chunk.
4.  **Metadata Tagging:**
    * `Act/Document Name`
    * `Section/Article Number`
    * `Page Number`
    * `Category` (Act, Judgment, Notes)

---

## ⚙️ Implementation Strategy

### 1. Retrieval Strategy
* **Embeddings:** Dense embeddings using OpenAI's `text-embedding-3-large`.
* **Search:** Vector similarity search (Top K = 5–10).
* **Filtering:** Metadata filters applied where possible (e.g., filtering by specific Act).
* *Note: Hybrid retrieval (BM25) is omitted in this version to prioritize rapid development.*

### 2. Assistant Workflow
1.  **User Query:** The user asks a question via the UI.
2.  **Retrieval:** The Assistant calls the vector store as a "Tool."
3.  **Generation:** Relevant chunks are retrieved, and the Assistant generates an answer.
4.  **Citation:** The final response includes source metadata (Act name, Section, Page).

### 3. System Prompt
The following system message is used to strictly control the Assistant's behavior:

```text
You are an AI legal assistant created for the college law department.
Use only the retrieved context provided through the retrieval tool.
Always include act names, section numbers, and case citations.
If the required information does not appear in the retrieved documents,
respond with: "No relevant information found in the provided knowledge base."
