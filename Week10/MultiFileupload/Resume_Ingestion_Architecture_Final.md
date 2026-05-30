# Resume Ingestion Architecture for Existing Resume Search Backend

## 1. Feature Overview

This feature adds a new Resume Ingestion flow to the same existing Node.js + Express + TypeScript backend that already powers resume search.

The new flow should:
- keep the existing single-resume upload flow as it is
- add a separate batch upload and processing API for up to 5 files at a time
- accept resume files in PDF, DOC, and DOCX formats
- extract text from each file
- clean and normalize the extracted text
- convert each resume into structured JSON
- generate one embedding vector per resume
- store each resume as one MongoDB document
- make the data searchable later using the existing retrieval and vector search stack

Design goals:
- keep the backend monolithic
- reuse the same server, logging, and MongoDB connection
- keep the flow simple and phase-by-phase
- do not chunk resumes
- one resume should always become one JSON document and one embedding vector
- keep optional LLM parsing disabled by default and configurable through `.env`

---

## 2. Recommended Backend Flow

### Single resume flow

```text
Resume file
  ↓
Validate file
  ↓
Read content
  ↓
Clean text
  ↓
Extract structured fields
  ↓
Generate embedding
  ↓
Store in MongoDB
  ↓
Ready for search
```

### Batch flow for 5 resumes

```text
Resumes folder or batch upload
  ↓
Validate up to 5 files
  ↓
Process each file independently
  ↓
Extract text per file
  ↓
Clean per file
  ↓
Parse per file
  ↓
Generate one embedding per file
  ↓
Store each resume as one document
  ↓
Return batch summary
```

### Important rule for batches

- One batch contains a maximum of 5 resumes.
- Each file is processed independently.
- One failure should not block the other files unless strict mode is enabled later.
- The API should return a per-file success or failure summary.
- The batch API is separate from the single-resume upload API.

---

## 3. Suggested Folder Structure

```text
src/
├── routes/
│   ├── ingestionRoutes.ts
│   ├── index.ts
│   └── searchRoutes.ts
├── controllers/
│   └── ingestionController.ts
├── services/
│   ├── ResumeIngestionService.ts
│   ├── ResumeBatchIngestionService.ts
│   ├── ResumeParserService.ts
│   ├── AlgorithmResumeParser.ts
│   ├── LLMResumeParser.ts
│   ├── ResumeEmbeddingService.ts
│   └── ResumeStorageService.ts
├── repositories/
│   └── ResumeIngestionRepository.ts
├── config/
│   ├── multerConfig.ts
│   ├── fileTypeConfig.ts
│   ├── skills.ts
│   └── app.ts
├── middleware/
│   ├── errorHandler.ts
│   ├── logger.ts
│   └── requestId.ts
├── utils/
│   ├── regex.ts
│   ├── textCleaner.ts
│   └── fileHelpers.ts
└── types/
    └── ingestion.ts

Resumes/
uploads/
```

### Folder notes

- `Resumes/` is the source folder for resume files to be ingested in batches.
- `uploads/` is a temporary folder used during upload and parsing.
- The backend should continue using the same `src/` tree and the same server.

---

## 4. File Responsibilities

| File | Responsibility |
|---|---|
| `src/routes/ingestionRoutes.ts` | Exposes ingestion endpoints and batch endpoints |
| `src/controllers/ingestionController.ts` | Validates requests and shapes responses |
| `src/services/ResumeIngestionService.ts` | Orchestrates single-resume ingestion |
| `src/services/ResumeBatchIngestionService.ts` | Orchestrates batch ingestion for up to 5 files |
| `src/services/ResumeParserService.ts` | Detects file type and extracts raw text from PDF, DOC, DOCX |
| `src/services/AlgorithmResumeParser.ts` | Parses resume text using regex, keyword matching, and text scanning |
| `src/services/LLMResumeParser.ts` | Optional LLM-based parser controlled by env variables |
| `src/services/ResumeEmbeddingService.ts` | Builds the embedding input and calls Mistral |
| `src/services/ResumeStorageService.ts` | Validates and stores the final document |
| `src/repositories/ResumeIngestionRepository.ts` | Writes resume documents and batch tracking data to MongoDB |
| `src/config/multerConfig.ts` | File size, file type, and upload storage rules |
| `src/config/fileTypeConfig.ts` | Allowed extensions and MIME types for PDF/DOC/DOCX |
| `src/config/skills.ts` | Static skill dictionary for keyword detection |
| `src/utils/regex.ts` | Shared regex helpers for email, phone, roles, and experience |
| `src/utils/textCleaner.ts` | Normalizes extracted text before parsing |
| `src/utils/fileHelpers.ts` | File name, extension, and cleanup helpers |

---

## 5. Processing Flow

### Step-by-step flow

