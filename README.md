# Weekly Report Generator

A Claude Code skill for generating polished weekly research reports in LaTeX format. Designed for PhD students and researchers who report to their advisors weekly.

## What it does

- Interviews you about your week's work (activities, detail level, figures, author info)
- Uses **clickable options** for detail level (Detailed / Brief / Skip) — no typing required
- Checks for information gaps and asks before fabricating anything
- Presents content in plain text for your review **before** writing any LaTeX
- Generates a structured `.tex` project with title page, table of contents, and numbered sections
- Packages everything into a zip, ready to compile

## Install

```bash
claude mcp install weekly-report
```

Or manually: copy this directory into `.claude/skills/weekly-report/`.

## Usage

Just say:

- "write my weekly report"
- "generate weekly report"
- "周报"
- "/weekly-report"

The skill will walk you through the rest.

## Output

```
weekly_report_YYYY-MM-DD/
├── main.tex
├── 1_highlights.tex
├── 2_section.tex
├── ...
└── figure/

weekly_report_YYYY-MM-DD.zip
```

Compile with `pdflatex main.tex` (run twice for TOC).

## Design principles

- **No fabrication** — never invents details. If info is missing, it asks.
- **Review before LaTeX** — you see plain-text content first, approve it, then it generates code.
- **Highlights are short** — 3-8 word tags, not full sentences.
- **You control detail** — every section gets a Detailed / Brief / Skip choice.
- **Advisor-aware** — written as a report *to* your advisor, from your perspective.
