# Enterprise RAG and Knowledge Intelligence Platform – Architecture

## 1. High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           FRONTEND (React + TypeScript + Tailwind)           │
│                                                                             │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────────────┐  │
│  │Dashboard │ │Ingestion │ │Chunking  │ │Search    │ │Document          │  │
│  │Module    │ │Module    │ │Studio    │ │Studio    │ │Comparison Module │  │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘ └──────────────────┘  │
└─────────────────────────────────┬───────────────────────────────────────────┘
                                  │ HTTPS / JWT
                                  ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         API GATEWAY & AUTH LAYER                             │
│                    (FastAPI + JWT + RBAC Middleware)                          │
└─────────────────────────────────┬───────────────────────────────────────────┘
                                  │
         ┌────────────────────────┼────────────────────────┐
         ▼                        ▼                        ▼
┌─────────────────┐  ┌─────────────────────┐  ┌─────────────────────────────┐
│ INGESTION       │  │ INTELLIGENCE        │  │ RETRIEVAL & AI LAYER        │
│ SERVICE         │  │ SERVICE             │  │                             │
│                 │  │                     │  │ ┌─────────────────────────┐ │
│ • PDF Parser    │  │ • Chunking Engine   │  │ │ Search Engine           │ │
│ • DOCX Parser   │  │ • Embedding Engine  │  │ │ • Semantic Search       │ │
│ • Web Crawler   │  │ • Document Analyzer │  │ │ • Keyword Search        │ │
│ • Confluence    │  │ • Metadata Engine   │  │ │ • Hybrid Retrieval      │ │
│   Connector     │  │                     │  │ └─────────────────────────┘ │
│ • CSV Parser    │  │                     │  │ ┌─────────────────────────┐ │
│ • Text Cleaner  │  │                     │  │ │ RAG Pipeline            │ │
└────────┬────────┘  └──────────┬──────────┘  │ │ • LangChain Chains     │ │
         │                      │             │ │ • LangGraph Agents      │ │
         │                      │             │ │ • Groq LLM             │ │
         │                      │             │ └─────────────────────────┘ │
         │                      │             │ ┌─────────────────────────┐ │
         │                      │             │ │ Agentic Workflow        │ │
         │                      │             │ │ • Query Planner         │ │
         │                      │             │ │ • Multi-Step Retriever  │ │
         │                      │             │ │ • Answer Synthesizer    │ │
         │                      │             │ └─────────────────────────┘ │
         │                      │             └──────────────┬──────────────┘
         │                      │                            │
         ▼                      ▼                            ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                        MONGODB ATLAS (Data Layer)                            │
