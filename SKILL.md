---
name: pdf-writer
description: "Generates a plain PDF through the office converter from text for internal use — notes, a summary, an export, a certificate on a designed page. A document a client or management will read (a quote, a proposal, a report, a letter) is the `business-document` skill; a slide deck is `deck`; a spreadsheet in PDF is `xlsx`."
---
# PDF writer — documents as PDF

Create a `.pdf` from content the conversation provides, for internal use: notes, summaries, exports, a certificate on a designed page. A quote, a proposal, a report or a letter that a client or management will read is made with the `business-document` skill, never here: this converter's layout reads as an academic paper. A presentation in PDF is the `deck` skill. Your container runs no Python, so you never build the PDF yourself: the office converter does.

## When to activate

- "export these notes to PDF"
- "a PDF of this summary, just for us"
- `source-to-artifact` with an internal PDF as the target

## Choose the path

### Path 1 — markdown to PDF (the default)

For text with headings, lists and tables. Write `<name>.md` in the workspace, starting with a YAML block for the title:

```
---
title: Quarterly report for Northwind Traders
author: Ada Rossi
date: 2026-10-05
---
```

then `#` sections, `- ` bullets and pipe tables. Then:

```
call_recipe("cerase-office-converter.convert_md_to_pdf", {"path": "<name>.md", "output_filename": "<name>.pdf"})
```

### Path 2 — a Word document to PDF

When the document already exists as a .docx (made with the `docx` skill, or sent by the person):

```
call_recipe("cerase-office-converter.convert_docx_to_pdf", {"path": "<file>.docx", "output_filename": "<name>.pdf"})
```

### Path 3 — a designed page to PDF

When the layout itself matters: a certificate, a letter on letterhead, a one-page flyer, columns placed where you choose. Write `<name>.html` in the workspace, one file with its CSS in a `<style>` block, then:

```
call_recipe("cerase-office-converter.convert_html_to_pdf", {"path": "<name>.html", "output_filename": "<name>.pdf", "paper": "A4", "orientation": "portrait"})
```

It prints the page the way a browser does, so CSS grid, flexbox and background colours are kept. `paper` is A4, A3, A5, letter or legal; `orientation` is portrait or landscape. Put images inline as `data:` URIs: the converter receives the HTML file alone, so an image named by a relative path is not found.

Each path answers `{path, filename, size_bytes}`: the PDF is in your workspace at `path`, which is `outputs/<name>.pdf`. These calls are the complete set. Do not invent others.

## Deliver

Attach the file: `[[attach: outputs/<name>.pdf]]`. Never paste its content or any base64 in the chat, and do not show the person file paths they do not need.

## Style rules

- **A4 portrait** unless the person asks for landscape.
- **Fonts**: in Path 3 use Liberation Serif, Liberation Sans, DejaVu or Noto; other fonts are not on the converter and print as a substitute.
- **One title**, then sections; a report longer than a few pages starts with a short summary.

## Don't

- Don't write Python, or call `pandoc`, `libreoffice` or a browser from bash: none of them is in your container.
- Don't produce more than 50 pages without asking: it usually means the source was too long to turn into prose.
- Don't password-protect a PDF: this skill cannot.
