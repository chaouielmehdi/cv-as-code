# cv-as-code

Chaoui El Mehdi's CV, versioned as LaTeX source instead of a static PDF or
Word document. The general CV is available in **French and English**
(`main/fr/`, `main/en/`), plus tailored copies for specific job offers
(`adapted-by-offers/`). The `sections/*.tex` files are the single source of
truth — there is no original PDF stored in this repo. Every merge to `main`
recompiles every CV and publishes them to permanent download links.

## Repo structure

```
cv-as-code/
├── main/fr/                   # General CV — French
│   ├── main.tex               # Layout & styling only (margins, fonts, section styling)
│   └── sections/
│       ├── profile.tex        # Profil Professionnel
│       ├── experience.tex     # Expérience Professionnelle + Stage
│       ├── education.tex      # Formation
│       ├── skills.tex         # Compétences Techniques, Langues, Atouts, Interests
│       └── projects.tex       # Personal/side projects (empty placeholder)
├── main/en/                   # General CV — English, same structure, translated
│   ├── main.tex
│   └── sections/
│       ├── profile.tex        # Professional Summary
│       ├── experience.tex     # Professional Experience + Internship
│       ├── education.tex      # Education
│       ├── skills.tex         # Technical Skills, Languages, Strengths, Interests
│       └── projects.tex
├── adapted-by-offers/          # CVs tailored to one job offer each
│   ├── alton-narsa-chatbot/fr/        # Alton → client NARSA, chatbot project
│   └── alton-anapec-matching-app/fr/  # Alton → client ANAPEC, matching app
├── assets/logos/               # Company/school logos, shared by every CV
│   └── *.png
├── .github/workflows/build.yml # CI: compile every CV on PRs; on main also publish to Releases + Pages
├── skills/
│   ├── cv-editor/SKILL.md     # How to edit CV content correctly, in both languages
│   └── cv-export/SKILL.md     # How to hand over the compiled PDFs
└── .gitignore
```

Every CV folder is an independent LaTeX build — each has its own `main.tex`
and `sections/`, and all of them reference the same logos in `assets/logos/`
via relative paths (`../../assets/logos/...` from `main/<lang>/`,
`../../../assets/logos/...` from `adapted-by-offers/<offer>/<lang>/`).

## Editing the CV

Edit the relevant file in `main/fr/sections/` or `main/en/sections/`.
`main.tex` in any folder should only change for layout/styling tweaks — see
`skills/cv-editor/SKILL.md` for full conventions (one-page-oriented
trimming, tense, bullet style, ATS-safety rules, how to add/remove/reorder
entries without breaking LaTeX syntax, and — importantly — how to keep the
French and English versions in sync).

**A content change to the general CV almost always needs to be made in both
`main/fr/` and `main/en/`.** The two are independent files, not templates
driven by one source, so nothing propagates automatically.

### Offer-adapted versions

Each job offer gets its own folder,
`adapted-by-offers/<company>-<client>-<project>/<lang>/`, created as a full
copy of the current `main/<lang>/` and then edited for that offer only.
Copies are intentionally independent: editing an offer never changes `main/`
or other offers, and later edits to `main/` don't flow into existing offers.
Full conventions are in `skills/cv-editor/SKILL.md`.

## Rebuilding the PDFs

The CV is **not** built or committed locally. Every change goes through a
pull request — never push directly to `main`:

```bash
git checkout -b add-new-role
git add main/fr/sections/experience.tex main/en/sections/experience.tex
git commit -m "Add new role"
git push -u origin add-new-role
# then open a PR into main and merge it once the "Build CV" check is green
```

GitHub Actions (`.github/workflows/build.yml`) will:
1. **On the PR:** compile every CV (`main/*/main.tex` and
   `adapted-by-offers/*/*/main.tex`) with `xu-cheng/latex-action`, so a broken
   LaTeX change fails the PR check before it reaches `main`. The compiled PDFs are attached to the run as the
   `cv-pdfs` artifact for previewing. Nothing is published.
