# cv-as-code

Chaoui El Mehdi's CV, versioned as LaTeX source instead of a static PDF or
Word document, available in **French and English**. Content lives in
`fr/sections/*.tex` and `en/sections/*.tex` and is the single source of
truth — there is no original PDF stored in this repo. Every push to `main`
recompiles both language versions and publishes them to permanent download
links.

## Repo structure

```
cv-as-code/
├── fr/                        # French CV
│   ├── main.tex               # Layout & styling only (margins, fonts, section styling)
│   └── sections/
│       ├── profile.tex        # Profil Professionnel
│       ├── experience.tex     # Expérience Professionnelle + Stage
│       ├── education.tex      # Formation
│       ├── skills.tex         # Compétences Techniques, Langues, Atouts, Interests
│       └── projects.tex       # Personal/side projects (empty placeholder)
├── en/                        # English CV — same structure, translated
│   ├── main.tex
│   └── sections/
│       ├── profile.tex        # Professional Summary
│       ├── experience.tex     # Professional Experience + Internship
│       ├── education.tex      # Education
│       ├── skills.tex         # Technical Skills, Languages, Strengths, Interests
│       └── projects.tex
├── assets/logos/               # Company/school logos, shared by both languages
│   └── *.png
├── .github/workflows/build.yml # CI: compile both → publish to Releases + Pages
├── skills/
│   ├── cv-editor/SKILL.md     # How to edit CV content correctly, in both languages
│   └── cv-export/SKILL.md     # How to hand over the compiled PDFs
└── .gitignore
```

`fr/` and `en/` are two independent LaTeX builds — each has its own
`main.tex` and `sections/`, but both reference the same logos in
`assets/logos/` via relative `../assets/logos/...` paths.

## Editing the CV

Edit the relevant file in `fr/sections/` or `en/sections/`. `main.tex` in
either folder should only change for layout/styling tweaks — see
`skills/cv-editor/SKILL.md` for full conventions (one-page-oriented
trimming, tense, bullet style, ATS-safety rules, how to add/remove/reorder
entries without breaking LaTeX syntax, and — importantly — how to keep the
French and English versions in sync).

**A content change almost always needs to be made in both `fr/` and `en/`.**
The two are independent files, not templates driven by one source, so
nothing propagates automatically.

## Rebuilding the PDFs

The CV is **not** built or committed locally. Just commit your changes and
push to `main`:

```bash
git add fr/sections/experience.tex en/sections/experience.tex
git commit -m "Add new role"
git push origin main
```

GitHub Actions (`.github/workflows/build.yml`) will:
1. Compile `fr/main.tex` and `en/main.tex` independently with `xu-cheng/latex-action`.
2. Publish the results as `CV-CHAOUI-EL-MEHDI-FR.pdf` and `CV-CHAOUI-EL-MEHDI-EN.pdf`
   to a GitHub Release tagged `latest`, replacing the previous assets.
3. Also deploy both PDFs to GitHub Pages, for inline browser viewing.

Build time is roughly 30–60 seconds after the push.

## Getting the compiled PDFs

Four permanent URLs (two languages × download/view), updated on every push
to `main`:

|          | Download (forces "Save As")                                                                          | View inline in the browser                                            |
|----------|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------|
| French   | https://github.com/chaouielmehdi/cv-as-code/releases/download/latest/CV-CHAOUI-EL-MEHDI-FR.pdf         | https://chaouielmehdi.github.io/cv-as-code/CV-CHAOUI-EL-MEHDI-FR.pdf     |
| English  | https://github.com/chaouielmehdi/cv-as-code/releases/download/latest/CV-CHAOUI-EL-MEHDI-EN.pdf         | https://chaouielmehdi.github.io/cv-as-code/CV-CHAOUI-EL-MEHDI-EN.pdf     |

GitHub always serves Release assets with a forced download — that's a
platform behavior, not something this repo can turn off. The Pages URLs are
the fix: Pages serves the same PDFs as a normal `Content-Type: application/pdf`
response, so browsers open them directly instead of prompting to save.

All four links are safe to bookmark, share with recruiters, or link from
LinkedIn.

**Note:** GitHub Pages must be enabled once manually: repo
**Settings → Pages → Build and deployment → Source: GitHub Actions**. (If
this repo is ever made private again, Pages also requires a GitHub Pro/Team/
Enterprise plan, and the published Pages site is publicly reachable
regardless of the repo's visibility.)

## Compiling locally (optional)

```bash
cd fr && pdflatex -interaction=nonstopmode main.tex     # French
cd ../en && pdflatex -interaction=nonstopmode main.tex  # English
```

Requires a LaTeX distribution (e.g. `texlive-latex-base`,
`texlive-latex-recommended`, `texlive-fonts-recommended`, `lmodern`,
`needspace`, and `xparse`). Build artifacts (`*.aux`, `*.log`, `*.out`,
`*.pdf`) are gitignored.