1. The client uploads one file or a batch of up to 5 files.
2. The API validates file type, file size, and batch count.
3. The backend detects whether the file is PDF, DOC, or DOCX.
4. The parser extracts raw text from the file.
5. The cleaner normalizes the text.
6. The algorithm parser extracts structured fields.
7. Skills are detected using keyword matching.
8. The embedding service builds one embedding input string per resume.
9. Mistral generates one vector per resume.
10. The repository stores one MongoDB document per resume.
11. Temporary files are removed.
12. The API returns a single resume result or a batch summary.

### Batch behavior

- If 5 files are sent, the backend processes them one by one or with limited concurrency.
- If one file fails, the response should still include the other file results.
- Each item in the batch should contain its own status, error message, and stored document details when successful.

---

## 6. Database Structure

Use MongoDB Atlas and keep the existing search collection as the main destination for searchable resume documents.

### Primary collection: `resumes`

| Field | Type | Purpose |
|---|---|---|
| `fileName` | string | Original file name |
| `sourcePath` | string | Temporary file path used during processing |
| `batchId` | string | Groups files processed in the same batch |
| `sourceType` | string | `single` or `batch` |
| `mimeType` | string | File MIME type |
| `rawText` | string | Extracted content from the file |
| `cleanText` | string | Normalized text |
| `name` | string | Candidate name |
| `email` | string | Candidate email |
| `phone` | string | Candidate phone |
| `location` | string | Candidate location |
| `company` | string | Current or recent company |
| `role` | string | Current or recent role |
| `education` | string | Education details |
| `totalExperience` | number | Years of experience |
| `skills` | string[] | Matched skills |
| `experienceSummary` | string | Short experience summary |
| `embedding` | number[] | Mistral vector |
| `embeddingModel` | string | Model name used |
| `embeddingDimension` | number | Expected vector size, `1024` |
| `processingStatus` | string | `stored`, `failed`, or `pending` |
| `parseMode` | string | `algorithm` or `llm` |
| `createdAt` | date | Insert time |
| `updatedAt` | date | Last update time |

### Optional batch tracking collection: `resume_ingestion_jobs`

This collection is useful for batch processing and reporting.

| Field | Type | Purpose |
|---|---|---|
| `batchId` | string | Unique batch identifier |
| `requestedBy` | string | Optional user or job source |
| `totalFiles` | number | Number of files in the batch |
| `successCount` | number | Number of successful files |
| `failureCount` | number | Number of failed files |
| `status` | string | `running`, `completed`, `completed_with_errors`, `failed` |
| `fileResults` | array | Per-file outcome summary |
| `startedAt` | date | Batch start time |
| `completedAt` | date | Batch end time |

### Storage principle

- One resume should create one document.
- One resume should create one embedding.
- Do not split a single resume into multiple records.
- Do not chunk the text.
- Store batch metadata separately if batch tracking is required.

---

## 7. Embedding Flow

The embedding should be created from the structured resume content plus the raw text.

### Recommended embedding input

```text
name
role
skills
company
rawText
```

### Embedding rules

- Use `mistral-embed` as the default model.
- Keep the dimension at `1024`.
- Read keys and model settings from `.env`.
- Generate embeddings only after parsing succeeds.
- Store both the vector and the model name with the document.
- Do not expose embedding dimensions to the client in normal API responses.

### Batch note

- For batch runs, each resume still gets its own embedding.
- To avoid provider throttling, the batch processor can use a small concurrency limit rather than processing all 5 embeddings at the same time.

---

## 8. Validation Strategy

Validation should happen at every boundary.

| Stage | Validation |
|---|---|
| Upload | Accept only PDF, DOC, and DOCX |
| Upload | Reject batches larger than 5 files |
| Upload | Enforce max file size per file |
| Extraction | Reject unreadable or empty documents |
| Cleaning | Reject text that becomes empty after normalization |
| Parsing | Validate parsed fields and basic types |
| Embedding | Validate API key and model presence |
| Database | Validate final document shape before insert |

### Request-level validations

- Accept only supported file formats.
- Reject files with invalid MIME types or extensions.
- Reject empty file names.
- Reject batches larger than 5 files.
- Reject files larger than the configured size limit.
- Reject documents that produce no useful text.
- Reject storage if embedding generation fails.
- Validate `batchId` when batch status is queried later.

---

## 9. Error Handling

Each stage should fail clearly and return a useful message.

| Error | Expected message |
|---|---|
| Invalid file type | Only PDF, DOC, and DOCX allowed |
| Batch too large | Batch size exceeds 5 files |
| File too large | File size exceeds limit |
| Empty document | Resume extraction failed |
| Parse failure | Resume parsing failed |
| Embedding failure | Mistral embedding failed |
| MongoDB failure | Resume ingestion failed |

### Error handling principles

