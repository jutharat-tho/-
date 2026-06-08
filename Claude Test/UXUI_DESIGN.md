# UX/UI Design Spec — Leasing Design BO: User Management & Authentication

**Status:** `draft`
**Related docs:** [`requirement.md`](./requirement.md) · [`figma-links.md`](./figma-links.md) · [`design-tokens.md`](./design-tokens.md)
**Last updated:** 2026-05-23
**Owner:** UX/UI

> Spec describes screens, states, copy, components, and accessibility notes. Business rules live in `requirement.md`. Source of truth for visuals is Figma — when this doc and Figma disagree, **Figma wins** and this doc must be updated.

---

## 1. Feature inventory

| ID | Feature | Figma section | Coverage |
|---|---|---|---|
| F-01 | Login with Temporary Password | `177:98173` | ✅ designed |
| F-02 | Login with Password Expire | `177:98174` | ✅ designed |
| F-03 | Set New Password | inside F-01 / F-02 | ✅ designed |
| F-04 | Super Admin — Create User | `293:123977` | ✅ designed |
| F-05 | Super Admin — Edit User | `293:123983` | ✅ designed |
| F-06 | Super Admin — Reset Password | `336:39954` (modal) | ✅ designed |
| F-07 | Forgot Password | — (process only; user contacts admin) | n/a — no UI |
| F-08 | Auto Logout | — (background timer) | ⚠️ UX feedback TBD |
| F-09 | Settings — Product Category | `4123:37453` + modal `4126:38865` | ✅ designed (Figma) · ⚠️ BRD reverse-engineered |
| F-10 | Settings — Subcategories | `4131:16139` + modal `4131:17424` | ✅ designed (Figma) · ⚠️ BRD reverse-engineered |
| F-11 | Settings — Product Group + sub-modals | `4165:16008` / `4443:12536` / `4537:9694` | ✅ designed (Figma) · ⚠️ most complex — 3 nested tables |
| F-12 | Settings — Product Model List | `4199:24667` + modal `4199:26977` | ✅ designed (Figma) · ⚠️ BRD reverse-engineered |
| F-13 | Settings — Partner Type | `4215:3686` + modal `4215:4106` | ✅ designed (Figma) · ⚠️ BRD reverse-engineered |
| F-14 | Settings — Partner Information (2 form variants) | `4216:27149` (juristic + personal) | ✅ designed (Figma) · ⚠️ BRD reverse-engineered |
| F-15 | **Loan Application** module — full | file `gQFZW0Dx2q9LDxdpAghaKZ` | ⏳ scope placeholder — file exists, screens TBD |
| F-16 | **Customer** module — full | file `oBfkloeAjurpB977NnWo8H` | ⏳ scope placeholder — file exists, screens TBD |
| F-17 | **Settings screens** (assembly of F-09..F-14) | file `kYSQdQ57JG8oOVjLElJDYv` | ⏳ scope placeholder — screen-level container, components already in library |
| F-18 | **Content Management** module — full | file `nj3GMHSybP4HFguzfOlUM5` | ⏳ scope placeholder — file exists, screens TBD |

---

## 2. Screen inventory & state coverage

Legend for the 7-state checklist:
- D = default · L = loading · E = empty · X = error · S = success · DIS = disabled · R = responsive

### 2.1 Feature F-01 — Login with Temporary Password

| Screen ID | Name | D | L | E | X | S | DIS | R | Figma |
|---|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| L1 | Login | ✅ | ⚠️ | ✅ | ✅ | ✅ | ⚠️ | ⚠️ | `139:28609`, `139:28610`, `139:28611`, `182:68431`, `182:71630` |
| L2 | New password | ✅ | ⚠️ | ✅ | ✅ | ✅ | ⚠️ | ⚠️ | `139:29024`, `139:29025`, `139:29026` |
| L2-DLG | Password duplicate dialog (matches temp) | ✅ | n/a | n/a | n/a | n/a | n/a | ⚠️ | `329:38870` |

### 2.2 Feature F-02 — Login with Password Expire

