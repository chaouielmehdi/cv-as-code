# cv-as-code

Chaoui El Mehdi's CV, versioned as LaTeX source instead of a static PDF or
Word document. Content lives in `sections/*.tex` and is the single source of
truth — there is no original PDF stored in this repo. Every push to `main`
recompiles the CV and publishes the PDF to a permanent download link.

## Repo structure

```
cv-as-code/
├── main.tex                  # Layout & styling only (margins, fonts, section styling)
├── sections/
│   ├── profile.tex           # Profil Professionnel
│   ├── experience.tex        # Expérience Professionnelle + Stage
│   ├── education.tex         # Formation
│   ├── skills.tex            # Compétences Techniques, Langues, Atouts, Interests
│   └── projects.tex          # Personal/side projects (empty placeholder)
├── .github/workflows/build.yml   # CI: compile → publish to Releases + Pages
├── skills/
│   ├── cv-editor/SKILL.md    # How to edit CV content correctly
│   └── cv-export/SKILL.md    # How to hand over the compiled PDF
└── .gitignore
```

## Editing the CV

Edit the relevant file in `sections/`. `main.tex` should only change for
layout/styling tweaks — see `skills/cv-editor/SKILL.md` for full conventions
(one-page-oriented trimming, tense, bullet style, ATS-safety rules, and how to
add/remove/reorder entries without breaking LaTeX syntax).

## Rebuilding the PDF

The CV is **not** built or committed locally. Just commit your changes to
`sections/*.tex` (or `main.tex`) and push to `main`:

```bash
git add sections/experience.tex
git commit -m "Add new role"
git push origin main
```

GitHub Actions (`.github/workflows/build.yml`) will:
1. Compile `main.tex` with `xu-cheng/latex-action`.
2. Publish the resulting PDF as `CV-CHAOUI-EL-MEHDI.pdf` to a GitHub Release
   tagged `latest`, replacing the previous asset.
3. Also deploy the same PDF to GitHub Pages, for inline browser viewing.

Build time is roughly 30–60 seconds after the push.

## Getting the compiled PDF

Two permanent URLs, same PDF, updated on every push to `main`:

- **Download (forces "Save As")**:
  https://github.com/chaouielmehdi/cv-as-code/releases/download/latest/CV-CHAOUI-EL-MEHDI.pdf
- **View inline in the browser**:
  https://chaouielmehdi.github.io/cv-as-code/CV-CHAOUI-EL-MEHDI.pdf

GitHub always serves Release assets with a forced download — that's a
platform behavior, not something this repo can turn off. The Pages URL is
the fix: Pages serves the same PDF as a normal `Content-Type: application/pdf`
response, so browsers open it directly instead of prompting to save.

Both links are safe to bookmark, share with recruiters, or link from LinkedIn.

**Note:** this repo is private, and GitHub Pages needs a GitHub Pro (or
Team/Enterprise) plan to publish from a private repository — and the
published Pages site is publicly reachable at that URL regardless of the
repo's visibility. Pages must also be enabled once manually: repo
**Settings → Pages → Build and deployment → Source: GitHub Actions**.

## Compiling locally (optional)

```bash
pdflatex -interaction=nonstopmode main.tex
```

Requires a LaTeX distribution (e.g. `texlive-latex-base`,
`texlive-latex-recommended`, `texlive-fonts-recommended`, and `lmodern`).
Build artifacts (`*.aux`, `*.log`, `*.out`, `*.pdf`) are gitignored.
