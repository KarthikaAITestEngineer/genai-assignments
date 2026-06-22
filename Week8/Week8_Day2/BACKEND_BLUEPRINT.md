# Backend Implementation Blueprint

## Enterprise RAG Platform – FastAPI Backend

---

## 1. Project Structure

```
backend/
├── app/
│   ├── __init__.py
│   ├── main.py                      # FastAPI app entry point
│   ├── config.py                    # Settings & environment config
│   ├── database.py                  # MongoDB connection manager
│   │
│   ├── api/
│   │   ├── __init__.py
│   │   ├── deps.py                  # Shared dependencies (get_db, get_current_user)
│   │   └── v1/
│   │       ├── __init__.py
│   │       ├── router.py            # Main API router aggregator
│   │       ├── auth.py              # Auth endpoints
│   │       ├── documents.py         # Document ingestion endpoints
│   │       ├── chunking.py          # Chunking endpoints
│   │       ├── embeddings.py        # Embedding endpoints
│   │       ├── search.py            # Search endpoints
│   │       ├── rag.py               # RAG Q&A endpoints
│   │       ├── comparison.py        # Document comparison endpoints
│   │       ├── analytics.py         # Analytics endpoints
│   │       └── admin.py             # Admin endpoints
│   │
│   ├── models/
│   │   ├── __init__.py
│   │   ├── user.py                  # User Pydantic models
│   │   ├── document.py              # Document models
│   │   ├── chunk.py                 # Chunk models
│   │   ├── search.py                # Search request/response models
│   │   ├── rag.py                   # RAG models
│   │   └── analytics.py             # Analytics models
│   │
│   ├── services/
│   │   ├── __init__.py
│   │   ├── auth_service.py          # Auth business logic
│   │   ├── document_service.py      # Document CRUD & processing
│   │   │
│   │   ├── ingestion/
│   │   │   ├── __init__.py
│   │   │   ├── pdf_parser.py        # PDF text extraction
│   │   │   ├── docx_parser.py       # DOCX text extraction
│   │   │   ├── csv_parser.py        # CSV parsing
│   │   │   ├── web_crawler.py       # Website crawling
│   │   │   ├── confluence.py        # Confluence connector
│   │   │   └── text_cleaner.py      # Text normalization
│   │   │
│   │   ├── chunking/
│   │   │   ├── __init__.py
│   │   │   ├── base.py              # Base chunking interface
│   │   │   ├── fixed_size.py        # Fixed size chunking
│   │   │   ├── recursive.py         # Recursive text splitter
│   │   │   ├── semantic.py          # Semantic chunking
│   │   │   ├── structure_based.py   # Structure-aware chunking
│   │   │   ├── query_aware.py       # Query-aware chunking
│   │   │   ├── metadata_aware.py    # Metadata-aware chunking
│   │   │   ├── llm_based.py         # LLM-powered chunking
│   │   │   └── agentic.py           # Agentic chunking
│   │   │
│   │   ├── embedding/
│   │   │   ├── __init__.py
│   │   │   ├── engine.py            # Embedding generation
│   │   │   ├── model_router.py      # Route docs to appropriate model
│   │   │   └── storage.py           # Vector storage in MongoDB
│   │   │
│   │   ├── search/
│   │   │   ├── __init__.py
│   │   │   ├── semantic_search.py   # Vector similarity search
│   │   │   ├── keyword_search.py    # Text-based search
│   │   │   ├── hybrid_search.py     # Combined retrieval
│   │   │   └── reranker.py          # Result re-ranking
│   │   │
│   │   ├── rag/
│   │   │   ├── __init__.py
│   │   │   ├── chain.py             # LangChain RAG chain
│   │   │   ├── agent.py             # LangGraph agentic workflow
│   │   │   ├── summarizer.py        # Document summarization
│   │   │   └── llm_router.py        # Groq model routing
│   │   │
│   │   └── comparison/
│   │       ├── __init__.py
│   │       └── analyzer.py          # Document comparison logic
│   │
│   ├── middleware/
│   │   ├── __init__.py
│   │   ├── auth_middleware.py       # JWT validation
│   │   ├── rbac.py                  # Role-based access control
│   │   ├── audit.py                 # Audit logging middleware
│   │   ├── rate_limit.py            # API rate limiting
│   │   └── cors.py                  # CORS configuration
│   │
│   ├── utils/
│   │   ├── __init__.py
│   │   ├── security.py              # Password hashing, token generation
│   │   ├── validators.py            # Input validation helpers
│   │   └── helpers.py               # General utility functions
│   │
│   └── worker/
│       ├── __init__.py
│       ├── celery_app.py            # Celery configuration
│       └── tasks.py                 # Background tasks (chunking, embedding)
│
├── tests/
│   ├── __init__.py
│   ├── conftest.py                  # Test fixtures
│   ├── test_auth.py
│   ├── test_documents.py
│   ├── test_chunking.py
│   ├── test_search.py
│   └── test_rag.py
│
├── requirements.txt
├── Dockerfile
├── .env.example
└── pyproject.toml
```

---

## 2. Phase 1 Implementation: Foundation

### 2.1 Configuration (`app/config.py`)

