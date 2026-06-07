---
name: weekly-report
description: Generate weekly research progress reports in LaTeX, packaged as a zip ready to compile to PDF. Trigger when the user says "write my weekly report", "generate weekly report", "make a week report", "周报", "weekly report", or asks you to create a LaTeX report about their weekly research progress. This skill covers the full workflow: interview the user about their week's work → generate well-structured LaTeX project following an academic-style report template → organize sections with proper formatting → package into a downloadable zip archive. Always use this skill when the user needs a structured weekly report for their research group, advisor, or lab, even if they don't explicitly mention LaTeX — they just need to describe what they worked on.
---

# Weekly Report Generator

You are helping the user create a polished weekly research report in LaTeX format. The report follows an academic-style template with a formal title page, table of contents, and numbered sections.

## Workflow

Follow these steps in order:

### Step 1: Initial information collection

Ask the user in one message:

- **Activities** — What did they work on this week? List the main items.
- **Figures** — Any figures/plots/screenshots to include?
- **Author info** — Reuse previously saved author info if available. If first use, ask.

After the user responds with their activities, proceed to Step 2.

### Step 2: Detail level and information gaps (use AskUserQuestion)

**CRITICAL: Use the AskUserQuestion tool for this step.** This gives the user clickable options instead of requiring typed responses.

For each activity the user listed, ask one question with these options:

| Label | Description |
|-------|-------------|
| **Detailed** | Full paragraphs with background, method, and outcomes |
| **Brief** | 2-3 sentences summarizing the key point |
| **Skip** | Exclude this activity from the report |

Set `multiSelect: false` so the user picks one per activity.

If multiple activities are independent (no dependency between choices), ask them in **one AskUserQuestion call** with one question per activity. The `header` for each question should use a short tag derived from the activity name (max 12 chars).

**Information gap check:** For any activity where the user provided only a minimal description (e.g., "helped with a PPT" or "read some papers"), ask a follow-up question:

| Label | Description |
|-------|-------------|
| **Add details now** | User will type additional context in "Other" |
| **Write what you have** | Generate a minimal section from available info; user fills gaps later |
| **Write and I'll review** | Generate what you can and mark uncertain parts for review |

**Example AskUserQuestion call:**

After user says they worked on: (1) GanPoJuncai PPT, (2) New framework design with literature survey:

```json
{
  "questions": [
    {
      "question": "How much detail for GanPoJuncai PPT? (The description is minimal — I'll ask for clarification separately.)",
      "header": "PPT detail",
      "options": [
        {"label": "Brief", "description": "2-3 sentences summarizing the key point"},
        {"label": "Detailed", "description": "Full paragraphs with background, method, and outcomes"},
        {"label": "Skip", "description": "Exclude this activity from the report"}
      ]
    },
    {
      "question": "How much detail for the new framework design?",
      "header": "Design detail",
      "options": [
        {"label": "Detailed", "description": "Full paragraphs with background, method, and outcomes"},
        {"label": "Brief", "description": "2-3 sentences summarizing the key point"},
        {"label": "Skip", "description": "Exclude this activity from the report"}
      ]
    }
  ]
}
```

If ANY activity has minimal context, also include an information-gap question:

```json
{
  "question": "I don't have enough context about 'GanPoJuncai PPT' to write meaningfully. What should I do?",
  "header": "PPT info gap",
  "options": [
    {"label": "Add details now", "description": "Select this and type additional context in 'Other'"},
    {"label": "Write what you have", "description": "Generate a minimal section from available info"},
    {"label": "Skip it", "description": "Exclude this activity from the report"}
  ]
}
```

**If the user selects "Add details now":** they will enter additional info via the "Other" text field. Read that input and incorporate the details.

**Keep asking until all detail levels are decided and all information gaps are resolved.** Do NOT proceed to drafting until every activity has a confirmed detail level and sufficient context.

### Step 3: Present Content and Review (CRITICAL — do NOT skip)

**Before writing any LaTeX**, present the content to the user in plain, readable formatting. Do NOT show LaTeX code at this stage.

