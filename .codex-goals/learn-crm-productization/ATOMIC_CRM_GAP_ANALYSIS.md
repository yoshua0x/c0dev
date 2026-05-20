# Atomic CRM Gap Analysis

## Source Reviewed

- `projects/enterlinks-monorepo/external-repos/atomic-crm/README.md`
- `projects/enterlinks-monorepo/external-repos/atomic-crm/package.json`
- `projects/enterlinks-monorepo/external-repos/atomic-crm/src/components/atomic-crm/root/CRM.tsx`
- `projects/enterlinks-monorepo/external-repos/atomic-crm/src/components/atomic-crm/types.ts`
- `projects/enterlinks-monorepo/external-repos/atomic-crm/supabase/schemas/01_tables.sql`
- `projects/enterlinks-monorepo/external-repos/atomic-crm/supabase/schemas/03_views.sql`
- `projects/enterlinks-monorepo/external-repos/atomic-crm/supabase/schemas/04_triggers.sql`
- `projects/enterlinks-monorepo/external-repos/atomic-crm/src/components/atomic-crm/contacts/*`
- `projects/enterlinks-monorepo/external-repos/atomic-crm/src/components/atomic-crm/companies/*`
- `projects/enterlinks-monorepo/external-repos/atomic-crm/src/components/atomic-crm/deals/*`
- `projects/enterlinks-monorepo/external-repos/atomic-crm/src/components/atomic-crm/notes/*`
- `projects/enterlinks-monorepo/external-repos/atomic-crm/src/components/atomic-crm/tasks/*`
- `projects/enterlinks-monorepo/external-repos/atomic-crm/src/components/atomic-crm/activity/*`
- `projects/enterlinks-monorepo/external-repos/atomic-crm/src/components/atomic-crm/dashboard/*`
- `projects/enterlinks-monorepo/external-repos/atomic-crm/src/components/atomic-crm/providers/commons/mergeContacts.ts`

## Architectural Translation

Atomic CRM is a conventional CRM built on React Admin resources and Supabase tables.

Learn CRM is a relationship-intelligence CRM built on:

- Next.js routes.
- `@enterlinks/learn-crm` derived workspace model.
- Paperclip plugin state for durable CRM actions.
- Tenant-scoped auth.
- Strong provenance and public-evidence boundaries.

Translation rule: adopt the user-facing primitive, not Atomic's infrastructure.

## Primitive Mapping

| Atomic primitive | Atomic source | Learn current | Productization target |
| --- | --- | --- | --- |
| Resource registry | `root/CRM.tsx` | Single client workbench with in-memory active view | Route-backed CRM resources under `/crm/*` |
| Contact index | `contacts/ContactList.tsx` | Ranked queue table, first 18 rows visible | Full contact index with real navigation, filters, saved queues, bulk actions |
| Contact profile | `contacts/ContactShow.tsx`, `ContactAside.tsx` | Side dossier rail | Dedicated contact route with sections for overview, notes, tasks, evidence, paths, merge |
| Contact filters | `contacts/ContactListFilter.tsx` | Search/filter/sort in command center | Keep compact command center, add routeable saved queues |
| Bulk selection | `ContactListContent.tsx`, `BulkTagButton.tsx` | Selected IDs, queue enrichment | Add bulk tag/export/enrichment affordances where durable APIs exist |
| Contact notes | `notes/NoteCreate.tsx`, `NotesIterator.tsx` | Internal notes API and dossier preview | Subject timeline with private/public evidence boundary |
| Contact tasks | `ContactTasksList.tsx`, `tasks/*` | Task update API, small contact preview | Task buckets on contact detail and workloop |
| Tags | `tags/*`, `BulkTagButton.tsx` | Tag add to selected contact | Tag palette, bulk tag, and profile tag management |
| Merge | `ContactMergeButton.tsx`, `mergeContacts.ts` | Merge governance decision only | Preview counts and non-destructive apply plan before canonical merge |
| Company index | `companies/CompanyList.tsx` | Domain cohort panel | Company/domain index with drill-down |
| Company profile | `companies/CompanyShow.tsx`, `CompanyAside.tsx` | Company evidence board plus cohorts | Company/domain profile with contacts, evidence, notes, vectors, source state |
| Deals/Kanban | `deals/DealListContent.tsx` | Vector board | Relationship vector board with detail surface and stage semantics |
| Deal detail | `deals/DealShow.tsx` | No vector detail | Vector detail modal/route with linked contacts/domains/evidence/next action |
| Dashboard | `dashboard/Dashboard.tsx` | CRM route is the workbench | CRM home can surface hot contacts, tasks, evidence debt, merge pressure |
| Activity log | `activity/ActivityLog.tsx`, SQL view | Activity array from plugin state | Subject timelines and global activity feed |
| Import/export | `ContactImportButton.tsx`, `useContactImport.tsx`, exporter | Runtime import fallback, no in-app import/export flow | Export current queue; import only through existing Learn-safe path |
| Configuration | `configuration` table and context | Hard-coded taxonomy/status/stages | Future settings surface, not first slice |

## Learn-Specific Non-Negotiables

- Direct-network and private provenance remain canonical; never flatten them into public claims.
- Public evidence cards require URLs.
- Exa enrichment must not receive private notes.
- Durable CRM writes go through API routes and Paperclip plugin state.
- Runtime import fallback stays read-only.
- Learn tenant auth and scoped cookies remain unchanged.

## Recommended First Slice

Ship contact drill-down before broadening every tab.

Why:

- Contacts are the central CRM object.
- Current data and API seams already support identity overrides, tags, notes, tasks, review decisions, enrichment queueing, merge status, evidence display, and relationship paths.
- Atomic CRM's contact profile pattern maps cleanly to Learn's needs.
- Browser dogfood can evaluate the main operator workflow immediately.

Minimum coherent contact slice:

- `/crm/contacts` index route or route-aware Contacts view.
- `/crm/contacts/[contactId]` profile route.
- Contact profile sections:
  - Header: name, role, company/domain, priority, review state.
  - Identity: override fields.
  - Reachability: email/phone/social counts and primary email.
  - Source authority: provenance and relationship paths.
  - Notes: private/internal/source-backed timeline.
  - Tasks: open/done/deferred/blocked with actions.
  - Evidence: linked public evidence.
  - Tags: existing tags and add tag.
  - Merge: candidate warning and link to merge governance.
- Preserve existing table and side dossier for fast scanning, but make "open" navigate to profile.

Second slice:

- Company/domain detail.
- Company profile tabs inspired by Atomic: activity, contacts, evidence/vectors.

Third slice:

- Vector detail.
- Adapt Atomic deal detail to Learn opportunity/vector detail.

## Risks

- Dynamic routes may collide with existing `app/crm/page.tsx` assumptions.
- Some current CRM state is local to `LearnCrmApp`; route-based pages need shared derivation or client route hydration.
- Contact IDs from fallback/demo snapshots must remain stable enough for tests.
- Writable state may be unavailable locally if Paperclip plugin is not running.
- Over-adapting Atomic's sales/deals language would make Learn CRM less accurate.

## Design Direction

Lean on operational CRM patterns:

- Dense index left, detail route/page on activation.
- Tabs inside detail pages only where the subject has real sections.
- Status/action rail where actions belong to the current subject.
- Keep copy terse and subject-specific.
- Prefer tables, lists, timelines, and stage boards over explanatory panels.