```python
from pydantic_settings import BaseSettings
from typing import Optional


class Settings(BaseSettings):
    # App
    APP_NAME: str = "Enterprise RAG Platform"
    APP_VERSION: str = "1.0.0"
    DEBUG: bool = False
    API_V1_PREFIX: str = "/api/v1"

    # MongoDB
    MONGODB_URL: str
    MONGODB_DB_NAME: str = "enterprise_rag"

    # JWT
    JWT_SECRET_KEY: str
    JWT_ALGORITHM: str = "HS256"
    JWT_ACCESS_TOKEN_EXPIRE_MINUTES: int = 15
    JWT_REFRESH_TOKEN_EXPIRE_DAYS: int = 7

    # Groq
    GROQ_API_KEY: str
    GROQ_MODEL_FAST: str = "llama3-8b-8192"
    GROQ_MODEL_QUALITY: str = "llama3-70b-8192"
    GROQ_MODEL_REASONING: str = "mixtral-8x7b-32768"

    # Redis
    REDIS_URL: str = "redis://localhost:6379"

    # File Upload
    MAX_FILE_SIZE_MB: int = 50
    ALLOWED_EXTENSIONS: list = ["pdf", "docx", "txt", "csv"]

    # Embedding
    EMBEDDING_MODEL_DEFAULT: str = "all-mpnet-base-v2"
    EMBEDDING_MODEL_FAST: str = "all-MiniLM-L6-v2"

    # Rate Limiting
    RATE_LIMIT_PER_MINUTE: int = 100

    # CORS
    CORS_ORIGINS: list = ["http://localhost:3000"]

    class Config:
        env_file = ".env"
        case_sensitive = True


settings = Settings()
```

### 2.2 Application Entry (`app/main.py`)

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from contextlib import asynccontextmanager

from app.config import settings
from app.database import connect_db, close_db
from app.api.v1.router import api_router
from app.middleware.rate_limit import RateLimitMiddleware
from app.middleware.audit import AuditMiddleware


@asynccontextmanager
async def lifespan(app: FastAPI):
    # Startup
    await connect_db()
    yield
    # Shutdown
    await close_db()


app = FastAPI(
    title=settings.APP_NAME,
    version=settings.APP_VERSION,
    lifespan=lifespan,
)

# CORS
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.CORS_ORIGINS,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Custom Middleware
app.add_middleware(RateLimitMiddleware)
app.add_middleware(AuditMiddleware)

# Routes
app.include_router(api_router, prefix=settings.API_V1_PREFIX)


@app.get("/api/v1/health")
async def health_check():
    return {"status": "healthy", "version": settings.APP_VERSION}
```

### 2.3 Database Connection (`app/database.py`)

```python
from motor.motor_asyncio import AsyncIOMotorClient
from app.config import settings

client: AsyncIOMotorClient = None
db = None


async def connect_db():
    global client, db
    client = AsyncIOMotorClient(settings.MONGODB_URL)
    db = client[settings.MONGODB_DB_NAME]
    # Verify connection
    await client.admin.command("ping")


async def close_db():
    global client
    if client:
        client.close()


def get_database():
    return db
```

### 2.4 Authentication Service (`app/services/auth_service.py`)

```python
from datetime import datetime, timedelta
from typing import Optional
from jose import JWTError, jwt
from passlib.context import CryptContext
from bson import ObjectId

from app.config import settings
from app.database import get_database
from app.models.user import UserCreate, UserInDB, TokenData

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")


def hash_password(password: str) -> str:
    return pwd_context.hash(password)


def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)


def create_access_token(data: dict, expires_delta: Optional[timedelta] = None) -> str:
    to_encode = data.copy()
    expire = datetime.utcnow() + (
        expires_delta or timedelta(minutes=settings.JWT_ACCESS_TOKEN_EXPIRE_MINUTES)
    )
    to_encode.update({"exp": expire, "type": "access"})
    return jwt.encode(to_encode, settings.JWT_SECRET_KEY, algorithm=settings.JWT_ALGORITHM)


def create_refresh_token(data: dict) -> str:
    to_encode = data.copy()
    expire = datetime.utcnow() + timedelta(days=settings.JWT_REFRESH_TOKEN_EXPIRE_DAYS)
    to_encode.update({"exp": expire, "type": "refresh"})
    return jwt.encode(to_encode, settings.JWT_SECRET_KEY, algorithm=settings.JWT_ALGORITHM)


def decode_token(token: str) -> Optional[TokenData]:
    try:
        payload = jwt.decode(token, settings.JWT_SECRET_KEY, algorithms=[settings.JWT_ALGORITHM])
        user_id: str = payload.get("sub")
        if user_id is None:
            return None
        return TokenData(user_id=user_id, role=payload.get("role"))
    except JWTError:
        return None


async def register_user(user_data: UserCreate) -> UserInDB:
    db = get_database()
    # Check existing user
    existing = await db.users.find_one({"email": user_data.email})
    if existing:
        raise ValueError("User with this email already exists")

    user_doc = {
        "email": user_data.email,
        "password_hash": hash_password(user_data.password),
        "name": user_data.name,
        "role": "viewer",  # Default role
        "auth_provider": "local",
        "is_active": True,
        "consent_gdpr": user_data.consent_gdpr,
        "consent_date": datetime.utcnow() if user_data.consent_gdpr else None,
        "created_at": datetime.utcnow(),
        "updated_at": datetime.utcnow(),
        "last_login": None,
    }
    result = await db.users.insert_one(user_doc)
    user_doc["_id"] = result.inserted_id
    return UserInDB(**user_doc)


