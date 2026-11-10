# Privacy by design

[← Back to the README](../README.md)

Invoices are financial documents, so the product is built to hold as little of them as possible.

## Principles

- **Processed in memory.** A free-tier upload is read, checked and discarded within the request. The PDF is not written to disk or saved to the database.
- **No file persistence in the free flow.** Nothing about the upload is kept for later. The database holds only application data such as accounts, sessions, cache and queued jobs.
- **No signup needed to check an invoice.** The checker works for guests.
- **Storage is opt-in.** Keeping files is never the default. Any storage option is a deliberate choice made by a paying user.

## Bulk uploads

When several invoices are chosen, the files are handed from the upload page to the results page inside the visitor's own browser, and the copy is deleted as soon as the results page has read it. The server then receives **one file at a time**, reads it in memory and forgets it, exactly like a single upload. Reloading the results page shows that the review is gone, because nothing was kept.

## The AI read

The AI vision read is switched on or off by an administrator. There is no per-visitor toggle and no hidden second path. When it is on, the rendered page images of the invoice are sent to the configured AI provider for that one request, and the answer comes back as structured data. The model only reads values. All arithmetic and every verdict come from the deterministic rules engine.

## The invoice assistant

The assistant answers questions about one invoice. To answer, it is given the **extracted fields, line items and findings as text**, never the PDF or the page images, and the question. Nothing about the invoice or the conversation is stored: each question is answered on its own, and only counters (how many questions were asked for an invoice) are kept for a short time to enforce limits. See [Invoice assistant](invoice-assistant.md).

## Evaluation data is separate

The public test invoices in the [Harness Reports](https://invoiceinspect.app/eval-harness-reports) are fictitious documents generated for testing. No visitor upload is ever used in them.

## More

See the [Security page](https://invoiceinspect.app/security) on the live site.
