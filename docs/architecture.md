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

## Supporting parts

- **Provider-agnostic AI layer** with automatic fallback between providers.
- **Plans and limits:** guest, free and paid tiers with per-day and per-minute limits, file-size and page caps.
- **Admin panel** for users, usage analytics, plans, AI and SMTP settings, and email logs.
- **Evaluation harness** that scores each extractor against answer keys. See [Accuracy and testing](accuracy-and-testing.md).
