# Learn CRM Audit + Hardening Experiments

| ID | Status | Hypothesis / Attempt | Evidence | Next |
| --- | --- | --- | --- | --- |
| E0 | Complete | Read-only `council-deep n=3` audit can establish the hardening backlog without touching CRM code. | Architecture/API, UI/composition/performance, and verification/dogfood agents identified the baseline issues now recorded in `AUDIT.md`. | Begin with task detail regression because it can break route durability after a user action. |
| E1 | Complete | Route/detail lookup should use all CRM follow-up tasks while the workloop queue remains open-only. | Added `allFollowUpTasks`, switched `findCrmTask`/`tasksForSubject` to all-task lookup, and added `done`/`deferred`/`blocked` regression coverage. `pnpm test -- test/learn-crm.test.ts` passed. | Normalize mutation API validation next. |
| E2 | Complete | CRM mutation routes should reject malformed payloads consistently before Paperclip writes. | Added shared CRM validation helpers; tightened note, queue, evidence, merge, review, and task routes; added invalid payload and queue normalization tests. `pnpm test -- test/learn-crm-api.test.ts` passed. | Harden accessibility/form affordances and route state next. |
| E3 | Complete | CRM async banners and work inputs can be made screen-reader/form friendly without changing data flows. | Added `role=status`/`aria-live=polite` to CRM action banners and added `name`/`type`/autocomplete metadata to command center, saved queue, profile, and subject work controls. `pnpm test -- test/learn-crm-detail.test.tsx test/ui-shell.test.tsx` passed. | Continue with state/performance and mobile overflow verification. |
| E4 | Complete | CRM list routes can reuse the route-derived workspace instead of deriving it again inside the shell. | `LearnCrmWorkspace` now accepts a prebuilt workspace with a fallback for tests; list routes pass through `workspace` from `getLearnCrmRouteData`. `pnpm test -- test/routes.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx` passed. | Continue with durable write proof and client state simplification. |
| E5 | Complete | Contact queue state should be shareable through the URL, including selected contact. | Added `selected` parsing/link generation, initial selected contact hydration, and live history replacement for query/filter/sort/selected changes on `/crm/contacts`. `pnpm test -- test/learn-crm-detail.test.tsx test/routes.test.ts` passed. | Continue with durable write proof and composition/performance cleanup. |
| E6 | Complete | `LearnCrmApp` can be reduced under repo size guidance by extracting focused composition pieces. | Extracted `useContactQueueState`, `SelectedContactCallout`, and `CrmGlobalStatusBanner`; `LearnCrmApp` is now 495 LOC. `pnpm typecheck` and `pnpm test -- test/learn-crm-detail.test.tsx test/ui-shell.test.tsx` passed. | Durable write proof and mobile/browser verification remain. |
| E7 | Complete | Browser dogfood can identify mobile overflow and live environment blockers before final checks. | Authenticated direct `agent-browser` loop covered CRM route graph. Fixed 375px body overflow. Paperclip writes blocked by read-only runtime import and API 502. Evidence detail blocked by 0 public sources. Report: `dogfood/iter-1/report.md`. | Run full checks; do not mark native goal complete until Paperclip durable state/evidence detail blockers are resolved or accepted. |
| E8 | Complete | Local Paperclip can be started, seeded, and used as the durable Learn CRM state source without hardcoding local UUIDs into app source. | Rebuilt skipped embedded Postgres symlinks, started `make paperclip-dev` with escalation, installed `learn-crm`, created local Learn Capital company, seeded import batch, and added local-only company-name resolution with regression coverage. Browser snapshot returned `sourceLabel=paperclip`. | Run durable write/reload proof and full source-blind route loop. |
| E9 | Complete | Durable CRM write can be proven with a browser-authenticated note roundtrip. | Browser-authenticated CRM saved note through `/api/crm/notes`; after reload `/api/crm/snapshot` returned `notes=1`, `visibility=internal`, `subjectId=contact:vcf-0338:e5a25b88e0ab`. Screenshot: `dogfood/iter-2/screenshots/contact-note-after-reload.png`. | Finish required route coverage and final checks. |
| E10 | Complete | Follow-up dogfood can pass after Paperclip durable state and seeded public evidence are available. | `dogfood/iter-2/report.md` covers `/crm`, contacts, contact detail, company/domain, vector, evidence list/detail, merge detail, workloop task, return to contacts, desktop/mobile screenshots, 375px overflow measurement, and 0 browser errors. | Close with final test/check evidence. |

## Experiment Protocol

- Before each fix, add or identify the smallest regression signal.
- After each fix, run the narrowest relevant test first, then the focused gate when related surfaces are touched.
- Record failed attempts; do not collapse them into the successful attempt.
- Save dogfood artifacts under `.codex-goals/learn-crm-audit-hardening/dogfood/iter-N/`.

## Planned Experiments

- None open for this hardening pass.