2. **After merging to `main`:** compile everything again, then publish the
   PDFs to a GitHub Release tagged `latest` (replacing the previous assets)
   and deploy them to GitHub Pages for inline browser viewing. Names:
   `CV-CHAOUI-EL-MEHDI-FR.pdf` / `-EN.pdf` for `main/`, and
   `CV-CHAOUI-EL-MEHDI-<LANG>-<OFFER>.pdf` for each offer, e.g.
   `CV-CHAOUI-EL-MEHDI-FR-ALTON-NARSA-CHATBOT.pdf`. New offer folders are
   picked up automatically.

Build time is roughly 30–60 seconds after the push or merge.

## Getting the compiled PDFs

Permanent URLs (download/view), updated on every push to `main`:

|          | Download (forces "Save As")                                                                          | View inline in the browser                                            |
|----------|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------|
| French   | https://github.com/chaouielmehdi/cv-as-code/releases/download/latest/CV-CHAOUI-EL-MEHDI-FR.pdf         | https://chaouielmehdi.github.io/cv-as-code/CV-CHAOUI-EL-MEHDI-FR.pdf     |
| English  | https://github.com/chaouielmehdi/cv-as-code/releases/download/latest/CV-CHAOUI-EL-MEHDI-EN.pdf         | https://chaouielmehdi.github.io/cv-as-code/CV-CHAOUI-EL-MEHDI-EN.pdf     |

Offer-adapted versions follow the same pattern with the offer suffix:

|                              | Download                                                                                                                   | View inline                                                                                       |
|------------------------------|------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| Alton / NARSA chatbot (FR)   | https://github.com/chaouielmehdi/cv-as-code/releases/download/latest/CV-CHAOUI-EL-MEHDI-FR-ALTON-NARSA-CHATBOT.pdf         | https://chaouielmehdi.github.io/cv-as-code/CV-CHAOUI-EL-MEHDI-FR-ALTON-NARSA-CHATBOT.pdf           |
| Alton / ANAPEC matching (FR) | https://github.com/chaouielmehdi/cv-as-code/releases/download/latest/CV-CHAOUI-EL-MEHDI-FR-ALTON-ANAPEC-MATCHING-APP.pdf   | https://chaouielmehdi.github.io/cv-as-code/CV-CHAOUI-EL-MEHDI-FR-ALTON-ANAPEC-MATCHING-APP.pdf     |

Like the rest of the repo, these are public.

GitHub always serves Release assets with a forced download — that's a
platform behavior, not something this repo can turn off. The Pages URLs are
the fix: Pages serves the same PDFs as a normal `Content-Type: application/pdf`
response, so browsers open them directly instead of prompting to save.

All these links are safe to bookmark, share with recruiters, or link from
LinkedIn.

**Note:** GitHub Pages must be enabled once manually: repo
**Settings → Pages → Build and deployment → Source: GitHub Actions**. (If
this repo is ever made private again, Pages also requires a GitHub Pro/Team/
Enterprise plan, and the published Pages site is publicly reachable
regardless of the repo's visibility.)

## Compiling locally (optional)

Compile from inside the folder holding the `main.tex` (paths are relative):

```bash
(cd main/fr && pdflatex -interaction=nonstopmode main.tex)                         # French
(cd main/en && pdflatex -interaction=nonstopmode main.tex)                         # English
(cd adapted-by-offers/alton-narsa-chatbot/fr && pdflatex -interaction=nonstopmode main.tex)  # an offer
```

Requires a LaTeX distribution (e.g. `texlive-latex-base`,
`texlive-latex-recommended`, `texlive-fonts-recommended`, `lmodern`,
`needspace`, and `xparse`). Build artifacts (`*.aux`, `*.log`, `*.out`,
`*.pdf`) are gitignored.
