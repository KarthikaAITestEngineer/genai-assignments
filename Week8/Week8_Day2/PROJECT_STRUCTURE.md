# Project Folder Structure – Quick Setup Commands

## Create Backend Structure

```bash
# From project root
mkdir -p backend/app/{api/v1,models,services/{ingestion,chunking,embedding,search,rag,comparison},middleware,utils,worker}
mkdir -p backend/tests
mkdir -p backend/uploads

# Create __init__.py files
touch backend/app/__init__.py
touch backend/app/api/__init__.py
touch backend/app/api/v1/__init__.py
touch backend/app/models/__init__.py
touch backend/app/services/__init__.py
touch backend/app/services/ingestion/__init__.py
touch backend/app/services/chunking/__init__.py
touch backend/app/services/embedding/__init__.py
touch backend/app/services/search/__init__.py
touch backend/app/services/rag/__init__.py
touch backend/app/services/comparison/__init__.py
touch backend/app/middleware/__init__.py
touch backend/app/utils/__init__.py
touch backend/app/worker/__init__.py
touch backend/tests/__init__.py
```

## Create Frontend Structure

```bash
# From project root
mkdir -p frontend/public
mkdir -p frontend/src/{assets,components/{common,layout,charts,documents,chunking,search,rag},pages/{Dashboard,Auth,Ingestion,ChunkingStudio,SearchStudio,RAG,Comparison,Admin},services,store,hooks,types,utils}
```

## Full Project Tree

