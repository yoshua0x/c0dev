# Learn CRM Productization Goal

## Objective

Turn the Learn CRM from a dense single-page review workbench into a proper, drillable CRM:

- Contacts open into durable profile/dossier surfaces.
- Companies and domains open into real organization profiles.
- Portfolio vectors behave like opportunity lanes with inspectable cards.
- Tasks, notes, tags, merge governance, evidence, and enrichment remain first-class CRM primitives.
- The implementation adapts useful Atomic CRM primitives without porting Atomic CRM's React Admin, Supabase, or schema wholesale.

Do not start Codex goal mode automatically from this plan. Use the prompt at the end when ready to start a focused goal run.

## Current Baseline

- Execution note: this goal pack is stored in c0dev, but the active Learn CRM
  source for this run is `/Users/0x/code/enterlinks/enterlinks-monorepo`.
  The c0dev `projects/enterlinks-monorepo` checkout did not contain the Learn
  CRM files at execution time.
- Learn CRM already has a strong domain model in `projects/enterlinks-monorepo/apps/enterlinks-web/packages/learn-crm/src/types.ts`:
  contacts, domains, companies, owners, tags, overrides, queues, notes, tasks, opportunities, relationship paths, merge candidates, evidence, scores, review decisions, enrichment jobs, and activity.
- Durable mutation seams already exist through `projects/enterlinks-monorepo/apps/enterlinks-web/app/api/crm/*` and `projects/enterlinks-monorepo/services/paperclip-plugins/plugin-learn-crm/src/state.ts`.
- The UI is still mostly a single workbench in `projects/enterlinks-monorepo/apps/enterlinks-web/components/crm/learn-crm-app.tsx`.
- Contacts are ranked and selectable, but not navigable as durable entities.
- The dossier side rail is useful but overloaded: review actions, identity overrides, tags, paths, evidence, notes, and tasks compete in one column.
- Company, vector, merge, evidence, and workloop views are summaries, not drill-down workflows.
- The previous shell cleanup pass removed duplicated Learn branding and introduced a CRM-local sub-nav. Keep that foundation.

## Atomic CRM Source Findings

Atomic CRM source lives in `projects/enterlinks-monorepo/external-repos/atomic-crm`.

Useful primitives to adapt:

1. Resource registry and route model
   - `src/components/atomic-crm/root/CRM.tsx`
   - Registers `deals`, `contacts`, `companies`, `contact_notes`, `deal_notes`, `tasks`, `sales`, `tags`.
   - Desktop and mobile have distinct route/layout behavior.

2. Contact list as a real resource index
   - `contacts/ContactList.tsx`
   - Search, filters, sort, import, export, create, bulk select, bulk tag, bulk export, bulk delete.

3. Contact profile
   - `contacts/ContactShow.tsx`
   - Primary content: identity header and notes stream.
   - Aside: status, personal info, background, tags, tasks, merge, vCard export, delete.
   - Mobile: profile tabs for notes, tasks, details.

4. Company profile
   - `companies/CompanyShow.tsx`
   - Tabs: activity, contacts, deals.
   - Aside: contact links, address, context, additional info.

5. Deal/opportunity board
   - `deals/DealListContent.tsx`, `DealColumn.tsx`, `DealCard.tsx`, `DealShow.tsx`
   - Kanban stages with drag/order persistence and modal detail.
   - For Learn, adapt to portfolio relationship vectors, not sales deals.

6. Tasks by due pressure
   - `tasks/TasksListByDueDate.tsx`
   - Buckets: overdue, today, tomorrow, this week, later.

7. Notes as activity substrate
   - `notes/NoteCreate.tsx`, `notes/NotesIterator.tsx`
   - Inline note creation, status propagation, last-seen update, attachments.
   - For Learn, keep private/public evidence boundaries.

8. Merge workflow
   - `contacts/ContactMergeButton.tsx`
   - Shows canonical winner, candidates, counts of tasks/notes/deals/emails/phones transferred, and explicit warning.
   - `providers/commons/mergeContacts.ts`
   - Reassigns tasks/notes/deals, merges emails/phones/tags, deletes loser.
   - For Learn, keep governance-first review before destructive apply.