async def authenticate_user(email: str, password: str) -> Optional[UserInDB]:
    db = get_database()
    user = await db.users.find_one({"email": email})
    if not user or not verify_password(password, user["password_hash"]):
        return None
    # Update last login
    await db.users.update_one(
        {"_id": user["_id"]},
        {"$set": {"last_login": datetime.utcnow()}}
    )
    return UserInDB(**user)
```

### 2.5 Auth Endpoints (`app/api/v1/auth.py`)

```python
from fastapi import APIRouter, Depends, HTTPException, status
from app.models.user import UserCreate, UserLogin, UserResponse, TokenResponse
from app.services.auth_service import (
    register_user, authenticate_user,
    create_access_token, create_refresh_token, decode_token,
)
from app.api.deps import get_current_user

router = APIRouter(prefix="/auth", tags=["Authentication"])


@router.post("/register", response_model=UserResponse, status_code=status.HTTP_201_CREATED)
async def register(user_data: UserCreate):
    try:
        user = await register_user(user_data)
        return UserResponse(
            id=str(user.id),
            email=user.email,
            name=user.name,
            role=user.role,
        )
    except ValueError as e:
        raise HTTPException(status_code=status.HTTP_409_CONFLICT, detail=str(e))


@router.post("/login", response_model=TokenResponse)
async def login(credentials: UserLogin):
    user = await authenticate_user(credentials.email, credentials.password)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid email or password",
        )
    token_data = {"sub": str(user.id), "role": user.role}
    return TokenResponse(
        access_token=create_access_token(token_data),
        refresh_token=create_refresh_token(token_data),
        token_type="bearer",
    )


@router.get("/me", response_model=UserResponse)
async def get_me(current_user=Depends(get_current_user)):
    return UserResponse(
        id=str(current_user["_id"]),
        email=current_user["email"],
        name=current_user["name"],
        role=current_user["role"],
    )
```

### 2.6 Document Ingestion (`app/services/ingestion/pdf_parser.py`)

```python
import fitz  # PyMuPDF
from typing import Dict


async def extract_pdf_text(file_path: str) -> Dict:
    """Extract text and metadata from PDF file."""
    doc = fitz.open(file_path)
    pages_text = []
    total_text = ""

    for page_num in range(len(doc)):
        page = doc[page_num]
        text = page.get_text("text")
        pages_text.append({"page": page_num + 1, "text": text})
        total_text += text + "\n"

    metadata = {
        "page_count": len(doc),
        "title": doc.metadata.get("title", ""),
        "author": doc.metadata.get("author", ""),
        "word_count": len(total_text.split()),
        "char_count": len(total_text),
    }

    doc.close()
    return {"text": total_text.strip(), "pages": pages_text, "metadata": metadata}
```

### 2.7 Document Upload Endpoint (`app/api/v1/documents.py`)

```python
import os
import uuid
from fastapi import APIRouter, Depends, HTTPException, UploadFile, File, status
from datetime import datetime

from app.api.deps import get_current_user
from app.database import get_database
from app.config import settings
from app.services.ingestion.pdf_parser import extract_pdf_text
from app.services.ingestion.docx_parser import extract_docx_text
from app.services.ingestion.text_cleaner import clean_text

router = APIRouter(prefix="/documents", tags=["Documents"])

UPLOAD_DIR = "uploads"
os.makedirs(UPLOAD_DIR, exist_ok=True)


@router.post("/upload", status_code=status.HTTP_201_CREATED)
async def upload_document(
    file: UploadFile = File(...),
    current_user=Depends(get_current_user),
):
    # Validate file extension
    ext = file.filename.rsplit(".", 1)[-1].lower()
    if ext not in settings.ALLOWED_EXTENSIONS:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail=f"File type .{ext} not allowed. Allowed: {settings.ALLOWED_EXTENSIONS}",
        )

    # Validate file size
    content = await file.read()
    if len(content) > settings.MAX_FILE_SIZE_MB * 1024 * 1024:
        raise HTTPException(
            status_code=status.HTTP_413_REQUEST_ENTITY_TOO_LARGE,
            detail=f"File size exceeds {settings.MAX_FILE_SIZE_MB}MB limit",
        )

    # Save file
    file_id = str(uuid.uuid4())
    file_path = os.path.join(UPLOAD_DIR, f"{file_id}.{ext}")
    with open(file_path, "wb") as f:
        f.write(content)

    # Extract text
    if ext == "pdf":
        result = await extract_pdf_text(file_path)
    elif ext == "docx":
        result = await extract_docx_text(file_path)
    elif ext in ("txt", "csv"):
        result = {"text": content.decode("utf-8"), "metadata": {}}
    else:
        raise HTTPException(status_code=400, detail="Unsupported file type")

    # Clean text
    cleaned_text = clean_text(result["text"])

    # Store in MongoDB
    db = get_database()
    doc = {
        "title": file.filename,
        "source_type": ext,
        "file_path": file_path,
        "raw_text": result["text"],
        "cleaned_text": cleaned_text,
        "metadata": {
            **result.get("metadata", {}),
            "file_size_bytes": len(content),
            "mime_type": file.content_type,
            "word_count": len(cleaned_text.split()),
            "char_count": len(cleaned_text),
        },
        "status": "ready",
        "uploaded_by": current_user["_id"],
        "collection_name": "default",
        "is_deleted": False,
        "created_at": datetime.utcnow(),
        "updated_at": datetime.utcnow(),
    }
    result = await db.documents.insert_one(doc)

    return {
        "id": str(result.inserted_id),
        "title": file.filename,
        "status": "ready",
        "metadata": doc["metadata"],
    }


