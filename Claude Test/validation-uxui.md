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

Status values: `draft` → `in_review` → `approved` → `final`.

---

## 3. Open issues / pending decisions

| # | Issue | Owner | Target | Status |
|---|---|---|---|---|
| I-01 | Confirm exact wording for "Password expire" and "Password ซ้ำ" dialogs | BA | — | open |
| I-02 | Decide responsive scope (BO at < 1440px?) | PM + FE Lead | — | open |
| I-03 | Decide auto-logout UX (silent vs. 60s warning toast) | PM + BA | — | open |
| I-04 | Design empty / loading / disabled states for all forms | UX/UI | — | open |
| I-05 | Confirm Reset Password kills active sessions (backend behavior) | FE Lead | — | open |
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