9. Import/export workflow
   - `contacts/ContactImportButton.tsx`, `contacts/useContactImport.tsx`
   - CSV import batches contacts while creating/cacheing companies and tags.
   - Contact export joins company, owner, tags, emails, and phones.

10. Activity summary views
    - `supabase/schemas/03_views.sql`
    - `activity_log`, `contacts_summary`, `companies_summary`.
    - For Learn, derive equivalent summary selectors in `@enterlinks/learn-crm` instead of adding a new DB.

## Product Gaps

High-priority gaps:

- No `/crm/contacts/:id` durable detail route.
- No contact drill-down shell with clear sections for overview, notes, tasks, evidence, paths, merge, and provenance.
- Contact table caps visible rows and behaves like a queue preview rather than a full index.
- Companies view is a domain cohort list, not an inspectable company/domain profile.
- Vectors board has no drill-down card detail, stage action, or linked contacts/evidence workflow.
- Workloop is a summary panel; it lacks create task, due buckets, assignee/owner surface, and contact/company backrefs.
- Merge panel records decisions but does not provide a complete apply/reconcile workflow.
- Import/export primitives are not exposed in the CRM UI.
- Saved queues exist but are not promoted into routeable CRM views.
- Dossier still hides too much behind one side rail, especially notes/tasks/evidence.

Medium-priority gaps:

- No configuration surface for statuses, vector stages, task categories, tag palette, or review queues.
- No mobile-specific CRM drill-down behavior.
- Evidence and notes are visually separated, but not fully tied to contact/company/vector detail routes.
- Activity is append-only and useful, but not yet a full timeline per subject.

## Goal Shape

Target one focused productization pass, not a full CRM rewrite.

Quantitative success criteria:

- Map at least 10 Atomic CRM primitives into Learn-specific equivalents in `ATOMIC_CRM_GAP_ANALYSIS.md`.
- Implement at least 3 drill-down surfaces:
  - Contact detail.
  - Company/domain detail.
  - Vector/opportunity detail or task/workloop detail.
- Preserve current CRM API behavior and Paperclip plugin state boundaries.
- Keep Learn CRM route IA clear:
  - `/crm` as CRM home/workbench.
  - `/crm/contacts`
  - `/crm/contacts/[contactId]`
  - `/crm/companies` or `/crm/domains`
  - `/crm/companies/[companyId]` or `/crm/domains/[domainId]`
  - `/crm/vectors`
  - `/crm/vectors/[vectorId]`
- Add or update focused tests for route rendering, selectors/derivations, and at least one drill-down happy path.
- Run at least one source-blind `agent-browser-dogfood` pass against `https://learn.enterlinks.localhost:1355/crm` after implementation.

Quality bar:

- Data-dense, restrained, operational CRM.
- No marketing hero, no duplicate brand copy, no decorative cards inside cards.
- Clear hierarchy: global shell, CRM sub-nav, local route identity, local actions.
- Every contact/company/vector detail page must make the next action obvious within 5 seconds.
- Private notes and public evidence boundaries remain explicit.

## Scope

Likely write surfaces:

- `projects/enterlinks-monorepo/apps/enterlinks-web/app/crm/**`
- `projects/enterlinks-monorepo/apps/enterlinks-web/components/crm/**`
- `projects/enterlinks-monorepo/apps/enterlinks-web/packages/learn-crm/src/**`
- `projects/enterlinks-monorepo/apps/enterlinks-web/app/api/crm/**`
- `projects/enterlinks-monorepo/apps/enterlinks-web/test/**`

Possible Paperclip plugin writes only if a UI action needs durable state:

- `projects/enterlinks-monorepo/services/paperclip-plugins/plugin-learn-crm/src/**`
- `projects/enterlinks-monorepo/services/paperclip-plugins/plugin-learn-crm/tests/**`

Out of scope unless explicitly approved:

- Porting Atomic CRM wholesale.
- Adding Supabase or React Admin.
- Editing `.env` files.
- Changing tenant/auth behavior outside Learn CRM.
- Replacing Paperclip state as the durable CRM target.
- Broad ForHumanity/platform shell changes.

## Proposed Implementation Phases

### Phase 1: Route IA and Selectors

- Add subject lookup helpers in `@enterlinks/learn-crm`:
  - `contactById`
  - `domainById`
  - `companyById`
  - `opportunityById`
  - subject notes/tasks/evidence/activity selectors.