| Screen ID | Name | D | L | E | X | S | DIS | R | Figma |
|---|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| PE1 | Login | ✅ | ⚠️ | ✅ | ✅ | ✅ | ⚠️ | ⚠️ | `182:74610`, `182:74612`, `182:74614`, `189:75128` |
| PE1-DLG | Password expired dialog | ✅ | n/a | n/a | n/a | n/a | n/a | ⚠️ | `239:74547` |
| PE2 | New password | ✅ | ⚠️ | ✅ | ✅ | ✅ | ⚠️ | ⚠️ | `189:75131`, `189:75133`, `189:75135` |
| ~~PE2-DLG~~ | ~~Password duplicate dialog (matches last)~~ — **DEPRECATED 2026-05-25**: rule R-PWD-05 retired, this screen no longer needed | — | — | — | — | — | — | — | `329:38735` (do not implement) |

### 2.3 Feature F-04 — Create User

| Screen ID | Name | D | L | E | X | S | DIS | R | Figma |
|---|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| CU1 | User List | ✅ | ⚠️ | ⚠️ | ⚠️ | ✅ | n/a | ⚠️ | `235:11244`, `267:12463` |
| CU2 | Create User form | ✅ | ⚠️ | ✅ | ✅ | n/a | ⚠️ | ⚠️ | `202:9059`, `202:89836`, `382:50855` |
| CU2-DLG-CONFIRM | Confirm before submit | ✅ | n/a | n/a | n/a | n/a | n/a | ⚠️ | `239:62575` |
| CU2-DLG-DUP | Duplicate username/email dialog | ✅ | n/a | n/a | ✅ | n/a | n/a | ⚠️ | `382:51790` |
| CU3 | Success — user added | ✅ | n/a | n/a | n/a | ✅ | n/a | ⚠️ | `293:95474` |
| EMAIL | Email artifact (out-of-app preview) | ✅ | n/a | n/a | n/a | n/a | n/a | n/a | `200:8467` |

### 2.4 Feature F-05 — Edit User

| Screen ID | Name | D | L | E | X | S | DIS | R | Figma |
|---|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| EU1 | User List | ✅ | ⚠️ | ⚠️ | ⚠️ | n/a | n/a | ⚠️ | `293:123987` |
| EU2 | Edit form | ✅ | ⚠️ | n/a | ⚠️ | n/a | ⚠️ | ⚠️ | `293:123994` |
| EU2-DLG | Confirm before save | ✅ | n/a | n/a | n/a | n/a | n/a | ⚠️ | `293:123998` |
| EU3 | Updated list | n/a | n/a | n/a | n/a | ✅ | n/a | ⚠️ | `293:123989` |

### 2.5 Feature F-06 — Reset Password (modal triggered from EU2)

| Screen ID | Name | D | L | E | X | S | DIS | R | Figma |
|---|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| RP-DLG | Reset Password confirmation modal | ✅ | ⚠️ | n/a | ⚠️ | ✅ | n/a | ⚠️ | `336:39954` |

### 2.6 Feature F-09 to F-14 — Settings Module (NEW 2026-06-05)

> **Pattern:** Each entity (Category / Subcategory / Model / Partner Type) follows the same CRUD shape — **List page** + **Create modal (Fill=no)** + **Edit modal (Fill=yes)** + Status field on Edit only. Product Group (F-11) + Partner Information (F-14) break the pattern with larger composite forms.

#### F-09 Product Category

| Screen ID | Name | D | L | E | X | S | DIS | R | Figma |
|---|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| SET-CAT-1 | List | ✅ | ⚠️ | ⚠️ | ⚠️ | n/a | n/a | ⚠️ | `4123:37453` |
| SET-CAT-2 | Modal — Create (Fill=no) | ✅ | ⚠️ | ✅ | ⚠️ | n/a | ⚠️ | ⚠️ | `4126:38864` |
| SET-CAT-3 | Modal — Edit (Fill=yes) + Status | ✅ | ⚠️ | n/a | ⚠️ | n/a | ⚠️ | ⚠️ | `4126:38863` |

#### F-10 Subcategories

