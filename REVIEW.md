# Review guide: gtm-web-identifier-setter

A single-file GTM Community Template (`template.tpl`) that reads the Tapper query parameter from the
URL and writes the Tapper identifier to local storage. The Gallery serves the commit named in
`metadata.yaml`; GTM never auto-updates an imported template. There is no CI here, so the review is
the only check. This repo is PUBLIC: findings stay technical. History is thin (docs-only PRs), so
this guide comes from what the template does and the sister template's permission defect
(tracking-script#7).

## Proof rule

- Every finding cites `path:line` at the reviewed sha (`git show <sha>:template.tpl`), says what a
  visitor's browser gets wrongly, and how you know: a GTM Preview, or the template's `___TESTS___` section.
- No `path:line`, no finding.

## Real bugs here (block on these)

- **The identifier written wrongly**: the template reads the `tclid` query parameter and writes it to
  local storage under `tclid`. A renamed parameter or key, a write when the parameter is absent, or an
  overwrite with an empty value breaks the tracker's read and the attribution of every ad-click visit.
- **Consent added or removed without a decision**: the template has no consent logic today; a change
  that adds or bypasses one changes what every importer's site stores, so the PR must say why.
- **Permissions that do not match the calls**: GTM checks each permission (query parameter read, local
  storage key, globals) by exact key; a missing grant makes the tag throw, an extra one widens what
  every importer grants (the tracking-script#7 defect).
- **A broken release entry**: the `metadata.yaml` version must name the real commit of the template
  change, merged with a merge commit so the sha survives.
- **Public-repo leaks**: customer names, domains, counts or internal table names anywhere.

## Skip

- README and docs wording, template field labels that are not wrong.

## Checks worth running

- Read `template.tpl`'s permissions block against every sandboxed API call in the code.
