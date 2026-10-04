# pdf-writer-skill

A Cerase skill that has the assistant produce a PDF document from content the
conversation provides: a report, a business letter, a certificate, a summary.
The assistant uses it for requests such as "generate a PDF report on…" or
"export this summary to PDF", and when `source-to-artifact` asks for a PDF document. Slide
decks go to the `deck` skill, and spreadsheets to the `xlsx` skill with
`cerase-office-converter.convert_xlsx_to_pdf`.

## What the assistant does

It picks one of three paths:

1. **Markdown to PDF**, the default: writes the content as a markdown file in
   the workspace, with a YAML block for title, author and date, and converts
   it with `cerase-office-converter.convert_md_to_pdf` (pandoc with XeLaTeX).
2. **Word to PDF**, when the document already exists as `.docx`, for example
   from the `docx` skill or sent by the person:
   `cerase-office-converter.convert_docx_to_pdf` (LibreOffice).
3. **HTML to PDF**, when the layout itself matters, such as a certificate, a
   letter on letterhead or a one-page flyer: the assistant writes one HTML file
   with its CSS in a `<style>` block and converts it with
   `cerase-office-converter.convert_html_to_pdf` (headless Chromium), which
   keeps CSS grid, flexbox and background colours. Paper size (A4, A3, A5,
   letter, legal) and orientation are parameters of the call, and images go
   inline as `data:` URIs because the converter receives the HTML file alone.

The converter writes the PDF to `outputs/` in the workspace and returns its
path; the assistant attaches it with `[[attach: <path>]]` and never pastes its
content or base64 in the chat. The skill's layout rules ask for A4 portrait
unless landscape is requested; in path 3, only the Liberation Serif,
Liberation Sans, DejaVu or Noto fonts, which are the ones installed in the
converter; one title, then sections, and a short summary at the start of a
report longer than a few pages. The assistant's container has no Python,
pandoc, LibreOffice or browser, so the assistant never builds the PDF itself.
It asks before producing more than 50 pages and does not password-protect the
PDF.

## Requirements

- The `cerase-office-converter` connector for all three paths:
  `convert_md_to_pdf`, `convert_docx_to_pdf` and `convert_html_to_pdf`.

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
