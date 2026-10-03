# pdf-writer-skill

A Cerase skill that has the assistant produce a PDF document from structured
content: a report, a business letter, a certificate, a summary. The assistant
uses it for requests such as "generate a PDF report on…" or "export this
summary to PDF", and when `source-to-artifact` asks for a PDF document. Slide
decks go to the `deck` skill, and spreadsheets to the `xlsx` skill with
`cerase-office-converter.convert_xlsx_to_pdf`.

## What the assistant does

It picks one of three paths:

1. **Markdown to PDF**, the default: encodes the markdown as base64 and calls
   `cerase-office-converter.convert_md_to_pdf` (pandoc with XeLaTeX).
2. **Word to PDF**, when the document already exists as `.docx`, for example
   from the `docx` skill: `cerase-office-converter.convert_docx_to_pdf`
   (LibreOffice).
3. **reportlab**, only when the layout needs exact positioning that the first
   two cannot give, such as a certificate: the assistant writes the PDF with
   reportlab itself.

The result is attached to the reply as a file, never pasted as base64. The
skill's layout rules ask for A4 portrait unless landscape is requested,
pandoc's default margins, the page number bottom right and the title top left,
and only the Liberation or DejaVu fonts installed in the converter. The
assistant asks before producing more than 50 pages, does not embed images from
web URLs, and does not encrypt or password-protect the PDF.

## Requirements

- The `cerase-office-converter` connector for paths 1 and 2.
- Python with `reportlab` wherever the assistant runs code, for path 3.

## Files

| File | Purpose |
|---|---|
| `SKILL.md` | The instructions the assistant loads: `name` and `description` frontmatter, then the three paths and the layout rules. |
| `cerase.json` | Marketplace manifest: namespace `studio.guidance`, name `pdf-writer`, display name, description, licence. |
| `i18n.yaml` | Italian display name and description for the Marketplace; not sent to the assistant. |
| `LICENSE` | MIT licence text. |

## Installation

Published in the Cerase Marketplace as `studio.guidance/pdf-writer`
([marketplace page](https://marketplace.cerase.ai/en/p/studio.guidance/pdf-writer)).
A Cerase appliance does not attach it by default: an administrator installs it
from the Marketplace.

## License

MIT. See [LICENSE](LICENSE).
