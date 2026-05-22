# UXUI_DESIGN.md — Leasing BO: Authentication & User Management

**Status:** `draft`
**Version:** 0.1
**Owner:** UX/UI
**Date:** 2026-05-19
**Approvers needed:** BA, PM, Frontend Dev Lead

---

## 1. Scope

ออกแบบ Authentication + User Management สำหรับ **Leasing Back Office** ตาม BRD ที่ได้รับ
- Authentication flows (Login, First login, Forgot password via admin, Password expired)
- Super Admin: Create User + Reset Password
- Password policy + session policy

---

## 2. BRD Conflict Resolution Log

ระหว่างทำ Phase 1 พบ 4 จุดที่ต้อง confirm กับ PO — ผลลัพธ์ดังนี้

| # | BRD เดิม | Decision สุดท้าย | สถานะ |
|---|---|---|---|
| C-01 | "ก่อน Auto Logout ระบบแสดง Warning Modal" | **ไม่มี modal** — auto logout เงียบๆ | confirmed by PO ⚠ UX flag |
| C-02 | "Password ผิด >5 ครั้ง แสดง warning + บันทึก count" | **ไม่มี warning, ไม่ lock** | confirmed by PO ⚠ Security flag |
| C-03 | Forgot password flow | **ไม่มี link** บน Login → user ติดต่อ admin | confirmed |
| C-04 | First login → Set Password → กลับ Login | **คงไว้** ตาม BRD (ไม่ auto-login) | confirmed |

> ⚠️ **UX recommends** ทบทวน C-01 (เสียข้อมูลตอน form ยาว) และ C-02 (brute force risk) กับ stakeholder ก่อน sign-off สุดท้าย

---

## 3. Functional Requirements (จาก BRD)

### 3.1 Password Policy
- ความยาว: **8–12 ตัวอักษร**
- ต้องมี: ≥1 ตัวพิมพ์ใหญ่ [A-Z], ≥1 ตัวพิมพ์เล็ก [a-z], ≥1 ตัวเลข [0-9], ≥1 อักขระพิเศษ (`. ! @ # $ % ^ * _ - +`)
- ห้ามซ้ำ Password ล่าสุด 1 ตัว
- อายุ Password: **3 เดือน** นับจากเปลี่ยนล่าสุด

### 3.2 Temp Password
- หมดอายุ **7 วัน**, ใช้ได้ **1 ครั้ง**
- บังคับ Set Password เมื่อ login ด้วย Temp Password
- หมดอายุ → ติดต่อ Super admin

### 3.3 Session
- Auto Logout เมื่อ **inactive 30 นาที** (ไม่มี warning ตาม C-01)
- Logout → clear session + tokens ทั้งหมด

### 3.4 User Model
| Field | ค่า / Constraint |
|---|---|
| ชื่อ | required |
| นามสกุล | required |
| Email | required, unique, valid format |
| รหัสพนักงาน (= Username) | required, unique |
| Role | `USER` / `Admin` / `SuperAdmin` |
| Status | `ACTIVE` / `INACTIVE` |

---

## 4. Screen Inventory

| # | Screen | Type | ผู้ใช้ |
|---|---|---|---|
| 1 | Login Page | page | ทุก role |
| 2 | Set Password Page | page | First login + Password expired |
| 3 | Password Expired Modal | modal บน Login | ทุก role |
| 4 | BO: User Management List | page | SuperAdmin (Admin? — ขอ confirm) |
| 5 | Create User Form | modal | SuperAdmin |
| 6 | Reset Password Confirmation | dialog | SuperAdmin |
| — | Email Template (Temp Password) | non-UI | — |

---

## 5. Screen × States Matrix

| Screen | default | loading | empty | error | success | disabled | responsive |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| Login | ✓ | ✓ | — | ✓ | ✓ | ✓ | ✓ |
| Set Password | ✓ | ✓ | — | ✓ | ✓ | ✓ | ✓ |
| Password Expired Modal | ✓ | — | — | — | — | — | ✓ |
| User Management List | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Create User Form | ✓ | ✓ | — | ✓ | ✓ | ✓ | ✓ |
| Reset Password Confirm | ✓ | ✓ | — | ✓ | ✓ | — | ✓ |

---

## 6. Wireframes & Specs

### 6.1 Login Page

**Components:** `TextField/Username`, `TextField/Password` (มี visibility toggle), `Button/Primary`

**States:**
- `default`: 2 fields ว่าง, ปุ่ม disabled
- `loading`: spinner + "กำลังเข้าสู่ระบบ..." inputs disabled
- `error`: inline error "รหัสพนักงานหรือรหัสผ่านไม่ถูกต้อง"
- `success`: spinner สั้น → redirect BO ตาม role
- `disabled`: ปุ่ม disabled จน field ครบ
- `responsive`: mobile card padding 16px

**Accessibility:** label–input association, error `aria-live="polite"`, password toggle `aria-label`

