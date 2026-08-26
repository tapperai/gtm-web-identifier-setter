# GTM Web Identifier Setter — Testing Framework

> **Parent:** [GTM Web Identifier Setter](SPEC.md)

There is no automated test suite, build step, or `y-scripts/` simulator in
this repo — `template.tpl`'s own `___TESTS___` block is `scenarios: []`.
Verification is manual, inside the GTM UI's built-in Preview mode, because
the sandboxed JS (`getQueryParameters`, `localStorage` sandbox APIs) only
runs inside a real GTM container context.

---

## Prerequisites

- A Google Tag Manager web container with this template imported (Templates
  → New → Import from `template.tpl`, or the published Gallery version).
- A tag created from the template, with a trigger attached (e.g. "All
  Pages" or a specific page trigger).
- GTM **Preview** mode connected to the target page.

---

## Scenario 1: `tclid` present in the URL

```
https://example.com/landing?tclid=abc123
```

**What happens:**
1. Load the URL above with GTM Preview connected.
2. The tag fires on the configured trigger.
3. `getQueryParameters("tclid")` returns `"abc123"`.
4. `localStorage.setItem("tclid", "abc123")` runs.

**Verify (browser DevTools console on the page):**
```js
localStorage.getItem("tclid")
// Expect: "abc123"
```

Also check GTM Preview's tag firing panel — the tag should show as fired
with no errors, and `data.gtmOnSuccess()` should have been called (no
`gtmOnFailure` in the trace).

---

## Scenario 2: `tclid` absent from the URL

```
https://example.com/landing
```

**What happens:**
1. Load the URL above (no `tclid` param) with GTM Preview connected.
2. The tag fires, `getQueryParameters("tclid")` returns an empty/falsy
   value, the `if (tclid)` guard skips the `localStorage.setItem` call.
3. `data.gtmOnSuccess()` still runs — the tag does not error or block.

**Verify:**
```js
localStorage.getItem("tclid")
// Expect: unchanged from before this page load —
// null if never set, or the previously-stored value if one already existed.
```

---

## Scenario 3: repeat firing overwrites the previous value

```
https://example.com/landing?tclid=abc123   (first load)
https://example.com/landing?tclid=xyz789   (second load, same browser)
```

**What happens:** each load independently sets `localStorage["tclid"]` to
that load's URL value — no accumulation, no dedup.

**Verify:**
```js
localStorage.getItem("tclid")
// Expect: "xyz789" (the most recent value) after the second load
```

---

## Publishing checklist

Before publishing a template change to the GTM Gallery:

1. Bump `___INFO___.version` in `template.tpl` if the change is
   backward-incompatible for existing users.
2. Add an entry to `metadata.yaml`'s `versions` list with the new commit
   `sha` and a one-line `changeNotes`.
3. Re-run Scenarios 1–3 above in GTM Preview against the changed template
   before submitting to the Gallery.
