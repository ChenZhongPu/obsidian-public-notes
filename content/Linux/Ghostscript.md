---
title: Clean a PDF with Ghostscript
draft: false
tags:
  - Linux
date: 2025-12-08
---
_Ghostscript_ is an interpreter for the PostScript® language and PDF files, and it can be used to handle all nitty-gritty problems with respect to PDFs.

## PDF cannot be embedded into Latex

I rely heavily on **Draw.io** (**Diagrams.net**) to create diagrams and export them as PDFs for my LATE​X projects. If you've ever had LATE​X fail to recognize certain objects in those exported PDFs, **Ghostscript** is the robust solution that can save your day.

```bash
gs -dNOPAUSE -dBATCH -sDEVICE -sOUTPUT=out.pdf your_input.pdf
```
