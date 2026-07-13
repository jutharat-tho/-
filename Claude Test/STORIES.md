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

### LEASING-AUTH-06 — Set new password (after expiry) _(reuse-last guard REMOVED 2026-05-25)_

| Field | Value |
|---|---|
| Figma — default | [`189:75131`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=189-75131) |
| Figma — filled | [`189:75133`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=189-75133) |
| Figma — error (policy) | [`189:75135`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=189-75135) |
| ~~Figma — dialog (matches last)~~ | ~~[`329:38735`](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design?node-id=329-38735)~~ — do not implement, R-PWD-05 retired |
| Rules | R-PWD-01..R-PWD-03 _(R-PWD-05 retired — no reuse-last check)_ |
| Components | Text field × 2, Dialog (Confirm info) |
| Notes | Same UI as LEASING-AUTH-03 — only format validation (R-PWD-01..03). Backend trigger=`expired` (JWT scope=`CHANGE_PASSWORD`). API: `POST /auth/change-password`. **No reuse-last guard** since R-PWD-05 retired 2026-05-25. |

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

## Settings area _(NEW 2026-06-05 — reverse-engineered from Figma, BRD pending BA)_

> ⚠️ All R-CAT/R-SUB/R-PG/R-MOD/R-PTY/R-PTN rules in these stories are inferred. Block on BA confirmation before sprint planning.

### LEASING-SET-01 — Product Category (List + Create + Edit)

