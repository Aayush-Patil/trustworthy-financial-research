
# API Contract

All endpoints use the FastAPI backend.

## 1. Health check

GET /api/health

Response:
{
  "status": "ok"
}

## 2. Upload documents

POST /api/documents/upload

Content type: multipart/form-data

Input:
- files: one or more PDF files

Response:
{
  "documents": [
    {
      "document_id": "doc_001",
      "filename": "annual_report.pdf",
      "status": "uploaded",
      "warnings": []
    }
  ]
}

## 3. Discover company documents

POST /api/company/discover

JSON input:
{
  "company_name": "Example Company",
  "ticker": "EXAMPLE",
  "jurisdiction": "US",
  "start_year": 2023,
  "end_year": 2025
}

Response:
{
  "sources": [],
  "warnings": []
}

Source discovery must use approved sources. The system must report missing coverage rather than implying that all documents were found.

## 4. Create a research run

POST /api/runs

JSON input:
{
  "input_mode": "upload",
  "company_name": "Example Company",
  "ticker": "EXAMPLE",
  "jurisdiction": "US",
  "start_year": 2023,
  "end_year": 2025,
  "question": "Analyze revenue, cash flow and debt risks.",
  "document_ids": ["doc_001"],
  "requested_analyses": [
    "financial_performance",
    "cash_flow",
    "debt_and_liquidity",
    "business_risks"
  ]
}

For company research, input_mode is "company".
For uploaded documents, input_mode is "upload".

Response:
{
  "run_id": "run_001",
  "status": "queued"
}

The example company and IDs are illustrative test values.

## 5. Get run status

GET /api/runs/{run_id}

Response:
{
  "run_id": "run_001",
  "status": "running",
  "current_stage": "retrieval",
  "warnings": []
}

Run status values:
- queued
- running
- completed
- completed_with_warnings
- failed

## 6. Get final research result

GET /api/runs/{run_id}/result

Response:
{
  "run_id": "run_001",
  "status": "completed_with_warnings",
  "company": {},
  "periods": {},
  "report_sections": [],
  "claims": [],
  "evidence_items": [],
  "financial_calculations": [],
  "judge_reviews": [],
  "source_coverage": {},
  "warnings": [],
  "failed_nodes": []
}

The result must be generated from real workflow outputs. It must not return fabricated verification results.

## 7. Trace summary

GET /api/runs/{run_id}/trace

Response:
{
  "run_id": "run_001",
  "events": [],
  "failed_nodes": []
}

Trace access must be subject to appropriate authorization.

## 8. Shared claim status values

- verified_source
- calculated_checked
- interpretation_supported
- unresolved
- blocked
- not_evaluated

## 9. Integration rules

- Use JSON for request and response bodies, except multipart uploads.
- Use the shared Pydantic schemas as the source of truth.
- Return meaningful HTTP errors and structured error details.
- Do not put financial-analysis logic in frontend JavaScript.
- The frontend renders backend statuses; it does not independently decide whether claims are verified.
- Keep field names stable. Coordinate schema changes before implementing them.
- Use mock responses during initial development, then replace them with real backend outputs.
