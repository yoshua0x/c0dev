# Experiments

| ID | Status | Hypothesis | Slice | Fast score | Dogfood score | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| E0 | complete | Cleaning the Learn shell and moving CRM views into a local sub-nav reduces chrome confusion. | Shell cleanup | Pass: routes, learn-crm, ui-shell tests; typecheck; lint | Manual browser verified dashboard and CRM | Keep as baseline. |
| E1 | complete | A contact detail route will reduce single-page overload more than more cards inside the current workbench. | `/crm/contacts/[contactId]` | Pass: selector tests, route tests, contact detail static render, typecheck, lint | Blocked at auth gate; no access code available | Implemented contact profile route with identity, source authority, evidence, tasks, notes, merge status. |
| E2 | complete | Company/domain profiles should make the Companies tab feel like a CRM, not a summary widget. | Company/domain index and detail | Pass: cohort lookup and company detail static render | Blocked at auth gate; no access code available | Implemented `/crm/companies` and `/crm/companies/[companyId]`. |
| E3 | complete | Vector detail adapted from Atomic deals will make relationship pathways actionable without sales-pipeline language. | Vector board detail | Pass: opportunity lookup and vector detail static render | Blocked at auth gate; no access code available | Implemented `/crm/vectors` and `/crm/vectors/[vectorId]`; no drag/stage mutation added. |
| E4 | complete | Workloop task buckets will make open CRM work easier to scan than a mixed activity panel. | Workloop buckets and task update | Pass: task bucket selector tests, activity feed static render, typecheck, lint | Blocked at auth gate; no access code available | Added due-pressure/high-priority buckets and subject links from workloop tasks. |
| E5 | complete | Atomic-style merge preview counts will improve trust before canonical merge decisions. | Merge governance | Pass: merge summary selector tests, typecheck, lint | Blocked at auth gate; no access code available | Added non-destructive counts for records, notes, tasks, vectors, emails, phones, and tags. |
| E6 | complete | Exporting the filtered queue is a low-risk utility once contacts have stable index semantics. | Contact export | Pass: CSV transform tests, focused test subset, typecheck, lint | Blocked at auth gate; no access code available | Added command-center export for the current filtered/sorted contact view. Import remains later unless Learn-safe path is clear. |
| E7 | complete | Removing the 18-row cap will make Contacts behave like a CRM index instead of a queue preview. | Contact index pagination | Pass: pagination static render test, focused test subset, typecheck, lint | Blocked at auth gate; no access code available | Added paged contact table with visible-range counts and next/previous controls. |
| E8 | complete | Contact drill-down should be actionable, not only inspectable, if it is going to replace the overloaded dossier rail. | Contact profile action stack | Pass: contact action static render test, focused CRM/UI suite, typecheck, lint, diff checks | Blocked at auth gate; no access code available | Added route-level client actions for review state, enrichment queue, identity override, tags, internal notes, and task status transitions using existing CRM API seams. |
| E9 | complete | Company/domain and vector detail pages should expose first-class CRM work primitives, not only linked read-only context. | Non-contact subject work actions | Pass: subject action static render test, focused CRM/UI suite, typecheck, lint, diff check | Blocked at auth gate; report saved to dogfood/iter-2 | Added generic subject actions for domain/company and opportunity detail: review state, task creation, task transitions, internal notes, and enrichment where supported. |
| E10 | complete | Drill-down profiles need a subject-specific audit trail so notes, tasks, decisions, enrichment, and activity are first-class history, not scattered panels. | Subject timeline | Pass: timeline selector test, detail render assertions, focused CRM/UI suite, typecheck, lint, diff checks | Blocked at auth gate; report saved to dogfood/iter-3 | Added `timelineForSubject` and `CrmSubjectTimeline`; wired timeline panels into contact, company/domain, and vector detail pages. |
| E11 | complete | Saved queues should become shareable CRM views rather than only local button state. | Routeable contact queues | Pass: queue param/link static render test, focused CRM/UI suite, typecheck, lint, diff checks | Blocked at auth gate; report saved to dogfood/iter-4 | Added contact queue search-param parsing, current-view links, saved queue hrefs, and contact page hydration from `/crm/contacts?query=&filter=&sort=`. |
| E12 | complete | Public evidence should be inspectable and governable as its own CRM resource, not only embedded cards. | Evidence source detail | Pass: evidence selector/detail render tests, focused CRM/UI suite, typecheck, lint, diff checks | Blocked at auth gate; report saved to dogfood/iter-5 | Added `/crm/evidence/[sourceId]`, evidence usage selectors, evidence detail page, evidence decision actions, and links from evidence cards/profile evidence lists. |
| E13 | complete | Merge governance should have an inspectable candidate detail route before any destructive reconcile workflow exists. | Merge candidate detail | Pass: merge selector/detail render tests, focused CRM/UI suite, typecheck, lint, diff checks | Blocked at auth gate; report saved to dogfood/iter-6 | Added `/crm/merges/[mergeId]`, merge contact-row selector, merge detail page, merge decision actions, and links from merge queue/contact merge previews. |
| E14 | complete | Workloop tasks should be durable CRM records with their own detail surface, subject backrefs, and status actions. | Workloop task detail | Pass: task selector/detail render tests, focused CRM/UI suite, typecheck, lint, diff checks | Blocked at auth gate; report saved to dogfood/iter-7 | Added `/crm/workloop/[taskId]`, `findCrmTask`, task detail page, task status actions, and Workloop links from task cards to task records plus subject backrefs. |
| E15 | complete | `/crm` should orient operators across CRM resources instead of opening another overloaded workbench entry point. | CRM overview/home | Pass: overview static render test, focused CRM/UI suite, typecheck, lint, diff checks | Blocked at auth gate; report saved to dogfood/iter-8 | Added `CrmOverview`, changed `/crm` to a resource home, kept section routes as workbench entries, and left CRM sub-nav routeable with no active root tab. |
| E16 | complete | Contact profiles need an Atomic-style local section map so drill-down work does not become another long, overloaded page. | Contact profile section navigation | Pass: contact detail static render assertions, focused CRM/UI suite, typecheck, lint, diff checks | Blocked at auth gate; report saved to dogfood/iter-9 | Added `CrmDetailSectionNav`, anchored the contact profile sections, and exposed stable section targets for overview, identity, work, notes, timeline, evidence, and merge. |
| E17 | complete | Company and vector drill-downs need the same local profile section map as contacts to keep the CRM resource model coherent. | Company/vector section navigation | Pass: company/vector static render assertions, focused CRM/UI suite, typecheck, lint, diff checks | Blocked at auth gate; report saved to dogfood/iter-10 | Split company/vector detail views into dedicated modules under 500 LOC, then added anchored section nav for company intelligence, linked contacts, work, timeline, vectors/companies, and evidence. |
| E18 | complete | Contact profiles should expose related company and vector records directly, matching Atomic's record-to-record CRM navigation. | Contact related records | Pass: related selector tests, contact static render assertions, focused CRM/UI suite, typecheck, lint, diff checks | Blocked at auth gate; report saved to dogfood/iter-11 | Added `companyCohortForContactRow` and `opportunitiesForContact`, plus a Related CRM records panel linking contact profiles to company and vector detail routes. |
| E19 | complete | Contact drill-downs should support Atomic-style vCard export without leaking private notes or requiring a mutation. | Contact vCard export | Pass: vCard export tests, contact static render assertions, focused CRM/UI suite, typecheck, lint, diff checks | Blocked at auth gate; report saved to dogfood/iter-12 | Added pure vCard export helpers and a contact-profile `Export vCard` download link; escaped vCard fields and kept private reviewer notes out of the file. |
| E20 | complete | Contact profiles should surface adjacent people at the same company/domain so operators can traverse the relationship graph from one profile. | Contact related people | Pass: related people selector tests, contact static render assertions, focused CRM/UI suite, typecheck, lint, diff checks | Blocked at auth gate; report saved to dogfood/iter-13 | Added `relatedContactRowsForContactRow` in a separate profile-relations module and rendered related people links inside the contact profile related-records panel. |
| E21 | complete | Section routes need route-specific identity and scoped controls so the CRM stops feeling like one generic workbench. | Section route identity and scoped toolbar | Pass: route header static render test, focused CRM/UI suite, typecheck, lint, diff checks | Authenticated dogfood passed; 2 medium findings fixed and re-verified in dogfood/iter-14 | Added CRM route-specific headers, converted CRM section nav to links, kept the contact command center on Contacts only, and filtered personal domains out of vector company-domain lists. |