- Convert current in-page view state into route-aware CRM sections where practical.
- Keep the existing sub-nav but let it reflect active route.

### Phase 2: Contact Drill-Down

- Add a dedicated contact detail route.
- Split current dossier rail into a full profile layout:
  - Identity and reachability.
  - Source authority and relationship paths.
  - Notes timeline.
  - Tasks by status/due pressure.
  - Evidence cards.
  - Tags and identity overrides.
  - Merge candidate status.
- Keep the table as a fast index, but make row activation a real navigation option.

### Phase 3: Company/Domain Drill-Down

- Promote domain cohorts into inspectable company/domain profiles.
- Show linked contacts, source-backed company evidence, notes, and vector relevance.
- Preserve distinction between imported domain, enriched company, and reviewed company.

### Phase 4: Vectors and Workloop

- Give vector cards a detail surface with linked contacts/domains/evidence and next actions.
- Adapt Atomic deal-stage thinking into relationship pathway stages.
- Add task creation or task routing if existing API seams support it cleanly.
- Bucket workloop tasks by overdue/today/week/later where due dates exist, otherwise by priority/reason.

### Phase 5: Merge, Import, Export

- Improve merge governance with Atomic-style preview counts.
- Keep destructive apply gated behind explicit reviewer decision and durable state support.
- Add export affordance for current contact queue if low risk.
- Add import affordance only if it can call the existing Learn CRM import/runtime path without changing env.

### Phase 6: Dogfood Loop

Run source-blind browser testing after each meaningful UI slice:

- Start from `https://learn.enterlinks.localhost:1355/crm`.
- Do not read app source during the dogfood pass.
- Navigate like a CRM operator:
  - Find a high-priority contact.
  - Drill into contact details.
  - Add/review note/task/tag where writable.
  - Inspect company/domain profile.
  - Inspect vector evidence.
  - Review merge candidate.
  - Return to queue without losing orientation.
- Save findings to `.codex-goals/learn-crm-productization/dogfood/iter-N/`.

## Verification Plan

Focused checks from `projects/enterlinks-monorepo/apps/enterlinks-web`:

```bash
pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/ui-shell.test.tsx
pnpm typecheck
pnpm lint
```

Add tests as implementation requires:

- CRM route rendering tests.
- Contact/company/vector selector tests.
- Component static-render tests for new detail surfaces.
- Plugin state tests if mutating `plugin-learn-crm`.

Browser verification:

- All browser QA must use the `agent-browser-dogfood` skill and direct `agent-browser` workflow.
- Do not substitute Chrome DevTools, Computer Use, or in-app Browser for dogfood unless Yoshua explicitly approves a fallback for a blocked session.
- `https://learn.enterlinks.localhost:1355/crm`
- `https://learn.enterlinks.localhost:1355/crm/contacts`
- `https://learn.enterlinks.localhost:1355/crm/contacts/<known-contact-id>`
- Company/domain and vector detail routes added by the implementation.

Latest verification:

- `pnpm test -- test/learn-crm.test.ts test/learn-crm-detail.test.tsx`
  - 21 files passed, 106 tests passed.
- `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - 21 files passed, 107 tests passed.
- `pnpm typecheck`
  - Passed.
- `pnpm lint`
  - Passed.
- `agent-browser-dogfood`
  - Blocked at `/crm/login` because no CRM access code was available. Evidence:
    `.codex-goals/learn-crm-productization/dogfood/iter-1/report.md`.
- E4 and E5 follow-up verification:
  - `pnpm test -- test/learn-crm.test.ts test/learn-crm-detail.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
- E6 follow-up verification:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
  - `pnpm typecheck`
  - `pnpm lint`
- E7 follow-up verification:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
  - `pnpm typecheck`
  - `pnpm lint`
- E8 follow-up verification:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
    - 21 files passed, 109 tests passed.
  - `pnpm typecheck`
    - Passed.
  - `pnpm lint`
    - Passed.
  - `git diff --check`
    - Passed in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