@router.get("")
async def list_documents(
    skip: int = 0,
    limit: int = 20,
    current_user=Depends(get_current_user),
):
    db = get_database()
    cursor = db.documents.find(
        {"is_deleted": False}
    ).skip(skip).limit(limit).sort("created_at", -1)

    documents = []
    async for doc in cursor:
        documents.append({
            "id": str(doc["_id"]),
            "title": doc["title"],
            "source_type": doc["source_type"],
            "status": doc["status"],
            "metadata": doc["metadata"],
            "created_at": doc["created_at"].isoformat(),
        })

    total = await db.documents.count_documents({"is_deleted": False})
    return {"documents": documents, "total": total, "skip": skip, "limit": limit}
```

---

## 3. Phase 2 Implementation: Chunking Engine

### 3.1 Base Chunking Interface (`app/services/chunking/base.py`)

```python
from abc import ABC, abstractmethod
from typing import List, Dict
from app.models.chunk import ChunkResult


class BaseChunker(ABC):
    """Base interface for all chunking strategies."""

    @abstractmethod
    def chunk(self, text: str, **kwargs) -> List[ChunkResult]:
        """Split text into chunks."""
        pass

    @property
    @abstractmethod
    def strategy_name(self) -> str:
        """Return the strategy identifier."""
        pass
```

### 3.2 Fixed Size Chunking (`app/services/chunking/fixed_size.py`)

```python
from typing import List
from app.services.chunking.base import BaseChunker
from app.models.chunk import ChunkResult


class FixedSizeChunker(BaseChunker):
    def __init__(self, chunk_size: int = 500, overlap: int = 50):
        self.chunk_size = chunk_size
        self.overlap = overlap

    @property
    def strategy_name(self) -> str:
        return "fixed"

    def chunk(self, text: str, **kwargs) -> List[ChunkResult]:
        chunks = []
        start = 0
        chunk_index = 0

        while start < len(text):
            end = start + self.chunk_size
            chunk_text = text[start:end]

            chunks.append(ChunkResult(
                content=chunk_text,
                chunk_index=chunk_index,
                start_char=start,
                end_char=min(end, len(text)),
                token_count=len(chunk_text.split()),
                metadata={"chunk_size": self.chunk_size, "overlap": self.overlap},
            ))

            start += self.chunk_size - self.overlap
            chunk_index += 1

        return chunks
```

### 3.3 Recursive Chunking (`app/services/chunking/recursive.py`)

```python
from typing import List
from langchain.text_splitter import RecursiveCharacterTextSplitter
from app.services.chunking.base import BaseChunker
from app.models.chunk import ChunkResult


class RecursiveChunker(BaseChunker):
    def __init__(self, chunk_size: int = 1000, overlap: int = 200):
        self.splitter = RecursiveCharacterTextSplitter(
            chunk_size=chunk_size,
            chunk_overlap=overlap,
            separators=["\n\n", "\n", ". ", " ", ""],
        )

    @property
    def strategy_name(self) -> str:
        return "recursive"

    def chunk(self, text: str, **kwargs) -> List[ChunkResult]:
        docs = self.splitter.create_documents([text])
        chunks = []

        for idx, doc in enumerate(docs):
            start_char = text.find(doc.page_content)
            chunks.append(ChunkResult(
                content=doc.page_content,
                chunk_index=idx,
                start_char=max(0, start_char),
                end_char=max(0, start_char) + len(doc.page_content),
                token_count=len(doc.page_content.split()),
                metadata={"separators_used": True},
            ))

        return chunks
```

### 3.4 Semantic Chunking (`app/services/chunking/semantic.py`)

```python
from typing import List
import numpy as np
from sentence_transformers import SentenceTransformer
from app.services.chunking.base import BaseChunker
from app.models.chunk import ChunkResult


class SemanticChunker(BaseChunker):
    def __init__(self, model_name: str = "all-MiniLM-L6-v2", threshold: float = 0.5):
        self.model = SentenceTransformer(model_name)
        self.threshold = threshold

    @property
    def strategy_name(self) -> str:
        return "semantic"

    def chunk(self, text: str, **kwargs) -> List[ChunkResult]:
        # Split into sentences
        sentences = [s.strip() for s in text.split(". ") if s.strip()]
        if not sentences:
            return []

        # Generate embeddings for each sentence
        embeddings = self.model.encode(sentences)

        # Find breakpoints based on cosine similarity
        chunks = []
        current_chunk_sentences = [sentences[0]]
        current_start = 0

        for i in range(1, len(sentences)):
            similarity = np.dot(embeddings[i - 1], embeddings[i]) / (
                np.linalg.norm(embeddings[i - 1]) * np.linalg.norm(embeddings[i])
            )

            if similarity < self.threshold:
                # Breakpoint found
                chunk_text = ". ".join(current_chunk_sentences) + "."
                chunks.append(ChunkResult(
                    content=chunk_text,
                    chunk_index=len(chunks),
                    start_char=current_start,
                    end_char=current_start + len(chunk_text),
                    token_count=len(chunk_text.split()),
                    metadata={"similarity_threshold": self.threshold},
                ))
                current_chunk_sentences = [sentences[i]]
                current_start += len(chunk_text) + 1
            else:
                current_chunk_sentences.append(sentences[i])

        # Add last chunk
        if current_chunk_sentences:
            chunk_text = ". ".join(current_chunk_sentences) + "."
            chunks.append(ChunkResult(
                content=chunk_text,
                chunk_index=len(chunks),
                start_char=current_start,
                end_char=current_start + len(chunk_text),
                token_count=len(chunk_text.split()),
                metadata={"similarity_threshold": self.threshold},
            ))

        return chunks
