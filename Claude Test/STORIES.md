# STORIES.md — Leasing BO: Authentication & User Management

**Linked design spec:** `./UXUI_DESIGN.md`
**Linked tokens:** `./design-tokens.json` · `./design-tokens.css`
**Figma links:** `./figma-links.md`

> หลังออกแบบ Figma เสร็จ ให้แทนที่ `<FIGMA_NODE_URL>` ด้วย URL จริงจาก **Dev Mode → Copy link to selection**
> ชื่อ component ใน Figma **ต้องตรงกับ** `Component` ในแต่ละ story (ถ้าไม่ตรง dev จะ map ไม่ออก)

---

## AUTH-001: Login Page

**As a** registered user
**I want to** เข้าสู่ระบบด้วยรหัสพนักงานและรหัสผ่าน
**So that** ใช้งาน BO ตาม role ของฉันได้

### Acceptance Criteria
- [ ] กรอกรหัสพนักงาน + รหัสผ่านครบ → ปุ่ม "เข้าสู่ระบบ" enabled
- [ ] กดเข้าสู่ระบบสำเร็จ → redirect ไป BO หน้าหลักตาม role
- [ ] รหัสผ่านผิด → แสดง inline error "รหัสพนักงานหรือรหัสผ่านไม่ถูกต้อง"
- [ ] Password expired → เปิด `PasswordExpiredModal` (AUTH-003)
- [ ] ปุ่ม 👁 toggle password visibility ได้
- [ ] กรอกรหัสผ่านผิด >5 ครั้ง: ระบบยังให้กรอกต่อได้ (no lock, no warning) ตาม C-02

### Components (Figma ↔ code)
| Component | Figma node |
|---|---|
| `Page/Login` | `<FIGMA_NODE_URL>` |
| `TextField/Username` | `<FIGMA_NODE_URL>` |
| `TextField/Password` | `<FIGMA_NODE_URL>` |
| `Button/Primary` | `<FIGMA_NODE_URL>` |

### States
default · loading · error · success · disabled · responsive

### Dependencies
none

---

## AUTH-002: Set Password Page

**As a** user with temp password / expired password
**I want to** ตั้งรหัสผ่านใหม่ตาม policy
**So that** เข้าใช้งานต่อได้

### Acceptance Criteria
- [ ] แสดง real-time validation checklist 6 ข้อ (ความยาว 8–12, A-Z, a-z, 0-9, อักขระพิเศษ, ไม่ซ้ำ password เก่า)
- [ ] checklist update ทุก keystroke; ✓ เขียวเมื่อผ่าน
- [ ] 2 field "รหัสผ่านใหม่" + "ยืนยันรหัสผ่านใหม่" ต้องตรงกัน
- [ ] ปุ่ม "บันทึกรหัสผ่าน" disabled จนกว่า rule ครบ + password match
- [ ] บันทึกสำเร็จ → toast 2s "ตั้งรหัสผ่านสำเร็จ" → redirect Login (ตาม C-04)
- [ ] ถ้าใส่ password ซ้ำเก่า → rule #6 แสดง ✗ แดง
- [ ] ไม่มีปุ่ม Cancel (forced flow)

### Components
| Component | Figma node |
|---|---|
| `Page/SetPassword` | `<FIGMA_NODE_URL>` |
| `TextField/Password` | `<FIGMA_NODE_URL>` |
| `ValidationChecklist` | `<FIGMA_NODE_URL>` |
| `Toast/Success` | `<FIGMA_NODE_URL>` |

### States
default · loading · error (mismatch) · error (same as old) · success · disabled · responsive

### Dependencies
none

---

## AUTH-003: Password Expired Modal

**As a** user logging in with expired password
**I want to** เห็น modal แจ้งและไปตั้งรหัสผ่านใหม่
**So that** เข้าใช้งานต่อได้

### Acceptance Criteria
- [ ] Trigger หลังกดเข้าสู่ระบบ → backend ตอบ `password_expired`
- [ ] แสดง icon ⏰ + message + CTA "ตั้งรหัสผ่านใหม่"
- [ ] CTA → ไป `Page/SetPassword` (AUTH-002)
- [ ] ✕ ปิดได้ — กลับมาที่ Login (ยัง login ไม่ผ่าน)
- [ ] Esc ปิดได้, focus กลับไป username

