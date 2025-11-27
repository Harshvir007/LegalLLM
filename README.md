# LegalLLM

RAG Application for College Law Department

This repository documents the architecture and implementation strategy for building a Retrieval-Augmented Generation (RAG) application for a college law department using Option A: OpenAI Assistants API + Vector Store (Chroma or Pinecone).
The goal is rapid development while ensuring reliable, citation-grounded legal answers.

1. Problem Definition

The application should assist law students and faculty by enabling:

Section-based lookup (IPC, CrPC, Constitution, Acts)

Definitions of legal terms and doctrines

Comparisons of legal concepts

Retrieval of case laws and precedents

Summaries of judgments and long documents

Q&A over study materials, circulars, notes, and guidelines

Access to structured and unstructured legal knowledge

The system must always answer based on the uploaded documents, with proper citations.

2. Knowledge Base Preparation

To deliver the application quickly, prioritize fast ingestion of available law department documents.

Supported Document Types

Bare Acts

Case briefs and judgments

Lecture notes and PPTs

Research papers

Department handbooks

Circulars and notifications

Past question papers

Preparation Steps

Extract text using PyMuPDF or the unstructured library.

Perform OCR only where necessary using Tesseract.

Normalize text:

Remove page numbers, headers, footers

Clean OCR noise

Ensure section headings (e.g., “Section 24”, “Article 14”) remain intact

Chunk documents using section-aware logic:

Prefer splitting along legally meaningful boundaries

Chunk size between 512–1024 tokens

Store metadata:

Act/Document name

Section/Article number

Page number

Category (Act, Judgment, Notes, etc.)

This ensures high-quality retrieval.

3. Architecture Overview (Option A)

Option A focuses on using the OpenAI Assistants API for the core reasoning and generation, with an external vector store for retrieval.

Components

LLM: GPT-4.1, GPT-5.1 or GPT-4o

Embeddings: text-embedding-3-large

Vector Store: Chroma (local) or Pinecone (managed)

Document Ingestion Pipeline: Python script to extract text, chunk, embed, and index

Assistant: Retrieval-enabled OpenAI Assistant using “retrieval” tool mode

Frontend: Streamlit / FastAPI + simple UI

Deployment: Railway / Render / Vercel

High-Level Flow

User enters query.

Assistant uses retrieval tool to query vector store.

Store returns relevant chunks.

Assistant generates grounded answer with citations.

This approach minimizes custom backend logic and maximizes speed of development.

4. Retrieval Strategy

Even though the Assistants API handles most orchestration, retrieval quality depends on indexing.

Recommended Setup

Use dense embeddings from OpenAI.

Use a vector similarity search via Chroma or Pinecone.

Configure top_k = 5–10 depending on document size.

Use metadata filters when possible (e.g., filter by Act).

Why Not Hybrid Retrieval Here

To keep the system minimal for rapid development, the focus is only on dense retrieval.
Hybrid retrieval (BM25 + reranker) is optional and not required for Option A.

5. Building the RAG Pipeline (Option A)
5.1 Document Ingestion Script

The ingestion script performs:

Text extraction

Cleaning

Chunking

Embedding using text-embedding-3-large

Indexing into Chroma/Pinecone

This script is executed manually or as a batch job whenever new documents are added.

5.2 Connecting Vector Store to Assistant

OpenAI Assistants allow specifying tools that the model can call.
The vector store becomes a retrieval tool, enabling:

Querying chunks

Returning source metadata

Providing grounded context

5.3 Generating Responses

When the user asks a question:

Assistant rewrites query (internally).

Assistant calls the retrieval tool.

Relevant chunks are returned.

Assistant uses these chunks to generate answer.

Final response includes citations and source metadata.

5.4 Legal Prompt Template

Used as the Assistant’s system message.

You are an AI legal assistant created for the college law department.
Use only the retrieved context provided through the retrieval tool.
Always include act names, section numbers, and case citations.
If the required information does not appear in the retrieved documents,
respond with: "No relevant information found in the provided knowledge base."

6. UI Layer

For fast delivery, use Streamlit.

Must-Have Features

Search bar or chat interface

Display answer with citations

Button to view the source PDF section

Side panel showing retrieved context

History panel

Backend complexity is minimal because the Assistants API handles the workflow.

7. Evaluation Procedure
Evaluation Metrics

Context precision

Context recall

Faithfulness of answer

Citation correctness

Retrieval accuracy per query category

Evaluation Data

Past exam questions

Vivas

Law faculty sample queries

Frequently asked student questions

This ensures the system is reliable for academic use.

8. Deployment
Recommended Approach

Backend: Python + Assistants API

UI: Streamlit deployed on Railway or Vercel

Vector Store:

Pinecone (recommended for stability)

Chroma if cost or offline constraints exist

Steps

Containerize with Docker (optional but recommended).

Push to GitHub.

Deploy using CI/CD or directly via cloud platform.

Final Architecture Summary
Final Recommended Stack for Option A

OpenAI Assistants API with retrieval tool

Embeddings: text-embedding-3-large

Vector database: Pinecone or Chroma

Document ingestion pipeline in Python

Streamlit UI

Railway or Vercel deployment

This combination allows the entire system to be built, tested, and deployed in a very short time, while maintaining high accuracy and strong grounding in legal documents.

Model Architecture

<img width="680" height="596" alt="image" src="https://github.com/user-attachments/assets/a69a633d-fd71-4a0d-bb4a-115de581780c" />

<img width="1400" height="630" alt="image" src="https://github.com/user-attachments/assets/3df484f6-fc56-428e-a324-4042b5253938" />