- Use **Markdown-style formatting**: `**bold**` for key terms, numbered lists, bullet points, section headers.
- Display equations in plain form like `B̄·Ā ≠ Σ p_k·B_k·A_k` or on their own indented line.
- Make the text easy to scan: short paragraphs, visual breaks, structure.
- This is what Claude Code naturally outputs — not LaTeX source.

The content should look like a polished Markdown document, not code.

Show:

1. The list of sections and their titles
2. The highlights (as short, concise phrases — one line each)
3. The full text for each non-highlights section, written in final-ready prose

The highlights section MUST use short phrases only:
- "GanPoJuncai PPT" (not "Supported Dr. Hu in preparing a presentation for the Gan Po Jun Cai program")
- "Federated fine-tuning LoRA Aggregation survey" (not "Conducted a targeted literature survey on...")
- "Enhanced two-stage framework design with post-training distillation"

Each highlight should be a 3-8 word label that captures the essence. No full sentences.

Use AskUserQuestion to present the content for review:

```
questions: [{
  question: "Does this look good? What needs to be changed, added, or removed?",
  header: "Review",
  options: [
    {label: "Approve", description: "Content is ready. Generate the LaTeX and zip it."},
    {label: "Needs changes", description: "I'll describe what to change via 'Other'"},
    {label: "Add a section", description: "I want to add a new section. I'll describe it via 'Other'"}
  ]
}]
```

#### Handling review responses

**"Approve":** Proceed to Step 4 (Generate LaTeX).

**"Needs changes":** The user will describe what to change via "Other". Make the edits and present the updated content again. Use another AskUserQuestion round.

**"Add a section":** The user wants to insert a new section. Treat this exactly like a new activity:

1. Ask via AskUserQuestion for the detail level (Detailed / Brief) of the new section.
2. Check for information gaps — if the user's description is minimal, ask the follow-up info-gap question.
3. Write the new section content and present the **full updated content** (all sections) again for review.

**Iterate until the user approves.** Every time content changes (edits or new sections), show the complete updated output before asking for approval again.

### Step 4: Generate the LaTeX Project

Only after the user confirms the content, create the LaTeX project.

#### main.tex

```latex
\documentclass[12pt]{report}
\usepackage[a4paper]{geometry}
\usepackage{fancyhdr}
\usepackage{lastpage}
\usepackage{graphicx, wrapfig, subcaption, setspace, booktabs}
\usepackage[T1]{fontenc}
\usepackage[font=small, labelfont=bf]{caption}
\usepackage{fourier}
\usepackage[protrusion=true, expansion=true]{microtype}
\usepackage[english]{babel}
\usepackage{sectsty}
\usepackage{hyperref}
\usepackage{indentfirst}
\usepackage{titlesec}

\newcommand{\HRule}[1]{\rule{\linewidth}{#1}}
\onehalfspacing
\setcounter{tocdepth}{5}
\setcounter{secnumdepth}{5}
\setlength{\parindent}{2em}

\begin{document}
{\fontfamily{cmr}\selectfont
\title{
    \HRule{0.5pt} \\
    \LARGE \textbf{\uppercase{Weekly Report}} \\
    \HRule{2pt} \\ [0.5cm]
    \normalsize \today \vspace*{5\baselineskip}
}

\date{}
\author{
    [Author Name] \\
    [Affiliation] \\
    [Research Group]
}

\maketitle
\tableofcontents
\newpage

\sectionfont{}
\renewcommand\thesection{\arabic{section}}
\renewcommand\thesubsection{\arabic{section}.\arabic{subsection}}

% --- Sections will be placed here ---
\input{1_highlights}
\input{2_section2}
\input{3_section3}
...

\end{document}
```

Note: `\today` automatically inserts the compilation date. Do NOT manually add dates — the template handles it.

#### Section files

For each section, create a separate .tex file named `N_sectionname.tex`.

For the **Highlights** section (always first), use short phrases in `\item[]` entries:
```latex
\section{Highlights of the Week}
\begin{itemize}
    \item GanPoJuncai PPT
    \item Federated fine-tuning LoRA Aggregation survey
    \item Enhanced two-stage framework design with post-training distillation
\end{itemize}
```