## Scoring

Fast score:

- Unit/static render tests pass.
- Typecheck pass.
- Lint pass.
- No API or auth regression.

Dogfood score:

- 0 blocking issues: cannot reach CRM, route crash, auth loop, blank page.
- 0 severe workflow issues: cannot open contact, cannot return, actions misleading, private/public evidence confused.
- At most 3 moderate issues per slice before next implementation iteration.
- Findings saved under `.codex-goals/learn-crm-productization/dogfood/iter-N/`.

## Browser Dogfood Tasks

Use `agent-browser-dogfood` after a slice is implemented. The dogfood pass should be source-blind: inspect the running app, not source files.

Target URL:

```text
https://learn.enterlinks.localhost:1355/crm
```

Core scenarios:

1. Open CRM and orient within 5 seconds.
2. Find a high-priority contact.
3. Drill into the contact profile.
4. Identify source authority and public evidence.
5. Add or attempt note/tag/task action, respecting read-only state if present.
6. Navigate to company/domain or vector detail.
7. Review a merge candidate.
8. Return to contact queue without losing filters/orientation.

Artifacts:

- Screenshot of CRM index.
- Screenshot of contact detail.
- Screenshot of company/domain or vector detail.
- Short findings report with severity, reproduction path, expected, actual, and likely fix area.
