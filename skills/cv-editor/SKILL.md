---
name: cv-editor
description: Edit the CV content stored as LaTeX in this repository. Use whenever the user wants to add, remove, reorder, or rephrase anything on the CV (experience, education, skills, projects, summary), in French, English, or both — including the offer-adapted versions in adapted-by-offers/. Never edit a PDF, there isn't one; the LaTeX sections in main/fr, main/en and adapted-by-offers/<offer>/<lang> are the single source of truth.
---

# CV Editor

This repository holds Chaoui El Mehdi's CV as version-controlled LaTeX. The
general CV lives in `main/`, in **two languages**: `main/fr/` (French) and
`main/en/` (English). Tailored copies for specific job applications live in
`adapted-by-offers/<offer>/<lang>/` (see "Offer-adapted versions" below).
There is no source PDF anywhere in this repo — the `sections/*.tex` files
**are** the CV. Every compiled PDF is a build artifact regenerated from this
source by CI (see `skills/cv-export/SKILL.md` for how to hand the compiled
PDFs to the user).

## Repo structure

```
cv-as-code/
├── main/fr/
│   ├── main.tex               # Layout & styling ONLY — margins, fonts, colors,
│   │                          # section formatting, \input{} calls. No CV content.
│   └── sections/
│       ├── profile.tex        # Profil Professionnel (summary)
│       ├── experience.tex     # Expérience Professionnelle + Stage
│       ├── education.tex      # Formation
│       ├── skills.tex         # Compétences Techniques, Langues, Atouts, Interests
│       └── projects.tex       # Personal/side projects (currently empty — all
│                              # projects in the source CV are client engagements
│                              # already documented under their employer in
│                              # experience.tex)
├── main/en/                   # Same structure as main/fr/, translated content
│   ├── main.tex
│   └── sections/
│       ├── profile.tex        # Professional Summary
│       ├── experience.tex     # Professional Experience + Internship
│       ├── education.tex      # Education
│       ├── skills.tex         # Technical Skills, Languages, Strengths, Interests
│       └── projects.tex
├── adapted-by-offers/
│   └── <company>-<client>-<project>/
│       └── fr/ (and/or en/)   # Full independent copy of main/<lang>/, tailored
│           ├── main.tex       # to one job offer
│           └── sections/
├── assets/logos/               # Company/school logos, shared by every CV
├── .github/workflows/build.yml # Compiles every main.tex → releases/download/latest/CV-CHAOUI-EL-MEHDI-<LANG>[-<OFFER>].pdf
└── README.md
```

Every `main.tex` is an **independent** LaTeX build. All of them reference
the same logos in the repo-root `assets/logos/` via relative paths — there is
no per-CV copy of the images:
- from `main/fr/` and `main/en/`: `../../assets/logos/<name>.png`
- from `adapted-by-offers/<offer>/<lang>/`: `../../../assets/logos/<name>.png`

## Keeping French and English in sync (`main/`)

This section is about the general CV in `main/`. Offer-adapted versions
follow their own rules — see "Offer-adapted versions" below.

**`main/fr/` and `main/en/` are not templates driven from one source — they are two
separate sets of files that happen to describe the same career.** Nothing
propagates automatically between them. This is the single most important
thing to get right when editing:

- A factual change (new role, new bullet, updated dates/duration, new
  technology, a title/wording tweak) needs to be applied in **both**
  `main/fr/sections/*.tex` and `main/en/sections/*.tex`, as a faithful translation —
  not necessarily word-for-word, but the same facts, same emphasis, same
  structure (same number of bullets in the same order).
- If a user asks for an edit without specifying a language, default to
  applying it to both files, translating naturally. If they explicitly say
  "just the French version" or "only in English," respect that — but flag
  that the two are now out of sync, so it's a deliberate choice, not a
  silent gap.
- When doing a broader content review, check both languages, not just one —
  it's easy for them to drift after several rounds of single-language edits.
- Layout/styling changes (spacing, logo sizes, page-break fixes, macros
  defined in `main.tex`) should also usually be mirrored in both `main/fr/main.tex`
  and `main/en/main.tex`, since they're meant to look and behave the same way —
  unless the user asks for a language-specific layout tweak.

## What's safe to edit vs. layout-only

