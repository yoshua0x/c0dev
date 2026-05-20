# Learn CRM Audit + Hardening Notes

## 2026-05-18 14:47 PDT

- Native Codex goal created: `learn-crm-audit-hardening`.
- Previous CRM productization goal is complete and remains the baseline. Prior evidence includes focused tests, typecheck, lint, diff check, and authenticated dogfood iter-14.
- Read-only council-deep audit completed with three angles:
  - Architecture/API/Paperclip: task detail route can become unresolved after status changes because current detail lookup appears tied to open follow-up tasks; API validation is uneven; durable mutation roundtrip still needs proof.
  - UI/composition/performance: `LearnCrmApp` is too broad, CRM list routes derive workspace twice, search/filter work is eager, and mobile/a11y details need focused hardening.
  - Verification/dogfood: source-blind authenticated loop must cover `/crm`, contacts, contact detail, company/domain, vector, evidence, merge candidate, workloop task, return to contacts, and at least one durable write.
- Vercel composition guidance loaded. Refactors should prefer explicit composed components/providers over more boolean or mode props.
- Vercel React best-practice guidance loaded. Fixes should avoid extra client serialization, route waterfalls, and avoidable expensive client work.
- Agent browser dogfood guidance loaded. Browser verification must use direct `agent-browser` / `agent-browser-dogfood` and save artifacts under this goal.

## Working Guardrails

- Preserve Learn auth, private/public evidence boundaries, and Paperclip durability semantics.
- Do not edit `.env` files.
- Do not revert unrelated dirty work in the Enterlinks monorepo.
- Prefer regression tests for stable bugs.
- Do not mark the native goal complete without focused checks, full checks, and dogfood evidence.

## 2026-05-18 14:49 PDT

- Fixed A1 task detail regression in the live Enterlinks CRM tree:
  - Added `allFollowUpTasks` to the derived workspace.
  - Kept `followUpTasks` as the open-only workloop queue.
  - Updated task detail selectors to resolve against all follow-up tasks.
  - Added regression coverage for `done`, `deferred`, and `blocked` task route IDs.
- Verification: `pnpm test -- test/learn-crm.test.ts` passed from `/Users/0x/code/enterlinks/enterlinks-monorepo/apps/enterlinks-web`.

## 2026-05-18 14:53 PDT

- Fixed A4 mutation validation gaps:
  - Added `app/api/crm/validation.ts` for shared string cleanup, enum checks, and saved queue normalization.
  - Notes now reject invalid subject types and visibility values.
  - Saved queues trim labels/query values, normalize filter/sort, and reject blank delete IDs.
  - Evidence, merge, and review decisions now reject bad decisions and blank IDs before Paperclip.
  - Tasks now reject invalid status and malformed new-task payloads before writable state access.
- Verification: `pnpm test -- test/learn-crm-api.test.ts` passed.

## 2026-05-18 14:55 PDT

- Fixed A6 accessibility/form affordance gaps:
  - CRM banners in command center, profile actions, subject work actions, task status, evidence decisions, and merge decisions now expose polite live-region status.
  - CRM search, saved queue, profile override, tag, note, and subject task/note controls now include explicit `name`, `type`, and autocomplete policy where applicable.
  - Added static render assertions for profile actions, subject work actions, and command center controls.
- Verification: `pnpm test -- test/learn-crm-detail.test.tsx test/ui-shell.test.tsx` passed.

## 2026-05-18 14:56 PDT

- Fixed A2 duplicate workspace derivation:
  - `LearnCrmWorkspace` accepts an optional prebuilt `LearnCrmWorkspaceModel`.
  - CRM list pages pass the `workspace` from `getLearnCrmRouteData`.
  - Static tests can still render the shell with snapshot-only fallback.
- Verification: `pnpm test -- test/routes.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx` passed.

## 2026-05-18 14:58 PDT

