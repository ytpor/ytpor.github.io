---
title: 'Converting DOCX and PDF to Markdown with MarkItDown'
date: 2026-07-10T10:01:00+08:00
draft: false
url: /2026/07/convert-docx-pdf-to-markdown
tags:
- markitdown
- markdown
- docx
- pdf
---

MarkItDown is a free Microsoft tool that converts documents such as DOCX and PDF into Markdown. It is useful for pulling existing documents into anything that works with plain text - static sites, note systems, or LLM pipelines.

Install it inside a Python virtual environment to keep its dependencies isolated. Create the environment and activate it:

```bash
cd ~/code
virtualenv -p python3 markitdown
cd ~/code/markitdown
source bin/activate
```

Install the package with pip. The `[all]` extra pulls in the optional dependencies for every supported format:

```bash
pip install "markitdown[all]"
```

Verify the installation:

```bash
markitdown --help
```

`markitdown` writes the converted Markdown to stdout, so redirect it to a file. Convert a DOCX file:

```bash
markitdown report.docx > report.md
```

PDF files work the same way:

```bash
markitdown manual.pdf > manual.md
```

To convert a whole directory, loop over the files with the shell. For DOCX files:

```bash
for f in *.docx; do
  markitdown "$f" > "${f%.docx}.md"
done
```

And for PDF files:

```bash
for f in *.pdf; do
  markitdown "$f" > "${f%.pdf}.md"
done
```

DOCX files generally convert with higher accuracy than PDFs, since the text and structure are already machine-readable. Scanned PDFs have no text layer at all, so run them through OCR first (for example with `ocrmypdf`) before converting. After conversion, open the generated `.md` files and review complex tables, images, and layouts - these are the areas most likely to need manual cleanup.