| Screen ID | Name | D | L | E | X | S | DIS | R | Figma |
|---|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| SET-SUB-1 | List | ✅ | ⚠️ | ⚠️ | ⚠️ | n/a | n/a | ⚠️ | `4131:16139` |
| SET-SUB-2 | Modal — Create | ✅ | ⚠️ | ✅ | ⚠️ | n/a | ⚠️ | ⚠️ | `4131:17423` |
| SET-SUB-3 | Modal — Edit | ✅ | ⚠️ | n/a | ⚠️ | n/a | ⚠️ | ⚠️ | `4131:17422` |

#### F-11 Product Group (largest)

| Screen ID | Name | D | L | E | X | S | DIS | R | Figma |
|---|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| SET-PG-1 | List | ✅ | ⚠️ | ⚠️ | ⚠️ | n/a | n/a | ⚠️ | `4165:16007` |
| SET-PG-2 | Create page (large form) | ✅ | ⚠️ | ✅ | ⚠️ | n/a | ⚠️ | ⚠️ | `4165:16009` |
| SET-PG-3 | Info / Edit page (3 nested tables) | ✅ | ⚠️ | n/a | ⚠️ | n/a | ⚠️ | ⚠️ | `4443:12536` |
| SET-PG-MOD-IR | Modal — Interest rate (Fill=no/yes) | ✅ | ⚠️ | ✅ | ⚠️ | n/a | n/a | ⚠️ | `4460:14573` / `4460:14572` |
| SET-PG-MOD-SM | Modal — Sub-model (Fill=no/yes) | ✅ | ⚠️ | ✅ | ⚠️ | n/a | n/a | ⚠️ | `4463:9144` / `4463:9143` |
| SET-PG-MOD-AT | Modal — Annual total (Fill=no/yes) | ✅ | ⚠️ | ✅ | ⚠️ | n/a | n/a | ⚠️ | `4463:9778` / `4463:9777` |

#### F-12 Product Model List

| Screen ID | Name | D | L | E | X | S | DIS | R | Figma |
|---|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| SET-MOD-1 | List | ✅ | ⚠️ | ⚠️ | ⚠️ | n/a | n/a | ⚠️ | `4199:24667` |
| SET-MOD-2 | Modal — Create | ✅ | ⚠️ | ✅ | ⚠️ | n/a | ⚠️ | ⚠️ | `4199:26976` |
| SET-MOD-3 | Modal — Edit | ✅ | ⚠️ | n/a | ⚠️ | n/a | ⚠️ | ⚠️ | `4199:26978` |

#### F-13 Partner Type

| Screen ID | Name | D | L | E | X | S | DIS | R | Figma |
|---|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| SET-PTY-1 | List | ✅ | ⚠️ | ⚠️ | ⚠️ | n/a | n/a | ⚠️ | `4215:3686` |
| SET-PTY-2 | Modal — Create | ✅ | ⚠️ | ✅ | ⚠️ | n/a | ⚠️ | ⚠️ | `4215:4105` |
| SET-PTY-3 | Modal — Edit | ✅ | ⚠️ | n/a | ⚠️ | n/a | ⚠️ | ⚠️ | `4215:4104` |

#### F-14 Partner Information (2 form variants)

| Screen ID | Name | D | L | E | X | S | DIS | R | Figma |
|---|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| SET-PTN-1 | List — Default | ✅ | ⚠️ | ⚠️ | ⚠️ | n/a | n/a | ⚠️ | `4215:6732` |
| SET-PTN-2 | Form — นิติบุคคล (juristic, large) | ✅ | ⚠️ | ✅ | ⚠️ | n/a | ⚠️ | ⚠️ | `4216:25929` |
| SET-PTN-3 | Form — บุคคลธรรมดา (individual, large) | ✅ | ⚠️ | ✅ | ⚠️ | n/a | ⚠️ | ⚠️ | `4295:16938` |