---

### 6.2 Set Password Page

**Components:** `TextField/Password` × 2 (รหัสผ่านใหม่ + ยืนยัน), `ValidationChecklist` (6 rules), `Button/Primary`

**Real-time validation checklist** (update ทุก keystroke):
1. ความยาว 8-12 ตัวอักษร
2. ตัวพิมพ์ใหญ่ ≥1 (A-Z)
3. ตัวพิมพ์เล็ก ≥1 (a-z)
4. ตัวเลข ≥1 (0-9)
5. อักขระพิเศษ ≥1 (`. ! @ # $ % ^ * _ - +`)
6. ไม่ซ้ำกับรหัสผ่านล่าสุด

**State icons:** ○ เทา = ยังไม่ผ่าน · ✓ เขียว = ผ่าน · ✗ แดง = ไม่ผ่าน (เช่นซ้ำ password เก่า)

**States:**
- `default`: ฟอร์มว่าง, checklist ○ ทั้งหมด, ปุ่ม disabled
- `loading`: spinner + "กำลังบันทึก..."
- `error: mismatch`: ใต้ field ยืนยัน "รหัสผ่านทั้งสองไม่ตรงกัน"
- `error: same as old`: rule #6 → ✗ แดง
- `success`: toast 2s "ตั้งรหัสผ่านสำเร็จ" → redirect Login
- `disabled`: จน checklist ครบ + 2 password ตรงกัน

---

### 6.3 Password Expired Modal

Trigger: backend ตอบ password expired หลังกด login

**Content:** ⏰ icon + "รหัสผ่านของคุณหมดอายุแล้ว กรุณาตั้งรหัสผ่านใหม่" + CTA "ตั้งรหัสผ่านใหม่" → ไป Set Password Page
**Close (✕):** ปิดได้ แต่ login ไม่ผ่าน user อยู่ที่ Login Page เฉยๆ

---

### 6.4 BO User Management List

**Layout:** Top nav (logo + notification + profile menu) + Left sidebar + Main content
**Main content:**
- Page title "จัดการผู้ใช้งาน" + CTA "+ สร้างผู้ใช้ใหม่" (top right)
- Filter bar: search input + Role dropdown + Status dropdown
- Table columns: รหัสพนง. | ชื่อ-นามสกุล | อีเมล | Role | Status | Action (⋮)
- Pagination: page nav + total count

**Row action menu (⋮):**
- ดูรายละเอียด
- แก้ไข
- Reset Password → เปิด Reset Password Confirm
- เปลี่ยน Status

**States:**
- `default`: ตาราง + pagination
- `loading`: skeleton 10 rows
- `empty`: illustration + "ยังไม่มี user ในระบบ" + CTA
- `error`: "โหลดข้อมูลไม่สำเร็จ" + ปุ่ม "ลองอีกครั้ง"
- `success`: toast หลัง action (create/reset/status change)
- `disabled`: row INACTIVE เทาลง + badge

---

### 6.5 Create User Form (Modal)

**Fields (required ทั้งหมด):**
1. ชื่อ
2. นามสกุล
3. รหัสพนักงาน (unique, validate onBlur)
4. อีเมล (unique, format, validate onBlur — แสดง hint "Temp Password จะถูกส่งไปอีเมลนี้")
5. Role — dropdown: USER / Admin / SuperAdmin
6. Status — radio: ACTIVE (default) / INACTIVE

**Actions:** ยกเลิก / สร้างผู้ใช้ (primary, disabled จน field ครบ)

**States:**
- `default`: ฟอร์มว่าง, primary disabled
- `loading`: ปุ่ม spinner "กำลังสร้าง...", ปิดไม่ได้
- `error: email duplicate`: inline ใต้ email
- `error: emp_id duplicate`: inline ใต้ รหัสพนักงาน
- `error: email format`: inline (onBlur)
- `success`: modal ปิด → toast "สร้างผู้ใช้สำเร็จ ส่งอีเมลให้ {email} แล้ว" + highlight row ใหม่ในตาราง 2s

---

### 6.6 Reset Password Confirmation Dialog

**Content:** 🔑 icon + "ต้องการ Reset Password ของ {firstName} {lastName} ({empId}) หรือไม่?" + "ระบบจะส่ง Temporary Password ไปยัง {email}"
**Actions:** ยกเลิก / ยืนยัน Reset (primary)

**States:**
- `default`: 2 ปุ่ม
- `loading`: primary spinner, ยกเลิก disabled
- `error`: inline "ส่งอีเมลไม่สำเร็จ กรุณาลองอีกครั้ง"
- `success`: dialog ปิด → toast "Reset password สำเร็จ ส่งอีเมลให้ {email} แล้ว"

---

## 7. Email Template — Temp Password

**Used in:** Create User (A), Reset Password (B)