```

### 3.5 Chunking API Endpoint (`app/api/v1/chunking.py`)

```python
from fastapi import APIRouter, Depends, HTTPException
from bson import ObjectId
from datetime import datetime

from app.api.deps import get_current_user
from app.database import get_database
from app.services.chunking.fixed_size import FixedSizeChunker
from app.services.chunking.recursive import RecursiveChunker
from app.services.chunking.semantic import SemanticChunker
from app.models.chunk import ChunkRequest, ChunkResponse

router = APIRouter(prefix="/chunking", tags=["Chunking"])

CHUNKERS = {
    "fixed": FixedSizeChunker,
    "recursive": RecursiveChunker,
    "semantic": SemanticChunker,
    # ... other strategies registered here
}


@router.post("/process", response_model=ChunkResponse)
async def process_chunks(
    request: ChunkRequest,
    current_user=Depends(get_current_user),
):
    db = get_database()

    # Get document
    doc = await db.documents.find_one({"_id": ObjectId(request.document_id)})
    if not doc:
        raise HTTPException(status_code=404, detail="Document not found")

    # Get chunker
    chunker_class = CHUNKERS.get(request.strategy)
    if not chunker_class:
        raise HTTPException(status_code=400, detail=f"Unknown strategy: {request.strategy}")

    chunker = chunker_class(**request.params) if request.params else chunker_class()

    # Generate chunks
    chunks = chunker.chunk(doc["cleaned_text"])

    # Store chunks in MongoDB
    chunk_docs = []
    for chunk in chunks:
        chunk_docs.append({
            "document_id": ObjectId(request.document_id),
            "content": chunk.content,
            "chunk_index": chunk.chunk_index,
            "chunking_strategy": request.strategy,
            "metadata": {
                "start_char": chunk.start_char,
                "end_char": chunk.end_char,
                "token_count": chunk.token_count,
                **chunk.metadata,
            },
            "embedding": None,  # Generated later
            "embedding_model": None,
            "created_at": datetime.utcnow(),
        })

    if chunk_docs:
        await db.chunks.insert_many(chunk_docs)

    # Update document status
    await db.documents.update_one(
        {"_id": ObjectId(request.document_id)},
        {"$set": {"status": "chunked", "updated_at": datetime.utcnow()}}
    )

    return ChunkResponse(
        document_id=request.document_id,
        strategy=request.strategy,
        total_chunks=len(chunks),
        chunks=[c.dict() for c in chunks],
    )


@router.get("/strategies")
async def list_strategies():
    return {
        "strategies": [
            {"id": "fixed", "name": "Fixed Size", "description": "Split text into equal-sized chunks"},
            {"id": "recursive", "name": "Recursive", "description": "Split using hierarchical separators"},
            {"id": "semantic", "name": "Semantic", "description": "Split based on meaning boundaries"},
            {"id": "structure", "name": "Structure-Based", "description": "Split based on document structure"},
            {"id": "query_aware", "name": "Query-Aware", "description": "Optimize chunks for expected queries"},
            {"id": "metadata_aware", "name": "Metadata-Aware", "description": "Include metadata context in chunks"},
            {"id": "llm_based", "name": "LLM-Based", "description": "Use LLM to identify logical boundaries"},
            {"id": "agentic", "name": "Agentic", "description": "Agent-driven adaptive chunking"},
        ]
    }
```

---

## 4. Phase 3 Implementation: Embeddings & Search

### 4.1 Embedding Engine (`app/services/embedding/engine.py`)

```python
from typing import List
from sentence_transformers import SentenceTransformer
from app.config import settings


class EmbeddingEngine:
    _models = {}

    @classmethod
    def get_model(cls, model_name: str) -> SentenceTransformer:
        if model_name not in cls._models:
            cls._models[model_name] = SentenceTransformer(model_name)
        return cls._models[model_name]

    @classmethod
    def generate_embeddings(cls, texts: List[str], model_name: str = None) -> List[List[float]]:
        model_name = model_name or settings.EMBEDDING_MODEL_DEFAULT
        model = cls.get_model(model_name)
        embeddings = model.encode(texts, show_progress_bar=False)
        return embeddings.tolist()

    @classmethod
    def generate_single(cls, text: str, model_name: str = None) -> List[float]:
        results = cls.generate_embeddings([text], model_name)
        return results[0]
```

### 4.2 Semantic Search (`app/services/search/semantic_search.py`)

```python
from typing import List, Dict
from bson import ObjectId
from app.database import get_database
from app.services.embedding.engine import EmbeddingEngine


