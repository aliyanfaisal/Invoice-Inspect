# Bulk upload and results

[← Back to the README](../README.md)

One invoice at a time is fine for a trial and painful for a month of invoices. Bulk upload lets someone check many invoices in a single go, without waiting for the whole batch before reading anything.

## How it works

1. **Choose many PDFs.** The upload box shows each file in a list, where it can be removed. Duplicates are skipped.
2. **The list is held to the plan.** Each invoice counts as one check, so the list never grows past the checks the person has left today, including any bonus checks they earned. A plan with no daily limit has no cap. The page shows the checks left at all times.
3. **The limit is checked again at the moment of starting**, so a page left open for hours can never start a batch the plan does not allow.
4. **The results page opens straight away.** The invoices are checked one after another, and each review appears the moment it is ready.

## The results page

| Part | What it does |
| --- | --- |
| **Invoice list** | One row per invoice with a status and a one-line summary, such as "2 errors, 120.00" or "No errors found". Beside the review on a desktop, a sideways strip on a phone. |
| **One review at a time** | The full review of the selected invoice: summary, details, tax check, findings, evidence, report download and the assistant. |
| **Progress and counts** | A progress bar and counts of invoices with errors, with warnings and clean, so a large batch can be scanned at a glance. |
| **Stop remaining** | Stops the rest of the batch after the current invoice. |
| **Try again** | Retries one invoice that failed for a temporary reason. |
| **Re-check in place** | Correcting a value re-runs the checks for that invoice only, without leaving the page. |

With a single invoice the list is hidden and the page looks like the normal single-invoice review.

## Respecting plan limits

| Situation | What happens |
| --- | --- |
| Too many checks a minute | The page waits and retries, with a visible countdown, instead of failing the invoice. |
| The day's checks run out | The remaining invoices are marked as not checked, with the reason and what to do next. |
| One invoice fails (too large, too many pages, unreadable scan) | It shows its own message and the rest of the batch carries on. |
| The session expires | The batch stops and says so, rather than failing every file. |

The message for someone who has run out depends on who they are: a guest is told to log in, an unverified account to verify their email, and a verified account to look at the plans.

## Privacy and design decisions

- **Files never wait on the server.** They pass from the upload page to the results page inside the visitor's own browser, and that copy is deleted as soon as the results page reads it. The server only ever sees one file at a time, in memory, for the length of its own request.
- **One request per invoice.** Each invoice is a normal single check, so a failure is isolated, each review can be shown the moment it is ready, and the server never holds a batch.
- **Nothing to resume.** Reloading the results page shows that the review is gone, which is the honest outcome of storing nothing.
- **The single-invoice path is unchanged.** The original upload still works for pages and browsers that cannot run the bulk flow.