### Components
| Component | Figma node |
|---|---|
| `Modal/PasswordExpired` | `<FIGMA_NODE_URL>` |
| `Button/Primary` | reuse จาก AUTH-001 |

### States
default · responsive

### Dependencies
blocks: AUTH-002 (CTA นำไป)

---

## USER-001: BO User Management List

**As a** SuperAdmin
**I want to** ดูและจัดการรายชื่อ user ในระบบ
**So that** สร้าง/แก้ไข/รีเซ็ตรหัสผ่านได้

### Acceptance Criteria
- [ ] แสดงตาราง: รหัสพนง. · ชื่อ-นามสกุล · อีเมล · Role · Status · Action(⋮)
- [ ] Pagination: 20 rows/page (default), แสดง "1-20 จาก N"
- [ ] Search field — filter ตาม รหัส/ชื่อ/อีเมล (debounce 300ms)
- [ ] Filter: Role dropdown + Status dropdown
- [ ] Row action menu (⋮): ดูรายละเอียด · แก้ไข · Reset Password · เปลี่ยน Status
- [ ] กด "+ สร้างผู้ใช้ใหม่" → เปิด `Modal/CreateUser` (USER-002)
- [ ] กด Reset Password ใน ⋮ → เปิด `Dialog/ResetPasswordConfirm` (USER-003)
- [ ] Row ที่ Status=INACTIVE → text เทาลง + badge "INACTIVE"

### Components
| Component | Figma node |
|---|---|
| `Page/UserManagement` | `<FIGMA_NODE_URL>` |
| `Table/Users` | `<FIGMA_NODE_URL>` |
| `Badge/Status` | `<FIGMA_NODE_URL>` |
| `Menu/RowAction` | `<FIGMA_NODE_URL>` |
| `Pagination` | `<FIGMA_NODE_URL>` |
| `Toolbar/SearchFilter` | `<FIGMA_NODE_URL>` |

### States
default · loading (skeleton) · empty · error · success (toast หลัง action) · disabled (row INACTIVE) · responsive

### Dependencies
blocks: USER-002, USER-003

### ⚠ Open Question
- Q-01: Admin role เห็นหน้านี้ไหม? (default: ❌ เฉพาะ SuperAdmin)

---

## USER-002: Create User Form

**As a** SuperAdmin
**I want to** สร้าง user ใหม่และส่ง temp password ให้
**So that** user สามารถ login ครั้งแรกได้

### Acceptance Criteria
- [ ] แสดงฟอร์ม modal — fields ครบ 6: ชื่อ · นามสกุล · รหัสพนักงาน · อีเมล · Role · Status
- [ ] ทุก field required
- [ ] Email validation onBlur (format + ซ้ำ)
- [ ] รหัสพนักงาน validation onBlur (ซ้ำ)
- [ ] Role dropdown: USER / Admin / SuperAdmin
- [ ] Status radio: ACTIVE (default) / INACTIVE
- [ ] ปุ่ม "สร้างผู้ใช้" disabled จนกว่า required ครบ
- [ ] สำเร็จ → backend gen temp password + send email → modal ปิด → toast "สร้างผู้ใช้สำเร็จ ส่งอีเมลให้ {email} แล้ว" → row ใหม่ highlight 2s
- [ ] ระหว่าง loading: ปิด ✕ ไม่ได้, inputs disabled
- [ ] Esc ระหว่าง default ปิดได้, default state มี confirm ก่อนปิดถ้ามี data

### Components
| Component | Figma node |
|---|---|
| `Modal/CreateUser` | `<FIGMA_NODE_URL>` |
| `TextField/*` | reuse |
| `Dropdown/Role` | `<FIGMA_NODE_URL>` |
| `RadioGroup/Status` | `<FIGMA_NODE_URL>` |
| `Button/Secondary` (ยกเลิก) | `<FIGMA_NODE_URL>` |