For the remaining sections:
- Use proper LaTeX formatting (sections, subsections, itemize/enumerate, figures, tables)
- Match the detail level the user requested
- Write in clear, formal academic English

**CRITICAL — LaTeX formatting and readability rules:**

1. **Break up dense text.** Never output a long unbroken paragraph. Use `\subsection{}` or `\paragraph{}` to create visual structure. Aim for paragraphs of 3-5 sentences max. A section with more than two paragraphs of plain text without structural breaks is unacceptable.

2. **Display equations on their own lines.** Important formulas and mathematical expressions must use `\[ ... \]` or `\begin{equation} ... \end{equation}`, never inline `$...$` for key equations. Inline math `$...$` is only for short variable references within a sentence (e.g., ``the $A$ matrix'').

3. **Use itemize/enumerate for lists.** When describing multiple items (papers, steps, components), use `\begin{itemize}` or `\begin{enumerate}` rather than embedding them in prose.

4. **Use `\textbf{}` for key terms and takeaways.** Each paper summary should bold the key insight so it stands out visually.

Example of GOOD formatting:
```latex
\subsection{FLoRA}

FLoRA identifies a fundamental mathematical error in standard LoRA aggregation (FedIT):
independently averaging $A$ and $B$ matrices introduces a cross-product noise term:
\[
\bar{B}\bar{A} \neq \sum_{k} p_k B_k A_k
\]
Their solution stacks client LoRA modules along the rank dimension, achieving noise-free
aggregation and naturally supporting heterogeneous ranks.

\textbf{Key insight:} Parameter-space averaging of LoRA components is inherently flawed,
reinforcing the design choice of restricting aggregation to the $A$ matrix only.
```

Example of BAD formatting (never do this):
```latex
FLoRA identifies a fundamental mathematical error in standard LoRA aggregation (FedIT):
independently averaging $A$ and $B$ matrices across clients introduces a cross-product
noise term --- $\bar{B}\bar{A} \neq \sum p_k B_k A_k$. Their solution stacks client LoRA
modules along the rank dimension rather than averaging them, achieving noise-free aggregation
and naturally supporting heterogeneous ranks. This confirms that parameter-space averaging
of LoRA components is inherently flawed, reinforcing the design choice of restricting
aggregation to the $A$ matrix only.
```

#### Figures

If the user provides figures:
- Copy them into a `figure/` directory in the project
- Use `\includegraphics` with proper width and caption
- Use `\begin{figure}[!ht] ... \end{figure}` for placement

### Step 5: Package as Zip

Create the project in the user's workspace directory (`<workspace>/weekly_report_YYYY-MM-DD/`), then package:

```bash
cd <workspace>
powershell Compress-Archive -Path weekly_report_YYYY-MM-DD/ -DestinationPath weekly_report_YYYY-MM-DD.zip
```

Do NOT attempt PDF compilation. Just provide the zip file.

### Step 6: Present the Result

Tell the user:
- Where the zip file and the source directory are saved
- A brief summary of what was generated

## Design Principles

1. **This is a report TO the user's advisor** — The user is reporting their own work to their research advisor. Frame all content from the user's first-person perspective. The user is the agent of all actions and reasoning. Do NOT invent feedback from the advisor or any other person unless the user explicitly states it.
2. **Be honest — no fabricated people or conversations** — Never fabricate what someone else said or did. If the user reports "my supervisor suggested X", you may include it. Never generate "The supervisor raised a concern..." or similar unless the user said so. If the user provides minimal info, ask for more. If they choose not to elaborate, write a brief factual statement.
3. **Highlights are short** — Each highlight is a 3-8 word label, not a sentence. Think of them as bullet-point tags.
4. **Review before LaTeX** — Always show plain-text content first. Never jump straight to .tex files.
5. **Match the template style** — Title page, font (fourier/cmr), spacing (onehalfspacing), section numbering, table of contents.
6. **Detail level is user-controlled** — Ask for each section. Don't assume.
7. **Keep the date field dynamic** — Use `\today` in the template; do not hardcode dates in content.
