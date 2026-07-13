---
name: cv-editor
description: Edit the CV content stored as LaTeX in this repository. Use whenever the user wants to add, remove, reorder, or rephrase anything on the CV (experience, education, skills, projects, summary) — never edit the PDF, there isn't one; the LaTeX sections are the single source of truth.
---

# CV Editor

This repository holds Chaoui El Mehdi's CV as version-controlled LaTeX. There is
no source PDF anywhere in this repo — `sections/*.tex` **is** the CV. Every
compiled PDF is a build artifact regenerated from this source by CI (see
`skills/cv-export/SKILL.md` for how to hand the compiled PDF to the user).

## Repo structure

```
cv-as-code/
├── main.tex                  # Layout & styling ONLY — margins, fonts, colors,
│                              # section formatting, \input{} calls. No CV content.
├── sections/
│   ├── profile.tex           # Profil Professionnel (summary)
│   ├── experience.tex        # Expérience Professionnelle + Stage
│   ├── education.tex         # Formation
│   ├── skills.tex            # Compétences Techniques, Langues, Atouts, Interests
│   └── projects.tex          # Personal/side projects (currently empty — all
│                              # projects in the source CV are client engagements
│                              # already documented under their employer in
│                              # experience.tex)
├── .github/workflows/build.yml  # Compiles main.tex → releases/download/latest/cv.pdf
└── README.md
```

## What's safe to edit vs. layout-only

- **Safe to edit freely: `sections/*.tex`.** This is where all real content
  lives — job history, bullet points, dates, degrees, skills. Editing these
  files is the normal way to update the CV.
- **Layout-only: `main.tex`.** Only touch this for margins, fonts, colors,
  spacing, or section-heading style. Never put CV content (a job, a bullet, a
  skill) directly in `main.tex` — it belongs in a `sections/*.tex` file so the
  diff history stays meaningful and content stays easy to find.

## CV conventions to preserve

- **One-page limit is the target for new/trimmed content.** The current CV
  (5+ years, multiple client engagements) naturally runs ~3 pages — that's
  expected given the real career history. When *adding* new material, prefer
  cutting older/less-relevant bullets over letting the document grow further;
  when asked to "tighten" or "shorten," cut low-signal bullets first.
- **Tense**: past roles use past tense ("Développement d'une solution...");
  a current/ongoing role (if one is ever added) uses present tense.
- **Strong verb bullets**: start each bullet with an action verb (Développement,
  Conception, Implémentation, Intégration, Optimisation, Automatisation, ...),
  not a noun phrase or "Responsible for."
- **Quantify impact where the source material supports it** (durations, number
  of channels/banks/clients integrated, etc.). Never invent a number that
  isn't backed by what the user tells you — CV content must stay factual.
- **Language**: the CV is written in French, matching the original source.
  Keep new content in French unless the user explicitly asks for an English
  (or bilingual) version.

## Adding / removing / reordering content without breaking LaTeX

- Every bullet list is a standard `itemize` block:
  ```latex
  \begin{itemize}
      \item Your new bullet here
  \end{itemize}
  ```
  Add or remove `\item` lines freely — just keep the `\begin{itemize}` /
  `\end{itemize}` pair balanced.
- Each job/role is its own `\subsection*{...}` block in `experience.tex`. To
  add a new role, copy an existing block (heading line, `Projet :`,
  `Réalisations :` itemize, `Technologies :` line) and edit the copy — don't
  build one from scratch, it's easy to typo a brace.
- Reordering roles/entries is just cutting and pasting whole blocks — each one
  is self-contained (starts at `\subsection*` or `\textit{...}`, ends before
  the next one). Keep experience in reverse-chronological order (newest
  first), matching the source CV.
- **Escape special LaTeX characters** in any new text: `&` → `\&`, `%` → `\%`,
  `#` → `\#`, `_` → `\_`. Use `--` for an en dash (date ranges) and `---` for
  an em dash. Use `` `` '' `` for curly quotes if needed, or plain `"..."` —
  don't paste smart quotes copied from a Word doc/PDF, they can break
  compilation.
- After any edit, compile locally to check for errors:
  ```bash
  pdflatex -interaction=nonstopmode main.tex
  ```
  A clean compile ends with `Output written on main.pdf`. Delete the local
  build artifacts afterward (or rely on `.gitignore` — they're already
  ignored) before committing.

## ATS-compatibility reminders

- Never add tables, multi-column layouts, text boxes, or custom fonts/symbols
  for section headers or bullets — they break ATS parsers. Plain
  `itemize`/`\section*`/`\subsection*` only.
- Keep contact info, dates, and company names as plain selectable text —
  never as text baked into an image. Every fact on the CV must exist as real
  text somewhere, regardless of any decoration placed next to it.
- **Company/school logos are the one sanctioned exception**, added via the
  `\cvlogo{path}` macro (defined in `main.tex`) at the end of a
  `\subsection*` heading — see `assets/logos/` and how `sections/experience.tex`
  and `sections/education.tex` use it. They are purely decorative and placed
  *in addition to* the plain-text company name that's already in the
  heading, never instead of it. Rules for adding a new one:
  - Extract/save the logo as a PNG in `assets/logos/`, cropped to its visual
    content (no large empty margins — `\cvlogo` fixes the height, so a
    logo with lots of internal whitespace will render illegibly small).
  - Reference it with `\cvlogo{assets/logos/<name>.png}` right after the
    heading text, before the closing `}`.
  - Never let a logo be the *only* place a company name appears — if you
    can't confirm the name is also present as plain text nearby, don't add
    the logo.
  - After adding one, run `pdftotext main.pdf -` (or `pdftotext -layout`) and
    confirm the company name still shows up in the plain-text output — that's
    the real ATS-safety check, not just how it looks.
- Don't use `\newpage` to force layout — let content flow naturally.
- Keep standard section names ATS systems expect (Expérience Professionnelle,
  Formation, Compétences) rather than creative headers.
- Avoid unicode symbols/emoji in headers or bullets (the original CV used
  icons like ☎️ ✉️ 💻 — those were intentionally dropped in this LaTeX
  version for ATS-safety; don't reintroduce them).
