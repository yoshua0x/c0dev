# Learn CRM Audit + Hardening Goal

## Goal Command

```text
/goal Audit and harden Learn CRM end to end so CRM routes, writable actions, mobile UX, accessibility, performance-sensitive state, and Paperclip-backed mutation flows pass focused tests, full checks, and authenticated source-blind dogfood while preserving Learn auth, private/public evidence boundaries, and existing Paperclip durability semantics. Record every experiment under .codex-goals/learn-crm-audit-hardening.
```

## Objective

Audit and fix the existing Learn CRM implementation end to end. The pass is complete only when route correctness, writable actions, mobile behavior, accessibility affordances, performance-sensitive state ownership, and Paperclip-backed mutation semantics are verified with tests and authenticated dogfood evidence.

## Success Criteria

- Baseline findings in `AUDIT.md` are fixed, explicitly accepted with rationale, or blocked with evidence.
- Focused test gate passes:

```bash
pnpm test -- test/routes.test.ts test/session.test.ts test/middleware.test.ts test/learn-crm-api.test.ts test/learn-crm.test.ts test/learn-crm-detail.test.tsx test/ui-shell.test.tsx
```

- Full checks pass:

```bash
pnpm test
pnpm typecheck
pnpm lint
git diff --check
```

- Authenticated `agent-browser-dogfood` source-blind CRM loop passes with 0 critical/high issues and no more than 3 fixed medium issues.
- Dogfood covers `/crm`, contacts, one contact detail, company/domain, vector, evidence, merge candidate, workloop task, and return to contacts.
- At least one writable CRM action persists through local durable Paperclip state and survives reload, or the run stops with blocker evidence proving Paperclip state is unavailable.
- Private notes do not leak into public evidence, vCard, or exports.

## Boundaries

Allowed write scope:

- `/Users/0x/code/enterlinks/enterlinks-monorepo/apps/enterlinks-web/app/crm/**`
- `/Users/0x/code/enterlinks/enterlinks-monorepo/apps/enterlinks-web/components/crm/**`
- `/Users/0x/code/enterlinks/enterlinks-monorepo/apps/enterlinks-web/app/api/crm/**`
- `/Users/0x/code/enterlinks/enterlinks-monorepo/packages/learn-crm/src/**`
- Focused CRM, shell, route, API, and plugin tests.

Out of scope:

- Atomic CRM infrastructure port.
- Supabase or React Admin integration.
- `.env` or environment variable edits.
- Broad non-CRM shell work.
- Destructive merge apply/reconcile work unless separately approved.
- Reverting unrelated dirty files or untracked business/strategy work.

## Baseline

The previous CRM productization goal is complete and is the baseline. Current hardening starts from the read-only `council-deep n=3` audit:

- Architecture/API/Paperclip: routes and detail surfaces exist, but task detail routes can become unresolved after status changes; API validation is uneven; durable mutation roundtrip is not yet proven.
- UI/composition/performance: `LearnCrmApp` owns too much state, duplicate workspace derivation exists on CRM list routes, search/filter work is eager, and mobile/a11y details need hardening.
- Verification/dogfood: focused and full checks need rerun after fixes; authenticated source-blind browser loop must cover the full CRM route graph and at least one write action.

## Feedback Loop

- Every experiment gets a row in `EXPERIMENTS.md`.
- Chronological observations, commands, blockers, and dogfood notes go in `EXPERIMENT_NOTES.md`.
- Audit findings live in `AUDIT.md` with severity, evidence, and disposition.
- Add regression tests before or with fixes when the bug is stable enough to encode.
- Prefer one high-risk fix per iteration: test, implement, focused gate, notes.

## Fix Order

1. Task detail regression: non-open tasks must remain resolvable after status changes.
2. Writable action roundtrip: at least one note/task/review/tag or queue action persists through local Paperclip state and survives reload.
3. API validation gaps: notes, queues, evidence, merge, task, and review routes reject invalid payloads consistently.
4. CRM state/composition: reduce `LearnCrmApp` responsibility, centralize mutation helpers where practical, and keep files under repo size guidance.
5. Route/URL correctness: filters, sort, page, and selected contact are shareable or explicitly documented where intentionally local.
6. UI hardening: `aria-live` banners, form `name`/`type`/autocomplete policy, and no body-level horizontal scroll at 375px.
7. Performance sanity: avoid duplicate workspace derivation on CRM list routes and reduce avoidable client serialization/search work.
8. Authenticated dogfood loop: desktop and 375px mobile screenshots, report under `dogfood/iter-N/`.

## Stop Policy

Do not mark the native goal complete until every success criterion is satisfied. Stop and record blocker evidence if Paperclip durable state, authentication, or browser access is unavailable. Stop before destructive git or broad reconciliation work unless Yoshua explicitly approves it in-thread.

## Completion Evidence

- Focused gate: passed on 2026-05-18.
- Full checks: `pnpm test`, `pnpm typecheck`, `pnpm lint`, and `git diff --check` passed on 2026-05-18.
- Paperclip durable state: local Paperclip `local_trusted` server started at `127.0.0.1:3100`; `learn-crm` plugin installed/activated; Learn CRM import seeded.
- Writable action: internal note persisted after reload with `sourceLabel=paperclip`, `visibility=internal`, `subjectId=contact:vcf-0338:e5a25b88e0ab`.
- Dogfood: `dogfood/iter-2/report.md`, 0 critical/high issues, required route loop covered, desktop and 375px screenshots captured.
