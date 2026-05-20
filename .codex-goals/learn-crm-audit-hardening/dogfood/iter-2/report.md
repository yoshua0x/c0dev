# Learn CRM Dogfood Iteration 2

Date: 2026-05-18
Target: `https://learn.enterlinks.localhost:1355/crm`
Session: `learn-crm-hardening-iter2`

## Summary

- Critical: 0
- High: 0
- Medium: 0
- Low: 0

Authenticated source-blind route loop passed after local Paperclip was made available. The app reported `sourceLabel: paperclip`, one internal CRM note persisted after reload, 375px mobile had no body-level horizontal overflow, and the browser reported no page errors after the loop.

## Setup Evidence

- Paperclip dev server: `http://127.0.0.1:3100/api/health` returned `status=ok`, `deploymentMode=local_trusted`.
- Learn CRM plugin: installed and activated as `learn-crm`.
- Local Learn company: `04af5e21-8631-481b-ba02-5e3a89b61304`.
- Imported snapshot: 2,589 contacts, 1,235 domains, 3,284 review tasks.
- Seeded public evidence: `evidence:dogfood:learn-crm-hardening`.

## Route Coverage

- `/crm`: `screenshots/crm-authenticated.png`
- `/crm/contacts`: `screenshots/contacts-desktop.png`
- `/crm/contacts/contact%3Avcf-0338%3Ae5a25b88e0ab`: `screenshots/contact-detail.png`
- `/crm/companies/domain%3Asummit.co`: `screenshots/company-detail.png`
- `/crm/vectors/opportunity%3Aai_portfolio_ipo`: `screenshots/vector-detail.png`
- `/crm/evidence`: `screenshots/evidence-list.png`
- `/crm/evidence/evidence%3Adogfood%3Alearn-crm-hardening`: `screenshots/evidence-detail.png`
- `/crm/merges/merge%3Apaddycochrane%40gmail.com`: `screenshots/merge-detail.png`
- `/crm/workloop/followup%3Acontact%3Avcf-0338%3Ae5a25b88e0ab%3Aidentity`: `screenshots/workloop-task-detail.png`
- Return to `/crm/contacts`: `screenshots/contacts-return.png`

## Durable Write Proof

- Action: internal note on `contact:vcf-0338:e5a25b88e0ab`.
- Note body: `Dogfood durable note 2026-05-18T22:31Z: verifies Paperclip-backed CRM note persistence after reload.`
- Before reload snapshot: `sourceLabel=paperclip`, `notes=1`.
- After reload snapshot: `sourceLabel=paperclip`, `notes=1`, `visibility=internal`, `subjectId=contact:vcf-0338:e5a25b88e0ab`.
- Evidence screenshot: `screenshots/contact-note-after-reload.png`.

## Mobile

- Viewport: 375 x 900.
- Measurement: `innerWidth=375`, `documentScrollWidth=375`, `bodyScrollWidth=375`.
- Evidence screenshot: `screenshots/contacts-mobile-375.png`.

## Privacy Boundary

- Public evidence route displayed source-backed evidence only.
- The persisted note used `visibility=internal`; it did not appear in public evidence detail during the route loop.

## Console / Errors

- `agent-browser errors`: empty after route loop.
- `agent-browser console --clear`: no actionable console errors after route loop.