- E9 follow-up verification:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
    - 21 files passed, 110 tests passed.
  - `pnpm typecheck`
    - Passed.
  - `pnpm lint`
    - Passed.
  - `git diff --check`
    - Passed in `/Users/0x/code/enterlinks/enterlinks-monorepo`.
  - `agent-browser-dogfood`
    - Direct `agent-browser` session reached `/crm/login` and blocked at access-code gate. Evidence:
      `.codex-goals/learn-crm-productization/dogfood/iter-2/report.md`.
- E10 follow-up verification:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
    - 21 files passed, 111 tests passed.
  - `pnpm typecheck`
    - Passed after updating CRM dynamic detail routes to await Next route params.
  - `pnpm lint`
    - Passed.
  - `git diff --check`
    - Passed in `/Users/0x/code/enterlinks/enterlinks-monorepo`.
  - `agent-browser-dogfood`
    - Direct `agent-browser` session reached `/crm/login` and blocked at access-code gate. Evidence:
      `.codex-goals/learn-crm-productization/dogfood/iter-3/report.md`.
- E11 follow-up verification:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
    - 21 files passed, 112 tests passed.
  - `pnpm typecheck`
    - Passed.
  - `pnpm lint`
    - Passed.
  - `git diff --check`
    - Passed in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
  - `agent-browser-dogfood`
    - Direct `agent-browser` session attempted `/crm/contacts?query=capital&filter=work&sort=name`, redirected to `/crm/login`, and blocked at access-code gate. Evidence:
      `.codex-goals/learn-crm-productization/dogfood/iter-4/report.md`.
- E12 follow-up verification:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
    - 21 files passed, 114 tests passed.
  - `pnpm typecheck`
    - Passed.
  - `pnpm lint`
    - Passed.
  - `git diff --check`
    - Passed in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
  - `agent-browser-dogfood`
    - Direct `agent-browser` session attempted `/crm/evidence/source%3Aatomic-crm`, redirected to `/crm/login`, and blocked at access-code gate. Evidence:
      `.codex-goals/learn-crm-productization/dogfood/iter-5/report.md`.
- E13 follow-up verification:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
    - 21 files passed, 115 tests passed.
  - `pnpm typecheck`
    - Passed.
  - `pnpm lint`
    - Passed.
  - `git diff --check`
    - Passed in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
  - `agent-browser-dogfood`
    - Direct `agent-browser` session attempted `/crm/merges/merge%3Aoperator%40example.org`, redirected to `/crm/login`, and blocked at access-code gate. Evidence:
      `.codex-goals/learn-crm-productization/dogfood/iter-6/report.md`.
- E14 follow-up verification:
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
    - 21 files passed, 117 tests passed.
  - `pnpm typecheck`
    - Passed.
  - `pnpm lint`
    - Passed.
  - `git diff --check`
    - Passed in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
  - `agent-browser-dogfood`
    - Direct `agent-browser` session attempted `/crm/workloop/task%3Adetail`, redirected to `/crm/login`, and blocked at access-code gate. Evidence:
      `.codex-goals/learn-crm-productization/dogfood/iter-7/report.md`.
- E15 follow-up verification:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
    - 21 files passed, 118 tests passed.
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
    - 21 files passed, 118 tests passed.
  - `pnpm typecheck`
    - Passed.
  - `pnpm lint`
    - Passed.
  - `git diff --check`
    - Passed in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
  - `agent-browser-dogfood`
    - Direct `agent-browser` session attempted `/crm`, redirected to `/crm/login`, and blocked at access-code gate. Evidence:
      `.codex-goals/learn-crm-productization/dogfood/iter-8/report.md`.
- E16 follow-up verification:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
    - 21 files passed, 118 tests passed.
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
    - 21 files passed, 118 tests passed.
  - `pnpm typecheck`
    - Passed.
  - `pnpm lint`
    - Passed.
  - `git diff --check`
    - Passed in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
  - `agent-browser-dogfood`
    - Direct `agent-browser` session attempted `/crm/contacts/contact%3Aroute-profile`, redirected to `/crm/login`, and blocked at access-code gate. Evidence:
      `.codex-goals/learn-crm-productization/dogfood/iter-9/report.md`.
- E17 follow-up verification:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
    - 21 files passed, 118 tests passed.
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
    - 21 files passed, 118 tests passed.
  - `pnpm typecheck`
    - Passed.
  - `pnpm lint`
    - Passed.
  - `git diff --check`
    - Passed in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
  - `agent-browser-dogfood`
    - Direct `agent-browser` session attempted `/crm/companies/domain%3Asummit.co`, redirected to `/crm/login`, and blocked at access-code gate. Evidence:
      `.codex-goals/learn-crm-productization/dogfood/iter-10/report.md`.