│                                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐   │
│  │ documents    │  │ chunks       │  │ embeddings   │  │ users &      │   │
│  │ collection   │  │ collection   │  │ (vector      │  │ audit_logs   │   │
│  │              │  │              │  │  search)     │  │              │   │
│  └──────────────┘  └──────────────┘  └──────────────┘  └──────────────┘   │
└─────────────────────────────────────────────────────────────────────────────┘
```

## 2. Component Architecture

### 2.1 Frontend Architecture

```
frontend/
├── src/
│   ├── components/          # Reusable UI components
│   │   ├── common/          # Buttons, Cards, Modals
│   │   ├── charts/          # Visualization components
│   │   └── layout/          # Navigation, Sidebar, Header
│   ├── pages/
│   │   ├── Dashboard/       # Analytics dashboard
│   │   ├── Ingestion/       # File upload, web crawl, confluence
│   │   ├── ChunkingStudio/  # Chunking config & visualization
│   │   ├── SearchStudio/    # Search interface
│   │   ├── RAG/             # Question answering
│   │   ├── Comparison/      # Document comparison
│   │   └── Auth/            # Login, Register
│   ├── services/            # API client services
│   ├── store/               # State management (Zustand/Redux)
│   ├── hooks/               # Custom React hooks
│   ├── types/               # TypeScript interfaces
│   └── utils/               # Utility functions
```

### 2.2 Backend Architecture

```
backend/
├── app/
│   ├── api/
│   │   ├── routes/
│   │   │   ├── auth.py           # Authentication endpoints
│   │   │   ├── ingestion.py      # Document ingestion endpoints
│   │   │   ├── chunking.py       # Chunking endpoints
│   │   │   ├── search.py         # Search endpoints
│   │   │   ├── rag.py            # RAG Q&A endpoints
│   │   │   ├── comparison.py     # Document comparison endpoints
│   │   │   └── analytics.py      # Dashboard analytics endpoints
│   │   └── middleware/
│   │       ├── auth.py           # JWT verification
│   │       ├── rbac.py           # Role-based access
│   │       └── audit.py          # Audit logging
│   ├── services/
│   │   ├── ingestion/
│   │   │   ├── pdf_parser.py
│   │   │   ├── docx_parser.py
│   │   │   ├── web_crawler.py
│   │   │   ├── confluence_connector.py
│   │   │   └── text_cleaner.py
│   │   ├── chunking/
│   │   │   ├── fixed_size.py
│   │   │   ├── recursive.py
│   │   │   ├── semantic.py
│   │   │   ├── structure_based.py
│   │   │   ├── query_aware.py
│   │   │   ├── metadata_aware.py
│   │   │   ├── llm_based.py
│   │   │   └── agentic.py
│   │   ├── embedding/
│   │   │   ├── engine.py         # Sentence Transformers
│   │   │   └── storage.py        # MongoDB vector storage
│   │   ├── search/
│   │   │   ├── semantic.py       # Vector search
│   │   │   ├── keyword.py        # Text search
│   │   │   ├── hybrid.py         # Combined retrieval
│   │   │   └── reranker.py       # Result ranking
│   │   ├── rag/
│   │   │   ├── chain.py          # LangChain RAG chain
│   │   │   ├── agent.py          # LangGraph agent
│   │   │   └── summarizer.py     # Summary generation
│   │   └── comparison/
│   │       └── analyzer.py       # Document comparison logic
│   ├── models/                   # Pydantic models
│   ├── database/                 # MongoDB connection & schemas
│   └── config/                   # App configuration
```

### 2.3 AI/ML Pipeline Architecture

```
┌────────────────────────────────────────────────────────────┐
│                    AI PIPELINE                              │
│                                                            │
│  ┌──────────┐    ┌──────────┐    ┌──────────────────────┐ │
│  │ Document │───▶│ Chunking │───▶│ Embedding Generation │ │
│  │ Parsing  │    │ Engine   │    │ (Sentence            │ │
│  │          │    │          │    │  Transformers)       │ │
│  └──────────┘    └──────────┘    └──────────┬───────────┘ │
│                                             │             │
│                                             ▼             │
│  ┌──────────────────────────────────────────────────────┐ │
│  │              MongoDB Atlas Vector Store               │ │
│  └──────────────────────────────┬───────────────────────┘ │
│                                 │                         │
│           ┌─────────────────────┼─────────────────┐       │
│           ▼                     ▼                 ▼       │
│  ┌──────────────┐  ┌──────────────────┐  ┌────────────┐  │
│  │ Semantic     │  │ Hybrid           │  │ Keyword    │  │
│  │ Search       │  │ Retrieval        │  │ Search     │  │
│  └──────┬───────┘  └────────┬─────────┘  └─────┬──────┘  │
│         │                   │                   │         │
│         └───────────────────┼───────────────────┘         │
│                             ▼                             │
│  ┌──────────────────────────────────────────────────────┐ │
│  │              LangGraph Agentic Workflow                │ │
│  │                                                       │ │
│  │  ┌─────────┐   ┌──────────┐   ┌───────────────────┐  │ │
│  │  │ Query   │──▶│ Retrieve │──▶│ Generate Answer   │  │ │
│  │  │ Planner │   │ & Rank   │   │ (Groq LLM)       │  │ │
│  │  └─────────┘   └──────────┘   └───────────────────┘  │ │
│  └──────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────┘
```

## 3. Data Flow Architecture

```
                    INGESTION FLOW
                    ─────────────
User Upload ──▶ File Parser ──▶ Text Extraction ──▶ Cleaning
     │                                                  │
     ▼                                                  ▼
Web URL ────▶ Web Crawler ──▶ Content Extraction ──▶ Cleaning
     │                                                  │
     ▼                                                  ▼
