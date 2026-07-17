# cv-as-code

This repo holds Chaoui El Mehdi's CV as LaTeX source, in **two languages**:
`fr/` (French) and `en/` (English). Each has its own `main.tex` and
`sections/*.tex` — these are the single source of truth for content; there
is no PDF stored in this repo. CI compiles both on every push to `main` and
publishes them to permanent GitHub Releases links.

`fr/` and `en/` are independent files, not one templated source — a content
change (a new role, an updated bullet, a reworded title) needs to be made in
**both** languages, or they will drift out of sync. When editing one, always
check whether the other needs the same edit.

For how to edit content vs. layout, CV conventions (tense, bullet style,
one-page-oriented trimming, ATS-safety, keeping FR/EN in sync), and how to
hand over the compiled PDFs, see:
- `skills/cv-editor/SKILL.md`
- `skills/cv-export/SKILL.md`

## Git workflow

Every change to this repo goes through a pull request — never push directly
to `main`.

1. Create a branch, commit the change, push it, and open a PR into `main`.
2. Merge the PR automatically once it's open, unless the user has explicitly
   said not to (e.g. they want to review it first, or asked you to hold off).
   Default to merging — don't wait for separate confirmation on each PR.
3. If CI (the `Build CV` workflow) fails on the PR, fix it and re-push before
   merging — don't merge a broken build.
