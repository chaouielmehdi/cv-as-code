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
├── .github/workflows/build.yml   # CI: compile → publish to GitHub Releases
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
2. Publish the resulting PDF as `cv.pdf` to a GitHub Release tagged `latest`,
   replacing the previous asset.

Build time is roughly 30–60 seconds after the push.

## Download the compiled PDF

The compiled CV always lives at this fixed, permanent URL — it never changes
across builds:

**https://github.com/chaouielmehdi/cv-as-code/releases/download/latest/cv.pdf**

Safe to bookmark, share with recruiters, or link from LinkedIn.

## Compiling locally (optional)

```bash
pdflatex -interaction=nonstopmode main.tex
```

Requires a LaTeX distribution (e.g. `texlive-latex-base`,
`texlive-latex-recommended`, `texlive-fonts-recommended`, and `lmodern`).
Build artifacts (`*.aux`, `*.log`, `*.out`, `*.pdf`) are gitignored.