```
HomeassignmentProject/
├── ARCHITECTURE.md
├── IMPLEMENTATION_BLUEPRINT.md
├── BACKEND_BLUEPRINT.md
├── FRONTEND_BLUEPRINT.md
├── PROJECT_STRUCTURE.md
├── docker-compose.yml
├── .gitignore
│
├── backend/
│   ├── app/
│   │   ├── __init__.py
│   │   ├── main.py
│   │   ├── config.py
│   │   ├── database.py
│   │   ├── api/
│   │   │   ├── __init__.py
│   │   │   ├── deps.py
│   │   │   └── v1/
│   │   │       ├── __init__.py
│   │   │       ├── router.py
│   │   │       ├── auth.py
│   │   │       ├── documents.py
│   │   │       ├── chunking.py
│   │   │       ├── embeddings.py
│   │   │       ├── search.py
│   │   │       ├── rag.py
│   │   │       ├── comparison.py
│   │   │       ├── analytics.py
│   │   │       └── admin.py
│   │   ├── models/
│   │   │   ├── __init__.py
│   │   │   ├── user.py
│   │   │   ├── document.py
│   │   │   ├── chunk.py
│   │   │   ├── search.py
│   │   │   ├── rag.py
│   │   │   └── analytics.py
│   │   ├── services/
│   │   │   ├── __init__.py
│   │   │   ├── auth_service.py
│   │   │   ├── document_service.py
│   │   │   ├── ingestion/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── pdf_parser.py
│   │   │   │   ├── docx_parser.py
│   │   │   │   ├── csv_parser.py
│   │   │   │   ├── web_crawler.py
│   │   │   │   ├── confluence.py
│   │   │   │   └── text_cleaner.py
│   │   │   ├── chunking/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── base.py
│   │   │   │   ├── fixed_size.py
│   │   │   │   ├── recursive.py
│   │   │   │   ├── semantic.py
│   │   │   │   ├── structure_based.py
│   │   │   │   ├── query_aware.py
│   │   │   │   ├── metadata_aware.py
│   │   │   │   ├── llm_based.py
│   │   │   │   └── agentic.py
│   │   │   ├── embedding/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── engine.py
│   │   │   │   ├── model_router.py
│   │   │   │   └── storage.py
│   │   │   ├── search/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── semantic_search.py
│   │   │   │   ├── keyword_search.py
│   │   │   │   ├── hybrid_search.py
│   │   │   │   └── reranker.py
│   │   │   ├── rag/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── chain.py
│   │   │   │   ├── agent.py
│   │   │   │   ├── summarizer.py
│   │   │   │   └── llm_router.py
│   │   │   └── comparison/
│   │   │       ├── __init__.py
│   │   │       └── analyzer.py
│   │   ├── middleware/
│   │   │   ├── __init__.py
│   │   │   ├── auth_middleware.py
│   │   │   ├── rbac.py
│   │   │   ├── audit.py
│   │   │   ├── rate_limit.py
│   │   │   └── cors.py
│   │   ├── utils/
│   │   │   ├── __init__.py
│   │   │   ├── security.py
│   │   │   ├── validators.py
│   │   │   └── helpers.py
│   │   └── worker/
│   │       ├── __init__.py
│   │       ├── celery_app.py
│   │       └── tasks.py
│   ├── tests/
│   │   ├── __init__.py
│   │   ├── conftest.py
│   │   ├── test_auth.py
│   │   ├── test_documents.py
│   │   ├── test_chunking.py
│   │   ├── test_search.py
│   │   └── test_rag.py
│   ├── uploads/
│   ├── requirements.txt
│   ├── Dockerfile
│   ├── .env.example
│   └── pyproject.toml
│
├── frontend/
│   ├── public/
│   │   ├── index.html
│   │   └── favicon.ico
│   ├── src/
│   │   ├── main.tsx
│   │   ├── App.tsx
│   │   ├── vite-env.d.ts
│   │   ├── index.css
│   │   ├── assets/
│   │   │   └── logo.svg
│   │   ├── components/
│   │   │   ├── common/
│   │   │   │   ├── Button.tsx
│   │   │   │   ├── Card.tsx
│   │   │   │   ├── Modal.tsx
│   │   │   │   ├── Input.tsx
│   │   │   │   ├── Select.tsx
│   │   │   │   ├── Badge.tsx
│   │   │   │   ├── Spinner.tsx
│   │   │   │   ├── EmptyState.tsx
│   │   │   │   ├── Pagination.tsx
│   │   │   │   └── FileUpload.tsx
│   │   │   ├── layout/
│   │   │   │   ├── AppLayout.tsx
│   │   │   │   ├── Sidebar.tsx
│   │   │   │   ├── Header.tsx
│   │   │   │   └── ProtectedRoute.tsx
│   │   │   ├── charts/
│   │   │   │   ├── BarChart.tsx
│   │   │   │   ├── LineChart.tsx
│   │   │   │   ├── PieChart.tsx
│   │   │   │   └── MetricCard.tsx
│   │   │   ├── documents/
│   │   │   │   ├── DocumentList.tsx
│   │   │   │   ├── DocumentCard.tsx
│   │   │   │   ├── DocumentDetail.tsx
│   │   │   │   └── UploadModal.tsx
│   │   │   ├── chunking/
│   │   │   │   ├── ChunkViewer.tsx
│   │   │   │   ├── ChunkStats.tsx
│   │   │   │   ├── StrategySelector.tsx
│   │   │   │   └── ChunkComparison.tsx
│   │   │   ├── search/
│   │   │   │   ├── SearchBar.tsx
│   │   │   │   ├── SearchResults.tsx
│   │   │   │   ├── ResultCard.tsx
│   │   │   │   └── SearchFilters.tsx
│   │   │   └── rag/
│   │   │       ├── ChatInterface.tsx
│   │   │       ├── ChatMessage.tsx
│   │   │       ├── SourceCitation.tsx
│   │   │       └── AgentSteps.tsx
│   │   ├── pages/
│   │   │   ├── Dashboard/
│   │   │   │   └── DashboardPage.tsx
│   │   │   ├── Auth/
│   │   │   │   ├── LoginPage.tsx
│   │   │   │   └── RegisterPage.tsx
│   │   │   ├── Ingestion/
│   │   │   │   ├── IngestionPage.tsx
│   │   │   │   └── WebCrawlPage.tsx
│   │   │   ├── ChunkingStudio/
│   │   │   │   └── ChunkingStudioPage.tsx
│   │   │   ├── SearchStudio/
│   │   │   │   └── SearchStudioPage.tsx
│   │   │   ├── RAG/
│   │   │   │   └── RAGPage.tsx
│   │   │   ├── Comparison/
│   │   │   │   └── ComparisonPage.tsx
│   │   │   └── Admin/
│   │   │       ├── UsersPage.tsx
│   │   │       └── AuditPage.tsx
│   │   ├── services/
│   │   │   ├── api.ts
│   │   │   ├── authService.ts
│   │   │   ├── documentService.ts
│   │   │   ├── chunkingService.ts
│   │   │   ├── searchService.ts
│   │   │   ├── ragService.ts
│   │   │   ├── comparisonService.ts
│   │   │   └── analyticsService.ts
│   │   ├── store/
│   │   │   ├── authStore.ts
│   │   │   ├── documentStore.ts
│   │   │   ├── searchStore.ts
│   │   │   └── uiStore.ts
│   │   ├── hooks/
│   │   │   ├── useAuth.ts
│   │   │   ├── useDocuments.ts
│   │   │   ├── useSearch.ts
│   │   │   └── useDebounce.ts
│   │   ├── types/
│   │   │   ├── auth.ts
│   │   │   ├── document.ts
│   │   │   ├── chunk.ts
│   │   │   ├── search.ts
│   │   │   ├── rag.ts
│   │   │   └── analytics.ts
│   │   └── utils/
│   │       ├── constants.ts
│   │       ├── formatters.ts
│   │       └── validators.ts
│   ├── tailwind.config.js
│   ├── postcss.config.js
│   ├── tsconfig.json
│   ├── vite.config.ts
│   ├── package.json
│   ├── Dockerfile
│   ├── nginx.conf
│   └── .env.example
│
└── docs/
    ├── api-reference.md
    ├── deployment-guide.md
    └── user-guide.md
```

## Docker Compose (Root Level)

```yaml
version: "3.8"

services:
  frontend:
    build: ./frontend
    ports:
      - "80:80"
    depends_on:
      - backend
    environment:
      - VITE_API_URL=http://localhost:8000/api/v1

  backend:
    build: ./backend
    ports:
      - "8000:8000"
    env_file:
      - ./backend/.env
    depends_on:
      - redis
    volumes:
      - ./backend/uploads:/app/uploads

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

  celery-worker:
    build: ./backend
    command: celery -A app.worker.celery_app worker --loglevel=info
    env_file:
      - ./backend/.env
    depends_on:
      - redis
      - backend

volumes:
  redis_data:
```

## Getting Started (Quick Start)

```bash
# 1. Clone and enter project
cd HomeassignmentProject

# 2. Backend setup
cd backend
python -m venv venv
venv\Scripts\activate        # Windows
pip install -r requirements.txt
cp .env.example .env         # Fill in your credentials
uvicorn app.main:app --reload --port 8000

# 3. Frontend setup (new terminal)
cd frontend
npm install
cp .env.example .env
npm run dev

# 4. Or use Docker Compose (from root)
docker-compose up --build
```
