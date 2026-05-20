# Experiment Notes

## 2026-05-18 Baseline

Learn CRM shell cleanup is already implemented in the current dirty worktree:

- Learn left rail now shows the wordmark once.
- Top bar hides duplicate brand and shows route identity.
- CRM sub-nav is local to the CRM.
- Focused tests, typecheck, and lint passed in the previous pass.
- Browser verified `https://learn.enterlinks.localhost:1355/crm` and dashboard shell behavior.

Keep this as the starting point for CRM productization.

## 2026-05-18 Atomic Source Deep Dive

Atomic CRM has the missing CRM shape:

- Resource registry with explicit route/resources.
- Contact index with full search/filter/sort/import/export/bulk actions.
- Contact detail with notes primary content and status/details/tags/tasks/merge in the aside.
- Company detail with activity/contacts/deals tabs.
- Deal board with stage columns and modal detail.
- Task list bucketed by due pressure.
- Notes as a reusable subject timeline.
- Merge preview and apply semantics.
- Activity log and summary views.

Do not port infrastructure. Adapt the primitives into Learn's current Next.js + Paperclip model.

## 2026-05-18 Learn CRM Current Shape

Learn already has richer domain primitives than Atomic for this use case:

- Relationship provenance and source authority.
- Evidence source decisions.
- Portfolio vector/opportunity scoring.
- Review decisions.
- Enrichment jobs.
- Read-only fallback when durable Paperclip state is missing.

The issue is interaction architecture, not data model completeness. The single-page workbench asks one surface to be an index, detail panel, evidence board, work queue, merge queue, and vector board at once.

## Product Bet

Contact drill-down is the highest leverage first slice.

Reasoning:

- It absorbs the overloaded side rail into a proper profile.
- It gives the CRM a durable mental model: index to entity to related work.
- It exercises existing APIs without requiring new backend state.
- It creates a clear browser dogfood path.

Follow contact drill-down with company/domain detail and vector detail.

## 2026-05-18 02:01 PDT

- Executed the first productization slice in `/Users/0x/code/enterlinks/enterlinks-monorepo` because the c0dev project checkout did not contain the Learn CRM files.
- Added `@enterlinks/learn-crm` selector helpers for route id decoding and subject lookup.
- Added route-aware CRM section links and index routes for Contacts, Companies, Vectors, Merges, Workloop, and Evidence.
- Added detail routes for contacts, companies/domains, and vectors.
- Added subject detail views covering contact identity/reachability, source authority, tasks, notes, evidence, merge status, company-linked contacts/vectors/evidence, and vector-linked contacts/domains/evidence.
- Added tests for subject selectors and static rendering of the three detail surfaces.
- Verification passed:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
- `agent-browser-dogfood` reached `/crm/login` but could not continue without a CRM access code. No credential guessing attempted. Report saved at `.codex-goals/learn-crm-productization/dogfood/iter-1/report.md`.

## 2026-05-18 02:04 PDT

- Completed E4 workloop bucket pass.
- Added `bucketCrmTasks` in `@enterlinks/learn-crm` to group open tasks by overdue, today, this week, later, high priority, and open queue.
- Updated `CrmActivityFeed` to render bucketed tasks with counts and links back to contact/company/vector subject detail routes.
- Added selector tests and static render coverage for the bucketed workloop.
- Verification passed:
  - `pnpm test -- test/learn-crm.test.ts test/learn-crm-detail.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`

## 2026-05-18 02:05 PDT

- Completed E5 merge governance preview pass.
- Added `mergeCandidateSummary` in `@enterlinks/learn-crm` for non-destructive review counts across candidate records, notes, tasks, vectors, emails, phones, and tags.
- Rendered summary counts in contact merge status and the merge governance panel.
- Kept canonical merge application out of scope; decisions remain non-destructive until durable apply semantics are explicitly added.
- Verification passed:
  - `pnpm test -- test/learn-crm.test.ts test/learn-crm-detail.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`

## 2026-05-18 02:07 PDT