- Fixed A7 route/shareability gap for the contact workbench:
  - `parseContactQueueParams` now accepts `selected`.
  - `contactQueueHref` can include selected contact IDs.
  - `/crm/contacts` hydrates initial selected contact from the URL.
  - Contact search/filter/sort/selected changes update browser history on `/crm/contacts` without forcing a server navigation.
- Verification: `pnpm test -- test/learn-crm-detail.test.tsx test/routes.test.ts` passed.

## 2026-05-18 15:00 PDT

- Fixed A8 composition/file-size issue:
  - Extracted contact queue URL/state management into `useContactQueueState`.
  - Extracted selected-contact footer callout into `SelectedContactCallout`.
  - Extracted non-contact action banner into `CrmGlobalStatusBanner`, with live-region semantics.
  - `LearnCrmApp` is now 495 LOC.
- Verification: `pnpm typecheck` passed and `pnpm test -- test/learn-crm-detail.test.tsx test/ui-shell.test.tsx` passed.

## 2026-05-18 15:11 PDT

- Completed authenticated direct `agent-browser` dogfood iter-1.
- Fixed A5 mobile overflow:
  - Before: 375px viewport produced 955px document width.
  - After: 375px viewport produces 375px document width.
- Added privacy hardening discovered during evidence review:
  - Private note bodies are no longer rendered on evidence board private-reference cards.
  - Added regression test for private note redaction on evidence surfaces.
- Durable writes are blocked in this environment:
  - CRM UI reports `runtime-import`.
  - Writable controls are disabled.
  - Authenticated `/api/crm/notes` probe returned 502 `fetch failed`.
- Evidence detail browser traversal is blocked in this environment because runtime import has `0 public sources`.

## 2026-05-18 15:38 PDT

- Resolved the Paperclip durability blocker:
  - `make paperclip-dev` initially failed because embedded Postgres native symlinks were missing after ignored install scripts.
  - Ran the package symlink hydration script from the embedded Postgres package directory.
  - Re-ran `make paperclip-dev` with escalation so embedded Postgres could create lock files under `~/.paperclip`.
  - Paperclip started at `http://127.0.0.1:3100`, local trusted mode.
  - Installed/activated `learn-crm` plugin from `/Users/0x/code/enterlinks/enterlinks-monorepo/services/paperclip-plugins/plugin-learn-crm`.
  - Created local Learn Capital company `04af5e21-8631-481b-ba02-5e3a89b61304`.
  - Seeded import snapshot into plugin state: 2,589 contacts, 1,235 domains, 3,284 review tasks.
  - Seeded one source-backed public evidence record so `/crm/evidence/[sourceId]` could be traversed.
- Fixed local Paperclip company resolution:
  - `learn-capital` is not a Paperclip UUID, so plugin state rejected it with `invalid input syntax for type uuid`.
  - Added local-only `/api/companies` name resolution in `lib/learn-crm/paperclip.ts`, with test coverage.
  - No `.env` edits and no local UUID hardcoded in source.
- Durable write proof:
  - Browser session reported `sourceLabel=paperclip`.
  - Saved internal note for `contact:vcf-0338:e5a25b88e0ab`.
  - Reloaded `/crm/contacts`.
  - Browser-authenticated snapshot still reported `notes=1`, same note body, `visibility=internal`.
- Dogfood iter-2:
  - Covered `/crm`, contacts, contact detail, company/domain, vector, evidence list/detail, merge candidate, workloop task, and return to contacts.
  - 375px viewport measured `documentScrollWidth=375`, `bodyScrollWidth=375`.
  - `agent-browser errors` empty after route loop.
  - Report: `dogfood/iter-2/report.md`.
- Final checks:
  - Focused test gate passed.
  - `pnpm test` passed: 21 files, 134 tests.
  - `pnpm typecheck` passed.
  - `pnpm lint` passed.
  - `git diff --check` passed in Enterlinks repo and goal ledger.
