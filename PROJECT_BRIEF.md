
# Trustworthy Financial Research System

## 1. Project objective

Build a multi-agent financial research and decision-support system that produces source-grounded financial analysis from company documents and uploaded PDFs.

The system must preserve source provenance, calculate financial metrics deterministically, check claims using three specialized LLM judges, detect conflicts, expose uncertainty, and provide a reviewable research report.

It must not execute trades or investment transactions.

## 2. Input modes

### Mode A: Company research
Accept company name, ticker where available, jurisdiction, requested reporting periods, and a research question. Discover documents from approved sources and report source coverage and missing documents.

### Mode B: Document upload
Accept multiple PDFs. Validate and parse files, preserve document metadata and page references, and send them through the same ingestion and research pipeline.

## 3. Locked architecture

1. Custom HTML/CSS/JavaScript frontend.
2. FastAPI backend.
3. Pydantic schemas for shared data contracts.
4. SQLite for run metadata, document records, financial facts, claims, reviews and workflow events.
5. Docling for layout-aware PDF parsing, with PyMuPDF as a fallback and OCR when necessary.
6. Structure-aware chunking that preserves financial table context and source locations.
7. BGE embeddings with persistent local Qdrant storage.
8. BM25 lexical retrieval and Reciprocal Rank Fusion.
9. Second-stage context expansion and reranking.
10. Structured financial-fact extraction and deterministic Python calculations.
11. LangGraph workflow orchestration with explicit states and bounded retries.
12. Three specialized LLM judges.
13. Claim-level validation, report generation, tracing and evaluation.

GraphRAG is optional and must not be required for the initial implementation.

## 4. Retrieval design

Stage 1 retrieves candidate evidence through dense retrieval, lexical retrieval and structured financial-fact lookup.

Stage 2 deduplicates candidates, applies metadata filters, reconstructs missing context, preserves table headings and footnotes, and reranks a bounded set of passages.

Counter-evidence retrieval must be available for material claims and suspected inconsistencies.

Retrieval must preserve document IDs, document versions, page/section locations and evidence IDs.

## 5. Financial correctness

Financial calculations must use deterministic Python functions.

Before comparing financial facts, validate company identity, period, unit, currency, accounting definition, reporting scope and source version.

Every material claim must reference evidence or validated calculations.

LLM agreement must never override a failed deterministic check.

## 6. Three judge roles

Judge 1: Evidence and citation auditor.
Checks whether cited passages actually support claims.

Judge 2: Financial integrity auditor.
Checks periods, units, definitions, calculations and financial inconsistencies.

Judge 3: Adversarial skeptic.
Searches for counter-evidence, omissions, contradictory disclosures and unsupported assumptions.

Judges return structured verdicts and evidence IDs. A majority vote does not override hard validation failures.

## 7. Claim statuses

- verified_source
- calculated_checked
- interpretation_supported
- unresolved
- blocked
- not_evaluated

The frontend must display the status returned by the backend and must not infer verification from report wording.

## 8. Team ownership

Person 1: Core backend, shared schemas, financial calculations, orchestration, judges, acceptance rules and report assembly.

Person 2: Source discovery, PDF ingestion, parsing, chunking, embeddings, Qdrant, BM25, fusion, reranking and evidence retrieval.

Person 3: Custom frontend, API client, upload/discovery interface, report/evidence display and frontend integration tests.

Each person works on their assigned branch. Shared schemas and public API contracts must be coordinated before they change.

## 9. Reliability requirements

- No fabricated citations or financial facts.
- Insufficient evidence must produce a warning, abstention or unresolved status.
- Independent analysis branches may continue when another branch fails.
- Claims dependent on failed checks must not be labelled verified.
- Retries, model calls, retrieval rounds and execution time must be bounded.
- Preserve trace IDs, errors, source references and workflow status.
- Do not commit API keys, secrets, virtual environments or generated runtime data.

## 10. Evaluation requirements

Evaluate retrieval quality, citation support, numerical correctness, financial period/unit consistency, contradiction detection, abstention quality, latency and cost.

Use a manually checked benchmark and report measured results accurately.

A 90–95% result is a target for a clearly defined metric, not a guaranteed overall system accuracy.

## 11. Development rules

- Preserve working code.
- Implement only assigned modules.
- Follow shared schemas and API contracts.
- Do not replace the architecture without agreement.
- Add and run relevant tests.
- Do not claim tests passed unless they were actually executed.
- Report changed files, assumptions, environment requirements and known limitations.