async def semantic_search(
    query: str,
    top_k: int = 10,
    filters: Dict = None,
    model_name: str = None,
) -> List[Dict]:
    """Perform vector similarity search using MongoDB Atlas Vector Search."""
    db = get_database()

    # Generate query embedding
    query_embedding = EmbeddingEngine.generate_single(query, model_name)

    # Build vector search pipeline
    pipeline = [
        {
            "$vectorSearch": {
                "index": "chunk_vector_index",
                "path": "embedding",
                "queryVector": query_embedding,
                "numCandidates": top_k * 10,
                "limit": top_k,
            }
        },
        {
            "$project": {
                "content": 1,
                "document_id": 1,
                "chunk_index": 1,
                "chunking_strategy": 1,
                "metadata": 1,
                "score": {"$meta": "vectorSearchScore"},
            }
        },
    ]

    # Apply filters if provided
    if filters:
        filter_stage = {}
        if "document_id" in filters:
            filter_stage["document_id"] = ObjectId(filters["document_id"])
        if "strategy" in filters:
            filter_stage["chunking_strategy"] = filters["strategy"]
        pipeline[0]["$vectorSearch"]["filter"] = filter_stage

    results = []
    async for doc in db.chunks.aggregate(pipeline):
        results.append({
            "chunk_id": str(doc["_id"]),
            "content": doc["content"],
            "document_id": str(doc["document_id"]),
            "chunk_index": doc["chunk_index"],
            "score": doc["score"],
            "metadata": doc.get("metadata", {}),
        })

    return results
```

### 4.3 Hybrid Search (`app/services/search/hybrid_search.py`)

```python
from typing import List, Dict
from app.services.search.semantic_search import semantic_search
from app.services.search.keyword_search import keyword_search


async def hybrid_search(
    query: str,
    top_k: int = 10,
    semantic_weight: float = 0.7,
    keyword_weight: float = 0.3,
    filters: Dict = None,
) -> List[Dict]:
    """Combine semantic and keyword search with weighted scoring."""
    # Run both searches
    semantic_results = await semantic_search(query, top_k=top_k * 2, filters=filters)
    keyword_results = await keyword_search(query, top_k=top_k * 2, filters=filters)

    # Merge results using Reciprocal Rank Fusion (RRF)
    scores = {}
    k = 60  # RRF constant

    for rank, result in enumerate(semantic_results):
        chunk_id = result["chunk_id"]
        scores[chunk_id] = scores.get(chunk_id, {"result": result, "score": 0})
        scores[chunk_id]["score"] += semantic_weight * (1 / (k + rank + 1))

    for rank, result in enumerate(keyword_results):
        chunk_id = result["chunk_id"]
        scores[chunk_id] = scores.get(chunk_id, {"result": result, "score": 0})
        scores[chunk_id]["score"] += keyword_weight * (1 / (k + rank + 1))

    # Sort by combined score
    ranked = sorted(scores.values(), key=lambda x: x["score"], reverse=True)

    # Deduplicate and return top_k
    results = []
    seen_content = set()
    for item in ranked[:top_k]:
        content_hash = hash(item["result"]["content"][:100])
        if content_hash not in seen_content:
            seen_content.add(content_hash)
            item["result"]["hybrid_score"] = item["score"]
            results.append(item["result"])

    return results
```

---

## 5. Phase 4 Implementation: RAG & AI

### 5.1 LLM Router (`app/services/rag/llm_router.py`)

```python
from langchain_groq import ChatGroq
from app.config import settings


class LLMRouter:
    """Route requests to appropriate Groq model based on task complexity."""

    @staticmethod
    def get_llm(task: str = "default") -> ChatGroq:
        model_map = {
            "summarize": settings.GROQ_MODEL_FAST,       # Llama 3 8B
            "qa": settings.GROQ_MODEL_QUALITY,           # Llama 3 70B
            "agent": settings.GROQ_MODEL_REASONING,      # Mixtral 8x7B
            "chunk": settings.GROQ_MODEL_FAST,           # Llama 3 8B
            "default": settings.GROQ_MODEL_QUALITY,      # Llama 3 70B
        }

        model = model_map.get(task, model_map["default"])
        return ChatGroq(
            groq_api_key=settings.GROQ_API_KEY,
            model_name=model,
            temperature=0.1,
            max_tokens=4096,
        )
```

### 5.2 RAG Chain (`app/services/rag/chain.py`)

```python
from typing import Dict, List
from langchain.prompts import ChatPromptTemplate
from langchain.schema.output_parser import StrOutputParser
from langchain.schema.runnable import RunnablePassthrough

from app.services.rag.llm_router import LLMRouter
from app.services.search.hybrid_search import hybrid_search


RAG_PROMPT = ChatPromptTemplate.from_template("""
You are a helpful knowledge assistant. Answer the user's question based ONLY on the provided context.
If the context doesn't contain enough information to answer, say so clearly.
Always cite which source documents support your answer.

Context:
{context}

Question: {question}

Answer:""")


async def rag_answer(question: str, filters: Dict = None) -> Dict:
    """Execute RAG pipeline: retrieve + generate."""
    # Retrieve relevant chunks
    results = await hybrid_search(question, top_k=5, filters=filters)

    if not results:
        return {
            "answer": "I couldn't find relevant information to answer your question.",
            "sources": [],
            "confidence": 0.0,
        }

    # Format context
    context_parts = []
    sources = []
    for i, r in enumerate(results):
        context_parts.append(f"[Source {i+1}]: {r['content']}")
        sources.append({
            "chunk_id": r["chunk_id"],
            "document_id": r["document_id"],
            "score": r.get("hybrid_score", r.get("score", 0)),
            "preview": r["content"][:200],
        })

    context = "\n\n".join(context_parts)

    # Generate answer
    llm = LLMRouter.get_llm("qa")
    chain = RAG_PROMPT | llm | StrOutputParser()
    answer = await chain.ainvoke({"context": context, "question": question})

    return {
        "answer": answer,
        "sources": sources,
        "confidence": sum(s["score"] for s in sources) / len(sources) if sources else 0,
    }
