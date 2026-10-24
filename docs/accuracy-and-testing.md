# Accuracy and testing

[← Back to the README](../README.md)

InvoiceInspect is tested with an evaluation harness. It runs the checker on fictitious invoices whose correct values are known, compares what the checker found with those answers, and scores the result. Each invoice has an answer key that lists every field, every line item and the mistakes planted in it on purpose. The invoices, answer keys, raw results and every report are published on the [Harness Reports page](https://invoiceinspect.app/eval-harness-reports), and each invoice opens in a new tab from the [test invoices list](https://invoiceinspect.app/eval-harness-reports/invoices).

## Test reports

| Report | What it measured | Result |
| --- | --- | --- |
| [Report 1: Baseline](https://invoiceinspect.app/harness-reports/02-Test-Report-1-Baseline.pdf) | The extractor as it stood, on 30 independent and 40 generator invoices | Independent invoices: 84.5% field accuracy, 25 of 32 planted mistakes found, 30 false alarms. Generator invoices: 99.9%, 20 of 20, no false alarms. |
| [Report 2: Baseline After Fixes](https://invoiceinspect.app/harness-reports/03-Test-Report-2-After-Fixes.pdf) | The six reading problems from Report 1, fixed and scored again on the same invoices | Independent invoices: 98.1% field accuracy, 31 of 32 found, 6 false alarms. Generator invoices unchanged at 99.9%. |
| [Report 3: Multi Page Invoice Testing and Issues Found](https://invoiceinspect.app/harness-reports/04-Multi-Page-Invoice-Testing-and-Issues-Found.pdf) | 40 new multi-page invoices the extractor had never seen | 72.2% field accuracy, 13 of 20 found, 50 false alarms. Lower accuracy is expected on invoices like these. |
| [Report 4: Multi Page Invoice Fixes](https://invoiceinspect.app/harness-reports/05-Multi-Page-Invoice-Fixes.pdf) | The problems from the multi-page report, fixed and scored again on the same 40 invoices, plus the older sets scored again | Multi-page invoices: 99.98% field accuracy, 20 of 20 found, no false alarms. Independent invoices 99.1% and generator invoices 99.9% or better, so nothing got worse. The fixes were made on these invoices, so this shows the problems are gone, not how other invoices read. |
| [Report 5: Fresh Invoice Test, Rules and Vision Model](https://invoiceinspect.app/harness-reports/06-Fresh-Invoice-Test-Rules-and-Vision.pdf) | 30 new invoices in ten kinds of document that neither reader has seen, scored with the rules extractor and with a vision extractor in which an AI model reads a picture of each page | Rules: 62.0% field accuracy, 15 of 26 mistakes found, 30 false alarms. Vision: 99.4%, 26 of 26 found, no false alarms. On the older sets, which the rules were fitted to, the two are level on single-page invoices and rules lead on multi-page ones. |
| [Report 6: Rules Extractor General Improvements](https://invoiceinspect.app/harness-reports/07-Rules-Extractor-General-Improvements.pdf) | General changes to how the rules extractor reads text, scored on the 30 invoices of Report 5 and on the older sets | Field accuracy 62.0% to 98.9%, 26 of 26 mistakes found (was 15), false alarms 30 to 8. Made on those same invoices, so not a fresh measure; older sets unchanged. |
| [Report 7: Fresh Test, 50 Invoices](https://invoiceinspect.app/harness-reports/08-Fresh-Test-50-Invoices.pdf) | 50 new invoices (20 from the generator, 30 in ten new page structures, countries and languages), scored before any change | On the 30 new layouts: rules 70.3% field accuracy, 14 of 27 mistakes found, 53 false alarms; vision 97.9%, 27 of 27, 8 false alarms. Rules read the 20 generator invoices perfectly. |
| [Report 8: Rules Extractor Improvements on Corpus E](https://invoiceinspect.app/harness-reports/09-Rules-Extractor-Improvements-on-Corpus-E.pdf) | General fixes for the problems found in Report 7 (currency signs, label words in more languages, dates, a few structures), with ten of the 50 invoices kept aside | Field accuracy 92.2% to 99.9%, 37 of 37 mistakes found (was 24), false alarms 53 to 2. Ten invoices kept aside: 64.9% to 100%. Older sets unchanged or better. |

How the scoring works is explained in the [Evaluation Harness Guide](https://invoiceinspect.app/harness-reports/01-Evaluation-Harness-Guide.pdf), and the design and baseline results are written up in the [Research Report](https://invoiceinspect.app/research/InvoiceInspect-Research-Report.pdf).

## Test invoices

| Set | Invoices | What it holds |
| --- | --- | --- |
| **Corpus A: independent** | 30 | Written separately from the extractor with different PDF software: 10 page structures, 8 currencies, 4 languages, several tax types. 15 correct, 15 with one to three planted mistakes. |
| **Corpus B: built-in generator** | 40 | The project's own generator in 10 visual designs, half correct and half with one planted mistake. The easier set. |
| **Corpus C: multi-page** | 40 | 40 invoices with 40 answer keys. Of these, 9 run to two pages, 21 to three and 10 to four, and 20 carry a planted mistake. Wrapped descriptions, section totals, carried-forward rows, deposits, ship-to addresses and bank details, in five currencies including the Swiss number format. |
| **Corpus D: new independent set** | 30 | Written separately from the extractors after the fixes in Report 4, in ten kinds of document: till receipt, amount-first bill, prose list, e-mail, cards, grouped, tax column, quantity-first, sidebar and summary-first. 11 currencies and 6 languages. 15 correct, 15 with one to three planted mistakes. |

## Five extractors

| Name | What it does |
| --- | --- |
| `rules` | The real extractor: reads the PDF text and finds each field by pattern, position and arithmetic. |
| `rules-ocr` | The same extractor, run on a scanned picture of the page read by OCR software. |
| `vision` | A model-based extractor: each page is shown as a picture to an AI model, which reads every value and line item. It uses no text layer and no OCR software, and each answer is saved so scores can be rebuilt. |
| `oracle` | Test only. Ignores the PDF and returns the answer key, so it must score 100%. It shows whether the checking rules or the answer keys have a fault. |
| `broken` | Test only. Returns the answer key with about 30% of invoices damaged on purpose, so it must score badly. It shows the harness notices mistakes. |

## What the results show

- **The checking rules are correct.** With perfect input (the oracle) the rule engine found every planted mistake with no false alarms on all three sets: 32 of 32, 20 of 20 and 20 of 20.
- **Reading is the hard part.** Accuracy depends on page layout, and the extractor does much better on layouts it was built beside than on independent ones.
- **Fixing the reading problems works.** On the independent invoices field accuracy rose from 84.5% to 98.1% and false alarms fell from 30 to 6. That score was tuned on the same invoices, so Report 3 uses a fresh set.
- **Multi-page invoices expose new problems.** On the 40 new invoices the extractor scored 72.2%. All 10 Swiss-franc invoices score about 4%, because the Swiss number format (1'234.56) is not read, and the other 30 score 94.6%. The report also lists wrapped descriptions that are cut short, item codes joined to descriptions, section totals and deposit lines read as totals or discounts, and a few layout-specific misreads.
- **A model reads layouts nobody wrote rules for.** On the 30 new invoices the rules extractor scored 62.0% and the vision extractor, an AI model reading a picture of each page, scored 99.4%. The rules extractor failed on the layouts with no table (a prose list, an e-mail, cards). The vision extractor's mistakes were mostly misread digits in small print and the meaning of rows such as carried forward and deposits. The improved instruction was written after seeing mistakes on this set, so its score there is not a fresh measure.
- **Results are published as they are.** Each report lists the problems found, and later reports score the same invoices again so that the effect of each improvement can be measured.