- **Safe to edit freely: any `sections/*.tex`** (in `main/<lang>/` or
  `adapted-by-offers/<offer>/<lang>/`). This
  is where all real content lives — job history, bullet points, dates,
  degrees, skills. Editing these files is the normal way to update the CV.
- **Layout-only: every `main.tex`.** Only touch these for
  margins, fonts, colors, spacing, or section-heading style/macros. Never
  put CV content (a job, a bullet, a skill) directly in `main.tex` — it
  belongs in a `sections/*.tex` file so the diff history stays meaningful
  and content stays easy to find.

## CV conventions to preserve

- **One-page limit is the target for new/trimmed content.** The current CV
  (6+ years, multiple client engagements) naturally runs ~3 pages in both
  languages — that's expected given the real career history. When *adding*
  new material, prefer cutting older/less-relevant bullets over letting the
  document grow further; when asked to "tighten" or "shorten," cut
  low-signal bullets first (and do it in both languages).
- **Tense**: past roles use past tense; the current/ongoing role (Banque
  Populaire) uses present-tense framing ("[08/2025 -- Présent]" /
  "[08/2025 -- Present]").
- **Strong verb / noun-phrase bullets**: start each bullet with an action
  verb or its nominalized form (French: Développement, Conception,
  Implémentation, Intégration, Optimisation, Automatisation...; English:
  Development, Design, Implementation, Integration, Optimization,
  Automation...), not "Responsible for."
- **Quantify impact where the source material supports it** (durations,
  number of channels/banks/clients integrated, etc.). Never invent a number
  that isn't backed by what the user tells you — CV content must stay
  factual, in both languages.
- **Language**: `fr/` folders are written in French, `en/` folders in natural professional
  English — not a stiff word-for-word translation. Company/project names,
  proper nouns, and technology names stay as-is in both (e.g. "TAMKeeN",
  "l'EDP Lanacash" / "Lanacash EDP", "Camunda").

## Adding / removing / reordering content without breaking LaTeX

- Every bullet list is a standard `itemize` block:
  ```latex
  \begin{itemize}
      \item Your new bullet here
  \end{itemize}
  ```
  Add or remove `\item` lines freely — just keep the `\begin{itemize}` /
  `\end{itemize}` pair balanced. In `main/`, do this in both the `fr/` and
  `en/` versions of the file.
- Each job/role is its own `\subsection*{...}` block in `experience.tex`. To
  add a new role, copy an existing block (heading line, `Projet :`/`Project :`,
  `Réalisations :`/`Achievements :` itemize, `Technologies :` line) and edit
  the copy — don't build one from scratch, it's easy to typo a brace. Do
  this in both language files.
- Nested project sub-headings inside a job (like the CIH BANK sub-projects)
  use `\cvproject{title}` instead of bare `\textit{title}` — this reserves
  enough space to keep the heading and its first bullet on the same page.
  Always use `\cvproject`, not `\textit`, for these.
- Reordering roles/entries is just cutting and pasting whole blocks — each
  one is self-contained (starts at `\subsection*` or `\cvproject{...}`, ends
  before the next one). Keep experience in reverse-chronological order
  (newest first), matching the source CV, in both languages.
- **Escape special LaTeX characters** in any new text: `&` → `\&`, `%` → `\%`,
  `#` → `\#`, `_` → `\_`. Use `--` for an en dash (date ranges) and `---` for
  an em dash. Use `` `` '' `` for curly quotes if needed, or plain `"..."` —
  don't paste smart quotes copied from a Word doc/PDF, they can break
  compilation.
- After any edit, compile locally to check for errors — **from inside the
  folder that holds the `main.tex`**, since `\input` and logo paths are
  relative to it:
  ```bash
  cd main/fr && pdflatex -interaction=nonstopmode main.tex
  cd main/en && pdflatex -interaction=nonstopmode main.tex
  cd adapted-by-offers/<offer>/fr && pdflatex -interaction=nonstopmode main.tex
  ```
  A clean compile ends with `Output written on main.pdf`. Delete the local
  build artifacts afterward (or rely on `.gitignore` — they're already
  ignored) before committing.

## ATS-compatibility reminders

- Never add tables, multi-column layouts, text boxes, or custom fonts/symbols
  for section headers or bullets — they break ATS parsers. Plain
  `itemize`/`\section*`/`\subsection*` only. Applies to both languages.
- Keep contact info, dates, and company names as plain selectable text —
  never as text baked into an image. Every fact on the CV must exist as real
  text somewhere, regardless of any decoration placed next to it.
