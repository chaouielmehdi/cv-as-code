# cv-as-code

This repo holds Chaoui El Mehdi's CV as LaTeX source. There is no PDF stored
in this repo — the `.tex` files are the single source of truth. CI compiles
every CV on each PR into `main` (as a check) and, after merge, on `main`
again to publish them to permanent GitHub Releases + Pages links.

```
main/                           # The general CV, in two languages
├── fr/  main.tex + sections/
└── en/  main.tex + sections/
adapted-by-offers/              # One folder per job offer, tailored copies
├── alton-narsa-chatbot/fr/
└── alton-anapec-matching-app/fr/
assets/logos/                   # Logos shared by every CV
```

## The general CV (`main/`)

`main/fr/` and `main/en/` are independent files, not one templated source — a
content change (a new role, an updated bullet, a reworded title) needs to be
made in **both** languages, or they will drift out of sync. When editing one,
always check whether the other needs the same edit.

## Offer-adapted CVs (`adapted-by-offers/`)

Tailored versions of the CV for specific job applications. Conventions (set
by the user — follow them every time):

- **One folder per offer**, named `<company>-<client>-<project>`, lowercase
  and hyphenated (e.g. offer "boite: Alton | client: NARSA | project: chat
  bot" → `adapted-by-offers/alton-narsa-chatbot/`). Inside it, one folder per
  language (`fr/`, `en/`) — **only the languages the user asks for** (the
  first two offers are French only). If the language isn't stated, ask.
- **Every new offer starts as a full copy of the current `main/<lang>/`** —
  `main.tex` and all of `sections/`. After copying, fix the logo paths
  (`../../assets/logos/` → `../../../assets/logos/`, one level deeper) and
  add the provenance header at the top of the offer's `main.tex` (see an
  existing offer for the format, with the `main` commit it was copied from).
- **Then wait for the user to say what to change.** Don't tailor the content
  on your own initiative — the user gives the updates for each offer. Apply
  them to that offer's files only.
- **Offer versions are independent and duplication is intentional.** Don't
  refactor them into shared/templated/override-based sources, and don't
  "engineer" anything around them. An edit to an offer never touches `main/`
  or other offers, and an edit to `main/` does not propagate to existing
  offers (they're snapshots) unless the user asks for it.
- All the CV conventions in `skills/cv-editor/SKILL.md` still apply
  (factual content only — never invent experience; tense, bullet style,
  ATS-safety, LaTeX escaping).
- CI picks up new offer folders automatically (glob in
  `.github/workflows/build.yml`) and publishes each one as
  `CV-CHAOUI-EL-MEHDI-<LANG>-<OFFER>.pdf`, e.g.
  `CV-CHAOUI-EL-MEHDI-FR-ALTON-NARSA-CHATBOT.pdf` — see
  `skills/cv-export/SKILL.md` for the links.

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
