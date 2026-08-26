# GTM Web Identifier Setter — Environment Spinup

> **Status:** `LIVE`
>
> **Created:** 2026-08-26
> **Last updated:** 2026-08-26
>
> **Implemented in:** gtm-web-identifier-setter

## Overview

This repo has **no runtime environment to spin up**. It is a single Google
Tag Manager Community Template file (`template.tpl`) plus Gallery metadata
(`metadata.yaml`, `README.md`). There is no server, no cloud project, no
database, no queue, no CI/CD pipeline, and no build step — GTM's own
Template Editor UI (or a GTM Community Template Gallery submission) is the
only "deployment target."

Every section of the standard environment-spinup template below (Cloud
Services, Databases, Event Stores/Message Queues, Container Orchestration,
CI/CD, Cross-Repo Sync Scripts, Secrets & Configuration, Teardown/DR) is
omitted because none of it applies to this repo.

---

## "Spinup" Procedure (using the template locally / in a test container)

1. Open Google Tag Manager → a web container → **Templates** → **Search
   Gallery** (to use the published version) or **New** → **Import** and
   select `template.tpl` (to load this repo's copy directly).
2. Create a **Tag** from the imported template, attach a trigger.
3. Use GTM **Preview** mode to fire it against a real page — see
   [TESTING.md](TESTING.md) for scenarios and verification steps.

There is nothing to tear down: no provisioned infrastructure is created by
using or previewing the template.

---

## Files

- `template.tpl` — the entire template (all deployable logic + metadata).
- `metadata.yaml` — Gallery submission metadata (homepage, docs URL,
  per-version changelog).
- `README.md` — Gallery-facing description.