> 🎨 **Notable design tokens introduced by Settings module:**
> - Save button uses **`Secondary/Basic` (#019267, green)** instead of brand red — distinct from Auth flow's pattern
> - Cancel button uses **outline brand red** (`border-button-brand`)
> - Modal radius bumped from `radius-200` (8px) to `radius-400` (16px) for these large modals
> - Modal max-width = 1212px with backdrop blur 4px overlay

### Gaps to close before Dev handoff

| Gap | Affects | Action |
|---|---|---|
| No loading state for any submit action | All forms | Define button spinner / disabled style + skeleton if applicable |
| No empty state for user list (zero users) | CU1, EU1 | Design empty illustration + CTA "Create User" |
| No responsive variant below 1440px | All BO screens | Confirm whether BO supports anything smaller; design 1024px and 768px if yes |
| Disabled state for inputs / buttons | Login, forms | Token exists (`text/placeholder`) — apply consistently |
| Auto-logout warning toast (F-08) | All BO modules | Decide: silent logout vs. 1-minute warning toast |

---

## 3. Flow specs

### 3.1 Flow F-01 — Login with Temporary Password (happy path)

```
Email (out-of-app) → L1 Login (empty)
  → user enters username + temp password
  → submit
  → [valid + not expired] → L2 New password (default)
    → user enters new + confirm
    → submit
    → [meets policy + not matching temp] → save success
    → redirect to L1 Login → user logs in with new password → BO landing
```

**Decision points & matching error states:**

| Decision | If false | Screen / dialog |
|---|---|---|
| Username/Temp password correct? | no → inline error | L1 error (`139:28611`) — "ชื่อผู้ใช้งานหรือรหัสผ่านไม่ถูกต้อง กรุณาลองใหม่อีกครั้ง" |
| Temp password expired? | yes → inline error | L1 error (`182:68431`) — "รหัสผ่านไม่ถูกต้อง หากลืมรหัสผ่าน กรุณาติดต่อผู้ดูแลระบบ" |
| New password meets policy? | no → inline error per rule | L2 error (`139:29026`) |
| New password ≠ temp password? | no → dialog | L2-DLG (`329:38870`) — "Password ซ้ำ" |

### 3.2 Flow F-02 — Login with Password Expire (happy path)

```
PE1 Login (empty)
  → user enters username + password (which has expired)
  → [valid credentials] → PE1-DLG "Password expired"
    → user clicks "Set new password" → PE2 New password (default)
      → user enters new + confirm
      → [meets policy + ≠ last] → save success → redirect to PE1 Login → login with new password → BO landing
    → user dismisses dialog → back to PE1 (no change)
```

### 3.3 Flow F-04 — Create User (happy path)

```
CU1 User List (default)
  → click "+ Create User"
  → CU2 Create form (empty)
  → fill fields → click Save
  → CU2-DLG-CONFIRM (preview)
    → confirm → [unique username + email]
      → CU3 Success → CU1 with new user row
      → system sends temp-password email (EMAIL)
    → confirm → [duplicate] → CU2-DLG-DUP → back to form with field flagged
```

### 3.4 Flow F-05 / F-06 — Edit User + Reset Password

```
EU1 User List → click row "Edit"
  → EU2 Edit form (pre-filled)
  → either:
    (a) modify fields → Save → EU2-DLG confirm → EU3 Updated list
    (b) click "Reset Password" → RP-DLG → confirm
        → system generates temp password → sends email
        → admin returned to EU2; no password shown in UI
```

---

## 4. Component inventory (from Figma library)

| Component | Where used | Variants observed |
|---|---|---|
| Login Container (`Container` 251:50714) | L1, PE1 | default; with logo, language pill, form fields, primary button, version footer |
| Text Field | L1, L2, PE1, PE2, CU2, EU2 | default · filled · error (red border + red glow + helper) |
| Buttons/Basic | every action surface | primary brand (`#D82329` bg) — observed in `34:69` family |
| Dialog (Confirm info) | confirm/expire/duplicate flows | icon + title + subtitle + button — `349:128206` |
| Email artifact | sent after Create / Reset | logo + greeting + username + temp password + note |
| Language pill | L1, PE1 | TH default; pill 100px radius, flag icon |
| User table (data grid) | CU1, EU1 | rows × columns (Employee ID, Username, Name, Email, Role, Status, Action) — `1440x1024` |

**Component naming convention for Dev handoff:**
- Match Figma component name exactly (`Buttons/Basic`, `Text field`, `Dialog`) in code
- Variants → props (e.g. Text field `state="default" | "error"`)
- Tokens → CSS variables exactly as in `design-tokens.md`

---

## 5. Copy / Microcopy

### Error messages — Login

| Trigger | Thai (canonical) | English (proposed, confirm with BA) |
|---|---|---|
| Invalid username or temp password | ชื่อผู้ใช้งานหรือรหัสผ่านไม่ถูกต้อง กรุณาลองใหม่อีกครั้ง | "Incorrect username or password. Please try again." |
| Temp password expired or invalid (after multiple wrong tries) | รหัสผ่านไม่ถูกต้อง หากลืมรหัสผ่าน กรุณาติดต่อผู้ดูแลระบบ | "Incorrect password. If you forgot your password, please contact your administrator." |
| Password policy not met | (per Figma — currently shown as inline checklist) | "Password must meet all requirements." |
| New password matches temp | (dialog "Password ซ้ำ") — error code `2012` | "New password cannot match your temporary password." |
| ~~New password matches last~~ | **REMOVED 2026-05-25** — rule R-PWD-05 retired per SA. No reuse-last check anymore. | — |
| Password expired (login time) | (dialog "Password expire") | "Your password has expired. Please set a new password." |

### Confirmation dialogs — Create / Edit / Reset

| Dialog | Title | Body | Primary CTA | Secondary |
|---|---|---|---|---|
| Confirm Create | "ยืนยันการสร้างผู้ใช้งาน" (TBD) | "กรุณาตรวจสอบข้อมูลก่อนยืนยัน" | ยืนยัน | ยกเลิก |
| Confirm Edit | "ยืนยันการแก้ไขผู้ใช้งาน" (TBD) | "กรุณาตรวจสอบข้อมูลก่อนยืนยัน" | ยืนยัน | ยกเลิก |
| Confirm Reset | "ยืนยันการรีเซ็ตรหัสผ่าน" (TBD) | "ระบบจะส่ง Temporary Password ไปยังอีเมลของผู้ใช้" | ยืนยัน | ยกเลิก |

> All confirm/cancel copy needs BA sign-off — Figma shows the structure but the source-of-truth strings should come from the BA.

---

## 6. Accessibility notes

### 6.1 Focus & keyboard

- **Tab order on Login (L1, PE1):** Language pill → Username → Password → Show/hide password toggle → Primary button
- **Tab order on New password (L2, PE2):** New password → Show/hide → Confirm password → Show/hide → Primary button
- **Enter key:** submits the active form (no implicit submit on language pill)
- **Esc key:** closes any open dialog
- **Focus ring:** must use `border/primary` thicker or a visible outline — current Figma frames do not show a focus state; flag for design system update

### 6.2 Color & contrast

| Foreground / Background | Ratio | Pass? |
|---|---|---|
| `text/primary` `#18181B` on `background/primary` `#FFFFFF` | 16.1 : 1 | ✅ AAA |
| `text/secondary` `#71717A` on `#FFFFFF` | 4.7 : 1 | ✅ AA |
| `text/placeholder` `#D4D4D8` on `#FFFFFF` | 1.45 : 1 | ❌ — only acceptable as inactive placeholder; do not use for content |
| `text/button/primary` `#FFFFFF` on `background/button/brand` `#D82329` | 4.9 : 1 | ✅ AA |
| `text/button/critical` `#FF4545` on `#FFFFFF` | 3.6 : 1 | ⚠️ AA Large only — supplement with icon for error helper text |

### 6.3 ARIA / semantics (dev guidance)

- Login form → `<form>` with `aria-labelledby` pointing at the Pentor Leasing heading
- Each input → `<label>` (not just visual text), `aria-describedby` linking the helper/error text below
- Error message → `aria-live="polite"` so it's announced when it appears
- Dialog → `role="dialog"` `aria-modal="true"`, focus moved to the primary CTA on open, Tab traps inside
- Show/hide password toggle → `aria-pressed` + `aria-label="Show password" / "Hide password"`
- Language pill → `<button>` with `aria-haspopup="listbox"` if it opens a list

### 6.4 i18n notes

- The product is bilingual TH/EN (language pill exists). Test copy for line-break behavior in both languages.
- The error string "ชื่อผู้ใช้งานหรือรหัสผ่านไม่ถูกต้อง กรุณาลองใหม่อีกครั้ง" is ~50 Thai chars — input width 372px holds it on one line; ensure it wraps gracefully if width is reduced.
- Password rules listed inline on L2/PE2 must be fully localized when EN is enabled.

---

## 7. Interaction spec

| Element | Interaction | Spec |
|---|---|---|
| Primary button (`Buttons/Basic`) | hover | brightness 92% (filter), no transform |
| Primary button | active/pressed | brightness 88% |
| Primary button | disabled | use `background/secondary-hover` bg, `text/placeholder` text |
| Text field | focus | border `1px → 2px` `border/primary`, or apply focus ring token (TBD) |
| Text field | error | border `border/button/critical` + `--shadow-input-error` |
| Show/hide password | click toggle | swaps `eye-closed` ↔ `eye-open` icon; flips `type=password` ↔ `text` |
| Dialog | open | overlay 50% black, dialog fades + scales 0.96→1 over 150ms ease-out |
| Dialog | close | reverse, 100ms |
| Auto-logout | trigger | (proposal — pending BA) show toast 60 sec before, "เซสชันจะหมดอายุใน 1 นาที" with "ใช้งานต่อ" CTA |

---

## 8. Responsive notes

Current Figma frames are all `1440 × 1024`. For BO, this is acceptable as the primary target. Open questions:

1. **Does BO need to work at 1280 / 1024 widths?** If yes, design needs:
   - Login card centered with min-width 372px form column intact
   - User table with column hide rules below 1280px
2. **Mobile?** Not currently in scope. If marketing wants Login on mobile, design needs to be re-derived (single column, full-width inputs, etc.).

---

## 9. Handoff checklist (before Dev Mode)

- [ ] Component names in Figma == names in code (audit during Code Connect)
- [ ] All 7 states present per screen (see §2 gap table)
- [ ] Tokens exported (CSS vars + Style Dictionary) — see `design-tokens.md`
- [ ] Empty / loading / disabled designed for every form
- [ ] Confirm/cancel copy approved by BA (see §5 table)
- [ ] Focus state defined and documented
- [ ] Responsive breakpoint(s) decided
- [ ] Dev Mode enabled on file & verified
- [ ] Story-level Figma links added to `STORIES.md`

---

## 10. Change Log

| Date | Change | Reason |
|---|---|---|
| 2026-05-23 | Initial spec covering F-01 to F-06, plus accessibility, copy table, state inventory, gaps | First sync from Figma + FigJam — gaps captured for next iteration |
| 2026-05-25 | Synced SA spec — deprecated PE2-DLG screen (reuse-last guard removed per R-PWD-05 retire), removed "matches last" error copy row, added error code `2012` reference. See [SA folder](file:///Users/buttar/Documents/ai/SA/) for authoritative API spec + sequence diagrams. | SA team finalized auth implementation — design must follow actual backend behavior |
| 2026-06-05 | Added Settings module (F-09..F-14) — 18+ screens across Product Category / Subcategories / Product Group / Product Model / Partner Type / Partner Information. State matrix shows ~95% gap on loading/disabled/responsive (consistent with existing modules). Documented new tokens: green Save button, larger modal radius. | New scope from Figma `4122:14114` — design exists, BRD pending BA confirmation |
| 2026-06-09 | Added F-15..F-18 scope placeholders (Loan Application, Customer, Settings screens, Content Management). Project reorganized into 6 per-module Figma files; `Leasing-Design` renamed to `BO - User & Role Permission` (fileKey unchanged). Detailed screen inventory will populate when BA/PM shares specific node URLs. | BA/PM split master Figma into per-domain files — total scope now spans 18 features (F-01..F-18) |