- **Company/school logos are the one sanctioned exception**, added via the
  `\cvlogo[height][raise]{path}` macro (defined identically in every
  `main.tex`) at the end of a `\subsection*` heading —
  see `assets/logos/` and how `experience.tex`/`education.tex` use it in
  both languages. They are purely decorative and placed *in addition to*
  the plain-text company name that's already in the heading, never instead
  of it. Rules for adding a new one:
  - Extract/save the logo as a PNG in `assets/logos/` (repo root, shared by
    every CV), cropped to its visual content (no large empty margins
    — `\cvlogo` fixes the height, so a logo with lots of internal
    whitespace will render illegibly small).
  - Reference it as `\cvlogo{../../assets/logos/<name>.png}` from
    `main/<lang>/` (two levels up to the repo root), or
    `\cvlogo{../../../assets/logos/<name>.png}` from
    `adapted-by-offers/<offer>/<lang>/` (three levels up), right after the
    heading text, before the closing `}`. In `main/`, add it in both
    language files so the two stay visually consistent.
  - Default height/vertical alignment usually works; some logos (e.g. more
    square aspect ratios like CIH Bank's) need an explicit
    `\cvlogo[height][raise]{...}` override to look right next to the title
    baseline — check both language builds visually after changing this,
    since the same override should normally apply to both.
  - Never let a logo be the *only* place a company name appears — if you
    can't confirm the name is also present as plain text nearby, don't add
    the logo.
  - After adding one, run `pdftotext main.pdf -` (or `pdftotext -layout`) in
    each CV folder and confirm the company name still shows up in the
    plain-text output — that's the real ATS-safety check, not just how it
    looks.
- Don't use `\newpage` to force layout for ordinary content — let it flow
  naturally. The one exception already in place is forcing the
  Stage/Internship section onto a fresh page, since it previously landed
  stranded at the bottom of a page.
- Keep standard section names ATS systems expect in each language
  (Expérience Professionnelle/Professional Experience, Formation/Education,
  Compétences/Skills) rather than creative headers.
- Avoid unicode symbols/emoji in headers or bullets (the original CV used
  icons like ☎️ ✉️ 💻 — those were intentionally dropped in this LaTeX
  version for ATS-safety; don't reintroduce them).

## Offer-adapted versions (`adapted-by-offers/`)

Tailored copies of the CV for specific job applications. These conventions
were set by the user — follow them every time:

- **Folder naming:** `adapted-by-offers/<company>-<client>-<project>/<lang>/`,
  lowercase and hyphenated. E.g. "boite: Alton | client: ANAPEC | project:
  matching app" → `adapted-by-offers/alton-anapec-matching-app/fr/`. Create
  **only the languages the user asks for** — the first offers are French
  only, so there is no FR/EN sync rule for offers. If the language isn't
  stated, ask.
- **Starting a new offer:** copy the current `main/<lang>/` as-is (the whole
  folder: `main.tex` + `sections/`), then:
  1. Rewrite every logo path from `../../assets/logos/` to
     `../../../assets/logos/` (the offer folder is one level deeper).
  2. Add the provenance header right after the `% !TEX program` line of the
     offer's `main.tex`, naming the offer and the `main` commit it was
     copied from (copy the format from an existing offer).
  3. Don't change any content yet — **the user gives the updates** for each
     offer. Apply exactly what they ask, to that offer's files only.
- **Independent and duplicated on purpose.** Each offer is a standalone
  snapshot. Don't refactor offers into shared, templated, or override-based
  sources. Edits to an offer never touch `main/` or other offers; edits to
  `main/` don't propagate to existing offers unless the user asks for it.
- **Same CV conventions apply** (sections above): factual content only —
  tailoring means choosing what to emphasize, reorder, or cut, never
  inventing experience or skills; tense; bullet style; ATS-safety; LaTeX
  escaping; compile from inside the offer's language folder.
- **Build/publish:** nothing to configure — CI compiles every
  `adapted-by-offers/*/*/main.tex` and publishes it as
  `CV-CHAOUI-EL-MEHDI-<LANG>-<OFFER>.pdf` (offer folder name uppercased),
  e.g. `CV-CHAOUI-EL-MEHDI-FR-ALTON-NARSA-CHATBOT.pdf`. See
  `skills/cv-export/SKILL.md` for the links.
