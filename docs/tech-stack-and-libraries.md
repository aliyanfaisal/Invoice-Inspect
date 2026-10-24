# Tech stack and main libraries

[← Back to the README](../README.md)

## Stack

| Area | Tools |
| --- | --- |
| Backend | Laravel 13, PHP 8.3+ |
| Frontend | Blade, Tailwind CSS 4, Vite |
| Database | PostgreSQL |
| Admin panel | Filament 5 |
| AI | Anthropic Claude and OpenRouter (Gemini 2.5 Flash by default) behind one provider interface with automatic fallback |
| Testing and quality | PHPUnit, Laravel Pint, custom evaluation harness |
| Deployment | GitHub Actions |

## PDF and OCR toolchain

| Step | Tool |
| --- | --- |
| Native text and layout extraction | [smalot/pdfparser](https://github.com/smalot/pdfparser) |
| Rendering PDF pages to images (for OCR and vision) | [Ghostscript](https://www.ghostscript.com/) (`gs`) |
| OCR for scanned invoices | [Tesseract OCR](https://github.com/tesseract-ocr/tesseract), multi-language capable |
| Vision extraction | Gemini 2.5 Flash, Claude or other vision models; page images at 200 dpi, one request per invoice |
| Test invoice generation | [FPDF](http://www.fpdf.org/) for the built-in single-page and multi-page generators, [ReportLab](https://www.reportlab.com/opensource/) for the independently written invoice sets |
| Public test reports | [ReportLab](https://www.reportlab.com/opensource/), built from the saved run results |

## Main libraries

- [`laravel/framework`](https://laravel.com) for the application core
- [`filament/filament`](https://filamentphp.com) for the admin dashboard
- [`smalot/pdfparser`](https://github.com/smalot/pdfparser) for PDF text extraction
- [`league/commonmark`](https://commonmark.thephpleague.com) for the blog and landing-page Markdown
- [`symfony/yaml`](https://symfony.com/doc/current/components/yaml.html) for content front matter
- [`tailwindcss`](https://tailwindcss.com) and [`vite`](https://vite.dev) for the frontend build
- [`phpunit/phpunit`](https://phpunit.de) and [`laravel/pint`](https://laravel.com/docs/pint) for tests and code style
