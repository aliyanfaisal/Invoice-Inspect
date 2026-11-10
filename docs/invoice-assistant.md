# Invoice assistant

[← Back to the README](../README.md)

A finding tells you what is wrong, but not always what it means or what to do about it. Every review has an **Ask about this invoice** box so the person can ask.

## What the person sees

- **Suggested questions written from the invoice itself.** For an invoice with problems: "Why does line 2 not add up?", "Why is the grand total different from the lines plus tax?", "Is line 4 really charged twice?" and "What should I ask the supplier to correct?". For a clean invoice, questions built from its own figures, such as "Is the 10% tax calculated correctly?".
- **A short, plain answer** with a count of questions left for that invoice.
- **A clear note** that AI answers can be wrong and are not tax or legal advice.

## What the assistant is allowed to know

A small, low-cost model sees only:

- the extracted fields (supplier, dates, currency, subtotal, tax, total),
- the line items,
- the findings and their plain-language explanations,
- the question.

It never sees the PDF or the page images. Text that came from the invoice is cleaned and treated as data, never as instructions.

## Keeping it on topic

The assistant answers questions about the invoice in front of it, how the checks work, and short explanations of an invoice or tax term where that helps. Everything else is refused.

| Layer | What it does |
| --- | --- |
| **Screening** | Junk, empty questions and questions containing links or addresses are turned away before any model call. |
| **A strict instruction** | The model must say whether the question is in scope. Out of scope covers general knowledge, writing, code, opinions, questions about the assistant itself, and any request to forge or backdate an invoice, evade tax, commit fraud or hide an error. |
| **A refusal it cannot change** | When a question is out of scope the person sees a fixed message written by the system. Whatever the model wrote is discarded, so a cleverly worded question cannot talk it into a free-form answer. |
| **Answer checks** | An answer containing a link, an address or a figure that is not in the invoice data is dropped and replaced with a message that it could not be answered reliably. |
| **Injection resistance** | Instructions hidden in a supplier name, a description or the question itself are ignored, and the assistant never reveals its instructions. |

## Limits and spam protection

- **Questions per invoice are set per plan** in the admin, with no release needed (guests 5 and free accounts 10 by default; a plan can be unlimited, or have the assistant switched off).
- **A refused question still uses one question**, so spam gains nothing.
- **A daily cap per person** across all invoices stops someone from starting over with the same invoice to get fresh questions. Correcting a value and re-checking does not reset the count either.
- **Cloudflare Turnstile** is asked once per invoice, only when the person starts typing.
- **Rate limited**, and a model outage is reported without using up a question.

## Cost control

The assistant uses a small model chosen in the admin, separate from the one that reads invoices, with a short answer limit and no fallback model. Each question is a single, small request.

## What it does not do

Each question is answered on its own, so there is no memory of earlier questions. Nothing about the invoice or the conversation is stored, and the assistant does not give tax, legal or accounting advice.

## How it is tested

The assistant has its own automated tests: that a question about the invoice is answered and counted, that an out-of-scope question always gets the fixed refusal even if the model answers, that links and invented figures never reach the person, that limits follow the plan and the daily cap, that a re-check keeps the same count, that a model failure is not counted, that the spam check is required once, and that the suggested questions name what was actually found.
