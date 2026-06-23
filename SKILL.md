---
name: pdf-writer
description: "Genera PDF da contenuto strutturato (report, lettera, certificato). Usa cerase-office-converter via docx→pdf o md→pdf. Per slide-PDF usa `deck`/`pptx` con format=pdf."
---
# PDF writer — generated PDFs

Create `.pdf` documents from structured content. This skill is **for non-slide PDFs** — formal reports, business letters, certificates, summaries. For presentation-style PDFs use `deck` (Path A) instead.

## When to activate

- "genera un report PDF di ..."
- "crea una lettera in PDF per ..."
- "esporta questo riassunto in PDF"
- `source-to-artifact` Stage 4 with target_format=pdf and target_kind=document (not slides)

Don't activate for: slide decks (`deck`), spreadsheet exports (use `xlsx` → `cerase-office-converter.convert_xlsx_to_pdf`).

## Backend choice

### Path 1 — markdown source → PDF via pandoc + xelatex (recommended)

Fastest, most stable. Use when content is in markdown:

```
# 1. Encode the markdown source as base64
# 2. Call the converter recipe:
call_recipe("cerase-office-converter.convert_md_to_pdf", {input_b64: <base64 of markdown>})
```

Returns `{filename, size_bytes, contents_base64}`. Decode + write `.pdf` to workspace.

### Path 2 — docx source → PDF via LibreOffice

When the document was first created as .docx (e.g. via the `docx` skill), convert:

```
call_recipe("cerase-office-converter.convert_docx_to_pdf", {input_b64: <base64 of .docx>})
```

### Path 3 — direct reportlab (programmatic PDF, fallback)

When the content needs **precise layout control** (certificates, multi-column letters, custom positioning):

```python
from reportlab.lib.pagesizes import A4
from reportlab.pdfgen import canvas
c = canvas.Canvas('<filename>.pdf', pagesize=A4)
c.setFont('Helvetica-Bold', 24)
c.drawString(50, 800, '<title>')
c.setFont('Helvetica', 12)
c.drawString(50, 760, '<body>')
c.save()
```

Path 3 is power-tool — use ONLY when 1+2 don't give the control needed. Most reports fit Path 1.

## Style rules

- **A4 portrait** unless the user specifies landscape (rare for documents).
- **Margins**: 2cm/2.5cm L/R, 2cm T/B (default pandoc settings — don't override unless asked).
- **Header/footer**: page number bottom-right; document title top-left (pandoc handles via `--metadata`).
- **Font**: stick to Liberation Serif / Liberation Sans / DejaVu (these are in the converter image — others may render as placeholders).

## Output handling

After creating the PDF, attach to the reply so the user receives it as a file. Don't paste the base64 in chat. Don't echo file paths the user doesn't need to see.

## Don't

- Don't generate PDFs > 50 pages without asking — usually means the source was way too long to compress into prose. Check with the user.
- Don't embed external images (web URLs) — they render inconsistently. Convert image source separately if needed.
- Don't try to encrypt/password-protect: PoC scope, security on PDFs is v0.x.