- Stop the pipeline for the current file when one critical step fails.
- Continue the remaining files in the batch when possible.
- Return a readable error message to the client.
- Include `requestId` and `batchId` in every error response.
- Remove temporary files even when processing fails.
- Keep errors consistent with the existing backend error handler pattern.

---

## 10. Logging Strategy

The ingestion module should use structured logs similar to the retrieval flow.

### Required log fields

```json
{
  "requestId": "abc123",
  "batchId": "batch-001",
  "fileName": "resume.pdf",
  "stage": "extract",
  "durationMs": 120,
  "status": "success"
}
```

### Batch log fields

```json
{
  "requestId": "abc123",
  "batchId": "batch-001",
  "batchSize": 5,
  "successCount": 4,
  "failureCount": 1,
  "durationMs": 1840
}
```

### Logging goals

- Track time spent in each stage.
- Identify slow document extraction or embedding calls.
- Keep logs structured and machine-readable.
- Use the same request ID across upload, parse, embed, and insert steps.
- Include batch summary logs after batch completion.

---

## 11. Phase-wise Implementation Plan

### Phase 1: Project setup

- Add the ingestion folder structure.
- Register ingestion routes under `/v1`.
- Add required environment variables.
- Prepare file format settings for PDF, DOC, and DOCX.

### Phase 2: Single resume upload

- Add secure file upload validation.
- Accept supported file types only.
- Store files temporarily in `uploads/`.
- Expose `POST /v1/resume/upload`.

### Phase 2B: Batch resume upload

- Add a separate batch upload endpoint.
- Accept up to 5 resume files in one request.
- Process each file independently.
- Expose `POST /v1/resume/batch-inject`.

### Phase 3: File extraction

- Add `ResumeParserService`.
- Extract text from PDF, DOC, and DOCX.
- Expose `POST /v1/resume/extract` for debugging.

### Phase 4: Text cleaning

- Add `textCleaner` utilities.
- Normalize line breaks, spaces, duplicate lines, and noisy symbols.
- Expose `POST /v1/resume/clean`.

### Phase 5: Algorithm-based parsing

- Add `AlgorithmResumeParser`.
- Extract name, email, phone, skills, company, role, education, and experience.
- Expose `POST /v1/resume/parse`.

### Phase 6: Skills detection

- Add static skill dictionary in `skills.ts`.
- Match skills using keyword search.
- Reuse the same parser output.

### Phase 7: Optional LLM parser

- Add `LLMResumeParser` as an optional path.
- Use `USE_LLM_PARSER=true` to switch parsers.
- Keep algorithm parser as the default.

### Phase 8: Embedding generation

- Add resume embedding generation through Mistral.
- Use one resume to produce one vector.
- Store model name and dimension with the record.

### Phase 9: MongoDB storage

- Add repository logic for inserting the final resume document.
- Store parsed fields and embeddings in `resumes`.
- Add optional batch tracking in `resume_ingestion_jobs`.

### Phase 10: Single resume orchestration

- Add `ResumeIngestionService`.
- Combine upload, extraction, cleaning, parsing, embedding, and storage into one flow.
- Expose `POST /v1/resume/inject`.

### Phase 11: Batch orchestration

- Add `ResumeBatchIngestionService`.
- Process up to 5 files per batch.
- Return per-file success and failure results.
- Expose `POST /v1/resume/batch-inject`.

### Phase 12: Logging and error handling

- Add stage timing logs.
- Standardize error responses.
- Remove temporary files on success and failure.
- Add batch summary logs.

---

## 12. Final End-to-End Flow

### Single resume

```text
Resume file
  ↓
Validate file
  ↓
Read content
  ↓
Clean text
  ↓
Parse structured fields
  ↓
Generate embedding
  ↓
Store document
  ↓
Searchable resume
```

### Batch of 5 resumes

```text
Up to 5 resume files
  ↓
Validate batch
  ↓
Process file 1
  ↓
Process file 2
  ↓
Process file 3
  ↓
Process file 4
  ↓
Process file 5
  ↓
Store each resume separately
  ↓
Return batch summary
```

---

## 13. Practical Implementation Notes

- Keep ingestion inside the same server as retrieval.
- Keep the ingestion module separate, but under the same `src/` tree.
- Prefer the algorithm parser first because it is deterministic and easy to debug.
- Keep the optional LLM parser disabled by default.
- Do not chunk resumes for ingestion.
- Store exactly one embedding per resume.
- Use batch size 5 as the default and maximum.
- Keep the phase-wise rollout small so each stage can be tested independently.

---

## 14. Summary

This architecture adds a complete resume ingestion flow to the existing backend without changing the server model.

The flow is simple:
- upload or load up to 5 resumes
- extract text
- clean it
- parse resume fields
- generate one Mistral embedding per resume
- store one document per resume in MongoDB
- make the data searchable later

The recommended path is to implement it in phases so the system stays stable while each capability is added.