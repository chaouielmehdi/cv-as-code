---
name: cv-export
description: Hand over the compiled CV PDF when the user asks to "give me my CV", "export my CV", "send me the PDF", "download the CV", or similar — use this instead of compiling LaTeX locally or digging through GitHub Actions artifacts.
---

# CV Export

The compiled CV PDF is published to **two fixed, permanent URLs**, both
updated by the same CI run on every push to `main`:

```
Download (forces "Save As"):     https://github.com/chaouielmehdi/cv-as-code/releases/download/latest/cv.pdf
View inline in the browser:      https://chaouielmehdi.github.io/cv-as-code/cv.pdf
```

They're the same PDF. GitHub always serves Release assets with a forced
download (`Content-Disposition: attachment`) — that's a platform behavior,
not something this repo controls. GitHub Pages serves the identical file with
a normal `Content-Type: application/pdf` response, so it opens directly in
the browser instead of prompting to save.

## Default behavior

When asked to "give me my CV", "export my CV", "send me the PDF", "download
the CV", etc.:

1. Return the download (Releases) link. If the request implies wanting to
   *view* it rather than save it (e.g. "show me my CV", "let me see it",
   "preview it"), give the Pages link instead — or offer both.
2. Do **not**:
   - Compile `main.tex` locally and send that file instead.
   - Look through GitHub Actions run artifacts for a PDF.
   - Regenerate anything, unless the user explicitly asks for a fresh local
     build (e.g. to preview an uncommitted edit before pushing).

## If the request comes right after a push or edit

Builds take roughly 30–60 seconds. If the user just pushed a commit (or you
just committed and pushed an edit on their behalf) and then asks for the CV:

1. Check the status of the GitHub Actions workflow run for that commit
   (`Build CV` workflow, `.github/workflows/build.yml`) before handing over
   the link, so you don't hand over a stale PDF.
2. If the run is still in progress, say so and either wait briefly or ask if
   they want you to check back, rather than silently returning a possibly-old
   link.
3. Once the run succeeds, return the fixed Releases URL — same link as
   always, now pointing at the freshly compiled PDF.

## If the build failed

Do not return the download link — it would point to an outdated PDF (the
release asset is only replaced on success). Instead:

1. Report that the build failed.
2. Point the user to the Actions log for that run so they (or you, on
   request) can see the LaTeX compilation error.
3. Optionally offer to look at the log and fix the LaTeX error yourself, then
   push a fix and re-check the new run.

## Why Releases, not Actions artifacts

Actions artifacts are ephemeral (expire, require auth, and get a new URL each
run). The Releases asset at `releases/download/latest/cv.pdf` is a stable,
permanent link — safe to put on a resume, LinkedIn profile, or share directly
with a recruiter.

## If the Pages link 404s or the deploy step fails

This repo is private. GitHub Pages requires a paid plan (GitHub Pro, or
Team/Enterprise for an org) to publish from a private repository, and Pages
must be turned on once manually: repo **Settings → Pages → Build and
deployment → Source: GitHub Actions**. If the "Deploy to GitHub Pages" step
in `Build CV` fails or the URL 404s, that's almost always one of these two
things — not a bug in the workflow. Point the user at Settings → Pages rather
than trying to "fix" it in code.