```

### 5.3 LangGraph Agent (`app/services/rag/agent.py`)

```python
from typing import TypedDict, List, Annotated
from langgraph.graph import StateGraph, END
from langchain.schema.output_parser import StrOutputParser
from langchain.prompts import ChatPromptTemplate

from app.services.rag.llm_router import LLMRouter
from app.services.search.hybrid_search import hybrid_search


class AgentState(TypedDict):
    question: str
    sub_queries: List[str]
    retrieved_contexts: List[dict]
    evaluation: str
    final_answer: str
    iteration: int


async def plan_query(state: AgentState) -> AgentState:
    """Decompose complex query into sub-queries."""
    llm = LLMRouter.get_llm("agent")
    prompt = ChatPromptTemplate.from_template(
        "Break this complex question into 2-3 simpler sub-questions that can be "
        "answered independently. Return only the sub-questions, one per line.\n\n"
        "Question: {question}"
    )
    chain = prompt | llm | StrOutputParser()
    result = await chain.ainvoke({"question": state["question"]})
    sub_queries = [q.strip() for q in result.strip().split("\n") if q.strip()]
    return {**state, "sub_queries": sub_queries or [state["question"]]}


async def retrieve(state: AgentState) -> AgentState:
    """Retrieve context for all sub-queries."""
    all_contexts = []
    for query in state["sub_queries"]:
        results = await hybrid_search(query, top_k=3)
        all_contexts.extend(results)

    # Deduplicate
    seen = set()
    unique_contexts = []
    for ctx in all_contexts:
        if ctx["chunk_id"] not in seen:
            seen.add(ctx["chunk_id"])
            unique_contexts.append(ctx)

    return {**state, "retrieved_contexts": unique_contexts}


async def evaluate(state: AgentState) -> AgentState:
    """Evaluate if retrieved context is sufficient."""
    if not state["retrieved_contexts"]:
        return {**state, "evaluation": "insufficient"}

    llm = LLMRouter.get_llm("agent")
    context_preview = "\n".join([c["content"][:200] for c in state["retrieved_contexts"][:5]])
    prompt = ChatPromptTemplate.from_template(
        "Given this context, can you answer the question?\n"
        "Context: {context}\nQuestion: {question}\n"
        "Reply ONLY 'sufficient' or 'insufficient'."
    )
    chain = prompt | llm | StrOutputParser()
    result = await chain.ainvoke({"context": context_preview, "question": state["question"]})
    evaluation = "sufficient" if "sufficient" in result.lower() else "insufficient"
    return {**state, "evaluation": evaluation, "iteration": state.get("iteration", 0) + 1}


async def synthesize(state: AgentState) -> AgentState:
    """Generate final answer from all retrieved contexts."""
    llm = LLMRouter.get_llm("qa")
    context = "\n\n".join([f"[{i+1}] {c['content']}" for i, c in enumerate(state["retrieved_contexts"][:7])])
    prompt = ChatPromptTemplate.from_template(
        "Based on the following context, provide a comprehensive answer.\n"
        "Cite sources using [1], [2], etc.\n\n"
        "Context:\n{context}\n\nQuestion: {question}\n\nAnswer:"
    )
    chain = prompt | llm | StrOutputParser()
    answer = await chain.ainvoke({"context": context, "question": state["question"]})
    return {**state, "final_answer": answer}


def should_continue(state: AgentState) -> str:
    """Decide whether to refine or synthesize."""
    if state["evaluation"] == "sufficient" or state.get("iteration", 0) >= 3:
        return "synthesize"
    return "refine"


# Build the graph
def build_agent_graph():
    graph = StateGraph(AgentState)

    graph.add_node("plan", plan_query)
    graph.add_node("retrieve", retrieve)
    graph.add_node("evaluate", evaluate)
    graph.add_node("synthesize", synthesize)

    graph.set_entry_point("plan")
    graph.add_edge("plan", "retrieve")
    graph.add_edge("retrieve", "evaluate")
    graph.add_conditional_edges("evaluate", should_continue, {
        "synthesize": "synthesize",
        "refine": "plan",
    })
    graph.add_edge("synthesize", END)

    return graph.compile()


# Usage
async def agentic_rag(question: str) -> Dict:
    agent = build_agent_graph()
    initial_state = {
        "question": question,
        "sub_queries": [],
        "retrieved_contexts": [],
        "evaluation": "",
        "final_answer": "",
        "iteration": 0,
    }
    result = await agent.ainvoke(initial_state)
    return {
        "answer": result["final_answer"],
        "sub_queries": result["sub_queries"],
        "sources": [{"chunk_id": c["chunk_id"], "document_id": c["document_id"]}
                    for c in result["retrieved_contexts"]],
        "iterations": result["iteration"],
    }
```

---

## 6. Phase 5: Document Comparison

### 6.1 Comparison Service (`app/services/comparison/analyzer.py`)

```python
from typing import List, Dict
from difflib import SequenceMatcher
from app.services.embedding.engine import EmbeddingEngine
import numpy as np


