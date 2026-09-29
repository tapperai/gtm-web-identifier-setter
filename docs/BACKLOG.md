# Backlog

Living list of every TODO the user has called out. When the user mentions
something new, capture it here in technical words before starting work; this
repo is public, see CLAUDE.md "Public repo". When something
lands, move it under "Done" with the SHA / date.

Categories:

- **P0 — open**: explicitly asked, not shipped yet.
- **P0 — needs verification**: code says done but never end-to-end checked
  with the user; treat as suspect until verified.
- **P1 — open**: asked-for but lower urgency, or feature-parity items the
  user wants but hasn't blocked on.
- **P2 — open**: nice-to-have, future parity, deferred.
- **Done**: shipped + verified.

---

## P0 — open

(none)

---

## P0 — needs verification

(none)

---

## P1 — open

- **Broken docs link (found 2026-09-29).** `README.md` and `metadata.yaml` `documentation:` both point at `https://docs.tapper.ai/gtm/web-identifier-setter`, which returns 404. The Gallery listing links there: the Gallery API (`https://tagmanager.google.com/api/gallery/owners/tapperai/templates/gtm-web-identifier-setter`) returns it as `documentationUrl`. No page in the `docs` repo mentions this template today. The README link text also calls the template a "First-Party Cookie Setter", but it writes localStorage, not a cookie. Fix by adding that page to the `docs` repo, which needs no change here, or by pointing both links at a page that exists. Google's Gallery docs describe an update only as a new `versions` entry naming a `template.tpl` commit. Nobody has measured whether a change that only repoints `documentation` reaches the listing without one, so check the Gallery API afterwards. An earlier attempt, commit `9c87d19` (2026-06-28), sits on the stray `main` branch and on `gtm-web-identifier-setter/flawless-fixes`, and never reached `master`, the default branch the Gallery reads. It deletes the `documentation:` line, which Google's setup steps list as an entry, so do not cherry-pick it as-is. Land any fix on `master`, never on `main`.

---

## P2 — open

(none)

---

## Meta / hygiene

- Keep this file fresh: each new explicit user ask gets an entry **before** I
  start coding it. After it ships and the user confirms, move it under Done.
- Never silently drop an item — if I push back on scope, note the rationale
  inline.

---

## Done

- **2026-08-26** — Wired `document-first-template` submodule + bootstrapped
  `docs/SPEC.md`, `docs/BACKLOG.md`, `docs/TESTING.md`,
  `docs/ENVIRONMENT_SPINUP.md` and the `CLAUDE.md` pointer (operator sweep
  2026-08-26).
- Per `metadata.yaml` version history:
  - `8afb64f1dce1db7a245b7cb827de9a5b9ee2ff04` — Initial Release.
  - `1662742ff3d4f1bdd5558421ed80e0d8c6723223` — Update identifier setting
    method.
