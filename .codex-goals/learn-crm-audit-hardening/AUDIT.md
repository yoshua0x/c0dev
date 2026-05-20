# Learn CRM Audit

## Baseline Findings

| ID | Severity | Area | Finding | Evidence | Disposition |
| --- | --- | --- | --- | --- | --- |
| A1 | High | Workloop routes | Non-open tasks may stop resolving after status changes. | Read-only audit found task detail lookup tied to `followUpTasks`, while follow-up derivation filters open tasks. Marking a task `done`, `deferred`, or `blocked` can make `/crm/workloop/[taskId]` fail after refresh. | Fixed in E1; focused primitive test passed. |
| A2 | Medium | Data derivation | CRM list routes derive workspace twice. | `app/crm/crm-route-data.ts` builds route data; workspace is also reconstructed in the shell path. | Fixed in E4; route/detail shell tests passed. |
| A3 | Medium | State ownership | Client mutation and optimistic state ownership are split across app, dossier, profile action, and subject work components. | `LearnCrmApp`, contact dossier/actions, and subject actions each own overlapping mutation state. | Accepted residual: composition was reduced below repo size guidance in E6; deeper mutation-provider refactor is product expansion risk and not required for this hardening pass. |
| A4 | Medium | API validation | CRM mutation routes validate payloads unevenly. | Task route has stronger enum checks; notes and some plugin payloads accept typed values without consistent runtime rejection. | Fixed in E2; API validation suite passed. |
| A5 | Medium | Mobile UX | Mobile CRM layout needs 375px verification and possible horizontal overflow fixes. | Audit flagged table and protected-shell layout risks. | Fixed in E7; document width is 375px at 375px viewport after table grid min-width fix. |
| A6 | Medium | Accessibility | Async banners and form fields need accessibility hardening. | Banners/toasts need `aria-live`; CRM inputs need `name`, correct `type`, and autocomplete policy. | Fixed in E3; static render tests passed. |
| A7 | Medium | URL state | Filters, sort, page, and selected contact are not consistently shareable or intentionally documented as local. | Command center owns local state and only exposes limited view links. | Fixed in E5 for query/filter/sort/selected contact; route tests passed. |
| A8 | Medium | Composition | `LearnCrmApp` exceeds repo size guidance and carries too many responsibilities. | Baseline LOC was over 500 lines; Vercel composition guidance favors explicit subcomponents/providers. | Fixed in E6; `LearnCrmApp` is 495 LOC and typecheck passed. |
| A9 | High | Writable actions | Durable Paperclip mutation roundtrip is not proven for this goal. | Prior dogfood verified route/productization, but not a reload-surviving local durable write. | Fixed in E8: local Paperclip started, Learn CRM plugin installed, import seeded, browser snapshot reports `sourceLabel: paperclip`, and an internal note persisted after reload. |
| A10 | High | End-to-end verification | Authenticated source-blind dogfood loop has not run against this hardening goal. | Required route graph and write action loop need fresh artifacts under this goal. | Complete in E10: dogfood iter-2 covered the required route graph, 375px mobile, evidence detail, and durable note reload proof with 0 critical/high issues. |

## Additional Findings

| ID | Severity | Area | Finding | Evidence | Disposition |
| --- | --- | --- | --- | --- | --- |
| A11 | High | Privacy boundary | Private note bodies rendered on the evidence board under private references. | Code audit during dogfood found `CrmEvidenceBoard` displaying `note.body` for `visibility === "private"`. | Fixed in E7; private body redaction regression test passed. |

## Route/API Coverage Required

- `/crm`
- `/crm/contacts`
- `/crm/contacts/[contactId]`
- `/crm/companies`
- `/crm/companies/[companyId]`
- `/crm/vectors`
- `/crm/vectors/[vectorId]`
- `/crm/evidence`
- `/crm/evidence/[evidenceId]`
- `/crm/merges`
- `/crm/merges/[mergeId]`
- `/crm/workloop`
- `/crm/workloop/[taskId]`
- `/api/crm/notes`
- `/api/crm/queues`
- `/api/crm/evidence`
- `/api/crm/merges`
- `/api/crm/tasks`
- `/api/crm/reviews`
- Any tag or override routes used by the writable action proof.

## Dogfood Requirements

- Use `agent-browser-dogfood` and direct `agent-browser` only.
- Save reports and screenshots under `.codex-goals/learn-crm-audit-hardening/dogfood/iter-N/`.
- Cover desktop and 375px mobile for `/crm/contacts`.
- Source-blind operator loop:
  - open `/crm`;
  - inspect contacts;
  - open one contact detail;
  - perform one durable writable action when available;
  - reload and confirm persistence;
  - inspect company/domain, vector, evidence, merge candidate, workloop task;
  - return to contacts.

## Privacy Boundary Checks

- Private CRM notes must not appear in public evidence views.
- Private CRM notes must not appear in vCard exports.
- Private CRM notes must not appear in any public export path.