| Field | Value |
|---|---|
| Figma — list | [`4123:37453`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4123-37453) |
| Figma — create modal (Fill=no) | [`4126:38864`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4126-38864) |
| Figma — edit modal (Fill=yes) | [`4126:38863`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4126-38863) |
| Rules | R-CAT-01..R-CAT-04 _(pending BA confirmation)_ |
| Components | Data table · Buttons/Basic · Dialog (large modal, radius-400) · Text field · Text area · Select (รหัส) |
| Notes | Create form has 3 fields (รหัส*, ชื่อ*, คำอธิบาย). Edit form adds Status field (เปิด/ปิดใช้งาน). **Save button uses Secondary/Basic green (#019267)** — not brand red. |

### LEASING-SET-02 — Subcategories (List + Create + Edit)

| Field | Value |
|---|---|
| Figma — list | [`4131:16139`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4131-16139) |
| Figma — create | [`4131:17423`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4131-17423) |
| Figma — edit | [`4131:17422`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4131-17422) |
| Rules | R-SUB-01..R-SUB-03 _(pending BA)_ |
| Components | Data table · Dialog · Text field · Select (parent Category — FK) |
| Notes | Subcategory belongs to a parent Category (FK relationship inferred — confirm with BA). Same UI pattern as F-09. |

### LEASING-SET-03 — Product Group (List + Create form)

| Field | Value |
|---|---|
| Figma — list | [`4165:16007`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4165-16007) |
| Figma — create | [`4165:16009`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4165-16009) |
| Rules | R-PG-01..R-PG-03 _(pending BA)_ |
| Components | Data table · Form-grid (large) · Buttons/Basic |
| Notes | Create form is a **dedicated page (not modal)** because of size. Has header fields + 3 nested tables (interest rates / sub-models / annual totals) — but the tables are populated via separate modals in LEASING-SET-04. |

### LEASING-SET-04 — Product Group: nested modals (Interest rate / Sub-model / Annual total)

| Field | Value |
|---|---|
| Figma — info page (composite) | [`4443:12536`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4443-12536) |
| Figma — modal wrapper (3 types) | [`4537:9694`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4537-9694) |
| Figma — Interest rate Fill=no / yes | [`4460:14573`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4460-14573) · [`4460:14572`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4460-14572) |
| Figma — Sub-model Fill=no / yes | [`4463:9144`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4463-9144) · [`4463:9143`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4463-9143) |
| Figma — Annual total Fill=no / yes | [`4463:9778`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4463-9778) · [`4463:9777`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4463-9777) |
| Rules | R-PG-01, R-PG-02 _(pending BA)_ |
| Components | 3 Dialog variants · Form-grid · Number input · Currency input (likely) |
| Notes | Each of the 3 sub-modals has Fill=no (empty) + Fill=yes (data) variants. Wrapper `4537:9694` packages all 3 modal types together. Implement as 3 separate React components with consistent prop signature. |

### LEASING-SET-05 — Product Model List (List + Create + Edit)

| Field | Value |
|---|---|
| Figma — list | [`4199:24667`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4199-24667) |
| Figma — create | [`4199:26976`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4199-26976) |
| Figma — edit | [`4199:26978`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4199-26978) |
| Rules | R-MOD-01..R-MOD-02 _(pending BA)_ |
| Components | Data table · Dialog · Text field · Select (parent Group — FK) |
| Notes | Same CRUD pattern. Likely has FK to Product Group (TBD). |

### LEASING-SET-06 — Partner Type (List + Create + Edit)

| Field | Value |
|---|---|
| Figma — list | [`4215:3686`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4215-3686) |
| Figma — create | [`4215:4105`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4215-4105) |
| Figma — edit | [`4215:4104`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4215-4104) |
| Rules | R-PTY-01..R-PTY-02 _(pending BA)_ |
| Components | Data table · Dialog · Text field |
| Notes | Master data — list of partner types referenced by F-14. Same CRUD pattern. |

### LEASING-SET-07 — Partner Information (2 entity-type variants)

| Field | Value |
|---|---|
| Figma — list (Default) | [`4215:6732`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4215-6732) |
| Figma — Form นิติบุคคล (juristic) | [`4216:25929`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4216-25929) |
| Figma — Form บุคคลธรรมดา (individual) | [`4295:16938`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4295-16938) |
| Rules | R-PTN-01..R-PTN-03 _(pending BA)_ |
| Components | Data table · Radio/Toggle (entity type) · Form-grid (large) · Text field × many · Select (Partner Type FK) |
| Notes | **Two distinct full-page forms** depending on entity type selection. User picks type first → form structure changes. Implement as 2 separate React components sharing a parent route. Confirm with BA whether type is locked after create. |

---

## Loan Application area _(NEW 2026-06-12 — reverse-engineered from Figma, BRD pending BA)_

> ⚠️ 5-stage role lifecycle. An application flows Marketing → Support → Analyst → Approve+Contract → Disbursement. Node IDs are from Component Library `4022:7981`; screen assembly in BO file page `1001:5`. **All rules inferred — block on BA before sprint.**

### LEASING-LOAN-01 — Marketing: create application

| Field | Value |
|---|---|
| Figma — section | [`4623:49390`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4623-49390) |
| Key components | Application information (`4613:17736`, Type=Marketing/Agen) · Lease information · Insurance · Guarantor · Upload · Product group |
| Rules | R-LOAN-* _(pending BA)_ |
| Components | Form-grid (large) · Text field · Upload · Modal · Document details |
| Notes | Entry point of the lifecycle. `Type=Agen` variant likely typo for "Agent" — confirm. Multi-section form; Upload has no/yes states. |

### LEASING-LOAN-02 — Support: check documents

| Field | Value |
|---|---|
| Figma — section | [`4853:39932`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4853-39932) |
| Key components | List of loan application forms (`4623:49389`) · Check the documents (`4646:28832`) |
| Rules | R-LOAN-* _(pending BA)_ |
| Components | Data table · Document checklist |
| Notes | Support role reviews uploaded docs before passing to Analyst. |

### LEASING-LOAN-03 — Analyst: credit review

| Field | Value |
|---|---|
| Figma — section | [`4853:40234`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4853-40234) |
| Key components | Analysis report (`4632:92937`) · Account statement info (`4646:29376`, No data=yes/no) · Add transaction history + modal · Hire-purchase info · Guarantor info |
| Rules | R-LOAN-* _(pending BA)_ |
| Components | Form-grid · Data table · Checkbox grid (credit criteria) · Modal · Text area (notes) |
| Notes | Heaviest analysis screen — many checkbox criteria (อาชีพ / อายุงาน / ประวัติเครดิต etc). Account statement has empty-state (No data=yes). |

### LEASING-LOAN-04 — Approver: approve + record contract

| Field | Value |
|---|---|
| Figma — section | [`4853:40241`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4853-40241) |
| Key components | Approve (`4816:27790`) · Loan approval results (`4660:51426`) · Contract details (`4660:77023`, Default/Fill/Disabled) · Record of the contract (`4660:80191`, Step=1/2) · Modal-Preview · Window-Modal-Preview |
| Rules | R-LOAN-* _(pending BA)_ |
| Components | Approve panel · Multi-step form (Step 1/2) · Preview modal (debt table / contract doc) |
| Notes | Contract recording is a 2-step wizard. Preview modal shows either payment schedule table or contract document. Contract details has a Disabled state (read-only after approval). |

### LEASING-LOAN-05 — Disbursement preparation

| Field | Value |
|---|---|
| Figma — section | [`4938:32720`](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=4938-32720) |
| Figma — BO instance | [`1658:372537`](https://www.figma.com/design/gQFZW0Dx2q9LDxdpAghaKZ/BO---Loan-Application?node-id=1658-372537) |
| Key components | Loan Disbursement Preparation (`4903:55383`) · Transfer fees (`4903:50967`) · Transfer amount (`4903:54603`) |
| Rules | R-LOAN-* _(pending BA)_ |
| Components | 2-col layout · ~15 currency fields · Expense line-item table (10 rows) · green forward button |
| Notes | Most data-dense screen. Left = cost breakdown (ราคารถ, เงินดาวน์, ยอดจัดสินเชื่อ, ประกัน, ค่าคอมฯ...); right = expense table from "Master ค่าใช้จ่าย" filtered by vehicle type. **Actions:** ยกเลิก / บันทึกร่าง / ส่งต่อฝ่ายตรวจสอบ. ⚠️ instance in BO file has text overrides stripped (I-16). |

---

## Financial Management area _(NEW 2026-07-14 — Disbursement pages only, BRD pending BA)_

> ⚠️ Scope limited to the two Disbursement pages of `BO - Financial Management`. Other pages in that file are not registered yet.

### LEASING-FIN-01 — Disbursement (payment summary)

| Field | Value |
|---|---|
| Figma — page | [`1:2`](https://www.figma.com/design/xByR0qZwcrRDK7tFwjEF2t/BO---Financial-Management?node-id=1-2) |
| Figma — content | [`35:2485`](https://www.figma.com/design/xByR0qZwcrRDK7tFwjEF2t/BO---Financial-Management?node-id=35-2485) |
| Rules | R-FIN-01..R-FIN-03 _(pending BA)_ |
| Components | Payment details (`44:5627`) · Dropdown list (`1:8552`) · info blocks · money summary |
| Notes | Read-mostly summary before disbursing. 4 info blocks (ลูกค้า / สัญญา-รถ / บัญชีผู้ค้ารถ / บัญชีคอมมิชชั่น) + 2 payment breakdowns (ค่ารถ + ค่าบริการ) + green grand total (รวมยอดเงินโอนสุทธิ). Confirm which fields are editable vs read-only. |

### LEASING-FIN-02 — Disbursement Report + receipt

| Field | Value |
|---|---|
| Figma — section | [`77:5431`](https://www.figma.com/design/xByR0qZwcrRDK7tFwjEF2t/BO---Financial-Management?node-id=77-5431) |
| Figma — receipt doc | [`106:7289`](https://www.figma.com/design/xByR0qZwcrRDK7tFwjEF2t/BO---Financial-Management?node-id=106-7289) |
| Rules | R-FIN-04..R-FIN-05 _(pending BA)_ |
| Components | Financial Management-page wrapper (`43:9337`) · Payment details (report rows) · Receipt document (A4) |
| Notes | Flow: report list → detail → printable receipt (ใบเสร็จรับเงิน "ต้นฉบับ", PENTOR LEASING/CAPITAL). Rightmost = disbursement schedule table. **Needs dedicated print/PDF stylesheet** — not a normal responsive breakpoint. Confirm export format (PDF? print dialog?) with BA + FE. |

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
| 2026-05-25 | LEASING-AUTH-06 simplified — reuse-last guard removed (R-PWD-05 retired per SA), dialog `329:38735` marked do-not-implement, rules updated to R-PWD-01..03 only | SA team finalized auth — backend no longer checks against last password |
| 2026-06-05 | Added Settings area — 7 new stories (LEASING-SET-01..07) covering Product Category, Subcategories, Product Group + 3 sub-modals, Product Model, Partner Type, Partner Information (2 variants). All marked BRD-pending. | New Figma scope `4122:14114` — registered for FE planning |
| 2026-06-09 | Noted project file reorganization (Leasing-Design renamed to `BO - User & Role Permission`, 4 new per-domain files added). F-15..F-18 placeholders registered in UXUI_DESIGN — stories for these modules will be added when BA/PM shares specific screen node URLs (see I-14). | BA/PM split master into per-module files |
| 2026-06-12 | Added Loan Application area — 5 stories (LEASING-LOAN-01..05) for the role lifecycle (Marketing → Support → Analyst → Approve+Contract → Disbursement). All BRD-pending. | Loan Application content confirmed via MCP |
| 2026-07-14 | Added Financial Management area — 2 stories (LEASING-FIN-01 Disbursement, LEASING-FIN-02 Disbursement Report + receipt). Scope limited to these 2 pages per request. Flagged print/PDF stylesheet need. | New file BO - Financial Management scanned via MCP |