**Subject:**
- (A) `[Leasing BO] บัญชีของคุณถูกสร้างแล้ว — รหัสผ่านชั่วคราว`
- (B) `[Leasing BO] รีเซ็ตรหัสผ่าน — รหัสผ่านชั่วคราวใหม่`

**From:** `no-reply@{company-domain}` · display name `Leasing BO`

**Body variables:** `{firstName}`, `{lastName}`, `{employeeId}`, `{tempPassword}`, `{loginUrl}`, `{adminEmail}`

**Body content:**
- สวัสดี + ข้อความสถานการณ์ (created / reset)
- รหัสพนักงาน + รหัสผ่านชั่วคราว
- คำเตือน: ใช้ครั้งเดียว · หมดอายุ 7 วัน · ต้องตั้งรหัสใหม่ครั้งแรกที่ login
- CTA: "เข้าสู่ระบบ Leasing BO" → `{loginUrl}`
- Footer: "ส่งอัตโนมัติ กรุณาอย่าตอบกลับ"

**Security flagged:**
- ส่ง plain temp password — ต้อง HTTPS only
- SPF/DKIM/DMARC ต้องตั้งให้ครบ

---

## 8. Design Tokens (Proposed Defaults)

> ⚠️ Baseline professional fintech — ปรับได้ตาม brand ตอน high-fi

### Color
```
primary:   500 #3B82F6 · 600 #2563EB · 700 #1D4ED8
neutral:   0  #FFF · 50 #F9FAFB · 100 #F3F4F6 · 200 #E5E7EB · 300 #D1D5DB · 400 #9CA3AF · 500 #6B7280 · 700 #374151 · 900 #111827
semantic:  success #10B981 · warning #F59E0B · error #EF4444 · info #3B82F6
```

### Typography
```
font-family-base: 'IBM Plex Sans Thai', 'Sarabun', sans-serif
text-xs   12/16   helper
text-sm   14/20   body small, table
text-base 16/24   body, input
text-lg   18/28   subtitle
text-xl   20/28   card title
text-2xl  24/32   page title
text-3xl  30/36   hero
weights:  regular 400 · medium 500 · semibold 600 · bold 700
```

### Spacing (4px grid)
```
1=4 · 2=8 · 3=12 · 4=16 · 5=20 · 6=24 · 8=32 · 10=40 · 12=48
```

### Radius
```
sm=4 (input, badge) · md=8 (card, modal) · lg=12 (hero) · full=9999 (pill)
```

### Elevation
```
sm = 0 1 2 rgba(0,0,0,.05)
md = 0 4 6 rgba(0,0,0,.07) + 0 2 4 rgba(0,0,0,.06)
lg = 0 10 15 rgba(0,0,0,.10)   (modal)
```

### Component Tokens
| Component | Token |
|---|---|
| Input border | `neutral-300`, focus `primary-500` (2px ring) |
| Input padding | y `space-3`, x `space-4` |
| Button height | medium 40 / large 48 |
| Button primary bg | `primary-500` → hover `primary-600` → disabled `neutral-200` (text `neutral-400`) |
| Modal max-width | 480 (form) · 400 (confirm dialog) |
| Toast | bg `neutral-900` text `neutral-0` radius `md` shadow `lg` |

---

## 9. Accessibility Notes

- ทุก input ต้อง associate กับ label
- Error messages ใช้ `aria-live="polite"`
- Color contrast: text ต่อ background ≥ 4.5:1 (WCAG AA)
- Focus state มองเห็นได้ (2px ring สี `primary-500`)
- Password visibility toggle มี `aria-label`
- Modal: trap focus + ปิดด้วย Esc + restore focus ตอนปิด

---

## 10. Open Questions

| # | Question | Owner | Status |
|---|---|---|---|
| Q-01 | Admin role เห็นหน้า User Management ไหม หรือเฉพาะ SuperAdmin? | BA | open |
| Q-02 | รองรับมือถือ scope ไหม (BO ส่วนใหญ่ desktop) | PM | open |
| Q-03 | Email language: ไทยอย่างเดียว หรือ bilingual? | BA | open |
| Q-04 | Login url ใน email link ไปแบบ deep link prefill หรือเปล่า | Frontend Lead | open |
| Q-05 | Backend rate limit / CAPTCHA แม้ UI ไม่โชว์ warning (C-02) | Backend Lead | open |

---

## 11. Validation Sign-off

| Role | Name | Signed | Date | Notes |
|---|---|:-:|---|---|
| BA |  | ☐ |  |  |
| PM |  | ☐ |  |  |
| Frontend Dev Lead |  | ☐ |  |  |
| UX (self) |  | ☐ |  |  |

---

## 12. Next Steps

1. ส่ง doc นี้ให้ BA/PM/Frontend Lead review → ตอบ Open Questions
2. Resolve Open Questions → update doc
3. ย้ายไปทำ **Figma high-fidelity** (apply tokens) — sourced จาก wireframe section 6
4. เปิด **Dev Mode** + export tokens → handoff ใส่ STORIES.md
