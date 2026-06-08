# ILI GeoPackage + CSV FME Ingestion Constitution

## Core Principles

### I. Orchestration-Only API

FastAPI MUST act strictly as an orchestration and control plane.
It MUST NOT perform geospatial processing, transformation, or data alignment.
All processing logic MUST reside in FME Workbench / FME Flow.

---

### II. FME as Execution Engine

FME is the sole system responsible for:

* geometry processing
* spatial alignment
* transformation logic
* feature extraction

FastAPI MUST treat FME as an external deterministic execution system.

---

### III. Stateless API Design

FastAPI MUST remain stateless.
It MUST NOT persist processed datasets as final outputs.
All state MUST be stored in:

* job tracking store
* object storage (raw + intermediate artifacts)

---

### IV. File Immutability

All uploaded files (GeoPackage, CSV) are immutable artifacts.
They MUST NOT be modified after ingestion.
Each file MUST be associated with a unique ingestion_id.

---

### V. Job-Based Processing Model

All ingestion requests MUST create a job record.
Processing MUST be asynchronous.
No request is allowed to block waiting for FME execution.

---

### VI. Human-in-the-Loop Review Gate

FME-processed outputs MUST NOT be automatically published.
All processed results MUST pass through a user review step in the Web UI.
Users MUST explicitly approve results before database persistence.

---

## File Handling & Data Flow

Files MUST follow this lifecycle:

1. Upload via Web UI
2. Validate format and schema
3. Persist to object storage
4. Create job_id
5. Submit job to FME
6. FME processes file asynchronously
7. FME sends processed CSV back via callback
8. FastAPI stores processed result as review artifact
9. Web UI displays results for user review
10. User approves or rejects output
11. Approved results are published to database

---

### Accepted File Types

* `.gpkg` (GeoPackage)
* `.csv` (UTF-8 encoded structured data)

---

## API Interface Rules

* All endpoints MUST be versioned (`/v1/upload`, `/v1/jobs/{job_id}`)
* Every upload MUST return a job_id immediately
* No synchronous processing is allowed
* All endpoints MUST be idempotent
* Job status MUST be queryable via GET `/jobs/{job_id}`

---

## FME Integration Rules

FME MUST be invoked via:

* FME Flow REST API (preferred)
* or secure workspace trigger mechanism

Each job submission MUST include:

* job_id (correlation identifier)
* file_url (object storage reference)
* metadata (file type, schema version)

---

### FME Output Contract (Callback Model)

After processing, FME MUST:

* generate a processed CSV output
* send a POST request to FastAPI callback endpoint
* include job_id in all responses

FastAPI MUST:

* receive processed CSV asynchronously
* store output as a review artifact (NOT final data)
* link artifact to job_id

---

## Callback Endpoint Requirement

FastAPI MUST expose:

POST /v1/fme/callback

Responsibilities:

* validate job_id
* store processed CSV
* mark job state as PROCESSED
* trigger UI availability for review

---

## Job Lifecycle Management

### Job States

* RECEIVED
* VALIDATED
* STORED
* SUBMITTED_TO_FME
* RUNNING
* PROCESSED
* UNDER_REVIEW
* APPROVED
* REJECTED
* PUBLISHED
* FAILED

---

### Rules

* Every job MUST be traceable via job_id
* State transitions MUST be atomic
* Partial processing is invalid
* Jobs MUST support safe retries
* All transitions MUST be logged

---

## Review & Publish Workflow

### Review Rules

* Processed data MUST NOT be published automatically
* All outputs MUST be visible in Web UI before approval
* Each processed dataset MUST be tied to a job_id
* Multiple outputs per job MUST be versioned

---

### Publish Rules (CRITICAL)

When a user selects "Publish":

* job MUST be in PROCESSED or UNDER_REVIEW state
* FastAPI MUST validate review artifact exists
* Data MUST be written to the target database
* Publish action MUST be idempotent
* Publish event MUST be audited and logged

---

### Data State Model

* PROCESSED → received from FME
* UNDER_REVIEW → visible in UI
* APPROVED → user validated
* PUBLISHED → written to database

---

## Validation Requirements

* File type validation is mandatory
* GeoPackage structure MUST be verified before submission
* CSV schema MUST match registered schema definitions
* Corrupt or malformed files MUST be rejected before FME submission

---

## Observability Requirements

Every job MUST emit:

* job_id
* ingestion_id
* file type
* timestamps per stage

System MUST track:

* ingestion volume
* FME execution time
* failure rates
* retry counts
* review latency
* publish latency

---

## Security Requirements

* All uploads are untrusted input
* File size limits MUST be enforced
* FME endpoints MUST NOT be publicly exposed
* Secrets MUST be stored securely
* Authentication MUST protect upload, review, and publish endpoints
* Only authorized users MAY publish data

---

## Scalability Requirements

* API MUST support concurrent uploads
* FME workload MUST be throttled
* Backpressure MUST be applied under load
* Job submission MUST be async-safe
* Callback ingestion MUST handle retries safely

---

## Failure Handling Rules

* Partial processing is invalid
* Jobs MUST be fully retryable
* Retries MUST be idempotent
* FME failures MUST NOT corrupt ingestion records
* Callback failures MUST be recoverable
* Errors MUST be persisted per job_id

---

## Versioning & Breaking Changes

* API MUST be versioned (/v1/*)
* FME workflows MUST be version-pinned
* Schema definitions MUST be version-controlled
* Review logic changes MUST NOT break historical jobs
* Breaking changes require new job contract version

---

## Governance

This constitution governs all system behavior and supersedes implementation details.

All development MUST:

* comply with orchestration-only API rule
* preserve FME separation of concerns
* maintain full job traceability
* enforce human-in-the-loop approval before publication

Any deviation MUST be explicitly justified and documented.

---

**Version**: 1.1.0
**Ratified**: 2026-06-08
**Last Amended**: 2026-06-08