Confluence ─▶ API Connector ──▶ Page Extraction ───▶ Cleaning
                                                       │
                                                       ▼
                                              ┌─────────────────┐
                                              │ Document Store   │
                                              │ (MongoDB)        │
                                              └────────┬────────┘
                                                       │
                                                       ▼
                    PROCESSING FLOW
                    ───────────────
                    ┌─────────────────┐
                    │ Chunking Engine  │
                    │ (8 Strategies)   │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │ Embedding Engine │
                    │ (Sentence Trans.)│
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │ Vector Store    │
                    │ (MongoDB Atlas) │
                    └────────┬────────┘
                             │
                             ▼
                    RETRIEVAL FLOW
                    ─────────────
            User Query ──▶ Query Understanding
                                   │
                    ┌──────────────┼──────────────┐
                    ▼              ▼              ▼
              Semantic       Keyword        Hybrid
              Search         Search         Search
                    │              │              │
                    └──────────────┼──────────────┘
                                   ▼
                           Result Fusion & Ranking
                                   │
                                   ▼
                           RAG Generation (Groq)
                                   │
                                   ▼
                           Response + Sources
```

## 4. Database Schema Design

### 4.1 Collections

```
documents {
  _id: ObjectId,
  title: String,
  source_type: "pdf" | "docx" | "txt" | "csv" | "web" | "confluence",
  source_url: String (optional),
  raw_text: String,
  metadata: {
    file_size: Number,
    page_count: Number,
    word_count: Number,
    language: String,
    upload_date: DateTime,
    uploaded_by: ObjectId (ref: users)
  },
  status: "processing" | "ready" | "error",
  created_at: DateTime,
  updated_at: DateTime
}

chunks {
  _id: ObjectId,
  document_id: ObjectId (ref: documents),
  content: String,
  chunk_index: Number,
  chunking_strategy: String,
  metadata: {
    start_char: Number,
    end_char: Number,
    token_count: Number,
    page_number: Number,
    section_title: String,
    overlap_tokens: Number
  },
  embedding: [Float] (768-dim vector),
  created_at: DateTime
}

users {
  _id: ObjectId,
  email: String,
  password_hash: String,
  role: "admin" | "editor" | "viewer",
  name: String,
  created_at: DateTime,
  last_login: DateTime
}

audit_logs {
  _id: ObjectId,
  user_id: ObjectId (ref: users),
  action: String,
  resource: String,
  details: Object,
  ip_address: String,
  timestamp: DateTime
}

search_analytics {
  _id: ObjectId,
  query: String,
  search_type: "semantic" | "keyword" | "hybrid",
  results_count: Number,
  user_id: ObjectId,
  response_time_ms: Number,
  timestamp: DateTime
}
```

### 4.2 MongoDB Atlas Vector Search Index

```json
{
  "name": "vector_search_index",
  "type": "vectorSearch",
  "definition": {
    "fields": [
      {
        "type": "vector",
        "path": "embedding",
        "numDimensions": 768,
        "similarity": "cosine"
      },
      {
        "type": "filter",
        "path": "document_id"
      },
      {
        "type": "filter",
        "path": "chunking_strategy"
      }
    ]
  }
}
```

## 5. Security Architecture

```
┌─────────────────────────────────────────────────┐
│              SECURITY LAYERS                     │
│                                                 │
│  Layer 1: Transport Security (HTTPS/TLS)        │
│  Layer 2: JWT Authentication                    │
│  Layer 3: Role-Based Access Control (RBAC)      │
│  Layer 4: API Rate Limiting                     │
│  Layer 5: Input Validation & Sanitization       │
│  Layer 6: Audit Logging                         │
│  Layer 7: MongoDB Atlas Security (Encryption)   │
└─────────────────────────────────────────────────┘

Roles:
  admin  → Full access (CRUD all resources, manage users)
  editor → Upload documents, run searches, manage own content
  viewer → Read-only access, search, Q&A
```

## 6. Technology Stack Summary

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Frontend | React 18 + TypeScript | UI Framework |
| Styling | Tailwind CSS | Responsive Design |
| State | Zustand | Client State Management |
| Backend | FastAPI (Python 3.11+) | REST API |
| Auth | python-jose + passlib | JWT + Password Hashing |
| AI Framework | LangChain + LangGraph | RAG & Agents |
| Embeddings | Sentence Transformers (all-MiniLM-L6-v2) | Vector Generation |
| LLM | Groq (Llama 3 / Mixtral) | Text Generation |
| Database | MongoDB Atlas | Document + Vector Store |
| Web Crawling | BeautifulSoup + httpx | Website Ingestion |
| PDF Parsing | PyMuPDF (fitz) | PDF Text Extraction |
| DOCX Parsing | python-docx | Word File Parsing |
| Deployment | Docker + Docker Compose | Containerization |
