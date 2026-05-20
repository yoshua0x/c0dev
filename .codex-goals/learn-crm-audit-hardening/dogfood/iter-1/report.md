# Learn CRM Dogfood Iteration 1

Date: 2026-05-18 15:11 PDT

## Environment

- URL: `https://learn.enterlinks.localhost:1355/crm`
- Browser: direct `agent-browser`, session `learn-crm-hardening`
- Auth: local dev CRM session cookie generated with the non-production session fallback.
- Data source observed in UI: `runtime-import`
- Write mode observed in UI: read-only banner, `Read-only runtime import: start the Paperclip CRM plugin for durable actions.`

## Route Loop

| Step | Route | Result |
| --- | --- | --- |
| CRM contacts | `/crm/contacts` | Pass. Contact queue, filters, saved queues, selected dossier, and read-only status visible. |
| Contact detail | `/crm/contacts/contact%3Avcf-0338%3Ae5a25b88e0ab` | Pass. Rendered David Graziano profile with identity, authority, related records, work, notes, timeline, evidence, and merge sections. |
| Companies | `/crm/companies` | Pass. Organization map rendered and exposed company detail links. |
| Company detail | `/crm/companies/domain%3Asummit.co` | Pass. Rendered company intelligence, linked contacts, work, timeline, vectors, and evidence. |
| Vectors | `/crm/vectors` | Pass. Pathway board rendered and exposed vector detail links. |
| Vector detail | `/crm/vectors/opportunity%3Aai_portfolio_ipo` | Pass. Rendered pathway shape, linked contacts, company domains, work, timeline, and evidence. |
| Evidence | `/crm/evidence` | Pass for route shell. Runtime import has `0 public sources`, so no evidence detail link was available for source-blind browser inspection. |
| Merges | `/crm/merges` | Pass. Canonical contact decision list rendered and exposed merge detail links. |
| Merge detail | `/crm/merges/merge%3Apaddycochrane%40gmail.com` | Pass. Rendered canonical record, duplicates, linked vectors, decision panel, preview, tasks, and notes. |
| Workloop | `/crm/workloop` | Pass. Tasks/notes/activity route rendered and exposed task detail links. |
| Task detail | `/crm/workloop/followup%3Acontact%3Avcf-0338%3Ae5a25b88e0ab%3Aidentity` | Pass. Rendered task facts, subject backref, timeline, status actions, timing, and subject notes. |
| Return | `/crm/contacts` | Pass. Contacts route reloaded without auth loss. |

## Screenshots

- `contacts-desktop.png`
- `contacts-mobile-375.png` captured before overflow fix.
- `contacts-mobile-375-fixed.png` captured after overflow fix.
- `contact-detail.png`

## Findings

### Fixed Medium: Mobile body-level horizontal overflow

- Before fix at 375px:
  - `innerWidth: 375`
  - `documentElement.clientWidth: 375`
  - `documentElement.scrollWidth: 955`
  - `body.scrollWidth: 955`
- Root cause: the contact table min-width forced the grid item to its min-content width instead of scrolling inside the panel.
- Fix: added `min-w-0` to contact table/dossier grid-item roots and `max-w-full` to the table scroller.
- After fix at 375px:
  - `innerWidth: 375`
  - `documentElement.clientWidth: 375`
  - `documentElement.scrollWidth: 375`
  - `body.scrollWidth: 375`

### Blocked: Durable writable action roundtrip

- UI source label is `runtime-import`, not `paperclip`.
- All writable controls are disabled by read-only mode.
- Authenticated write probe:

```json
{
  "status": 502,
  "body": "{\"error\":\"fetch failed\"}"
}
```

- Conclusion: Paperclip durable CRM state is unavailable in this local run. Writable action reload persistence cannot be proven until the Learn CRM Paperclip plugin/API is running.

### Blocked: Evidence detail browser route

- `/crm/evidence` route renders, but runtime import reports `0 public sources`.
- No `/crm/evidence/[evidenceId]` link was available for source-blind browser traversal in this environment.
- Static route tests still cover encoded evidence detail routing; browser detail coverage requires a Paperclip/runtime snapshot with public evidence records.

## Severity Summary

- Critical: 0 open found in browser loop.
- High: 0 open found in browser loop, excluding environment blockers above.
- Medium: 1 found and fixed in this iteration.
- Blockers: Paperclip durable state unavailable; no public evidence records in runtime import.