- E18 follow-up verification:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
    - 21 files passed, 119 tests passed.
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
    - 21 files passed, 119 tests passed.
  - `pnpm typecheck`
    - Passed.
  - `pnpm lint`
    - Passed.
  - `git diff --check`
    - Passed in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
  - `agent-browser-dogfood`
    - Direct `agent-browser` session attempted `/crm/contacts/contact%3Arelated`, redirected to `/crm/login`, and blocked at access-code gate. Evidence:
      `.codex-goals/learn-crm-productization/dogfood/iter-11/report.md`.
- E19 follow-up verification:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
    - 21 files passed, 120 tests passed.
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
    - 21 files passed, 120 tests passed.
  - `pnpm typecheck`
    - Passed.
  - `pnpm lint`
    - Passed.
  - `git diff --check`
    - Passed in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
  - `agent-browser-dogfood`
    - Direct `agent-browser` session attempted `/crm/contacts/contact%3Avcard`, redirected to `/crm/login`, and blocked at access-code gate. Evidence:
      `.codex-goals/learn-crm-productization/dogfood/iter-12/report.md`.
- E20 follow-up verification:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
    - 21 files passed, 121 tests passed.
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
    - 21 files passed, 121 tests passed.
  - `pnpm typecheck`
    - Passed.
  - `pnpm lint`
    - Passed.
  - `git diff --check`
    - Passed in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
  - `agent-browser-dogfood`
    - Direct `agent-browser` session attempted `/crm/contacts/contact%3Apeer-profile`, redirected to `/crm/login`, and blocked at access-code gate. Evidence:
      `.codex-goals/learn-crm-productization/dogfood/iter-13/report.md`.
- E21 follow-up verification:
  - `pnpm test -- test/learn-crm-detail.test.tsx test/learn-crm.test.ts`
    - 21 files passed, 123 tests passed.
  - `pnpm test -- test/routes.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx`
    - 21 files passed, 123 tests passed.
  - `pnpm typecheck`
    - Passed.
  - `pnpm lint`
    - Passed.
  - `git diff --check`
    - Passed in `/Users/0x/code/enterlinks/enterlinks-monorepo` and `/Users/0x/code/ai/c0dev`.
  - `agent-browser-dogfood`
    - Direct authenticated `agent-browser` session verified `/crm/workloop`, `/crm/contacts`, and `/crm/evidence`.
    - Fixed and re-verified two medium dogfood findings: personal domains in vector company-domain lists, and generic Workloop route identity/contact toolbar bleed.
    - Evidence:
      `.codex-goals/learn-crm-productization/dogfood/iter-14/report.md`.

## Stop Conditions

Stop and report if:

- Durable Paperclip state is unavailable for a workflow that must mutate production CRM data.
- Auth/session blocks local browser testing.
- A proposed primitive requires env changes.
- Atomic CRM behavior conflicts with Learn private evidence/provenance rules.
- Scope expands into a backend rewrite instead of a focused productization pass.

## Ready Goal Prompt

Use this prompt when starting the goal run:

```text
Goal: Productize the Learn CRM into a proper drill-down CRM, using projects/enterlinks-monorepo/external-repos/atomic-crm as the reference primitive library and preserving Learn/Paperclip guardrails.

Read .codex-goals/learn-crm-productization/PLAN.md, EXPERIMENTS.md, EXPERIMENT_NOTES.md, and ATOMIC_CRM_GAP_ANALYSIS.md first. Then implement the smallest coherent productization slice that creates real CRM drill-down behavior: contacts first, then company/domain or vector detail if time allows.

Constraints:
- Do not port Atomic CRM wholesale.
- Do not add Supabase or React Admin.
- Do not edit .env files.
- Preserve Learn CRM API/Paperclip durability semantics.
- Add tests for selectors/routes/components touched.
- Use agent-browser-dogfood as the iterative source-blind browser QA loop after implementation.

Verify with focused tests, typecheck, lint, and browser dogfood against https://learn.enterlinks.localhost:1355/crm.
```