### States
default · loading · error (email dup, emp_id dup, email format) · success · disabled · responsive

### Dependencies
blocked by: USER-001 (เปิดจาก)
triggers: EMAIL-001 (ส่ง temp password)

---

## USER-003: Reset Password Confirmation Dialog

**As a** SuperAdmin
**I want to** reset password ของ user ที่ลืม
**So that** user ได้ temp password ใหม่ทางอีเมล

### Acceptance Criteria
- [ ] แสดง 🔑 icon + message ระบุชื่อ + รหัสพนง. + email ปลายทาง
- [ ] กด "ยืนยัน Reset" → backend gen + ส่งอีเมล
- [ ] สำเร็จ → dialog ปิด → toast "Reset password สำเร็จ ส่งอีเมลให้ {email} แล้ว"
- [ ] ระหว่าง loading: "ยกเลิก" disabled, primary spinner
- [ ] ล้มเหลว → inline error ใน dialog "ส่งอีเมลไม่สำเร็จ กรุณาลองอีกครั้ง"
- [ ] Esc ปิดได้ (default state)

### Components
| Component | Figma node |
|---|---|
| `Dialog/ResetPasswordConfirm` | `<FIGMA_NODE_URL>` |
| `Button/Primary` · `Button/Secondary` | reuse |

### States
default · loading · error · success · responsive

### Dependencies
blocked by: USER-001 (เปิดจาก row action)
triggers: EMAIL-001

---

## EMAIL-001: Temp Password Email Template

**As a** newly created or password-reset user
**I want to** ได้รับ temp password ทางอีเมล
**So that** ใช้ login ครั้งแรก/หลัง reset ได้

### Acceptance Criteria
- [ ] Trigger 2 จุด: หลัง Create User (USER-002), หลัง Reset Password (USER-003)
- [ ] Subject ต่างกันตาม trigger (ดู `UXUI_DESIGN.md` §7)
- [ ] Body มีตัวแปร: firstName, lastName, employeeId, tempPassword, loginUrl, adminEmail
- [ ] ระบุชัด: ใช้ครั้งเดียว · หมดอายุ 7 วัน · ตั้งรหัสผ่านใหม่เมื่อ login ครั้งแรก
- [ ] CTA button → loginUrl (HTTPS)
- [ ] Footer "ส่งอัตโนมัติ กรุณาอย่าตอบกลับ"
- [ ] รองรับ dark mode (mail client) — text สี dynamic

### Components (HTML email — ไม่มีใน Figma component lib)
- HTML email template — owner: Backend/DevOps
- Design reference: `UXUI_DESIGN.md` §7

### Security
- HTTPS only · SPF/DKIM/DMARC required

### Dependencies
blocked by: USER-002 / USER-003

---

## Story Dependency Graph

```
AUTH-001 (Login)
   ├── AUTH-002 (Set Password)
   │       └── AUTH-003 (Password Expired Modal) → AUTH-002
   │
USER-001 (User Mgmt List)
   ├── USER-002 (Create User) ──┐
   └── USER-003 (Reset Password)─┴── EMAIL-001 (Email)
```

## Suggested Sprint Split
| Sprint | Stories |
|---|---|
| Sprint 1 | AUTH-001, AUTH-002, AUTH-003 (auth flow ครบ) |
| Sprint 2 | USER-001, USER-002, USER-003 (admin tools) |
| Sprint 2 | EMAIL-001 (parallel with USER-002/003) |

---

## Handoff Checklist (ทำก่อนปิด ticket)
- [ ] ทุก `<FIGMA_NODE_URL>` แทนที่ด้วย URL จริง (จาก Dev Mode)
- [ ] Component name ใน Figma ตรงกับชื่อในตาราง Components
- [ ] Tokens export ตรงกับ `design-tokens.json`
- [ ] Acceptance criteria ทุกข้อมี assertion ใน QA test plan
- [ ] Email template (EMAIL-001) ส่งให้ backend dev ที่ implement
- [ ] Open Questions ใน UXUI_DESIGN.md §10 ตอบครบก่อน sprint start
