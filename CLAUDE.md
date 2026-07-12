# gtm-web-identifier-setter — repo guide (for AI agents)

## Push / remote-write guard
Local commits are fine. NEVER `git push`, open a PR, or publish this template
to the Google Tag Manager Community Template Gallery without EXPLICIT,
per-branch approval. Publishing = a new gallery version consumed by real
customer GTM containers.

## What this repo is
A single **Google Tag Manager Custom Template** ("Tapper - Identifier Setter").
The tag reads the Tapper click id (`tclid`) query parameter off the page URL
and writes it into browser `localStorage` under the key `tclid`, so downstream
Tapper tracking (the tracker SDK / tracking-script) can attribute the visit.
Distributed via GTM's Community Template Gallery, not deployed to any tapper
server. Peripheral: no build, no CI, no runtime we host.

## Stack
- **Format:** GTM custom-template `.tpl` file (a single text file, `template.tpl`
  — INFO / TEMPLATE_PARAMETERS / SANDBOXED_JS_FOR_WEB_TEMPLATE / WEB_PERMISSIONS
  / TESTS / NOTES sections delimited by `___SECTION___` markers).
- **Language:** GTM **Sandboxed JavaScript** (ES5-ish; NOT Node, NOT browser
  JS). You may only `require()` GTM-approved APIs (`getQueryParameters`,
  `localStorage`, …) — no `window`, no `document`, no npm.
- No `package.json`, no bundler, no lockfile. Nothing to install.

## The whole tag logic (current)
```js
const getQueryParameters = require("getQueryParameters");
const localStorage = require('localStorage');
const tclid = getQueryParameters("tclid");
if (tclid) localStorage.setItem("tclid", tclid);
data.gtmOnSuccess();
```

## Gate command
There is no compiler. Correctness is proven inside GTM's Template Editor:
paste `template.tpl`, run the `___TESTS___` block, and confirm no permission
violations. Any new API you `require()` MUST have a matching entry in
`___WEB_PERMISSIONS___` or the template fails to load. Always end a successful
path with `data.gtmOnSuccess()` (and `data.gtmOnFailure()` on error).

## Where things live
- `template.tpl` — the entire tag (edit the `___SANDBOXED_JS_FOR_WEB_TEMPLATE___`
  section for logic; `___WEB_PERMISSIONS___` for any new capability;
  `___TESTS___` for tests). This is the only source file.
- `metadata.yaml` — gallery version history (`sha` + `changeNotes` per release).
- `README.md`, `LICENSE` — docs/legal.

## Conventions to match
- Query-param + storage key is **`tclid`** verbatim (Tapper click id). Don't
  rename it — the tracker reads exactly this key.
- Guard before write (`if (tclid) …`) — never set an empty value.
- Whitelist the exact query key in permissions (`queryKeys` listItem `"tclid"`,
  `access_local_storage` key `"tclid"` with read/write) — GTM enforces least
  privilege; broadening permissions is a review-worthy change.
- To ship a change: append a new entry to `metadata.yaml.versions` with the new
  git `sha` + a one-line `changeNotes`.

## What NOT to do
- Don't add npm deps, a build step, TypeScript, or browser/Node globals —
  the GTM sandbox rejects them.
- Don't widen `___WEB_PERMISSIONS___` (extra query keys, cookies, other storage
  keys, network) without explicit approval — it changes what the tag can touch
  in customer browsers.
- Don't rename the `tclid` param/key or remove the empty-value guard.
- Don't publish to the gallery without approval (see top guard).
