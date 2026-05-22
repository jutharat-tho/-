# Stories — Dev Handoff

**Last updated:** 2026-05-23
**Owner:** UX/UI
**Related:** [`requirement.md`](./requirement.md) · [`UXUI_DESIGN.md`](./UXUI_DESIGN.md) · [`figma-links.md`](./figma-links.md) · [`design-tokens.md`](./design-tokens.md)

> One story per discrete, ship-able piece of UI work. Each story links directly to the Figma node(s), references the rules in `requirement.md`, and lists the components / tokens involved.
> **Definition of Done (story-level):** the screen renders with all referenced states, matches the Figma frame at 1440×1024, uses tokens from `design-tokens.md` (no hard-coded values), and has unit tests for happy + at least one error path.

---

## Conventions

- Story ID format: `LEASING-<area>-<n>` (e.g. `LEASING-AUTH-01`). Replace with your tracker prefix.
- "Components" column lists the canonical Figma component names — match them in code (see `UXUI_DESIGN.md` §4).
- "Rules" column references rule IDs from `requirement.md` so engineers can trace requirements.

---

## Auth area

### LEASING-AUTH-01 — Login screen (default + filled + invalid credentials)

| Field | Value |
|---|---|
| Figma — default | [`139:28609`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=139-28609) |
| Figma — filled | [`139:28610`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=139-28610) |
| Figma — invalid credentials | [`182:68431`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=182-68431) |
| Rules | R-TP-04, R-SES-03 |
| Components | Login Container, Text field, Buttons/Basic, Language pill |
| Notes | Submit POSTs username + password. On 401 → show inline error. Form must not lock user. |

### LEASING-AUTH-02 — Login: temp password expired (inline error)

| Field | Value |
|---|---|
| Figma | [`139:28611`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=139-28611) |
| Rules | R-TP-03, R-TP-04, R-TP-06 |
| Components | Text field (error variant), Buttons/Basic |
| Notes | API returns `temp_password_expired` → render copy: "รหัสผ่านไม่ถูกต้อง หากลืมรหัสผ่าน กรุณาติดต่อผู้ดูแลระบบ" |

### LEASING-AUTH-03 — Set new password (after temp login)

| Field | Value |
|---|---|
| Figma — default | [`139:29024`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=139-29024) |
| Figma — filled | [`139:29025`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=139-29025) |
| Figma — error (policy fail) | [`139:29026`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=139-29026) |
| Rules | R-PWD-01..R-PWD-04, R-TP-05 |
| Components | Text field × 2 (New, Confirm) + show/hide toggle, Buttons/Basic, password policy checklist |
| Notes | Policy validation client-side; submit returns to Login on success. |

### LEASING-AUTH-04 — Dialog: new password matches temp

| Field | Value |
|---|---|
| Figma | [`329:38870`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=329-38870) |
| Rules | R-PWD-04 |
| Components | Dialog (Confirm info) |
| Notes | Modal blocks until dismissed; returns focus to "New password" input. |

### LEASING-AUTH-05 — Login: password expired flow (dialog + redirect)

| Field | Value |
|---|---|
| Figma — login | [`182:74610`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=182-74610), [`182:74612`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=182-74612) |
| Figma — dialog | [`239:74547`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=239-74547) |
| Rules | R-PWD-06, R-TP-05 |
| Components | Login Container, Dialog (Confirm info) |
| Notes | On dialog confirm → route to New password (PE2). On dismiss → stay on Login. |

### LEASING-AUTH-06 — Set new password (after expiry) + reuse-last guard

| Field | Value |
|---|---|
| Figma — default | [`189:75131`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=189-75131) |
| Figma — filled | [`189:75133`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=189-75133) |
| Figma — error (policy) | [`189:75135`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=189-75135) |
| Figma — dialog (matches last) | [`329:38735`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=329-38735) |
| Rules | R-PWD-01..R-PWD-03, R-PWD-05 |
| Components | Text field × 2, Dialog (Confirm info) |
| Notes | Same UI as LEASING-AUTH-03 but guard is "matches previous password" (R-PWD-05) instead of temp. |

---

## User management area

### LEASING-USR-01 — User list (default + after create)

| Field | Value |
|---|---|
| Figma — default | [`235:11244`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=235-11244) |
| Figma — after create | [`267:12463`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=267-12463) |
| Rules | R-USR-01..R-USR-04 |
| Components | Data table (rows × columns), Buttons/Basic ("+ Create User"), Search input |
| Notes | Empty state (zero users) not yet designed — block on gap I-04. |

### LEASING-USR-02 — Create user form (empty + filled + duplicate field)

| Field | Value |
|---|---|
| Figma — empty | [`202:9059`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=202-9059) |
| Figma — filled | [`202:89836`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=202-89836) |
| Figma — duplicate | [`382:50855`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=382-50855) |
| Rules | R-USR-01, R-USR-02, R-USR-04 |
| Components | Text field, Select (Role, Status), Buttons/Basic |
| Notes | All 7 fields required. Username + email checked unique on submit. |

### LEASING-USR-03 — Confirm before save + success

| Field | Value |
|---|---|
| Figma — confirm | [`239:62575`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=239-62575) |
| Figma — success | [`293:95474`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=293-95474) |
| Figma — duplicate dialog | [`382:51790`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=382-51790) |
| Rules | R-USR-03, R-USR-04, R-TP-01, R-TP-02 |
| Components | Dialog (Confirm info), success state of table |
| Notes | On success: API call returns ID, temp-password email is triggered server-side. |

### LEASING-USR-04 — Edit user (form + confirm + updated list)

| Field | Value |
|---|---|
| Figma — entry | [`293:123987`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=293-123987) |
| Figma — form | [`293:123994`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=293-123994) |
| Figma — confirm | [`293:123998`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=293-123998) |
| Figma — updated | [`293:123989`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=293-123989) |
| Rules | R-USR-01, R-USR-02 |
| Components | Text field, Select, Buttons/Basic, Dialog |
| Notes | Username likely read-only on Edit — confirm with BA. |

### LEASING-USR-05 — Reset password (modal triggered from Edit)

| Field | Value |
|---|---|
| Figma | [`336:39954`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=336-39954) |
| Rules | R-TP-01, R-TP-02, R-FGT-02 |
| Components | Dialog (Confirm info) with primary brand button |
| Notes | On confirm, server generates temp password and emails the user. UI must **not** display the new password. |

---

## Cross-cutting

### LEASING-SES-01 — Auto logout (background)

| Field | Value |
|---|---|
| Figma | — (no UI yet; see UXUI_DESIGN.md §7) |
| Rules | R-SES-01, R-SES-02 |
| Components | (proposed) toast 60s before timeout |
| Notes | Blocked on decision I-03 (silent vs. warning toast). |

---

## Change Log

| Date | Change | Reason |
|---|---|---|
| 2026-05-23 | Initial story set covering all designed screens (LEASING-AUTH-01..06, LEASING-USR-01..05, LEASING-SES-01) | Kick-off — one story per ship-able piece of UI |