- Completed E6 contact export pass.
- Added `exportContactRowsCsv` in `@enterlinks/learn-crm`.
- Added command-center `Export view` action for the current filtered/sorted contact queue.
- Kept import out of this pass; no safe in-app import path was introduced.
- Verification passed:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`

## 2026-05-18 02:10 PDT

- Completed E7 contact index pagination pass.
- Replaced the hard `rows.slice(0, 18)` preview cap with paged contact table behavior.
- Added visible range counts and Previous/Next controls while keeping page selection scoped to the currently visible row set.
- Added static render coverage for paginated contact table output.
- Verification passed:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
  - `pnpm typecheck`
  - `pnpm lint`

## 2026-05-18 02:15 PDT

- Completed E8 contact profile action pass.
- Added `ContactProfileActions` as a route-level client island for the contact detail page.
- Reused existing Learn CRM APIs for review decisions, enrichment jobs, contact overrides, tags, internal notes, and task status updates.
- Kept read-only runtime/demo behavior explicit; non-Paperclip sources render disabled actions with the existing durable-state banner language.
- Added static render coverage for the contact action stack and read-only disabled state.
- Verification passed:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
  - `git diff --check` in `/Users/0x/code/enterlinks/enterlinks-monorepo`
  - `git diff --check` in `/Users/0x/code/ai/c0dev`

## 2026-05-18 08:13 PDT

- Yoshua clarified browser tooling policy: use `agent-browser-dogfood` for all browser QA.
- Updated `PLAN.md` so browser verification requires the `agent-browser-dogfood` skill and direct `agent-browser` workflow.
- Do not substitute Chrome DevTools, Computer Use, or in-app Browser for dogfood unless Yoshua explicitly approves a fallback for a blocked session.

## 2026-05-18 08:19 PDT

- Completed E9 non-contact subject work-action pass.
- Added `CrmSubjectWorkActions` for company/domain and vector detail pages.
- Expanded the CRM API client with subject-aware review decisions, subject-aware notes, and task creation/upsert input while keeping existing contact helpers compatible.
- Company/domain detail now supports domain review, internal notes, task creation/status transitions, and domain enrichment queue.
- Vector detail now supports opportunity review, internal notes, and task creation/status transitions. Enrichment is not shown for vectors because the existing enrichment job API only accepts contact/domain/company subjects.
- Verification passed:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
  - `git diff --check` in `/Users/0x/code/enterlinks/enterlinks-monorepo`
- `agent-browser-dogfood` iter-2 used direct `agent-browser` and reached `/crm/login`, then stopped at the CRM access-code gate. Report and screenshot saved under `.codex-goals/learn-crm-productization/dogfood/iter-2/`.

## 2026-05-18 08:23 PDT

- Completed E10 subject timeline pass.
- Added `timelineForSubject` in `@enterlinks/learn-crm` to derive a subject audit trail from notes, tasks, review decisions, enrichment jobs, and activity events.
- Added `CrmSubjectTimeline` and wired it into contact, company/domain, and vector detail pages.
- Fixed CRM dynamic detail routes to await Next route params, matching generated Next page types.
- Verification passed:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
  - `git diff --check` in `/Users/0x/code/enterlinks/enterlinks-monorepo`
- `agent-browser-dogfood` iter-3 used direct `agent-browser`; the first attempt hit sandbox limits on `/Users/0x/.agent-browser`, then escalated according to policy and reached `/crm/login`. It stopped at the CRM access-code gate. Report and screenshot saved under `.codex-goals/learn-crm-productization/dogfood/iter-3/`.

## 2026-05-18 08:28 PDT

- Completed E11 routeable contact queue pass.
- Added contact queue URL helpers in `crm-workbench-config`: parsing for `query`, `filter`, and `sort`, plus canonical `/crm/contacts?...` href generation.
- `/crm/contacts` now reads search params and hydrates the contact query/filter/sort state.
- Saved queue chips now render as links to their routeable contact views, and the current queue exposes a `View link` affordance.
- Added static render coverage for queue param parsing, queue href generation, saved queue links, and current-view links.
- Verification passed:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
  - `git diff --check` in `/Users/0x/code/enterlinks/enterlinks-monorepo`
  - `git diff --check` in `/Users/0x/code/ai/c0dev`
- `agent-browser-dogfood` iter-4 used direct `agent-browser` against a routeable contacts URL. It redirected to `/crm/login` and stopped at the CRM access-code gate. Report and screenshot saved under `.codex-goals/learn-crm-productization/dogfood/iter-4/`.

## 2026-05-18 08:35 PDT

- Completed E12 evidence source detail pass.
- Added `findEvidenceSource`, `notesForEvidenceSource`, `opportunitiesForEvidenceSource`, `contactsForEvidenceSource`, and `cohortsForEvidenceSource` selectors.
- Added `/crm/evidence/[sourceId]` detail route with source facts, reliability, public URL, linked vector usage, inherited contacts/companies, evidence notes, and evidence decision actions.
- Added `saveEvidenceDecision` API client helper and reused the existing `/api/crm/evidence-decisions` Paperclip-backed mutation seam.
- Linked evidence cards and profile evidence lists to the evidence detail route while preserving external public-source links.
- Verification passed:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
  - `git diff --check` in `/Users/0x/code/enterlinks/enterlinks-monorepo`
  - `git diff --check` in `/Users/0x/code/ai/c0dev`
- `agent-browser-dogfood` iter-5 used direct `agent-browser` against `/crm/evidence/source%3Aatomic-crm`. It redirected to `/crm/login` and stopped at the CRM access-code gate. Report and screenshot saved under `.codex-goals/learn-crm-productization/dogfood/iter-5/`.

## 2026-05-18 08:40 PDT

- Completed E13 merge candidate detail pass.
- Added `findMergeCandidate` and `contactRowsForMergeCandidate` selectors.
- Added `/crm/merges/[mergeId]` detail route with canonical contact, duplicate contact rows, linked vectors, non-destructive transfer preview counts, tasks, notes, and merge decision actions.
- Added `saveMergeDecision` API client helper against the existing `/api/crm/merge-decisions` Paperclip-backed seam.
- Linked merge queue rows and contact merge previews to the merge detail route.
- Kept destructive apply/reconcile out of scope; accepting a merge records governance only and does not delete or rewrite imported contact records.
- Verification passed:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
  - `git diff --check` in `/Users/0x/code/enterlinks/enterlinks-monorepo`
  - `git diff --check` in `/Users/0x/code/ai/c0dev`
- `agent-browser-dogfood` iter-6 used direct `agent-browser` against `/crm/merges/merge%3Aoperator%40example.org`. It redirected to `/crm/login` and stopped at the CRM access-code gate. Report and screenshot saved under `.codex-goals/learn-crm-productization/dogfood/iter-6/`.

## 2026-05-18 08:45 PDT

- Completed E14 workloop task detail pass.
- Added `findCrmTask` selector.
- Added `/crm/workloop/[taskId]` detail route with task facts, subject backref, subject timeline, timing, subject notes, and task status actions.
- Added `TaskStatusActions` against the existing `/api/crm/tasks` mutation seam.
- Updated Workloop task cards so task titles open task detail routes and a separate `Subject` link opens the connected contact/company/vector.
- Verification passed:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
  - `git diff --check` in `/Users/0x/code/enterlinks/enterlinks-monorepo`
  - `git diff --check` in `/Users/0x/code/ai/c0dev`
- `agent-browser-dogfood` iter-7 used direct `agent-browser` against `/crm/workloop/task%3Adetail`. It redirected to `/crm/login` and stopped at the CRM access-code gate. Report and screenshot saved under `.codex-goals/learn-crm-productization/dogfood/iter-7/`.

## 2026-05-18 08:55 PDT

- Completed E15 CRM overview/home pass.
- Replaced the `/crm` root workbench entry with `CrmOverview`: compact resource cards for Contacts, Companies, Vectors, Workloop, Evidence, and Merges plus source state and next-work links.
- Reused shared Learn CRM route data so root auth/tenant behavior stays aligned with section routes.
- Made CRM sub-nav active state optional so `/crm` can show the local resource nav without falsely selecting a section.
- Added static render coverage for the CRM overview, resource labels, route links, and read-only runtime banner.
- Verification passed:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
  - `git diff --check` in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
- `agent-browser-dogfood` iter-8 used direct `agent-browser` against `/crm`. It redirected to `/crm/login` and stopped at the access-code gate. Report and screenshot saved under `.codex-goals/learn-crm-productization/dogfood/iter-8/`.

## 2026-05-18 08:59 PDT

- Completed E16 contact profile section navigation pass.
- Adapted Atomic CRM's contact/company profile tab idea into Learn-safe anchor navigation instead of adding a client tab state machine.
- Added `CrmDetailSectionNav` and wired the contact detail page to anchored sections for Overview, Identity, Work, Notes, Timeline, Evidence, and Merge.
- Added stable `id` support to `ContactProfileActions` and `CrmSubjectTimeline` so section links land on first-class CRM primitives.
- Added static render assertions for the contact detail section nav and anchor routes.
- Verification passed:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
  - `git diff --check` in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
- `agent-browser-dogfood` iter-9 used direct `agent-browser` against `/crm/contacts/contact%3Aroute-profile`. It redirected to `/crm/login` and stopped at the access-code gate. Report and screenshot saved under `.codex-goals/learn-crm-productization/dogfood/iter-9/`.

## 2026-05-18 09:04 PDT

- Completed E17 company/vector profile section navigation pass.
- Split `crm-subject-detail.tsx` into a small contact-detail barrel plus dedicated `crm-company-detail.tsx` and `crm-vector-detail.tsx` modules to keep files under the repo size guardrail.
- Extended `CrmDetailSectionNav` to company and vector drill-downs:
  - Company: Overview, Intel, Contacts, Work, Timeline, Vectors, Evidence.
  - Vector: Overview, Pathway, Contacts, Work, Timeline, Companies, Evidence.
- Added anchored section IDs so every major company/vector primitive has a stable local target.
- Added static render assertions for company/vector section navigation.
- Verification passed:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
  - `git diff --check` in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
- `agent-browser-dogfood` iter-10 used direct `agent-browser` against `/crm/companies/domain%3Asummit.co`. It redirected to `/crm/login` and stopped at the access-code gate. Report and screenshot saved under `.codex-goals/learn-crm-productization/dogfood/iter-10/`.

## 2026-05-18 09:07 PDT

- Completed E18 contact related-record navigation pass.
- Added `companyCohortForContactRow` and `opportunitiesForContact` selectors in `@enterlinks/learn-crm`.
- Added a `Related CRM records` panel to contact profiles with direct links into the mapped company profile and linked vector detail routes.
- Extended the contact profile section nav with `Related`.
- Added selector coverage and static render assertions for company/vector links from contact detail.
- Verification passed:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
  - `git diff --check` in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
- `agent-browser-dogfood` iter-11 used direct `agent-browser` against `/crm/contacts/contact%3Arelated`. It redirected to `/crm/login` and stopped at the access-code gate. Report and screenshot saved under `.codex-goals/learn-crm-productization/dogfood/iter-11/`.

## 2026-05-18 09:11 PDT

- Completed E19 contact vCard export pass.
- Added `packages/learn-crm/src/contact-export.ts` with pure helpers:
  - `exportContactVCard`
  - `contactVCardDataHref`
  - `contactVCardFilename`
- Added an `Export vCard` download affordance to the contact profile header.
- Kept export read-only and local; no Paperclip/API mutation path.
- Added tests for escaped vCard fields, `.vcf` filename generation, and exclusion of private reviewer notes.
- Verification passed:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
  - `git diff --check` in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
- `agent-browser-dogfood` iter-12 used direct `agent-browser` against `/crm/contacts/contact%3Avcard`. It redirected to `/crm/login` and stopped at the access-code gate. Report and screenshot saved under `.codex-goals/learn-crm-productization/dogfood/iter-12/`.

## 2026-05-18 09:14 PDT

- Completed E20 contact related-people pass.
- Added `packages/learn-crm/src/profile-relations.ts` with `relatedContactRowsForContactRow`, keeping the larger selector file under the 500 LOC guardrail.
- Extended the contact `Related CRM records` panel with same-domain/company related people links.
- Added a fixture peer contact in the contact detail static test and asserted the related person route link.
- Added selector coverage proving related contacts exclude the current contact.
- Verification passed:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
  - `git diff --check` in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
- `agent-browser-dogfood` iter-13 used direct `agent-browser` against `/crm/contacts/contact%3Apeer-profile`. It redirected to `/crm/login` and stopped at the access-code gate. Report and screenshot saved under `.codex-goals/learn-crm-productization/dogfood/iter-13/`.

## 2026-05-18 09:29 PDT

- Completed E21 section route identity and scoped toolbar pass.
- Authenticated local CRM dogfood using direct `agent-browser` and a deterministic non-production `el_session_crm` cookie for `learn.enterlinks.localhost:1355`.
- Fixed dogfood finding: vector detail company-domain lists were including personal domains (`gmail.com`, `hotmail.com`). `cohortsForOpportunity` now excludes `likelyDomainType === "Personal"` cohorts.
- Fixed dogfood finding: `/crm/workloop` still looked like the generic CRM workbench and showed contact queue controls. Section routes now render route-specific headers, CRM section nav uses route links, and `CrmCommandCenter` only renders for Contacts.
- Verified with direct `agent-browser`:
  - `/crm/workloop` shows `Workloop` heading and task work first.
  - `/crm/contacts` reached from the Workloop sub-nav and still shows the contact queue toolbar.
  - `/crm/evidence` shows Evidence route identity without the contact queue toolbar.
- Verification passed:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
  - `git diff --check` in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
- `agent-browser-dogfood` report and screenshots saved under `.codex-goals/learn-crm-productization/dogfood/iter-14/`.
