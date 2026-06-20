# R02 — Artifact Storage and Retrieval

Type: **Infrastructure**  
Summary: Store, index, and retrieve images/files with metadata, derived text, embeddings, and links to conversations and entities.

## Goals

- Store non-text context as first-class artifacts.
- Preserve raw artifacts separately from derived text or summaries.
- Make artifacts retrievable through metadata, semantic search, and explicit conversation links.
- Keep derived artifact context rebuildable and traceable.

## Non-goals

- Replacing canonical message or event storage.
- Treating generated captions or extracted text as the artifact itself.
- Requiring every artifact type to be fully understood at ingestion time.
- Allowing arbitrary artifact access on every surface.

## Proposed Architecture

### Storage choice

Use S3-compatible object storage for raw blobs:

- self-hosted object storage
- cloud object storage
- compatible local development storage

Store only object URIs, hashes, metadata, and links in the database.

### Upload flow

1. Client requests artifact initialization with filename, mime type, and size.
2. Server returns `artifact_id` and a presigned upload URL.
3. Client uploads blob directly to object storage.
4. Client completes the artifact upload.
5. Server finalizes metadata and queues derivations.

### Derivations

Examples:

- images: captions and tags
- PDFs: extracted text and OCR when needed
- code bundles: unpacked filenames and summaries
- documents: chunked text and structural metadata

Derived text is useful for retrieval but does not replace the raw artifact.

## Data Model

Suggested records:

- `artifacts`
  - artifact id
  - owner id
  - sha/hash
  - mime type
  - size
  - object URI
  - source surface
  - filename
  - content hash version

- `artifact_links`
  - artifact id
  - conversation id
  - message id
  - relationship
  - creation time

- `derived_text`
  - artifact id
  - derivation kind
  - language
  - extracted or generated text
  - creation time

- `embeddings`
  - reference type/id
  - model
  - vector id
  - creation time

## Retrieval

Artifacts may be retrieved through:

- semantic search over derived text embeddings
- metadata filtering such as mime, date, source surface, and filename
- explicit links within a conversation
- entity links when available

## Security and Privacy

- Raw artifact access should require authorization.
- Presigned URLs should be short-lived.
- Sensitive artifacts should carry policy metadata.
- Some surfaces, especially voice or public displays, may receive summaries instead of raw details.

## Observability

Trace:

- artifact upload and completion
- derivation jobs
- derived text creation
- embedding creation
- retrieval paths that selected artifacts
- surface redaction or suppression decisions

## Evaluation

Test for:

- artifact metadata integrity
- raw artifact availability
- deterministic derivation where possible
- retrieval through metadata and semantic paths
- safe degradation when derivation fails
- no replacement of raw artifacts by generated summaries
