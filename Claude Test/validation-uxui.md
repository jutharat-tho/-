# Validation & Sign-off — Leasing Design BO

**Last updated:** 2026-05-23
**Owner:** UX/UI
**Related:** [`requirement.md`](./requirement.md) · [`UXUI_DESIGN.md`](./UXUI_DESIGN.md) · [`figma-links.md`](./figma-links.md)

> Use this file to capture design-review decisions, approvals, and what changed and why. **One row per decision** — keep entries short and link to the affected screen / spec section.

---

## 1. Approver matrix

| Role | Name | Required for sign-off | Status |
|---|---|---|---|
| BA | TBD | ✅ Yes | not assigned |
| PM | TBD | ✅ Yes | not assigned |
| Frontend Dev Lead | TBD | ✅ Yes | not assigned |
| UX/UI | (session) | ✅ Yes (author) | active |

---

## 2. Review status — per feature

| Feature | Status | BA | PM | FE Lead | Next action |
|---|---|---|---|---|---|
| F-01 Login (Temp Password) | `draft` | — | — | — | Schedule walkthrough |
| F-02 Login (Password Expire) | `draft` | — | — | — | Schedule walkthrough |
| F-03 Set New Password | `draft` | — | — | — | Schedule walkthrough |
| F-04 Create User | `draft` | — | — | — | Schedule walkthrough |
| F-05 Edit User | `draft` | — | — | — | Schedule walkthrough |
| F-06 Reset Password | `draft` | — | — | — | Schedule walkthrough |
| F-08 Auto Logout | `draft` | — | — | — | Decide silent vs. warning toast |
| F-09 Product Category | `draft` | — | — | — | **BRD missing** — confirm R-CAT-01..04 with BA |
| F-10 Subcategories | `draft` | — | — | — | **BRD missing** — confirm R-SUB-01..03 + FK relationship |
| F-11 Product Group | `draft` | — | — | — | **BRD missing** — most complex, 3 nested tables |
| F-12 Product Model | `draft` | — | — | — | **BRD missing** — confirm R-MOD-01..02 + FK to Group |
| F-13 Partner Type | `draft` | — | — | — | **BRD missing** — confirm R-PTY-01..02 |
| F-14 Partner Information | `draft` | — | — | — | **BRD missing** — confirm 2-variant form behavior |

Status values: `draft` → `in_review` → `approved` → `final`.

---

## 3. Open issues / pending decisions

| # | Issue | Owner | Target | Status |
|---|---|---|---|---|
| I-01 | Confirm exact wording for "Password expire" and "Password ซ้ำ" dialogs | BA | — | **partially resolved 2026-05-25** — SA defined error codes (`2012` matches-temp, `2001` invalid creds, `2002` temp expired). Wording-final still pending BA, but trigger/behavior is locked. |
| I-02 | Decide responsive scope (BO at < 1440px?) | PM + FE Lead | — | open |
| I-03 | Decide auto-logout UX (silent vs. 60s warning toast) | PM + BA | — | open |
| I-04 | Design empty / loading / disabled states for all forms | UX/UI | — | open |
| I-05 | Confirm Reset Password kills active sessions (backend behavior) | FE Lead | — | **resolved 2026-05-25** — Yes per SA R-SES-04 single-session enforcement: any new login (incl. after Reset → re-login with new temp) kicks all prior refresh tokens. Old access tokens die within 15 min naturally. |
| I-09 | Role naming convention drift — Figma user-matrix uses "Super Admin / Admin User / Admin Content / Marketing / Support / Credit Support / Credit Analyst / Contract / Credit Disbursement / Verify / Accounting" (11 roles). SA spec uses snake_case codes "super_admin, sale_mng, marketing, support, credit_ops, analyst, contract, verify, account" (9 roles). | BA + FE Lead | — | open (new 2026-05-25) |
| I-10 | Confirm if `POST /auth/refresh-token` flow needs any UI feedback (current SA spec is silent). Decision: silent rotation in background, no toast/loading visible to user. | FE Lead | — | open (new 2026-05-25) |
| I-11 | **Entire Settings module (F-09..F-14) has no BRD.** Rules R-CAT/R-SUB/R-PG/R-MOD/R-PTY/R-PTN reverse-engineered from Figma. Cover: field requireds, FK relationships (Category→Subcategory→Group→Model), code-gen policy, Partner entity-type lock after create. | BA + PM | — | **blocker** (new 2026-06-05) — cannot start sprint without |
| I-12 | Settings module — Save button uses **green** (Secondary/Basic `#019267`) — different from Auth module's brand-red. Is this intentional? Should we propagate to all "destructive vs. constructive" actions across BO (green=save/confirm, red=delete/cancel)? | UX/UI + Design system owner | — | open (new 2026-06-05) |
| I-13 | Settings module — modal radius bumped from `radius-200` (8px) to `radius-400` (16px) for these large modals (1212px wide). Should this be a new size token (e.g. `dialog/large`)? | UX/UI | — | open (new 2026-06-05) |
| I-06 | Define focus-ring token + apply across components | UX/UI | — | open |
| I-07 | EN copy for all error/dialog strings | BA | — | open |
| I-08 | Audit logging UI for admin actions (next release?) | PM | — | deferred |

---

## 4. Decisions log

| Date | Decision | Reason | Affects | Approved by |
|---|---|---|---|---|
| — | (no decisions yet) | — | — | — |

> Template for a future row:
> `2026-05-30 | Use 1440px as the single BO breakpoint for v1 | Customer support team works on managed laptops at 1440×900 | All BO screens | PM + UX/UI`

---

## 5. Change log

| Date | Change | Reason |
|---|---|---|
| 2026-05-23 | Initial validation file scaffolded with approver matrix, feature status, open issues | Kick-off — issues mirror gaps captured in UXUI_DESIGN.md §2 |
| 2026-05-25 | Synced from SA spec — partially resolved I-01 (error codes defined), resolved I-05 (single-session enforcement), opened I-09 (role naming drift) + I-10 (refresh-token UX) | SA team finalized auth implementation — propagated decisions into BRD + UXUI_DESIGN + STORIES |
| 2026-06-05 | Added Settings module (F-09..F-14) — 6 new feature rows, 7 new stories (LEASING-SET-01..07). Opened **I-11 (BRD missing — blocker)**, I-12 (green Save button precedent), I-13 (modal radius token) | Figma component library expanded with `↳ Page : Settings` (`4122:14114`) — Settings scope appeared without BRD |
