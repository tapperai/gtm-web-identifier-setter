## Document First Development

**Every new feature or significant change must have a spec in `docs/` before implementation begins.** Skip steps 1-5 only if the user explicitly asks to write code directly (e.g., "just fix this", "skip the spec").

1. **Before implementing any feature or significant change**, read the rules:
   - Read `docs/DOCUMENT_FIRST.md` for spec rules, structure, and conventions
2. Before writing code, check if a spec exists at `docs/{domain}/SPEC.md`
3. If no spec exists, create one from `docs/_templates/SPEC.md` first
4. **After writing the spec, open the file, record the decisions it locks in (what you measured, what would have made the answer no), and implement straight away.** No confirmation wait: agents decide from evidence and record the decision in the spec.
5. Implement from the spec and follow it exactly. Break the spec into independent work streams and **spawn parallel agents** (one per work stream) to maximize speed. Sequence dependent work — implement dependencies first, then parallelize everything that follows
6. After implementing, update the spec to match what was built — keep specs and code in sync at all times. Mark `Implemented in:` with the current repo name. Move completed items out of Remaining Work. Mark any sections that are designed but not yet coded as `PLANNED`
7. **For doc commands** (e.g., `Check spec drift`, full rerun, setup) — read `docs/DOCUMENT_FIRST.md` and follow its rules

**Docs here:** decisions in `docs/decisions/NNNN-title.md` (short, frozen, a newer record supersedes an old one); how each area works now in `docs/<area>.md` (one page per area, about 300 lines, updated by the PR that changes the area). The rule is the `docs-rule` skill in loop.

## Backlog Discipline

`docs/BACKLOG.md` is the single living list of every TODO the user has called out, **including ones that have already been shipped**.

- When the user asks for something new, capture it in `docs/BACKLOG.md` *before* starting work — under the right priority (`P0` / `P1` / `P2`).
- When something ships, **don't delete it** — move it under `## Done` with a one-line note + the date so we can audit what landed and when.
- Items that exist as code but the user has not yet seen working live in `## P0 — needs verification`.
- Never silently drop a request. If you push back on scope, write the rationale inline next to the entry.
- Treat `docs/BACKLOG.md` as authoritative when the user says "did you do X" — search there first before re-reading code or git log.

## Public repo

This repo is PUBLIC because the GTM Community Template Gallery serves it. Commits, PRs, issues, comments and docs here carry the technical change only: no customer names, domains, rates, counts, internal table names or operator quotes. Edit history keeps old text, so rewriting a PR body later does not remove a leak. Keep technical evidence (a 404, a Gallery field) here; put customer and business context in a private repo, in its `docs/BACKLOG.md` or on a private twin PR.

## What this repo is

A single GTM Community Template, `template.tpl`: it reads the `tclid` query parameter and writes it to `localStorage` under the key `tclid`. There is no build, no test suite and no CI workflow; `template.tpl`'s `___TESTS___` block is empty, so verification is manual in GTM Preview (`docs/TESTING.md`). `master` is the default branch and has no branch protection or merge queue.

**Release:** the GTM Community Template Gallery serves the commit named by the FIRST `versions[]` entry in `metadata.yaml` (newest first), not `master` HEAD. A change reaches no one until a new first entry points at its real commit sha. To see what the Gallery serves now, read `versions[0].sha` from https://tagmanager.google.com/api/gallery/owners/tapperai/templates/gtm-web-identifier-setter (drop the first line of the answer).
