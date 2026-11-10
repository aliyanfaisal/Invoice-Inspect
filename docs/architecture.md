# Architecture

[← Back to the README](../README.md)

The design rule: **reading** an invoice and **judging** it are separate jobs. Readers turn a PDF into fields. A deterministic rules engine decides whether those fields are right. A language model never decides a verdict.

## Pipeline

```text
PDF upload
   │
   ▼
PdfInspector ──► text layer (pdfparser) ─┐
   │                                     ├─► NormalizedDocument
   └──► Ghostscript → Tesseract OCR ─────┘
   │                                           │
   │                                           ▼
   └──► Ghostscript → page images        RulesExtractor
            └──► AI vision extraction ──► cross-check ◄──┘
                                   │
                                   ▼
                    Verifier ── deterministic rules
                    (line · subtotal · total · tax · rates · duplicates · required fields)
                                   │
                                   ▼
              Findings ──► AI explanation ──► Results page / PDF report
```

## Layers

| Layer | Job | Approach |
| --- | --- | --- |
| **Extraction** | Turn a PDF into structured fields | Two independent readers: native text plus OCR, and an AI vision model that quotes what it saw |
| **Cross-check** | Catch a misread on either side | The two readings are compared field by field; a value only the model found is kept only if its quote appears in the document text |
| **Verification** | Decide if the numbers are right | Deterministic rules engine: plain arithmetic, no AI in the verdict |
| **Reasoning** | Explain findings in plain English | LLM layer that explains and double-checks, but never overrides the math |
| **Review** | Handle unreadable numbers | The user picks the correct value and the checks re-run |
| **Assistant** | Answer questions about one invoice | A small model grounded in the extracted fields and findings, with a strict scope and per-plan limits. See [Invoice assistant](invoice-assistant.md) |

## Bulk upload

Checking many invoices does not change the pipeline above, it changes how files reach it. The browser keeps the chosen files, opens the results page straight away and sends the invoices to the server **one at a time**. Each request is a normal single-invoice check, so each review can be shown the moment it is ready, and a failure in one invoice never affects the others. Plan limits are enforced on every request. See [Bulk upload and results](bulk-upload-and-results.md).

```text
Browser: chosen PDFs ─► results page ─► invoice 1 ─► review 1 shown
                                    └─► invoice 2 ─► review 2 shown
                                    └─► invoice 3 ─► ...
Server: one file per request, read in memory, answered, forgotten
```

## Supporting parts

- **Provider-agnostic AI layer** with automatic fallback between providers.
- **Plans and limits:** guest, free and paid tiers with per-day and per-minute limits, file-size and page caps, and assistant questions per invoice. Counting is shared by the single and bulk flows.
- **Admin panel** for users, usage analytics, plans and limits, AI provider and model settings (the assistant has its own small model), billing, SMTP settings and email logs.
- **Database portability:** the application runs on PostgreSQL or MySQL, with a copy tool to move the data between them.
- **Evaluation harness** that scores each extractor against answer keys. See [Accuracy and testing](accuracy-and-testing.md).
