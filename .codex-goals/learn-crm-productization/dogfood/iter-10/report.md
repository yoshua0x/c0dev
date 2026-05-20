# Dogfood Report: Learn CRM E17 Company and Vector Profile Sections

| Field | Value |
|-------|-------|
| **Date** | 2026-05-18 09:03 PDT |
| **App URL** | https://learn.enterlinks.localhost:1355/crm/companies/domain%3Asummit.co |
| **Session** | learn-crm-e17 |
| **Scope** | Company/vector drill-down section navigation after splitting profile modules |

## Summary

| Severity | Count |
|----------|-------|
| Critical | 0 |
| High | 0 |
| Medium | 0 |
| Low | 0 |
| **Total** | **0** |

## Coverage

- Opened the direct company-detail route with direct `agent-browser`.
- Browser redirected to `https://learn.enterlinks.localhost:1355/crm/login`.
- Initial snapshot exposed only `Learn CRM`, `Access code`, disabled `Open CRM`, and Next.js Dev Tools.
- No company or vector profile UI was reachable without the access code.
- Screenshot: `screenshots/initial-login.png`.
- Console: no JavaScript errors. Development-only React DevTools and Vercel Analytics logs appeared, plus one LCP image priority warning for the Learn logo on the login screen.

## Blocker

Authenticated CRM dogfood remains blocked by the access-code gate. This pass confirms the auth boundary, but does not validate the E17 company/vector profile section navigation in-browser.

## Issues

No product issues filed from this pass. The session did not reach the CRM surface.
