<div align="center">

<img src="assets/logo-main.png" alt="InvoiceInspect" width="360">

**Before you pay an invoice, get a second opinion.**

Upload a PDF invoice and find out in seconds whether the line totals, tax and grand total actually add up, with the evidence to prove it. 

[**Try it live → invoiceinspect.app**](https://invoiceinspect.app) · [Portfolio case study](https://aliyanfaisal.com/products/invoiceinspect) · [Request source access](mailto:aliyanbarcha15@gmail.com?subject=InvoiceInspect%20private%20repo%20access)

![Laravel](https://img.shields.io/badge/Laravel-13-FF2D20?style=flat-square&logo=laravel&logoColor=white)
![PHP](https://img.shields.io/badge/PHP-8.3+-777BB4?style=flat-square&logo=php&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind-4-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=flat-square&logo=postgresql&logoColor=white)
![Status](https://img.shields.io/badge/Status-Live-10b981?style=flat-square)

<br>

<a href="https://invoiceinspect.app">
  <img src="assets/hero.png" alt="InvoiceInspect home page: upload a PDF invoice to check line totals, tax and the grand total" width="900">
</a>

</div>

---

> **This is a showcase repository.** The full source code lives in a private repository. The product is live and free to use with no signup, and I'm happy to give access to the source on request: [email me](mailto:aliyanbarcha15@gmail.com?subject=InvoiceInspect%20private%20repo%20access).

## What it does

InvoiceInspect is an invoice validator for finance teams, accounts payable, freelancers and business owners. It reads a vendor invoice, rebuilds every number from scratch and flags anything that doesn't reconcile **before** money leaves the account.

- **Line items:** quantity × unit price checked against every line total.
- **Subtotal and grand total:** line sums, discounts, shipping and tax reconciled against the printed totals.
- **VAT / GST / sales tax:** rates compared with the tax amounts actually charged, and tax IDs checked for presence.
- **Duplicate charges:** repeated line items and double-billed amounts surfaced for review.
- **Missing fields:** invoice number, dates and other required details called out.
- **Evidence, not just a verdict:** each finding shows *Expected* vs *Invoice says* vs *Difference*, tied back to the page it came from.

## Why it's trustworthy

The core idea is simple: **money math should never be guessed by a language model.**

| Layer | Job | Approach |
| --- | --- | --- |
| **Extraction** | Turn a PDF into structured fields | Two independent readers: **native text + OCR** (smalot/pdfparser, Ghostscript, Tesseract) and an **AI vision model** that reads the rendered pages and quotes what it sees |
| **Verification** | Decide if the numbers are right | **Deterministic rules engine**: plain arithmetic, no AI in the verdict |
| **Reasoning** | Explain findings in plain English | LLM layer that explains and double-checks, but never overrides the math |
| **Review** | Handle unreadable numbers | When a value can't be read with confidence, the user picks the correct one and the checks re-run |

## Highlights

- **Rules-based verification engine** with independent rules for line totals, subtotals, grand total, tax, expected rates, required fields and duplicate charges.
- **Dual-path PDF reading:** native text extraction for digital PDFs, Ghostscript + Tesseract OCR for scans, with a visible *scan reading quality* indicator.
- **AI vision extraction:** a vision model reads the page images and must quote the exact printed text for every value. It never calculates or infers. Its instruction defines what each value is (for example that a deposit is not a discount and that carried-forward rows are not line items), and its answers are tidied (a printed "kr" becomes SEK, a discount is a positive amount).
- **Cross-checking:** the AI read and the rules-based read are compared field by field, so a misread on either side is caught instead of trusted.
- **Downloadable PDF report** of the results for sharing with a vendor or an approver.
- **Evaluation harness with published results:** invoice generators and a scorer measure extraction and finding accuracy against known answers, for five extractors including a model-based one. The test invoices, answer keys, raw results and every test report are public, including the weak results (see [Accuracy and testing](#accuracy-and-testing)).
- **Provider-agnostic AI layer** with fallback between providers.
- **Plans and limits:** guest, free and paid tiers with per-day and per-minute limits, file-size and page caps.
- **Accounts and admin:** signup, login, email verification, password reset, and an admin dashboard for users, usage analytics, plans, SMTP settings and email logs.
- **Content and SEO:** blog system with Markdown rendering, guides, tax-specific landing pages (VAT, GST), sitemap and structured data.

## Accuracy and testing

Accuracy is measured, not claimed. An evaluation harness runs the checker on fictitious invoices whose correct values are known, including mistakes planted on purpose, and scores what it finds. The invoices, answer keys, raw results and every report are public, including the weak results.

| Finding | Evidence |
| --- | --- |
| **The checking rules are correct.** | With perfect input, the rule engine found every planted mistake with no false alarms. |
| **Reading is the hard part.** | On 30 independent invoices the rules extractor read 84.5% of fields correctly before fixes and 98.1% after (fixed on the same set, so not a fresh measure). |
| **A vision model covers layouts nobody wrote rules for.** | On 30 unseen invoices: rules 62.0%, vision 99.4%, with 26 of 26 planted mistakes found. |
| **Fixes generalize, with caveats.** | General fixes took the 50-invoice set from 92.2% to 99.9%. The ten invoices kept aside, which the fixes were not built on, went from 64.9% to 100%. |

Full detail is in [Accuracy and testing](docs/accuracy-and-testing.md): all eight reports, the test invoice sets, the five extractors and what the results show. Browse them live on the [Harness Reports page](https://invoiceinspect.app/eval-harness-reports).

## Privacy by design

Free-tier uploads are **processed in memory and never stored**. There is no file persistence in the free flow, and no signup is needed to check an invoice. Keeping files is never the default. See [Privacy by design](docs/privacy-by-design.md) and the [Security page](https://invoiceinspect.app/security).

## Main libraries

| Area | Tools |
| --- | --- |
| Application | [Laravel 13](https://laravel.com) on PHP 8.3+, [Filament](https://filamentphp.com) admin panel |
| PDF and OCR | [smalot/pdfparser](https://github.com/smalot/pdfparser), [Ghostscript](https://www.ghostscript.com/), [Tesseract](https://github.com/tesseract-ocr/tesseract) |
| AI | Anthropic Claude and OpenRouter behind one provider interface with fallback |
| Frontend | Blade, [Tailwind CSS 4](https://tailwindcss.com), [Vite](https://vite.dev) |
| Quality | PHPUnit, Laravel Pint, a custom evaluation harness |

The full stack, PDF and OCR toolchain and library list are in [Tech stack and main libraries](docs/tech-stack-and-libraries.md).

## Architecture at a glance

Reading an invoice and judging it are separate jobs. Two independent readers turn the PDF into fields, a cross-check compares them, and a deterministic rules engine decides the verdict. A language model never overrides the math.

```text
PDF upload ─► text layer + OCR ──┐
          └─► AI vision read ────┴─► cross-check ─► deterministic rules ─► findings ─► results page / PDF report
```

The full pipeline diagram and layer breakdown are in [Architecture](docs/architecture.md).

## Documentation

| Page | Covers |
| --- | --- |
| [Accuracy and testing](docs/accuracy-and-testing.md) | Evaluation harness, test reports, invoice sets, extractors, results |
| [Privacy by design](docs/privacy-by-design.md) | What is stored, what is not, and how the AI read works |
| [Tech stack and main libraries](docs/tech-stack-and-libraries.md) | Stack, PDF and OCR toolchain, libraries |
| [Architecture](docs/architecture.md) | Pipeline and layers |

## Links

- **Live app:** [invoiceinspect.app](https://invoiceinspect.app)
- **Case study and details:** [aliyanfaisal.com/products/invoiceinspect](https://aliyanfaisal.com/products/invoiceinspect)
- **Source access:** [aliyanbarcha15@gmail.com](mailto:aliyanbarcha15@gmail.com?subject=InvoiceInspect%20private%20repo%20access)

## About the author

Built by **Aliyan Faisal**, a full-stack developer who builds products end to end, from the rules engine to the deployment pipeline. More of my work is on my [portfolio](https://aliyanfaisal.com).

Interested in the code, a similar build, or working together? Reach out at [aliyanbarcha15@gmail.com](mailto:aliyanbarcha15@gmail.com).

---

<sub>© Aliyan Faisal. All rights reserved. This repository contains documentation and screenshots only.</sub>
