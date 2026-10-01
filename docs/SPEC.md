# GTM Web Identifier Setter

> **Status:** `LIVE`
>
> **Created:** 2026-08-26
> **Last updated:** 2026-10-01
>
> **Implemented in:** gtm-web-identifier-setter

## Overview

A single Google Tag Manager (GTM) Community Template Gallery **tag** template
(`template.tpl`) named "Tapper - Identifier Setter". When it fires in a GTM
web container, it reads the `tclid` query parameter off the current page URL
and, if present, writes it into the browser's `localStorage` under the key
`tclid`. This is how a Tapper click ID that arrives on a landing page via URL
gets persisted client-side so later Tapper tracking/conversion code on the
same origin can read it back out of `localStorage`.

The only reader of that key is the `gtm-web-event-dispatcher` template, which
sends it to `api.tapper.ai/gtm/track`. That endpoint has returned 404 since
2026-01-27 (re-checked 2026-10-01), so the dispatcher is inert and nothing
consumes the value this template writes. The docs-page item in
`docs/BACKLOG.md` is on hold until the dispatcher is retired or restored.

The whole repo is this one file plus GTM Gallery metadata — there is no
build step, no server, no dependencies, and no other source files.

---

## Schema

Not applicable — no database, no API, no persisted server-side state. The
only "schema" is the `localStorage` key it writes:

| Storage | Key | Value |
|---|---|---|
| `window.localStorage` (page origin) | `tclid` | Raw string value of the `tclid` URL query parameter |

---

## Contracts

Not applicable — this is a client-side GTM tag, not a service with an API.

---

## Logic

`template.tpl` → `___SANDBOXED_JS_FOR_WEB_TEMPLATE___` section:

```js
const getQueryParameters = require("getQueryParameters");
const localStorage = require('localStorage');

const tclid = getQueryParameters("tclid");

if (tclid) localStorage.setItem("tclid", tclid);

data.gtmOnSuccess();
```

- Runs entirely inside GTM's sandboxed JS runtime (Custom Templates sandbox),
  not raw browser JS — hence the `require()` calls for GTM's sandbox APIs
  instead of reading `location.search` / `window.localStorage` directly.
- `getQueryParameters("tclid")` reads the `tclid` query param from the
  current page URL.
- If `tclid` is present (non-empty), it's written to `localStorage["tclid"]`.
  If absent, nothing is written — no clearing, no default value.
- Always calls `data.gtmOnSuccess()` regardless of whether `tclid` was
  present, so the tag never blocks/fails a GTM trigger sequence.
- `___TEMPLATE_PARAMETERS___` is `[]` — the tag takes no user-configurable
  fields in the GTM UI; the `tclid` param name is hardcoded.

---

## Permissions (`___WEB_PERMISSIONS___`)

GTM sandboxed templates must declare every capability they use, or GTM
refuses to save/publish them:

| Permission | Scope |
|---|---|
| `get_url` | Query part only: `urlParts: specific` with `query: true`, `queriesAllowed: specific`, `queryKeys: ["tclid"]` |
| `access_local_storage` | Key `tclid`, `read: false`, `write: true` — write-only access to that one key |

---

## Edge Cases

- **No `tclid` in the URL**: the `if (tclid)` guard means `localStorage` is
  left untouched — an existing stored `tclid` from a previous page view is
  NOT overwritten or cleared when the param is absent.
- **`tclid` present but empty string**: `getQueryParameters` returning `""`
  is falsy, so it is treated the same as absent (not written).
- **Repeat firings on the same page**: each firing simply overwrites
  `localStorage["tclid"]` with the latest URL's value — idempotent, no
  dedup logic needed.

---

## Testing

See [TESTING.md](TESTING.md).

---

## Files

- `template.tpl` — the entire GTM Community Template (terms-of-service
  header, `___INFO___` metadata block, empty `___TEMPLATE_PARAMETERS___`,
  the sandboxed JS logic, `___WEB_PERMISSIONS___`, empty `___TESTS___`
  scenarios, and a creation-date `___NOTES___` footer)
- `metadata.yaml` — GTM Gallery submission metadata: homepage/docs URLs and
  a version history (`sha` + `changeNotes` per released version)
- `README.md` — Gallery-facing overview/feature list. Its docs link (labelled
  "Cookie Setter", a mislabel) and `metadata.yaml`'s `documentation` URL both
  point at https://docs.tapper.ai/gtm/web-identifier-setter, which returns
  404 (checked 2026-09-29); see Remaining Work

---

## Remaining Work

- The template logic has no known gaps.
- Broken documentation link in `README.md` and `metadata.yaml` (404, plus the
  "Cookie Setter" label in the README). Tracked as P1 in `docs/BACKLOG.md`.
