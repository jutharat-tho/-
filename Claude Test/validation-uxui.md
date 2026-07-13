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
| F-15 Loan Application module | `draft` | — | — | — | **Registered 2026-06-12** — 5-stage lifecycle, 5 stories (LEASING-LOAN-01..05). BRD reverse-engineered — confirm R-LOAN-* with BA. |
| F-16 Customer module | `placeholder` | — | — | — | **NEW 2026-06-09** — waiting for BA/PM to share screen node URLs from `BO - Customer` file |
| F-17 Settings screens (assembly) | `placeholder` | — | — | — | **NEW 2026-06-09** — components ready (F-09..F-14), need screen-level layout from `BO - Setting` file |
| F-18 Content Management module | `placeholder` | — | — | — | **NEW 2026-06-09** — waiting for BA/PM to share screen node URLs from `BO - Content Management` file |
| F-19 Financial Mgmt — Disbursement (2 pages) | `draft` | — | — | — | **NEW 2026-07-14** — Disbursement + Disbursement Report only. 2 stories (LEASING-FIN-01/02). BRD reverse-engineered. Other FM pages out of scope. |

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
| I-14 | **4 new Figma files** (`BO - Loan Application`, `BO - Customer`, `BO - Setting`, `BO - Content Management`) are scope placeholders — only Cover artwork visible from Figma API. Need BA/PM to share specific screen `node-id` URLs so UX/UI can register screens + draft stories. | BA / PM | — | **blocker** (new 2026-06-09) for F-15..F-18 |
| I-15 | Confirm the relationship between Component Library `↳ Page : Settings` (existing, F-09..F-14) and the new `BO - Setting` file. Likely component-vs-screen assembly split — component library defines reusable Forms+Modals, BO - Setting wires them into the actual pages with sidebar/nav. | UX/UI + Design system | — | open (new 2026-06-09) |
| I-16 | **Cross-file paste strips text overrides.** Copying Loan Application components from Component Library → `BO - Loan Application` drops label + placeholder text (structure survives, data disappears). Confirmed on Loan Disbursement Preparation instance `1658:372537`. Root cause: deep nested-instance hierarchy — override paths fail to resolve across files. **Workaround:** drag fresh instance from Assets panel instead of paste. **Real fix:** design-system team should flatten component nesting. | Design system + UX/UI | — | **blocker** for Loan App handoff (new 2026-06-12) |
| I-17 | **Variant naming inconsistent across Loan Application module** — mixes `Fill=`, `Type=`, `Upload=`, `Step=`, `No data=` and uses TH values (`Type=ตารางแสดงหนี้`, `Type=สัญญา`). Also `Type=Agen` likely typo for `Agent`. Standardize before Code Connect so FE prop API is consistent. | Design system | — | open (new 2026-06-12) |
| I-18 | **Disbursement receipt (`106:7289`) is a print/A4 document, not a screen** — needs a dedicated print stylesheet or PDF-export spec (page size, margins, page breaks, mono/no-color for print). Confirm delivery format with BA + FE (browser print dialog vs server-generated PDF). | BA + FE Lead | — | open (new 2026-07-14) |
| I-19 | Disbursement summary (`35:2485`) — confirm which money fields are user-editable vs computed/read-only, and where the source figures come from (Loan Application disbursement-prep stage LOAN-5?). Likely inherits from the loan flow. | BA | — | open (new 2026-07-14) |
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
| 2026-06-09 | Added F-15..F-18 placeholders (Loan Application, Customer, Settings screens, Content Management). 4 new Figma files registered. Opened **I-14 (need screen node URLs — blocker)**, I-15 (component-vs-screen split). | BA/PM reorganized into 6 per-domain Figma files |
| 2026-06-12 | F-15 Loan Application promoted placeholder → draft (5 stories registered). Opened **I-16 (paste strips overrides — blocker)** + I-17 (variant naming). Partially resolves I-14 for the Loan Application file. | Loan Application content confirmed + copy-paste issue diagnosed via MCP |
| 2026-07-14 | Added F-19 Financial Management (Disbursement + Disbursement Report only). 2 stories. Opened I-18 (receipt print/PDF spec) + I-19 (editable vs computed money fields). | New file scanned via MCP; scope limited to 2 pages per request |
