# Advanced Multimodal RAG Architecture

A scalable **Retrieval-Augmented Generation (RAG)** system designed to process multimodal knowledge sources and provide secure, domain-specific question answering.

## Overview

The system consists of two independent pipelines:

* **Data Ingestion & Synchronization Pipeline** — processes incoming knowledge-base files, converts them into searchable representations, and stores them in a vector database.
* **Secured Inference & Query Pipeline** — validates user queries, retrieves relevant knowledge, and generates responses strictly from the retrieved context.

This decoupled architecture improves modularity, scalability, and maintainability.

## Key Features

* **Multimodal Data Processing** — supports text, PDFs, structured data, and images.
* **Semantic Chunking** — splits unstructured documents into meaningful chunks for retrieval.
* **Vector-Based Retrieval** — converts processed content into embeddings and performs similarity-based retrieval.
* **Metadata Management** — associates retrieved content with source information such as filename, file type, URL, and timestamp.
* **LLM-Powered Image Understanding** — converts visual information into descriptive text and metadata for retrieval.
* **Domain Guardrails** — restricts the system to its designated knowledge domain.
* **Prompt Injection Protection** — rejects attempts to override system instructions or bypass the knowledge scope.
* **Grounded Responses** — responses are generated using retrieved context rather than unsupported information.

## Architecture

```text
                    ┌─────────────────────────┐
                    │     Knowledge Sources   │
                    │ PDF | TXT | CSV | Image │
                    └────────────┬────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │   Pipeline A: Ingestion │
                    │                         │
                    │ File Detection          │
                    │ Preprocessing            │
                    │ Chunking                 │
                    │ Image Understanding      │
                    │ Embedding Generation     │
                    └────────────┬────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │     Vector Database     │
                    │   Embeddings + Metadata │
                    └────────────┬────────────┘
                                 │
                                 ▼
User Query ────────►┌─────────────────────────┐
                    │ Pipeline B: Inference   │
                    │                         │
                    │ Query Guardrail         │
                    │ Query Embedding         │
                    │ Similarity Retrieval    │
                    │ Context + Query → LLM   │
                    └────────────┬────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │   Grounded Response     │
                    └─────────────────────────┘
```

## Pipeline A — Data Ingestion

The ingestion pipeline handles newly added knowledge-base files.

### Workflow

1. Detect incoming files from the configured repository.
2. Identify the file type.
3. Preprocess the content according to its format.
4. Split unstructured documents into semantic chunks.
5. Transform structured data into retrieval-friendly representations.
6. Process images using a vision-capable LLM to generate descriptions and metadata.
7. Generate embeddings for the processed content.
8. Store embeddings and metadata in the vector database.

### Supported Data

* `.txt`
* `.pdf`
* `.csv`
* `.png`
* `.jpg`

## Pipeline B — Secured Inference

The inference pipeline handles user questions and generates knowledge-grounded responses.

### Workflow

1. Receive a user query through a webhook or chat interface.
2. Pass the query through a **scope/guardrail layer**.
3. Reject queries that fall outside the designated knowledge domain.
4. Generate an embedding for valid queries.
5. Retrieve the most relevant chunks from the vector database.
6. Provide the retrieved context and original query to the LLM.
7. Generate a response based only on the retrieved information.

If the required information is not present in the retrieved context, the system is instructed to clearly indicate that the information is unavailable rather than generating an unsupported answer.

## Security & Guardrails

The system incorporates multiple layers of protection:

### Domain Restriction

Queries unrelated to the configured knowledge base are rejected before retrieval.

### Prompt Injection Defense

The inference pipeline is designed to prevent user instructions from overriding the system's response constraints.

### Grounded Generation

The LLM is instructed to use only the retrieved context when generating an answer.

### Hallucination Reduction

When the retrieved context does not contain sufficient information, the system is instructed not to fabricate an answer.

## Technology Stack

* **Workflow Automation:** n8n
* **LLMs:** LLM / Vision LLM
* **Retrieval:** Embeddings + Vector Similarity Search
* **Data Sources:** Google Drive / Knowledge Repository
* **Data Types:** Text, PDF, CSV, Images
* **Integration:** REST APIs, Webhooks
* **Vector Storage:** Vector Database

## Testing

The inference pipeline was designed to handle three categories of queries:

| Test Case        | Description                              | Expected Behavior                   |
| ---------------- | ---------------------------------------- | ----------------------------------- |
| In-Domain        | Query related to stored knowledge        | Generate grounded answer            |
| Out-of-Domain    | Unrelated coding/general query           | Reject query                        |
| Prompt Injection | Attempts to override system instructions | Reject or enforce system guardrails |

## Project Outcome

The project demonstrates how **RAG, workflow automation, multimodal processing, vector retrieval, and LLM security mechanisms** can be combined to build a reliable knowledge-grounded AI system.
The decoupled architecture allows the ingestion and inference workflows to operate independently, making the system easier to maintain and extend.

### Areas Explored

* Retrieval-Augmented Generation
* Generative AI
* LLM orchestration
* Multimodal AI
* Vector databases
* Workflow automation
* Prompt engineering
* AI guardrails
* REST APIs and webhooks