class DocumentComparer:
    @staticmethod
    async def compare_documents(doc_texts: List[str], doc_ids: List[str]) -> Dict:
        """Compare multiple documents for similarities and differences."""
        # Sentence-level comparison
        similarities = {}
        differences = {}

        for i in range(len(doc_texts)):
            for j in range(i + 1, len(doc_texts)):
                pair_key = f"{doc_ids[i]}_vs_{doc_ids[j]}"

                # Text similarity ratio
                ratio = SequenceMatcher(None, doc_texts[i], doc_texts[j]).ratio()

                # Semantic similarity via embeddings
                embeddings = EmbeddingEngine.generate_embeddings(
                    [doc_texts[i][:5000], doc_texts[j][:5000]]
                )
                cosine_sim = float(np.dot(embeddings[0], embeddings[1]) / (
                    np.linalg.norm(embeddings[0]) * np.linalg.norm(embeddings[1])
                ))

                similarities[pair_key] = {
                    "text_similarity": round(ratio, 4),
                    "semantic_similarity": round(cosine_sim, 4),
                }

                # Find unique sections
                sentences_a = set(doc_texts[i].split(". "))
                sentences_b = set(doc_texts[j].split(". "))

                differences[pair_key] = {
                    "only_in_first": list(sentences_a - sentences_b)[:10],
                    "only_in_second": list(sentences_b - sentences_a)[:10],
                    "common_count": len(sentences_a & sentences_b),
                }

        return {
            "document_ids": doc_ids,
            "similarities": similarities,
            "differences": differences,
        }
```

---

## 7. Phase 6: Analytics

### 7.1 Analytics Service (`app/api/v1/analytics.py`)

```python
from fastapi import APIRouter, Depends
from datetime import datetime, timedelta
from app.api.deps import get_current_user
from app.database import get_database

router = APIRouter(prefix="/analytics", tags=["Analytics"])


@router.get("/dashboard")
async def get_dashboard(current_user=Depends(get_current_user)):
    db = get_database()

    total_documents = await db.documents.count_documents({"is_deleted": False})
    total_chunks = await db.chunks.count_documents({})
    total_embedded = await db.chunks.count_documents({"embedding": {"$ne": None}})
    total_searches = await db.search_history.count_documents({})
    total_users = await db.users.count_documents({"is_active": True})

    # Recent activity (last 7 days)
    week_ago = datetime.utcnow() - timedelta(days=7)
    recent_searches = await db.search_history.count_documents(
        {"timestamp": {"$gte": week_ago}}
    )
    recent_uploads = await db.documents.count_documents(
        {"created_at": {"$gte": week_ago}, "is_deleted": False}
    )

    return {
        "overview": {
            "total_documents": total_documents,
            "total_chunks": total_chunks,
            "total_embeddings": total_embedded,
            "total_searches": total_searches,
            "total_users": total_users,
        },
        "recent_activity": {
            "searches_last_7_days": recent_searches,
            "uploads_last_7_days": recent_uploads,
        },
    }


@router.get("/search")
async def get_search_analytics(current_user=Depends(get_current_user)):
    db = get_database()

    # Average response time
    pipeline = [
        {"$group": {
            "_id": "$search_type",
            "avg_response_ms": {"$avg": "$response_time_ms"},
            "count": {"$sum": 1},
            "avg_results": {"$avg": "$results_count"},
        }}
    ]
    results = []
    async for doc in db.search_history.aggregate(pipeline):
        results.append(doc)

    return {"search_metrics": results}
```

---

## 8. Requirements File (`requirements.txt`)

```
# Core Framework
fastapi==0.110.0
uvicorn[standard]==0.27.1
pydantic==2.6.1
pydantic-settings==2.1.0

# Database
motor==3.3.2
pymongo==4.6.1

# Authentication
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
python-multipart==0.0.9

# AI / ML
langchain==0.2.0
langchain-groq==0.1.3
langgraph==0.1.0
sentence-transformers==3.0.0
numpy==1.26.4

# Document Parsing
PyMuPDF==1.23.26
python-docx==1.1.0
beautifulsoup4==4.12.3
httpx==0.27.0

# Background Tasks
celery==5.3.6
redis==5.0.1

# Utilities
python-dotenv==1.0.1
structlog==24.1.0

# Testing
pytest==8.0.2
pytest-asyncio==0.23.5
httpx==0.27.0
```

---

## 9. Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Install Python dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Expose port
EXPOSE 8000

# Run with uvicorn
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

## 10. Environment Variables (`.env.example`)

```env
# Application
APP_NAME=Enterprise RAG Platform
DEBUG=true

# MongoDB
MONGODB_URL=mongodb+srv://<username>:<password>@cluster.mongodb.net/
MONGODB_DB_NAME=enterprise_rag

# JWT
JWT_SECRET_KEY=your-super-secret-key-change-in-production
JWT_ALGORITHM=HS256
JWT_ACCESS_TOKEN_EXPIRE_MINUTES=15
JWT_REFRESH_TOKEN_EXPIRE_DAYS=7

# Groq
GROQ_API_KEY=gsk_your_groq_api_key

# Redis
REDIS_URL=redis://localhost:6379

# Embedding Models
EMBEDDING_MODEL_DEFAULT=all-mpnet-base-v2
EMBEDDING_MODEL_FAST=all-MiniLM-L6-v2

# File Upload
MAX_FILE_SIZE_MB=50

# CORS
CORS_ORIGINS=["http://localhost:3000"]

# Rate Limiting
RATE_LIMIT_PER_MINUTE=100
```
