# STORIES.md — Leasing BO

**Source:** Figma `OthuQyTNoG9V5s9Z94L5O3` — Leasing-Design (read-only analyze)
**Linked:** `./UXUI_DESIGN.md` · `./PROS_CONS_ANALYSIS.md` · `./figma-links.md`

> Stories ต่อไปนี้สรุปจาก Figma ที่มีอยู่ + เพิ่ม acceptance criteria + state gap ที่ต้องเติม
> Acceptance criteria ที่ขึ้นต้นด้วย ⚠ = ยังไม่ครบใน Figma → ต้องให้ Designer เพิ่ม

---

## EPIC 1 · Authentication (Page: Login & Logout)

### AUTH-001 · Login (3 variations)

**As a** ผู้ใช้งาน BO
**I want to** Login ด้วยรหัสพนักงาน + รหัสผ่าน
**So that** เข้าใช้งานระบบ Leasing BO

#### Acceptance Criteria
- [x] กรอกรหัสพนักงาน + รหัสผ่าน → ปุ่ม "เข้าสู่ระบบ" enabled
- [x] รองรับ language toggle ไทย/อังกฤษ (dropdown มุมขวาบน)
- [x] รหัสผ่านมี visibility toggle (👁)
- [x] แสดง version footer (เช่น `V. 0.1.4`)
- [x] มี 3 variations (Component / Component 2 / Component 3) — ต้อง confirm ว่าใช้ทำอะไร (default / error / disabled?)
- [ ] ⚠ State: loading
- [ ] ⚠ State: error (รหัสผ่านผิด)
- [ ] ⚠ State: success → redirect ไป BO หน้าหลัก
- [ ] ⚠ Failed login behavior (lock / warning / unlimited)

#### Figma references
ดู `figma-links.md` → AUTH-001 entries

---

### AUTH-002 · New Password (3 variations)

**As a** ผู้ใช้ที่ login ครั้งแรก / รหัสผ่านหมดอายุ
**I want to** ตั้งรหัสผ่านใหม่
**So that** เข้าใช้งานต่อได้

#### Acceptance Criteria
- [x] มี form ตั้งรหัสผ่านใหม่ (3 variations)
- [ ] ⚠ ตรวจสอบกับ Password policy (ความยาว, charset) — ดู Figma sticky note
- [ ] ⚠ State: loading
- [ ] ⚠ State: error (mismatch / not match policy)
- [ ] ⚠ State: success → redirect ไป Login หรือ BO

---

## EPIC 2 · Customer Information (Page: Customer information)

### CUST-001 · Search Customer

**As a** Sale / Officer
**I want to** ค้นหาลูกค้าจากเลขบัตรประชาชน
**So that** ตรวจว่ามีในระบบหรือยัง ก่อนสร้างใหม่

#### Acceptance Criteria
- [x] ช่อง Search + label
- [x] Pop-up "พบข้อมูล" / "ไม่พบข้อมูล" ตามผล
- [x] Decision tree: เจอข้อมูล → ดู/แก้ไข, ไม่เจอ → Create
- [ ] ⚠ State: loading (กำลังค้น)
- [ ] ⚠ State: empty (ยังไม่ค้น)
- [ ] ⚠ State: error (API fail)

---

### CUST-002 · Create Customer Information

**As a** Officer
**I want to** สร้างข้อมูลลูกค้าใหม่
**So that** มีข้อมูลพื้นฐานก่อนสร้างใบคำขอ

#### Acceptance Criteria
- [x] ครอบคลุม section: Personal info, Address (3 ที่อยู่), Work info, Bank info, Contact, Emergency contact, Other info, Sending documents
- [x] Cross-field rules ระบุครบ (สถานภาพ → คู่สมรส, สัญชาติ → ระบุสัญชาติ, อาชีพ → ระบุอาชีพ, ที่อยู่อาศัย → จำนวนเงิน/ค่าเช่า)
- [x] Validation specs ระบุครบ (Required/Optional, length, regex, charset, error msg) — อยู่ใน Figma sticky
- [x] Required `*` แสดงทุก field
- [x] Conditional show/hide ระบุ
- [ ] ⚠ State: loading (submit)
- [ ] ⚠ State: error (validation fail / API fail)
- [ ] ⚠ State: success (create สำเร็จ → ทำอะไรต่อ?)
- [ ] ⚠ Field a11y notes (aria-required, error live region)
- [ ] ⚠ Validation export เป็น JSON schema (currently ติดอยู่ใน sticky note)

---

### CUST-003 · Edit Customer Information

**As a** Officer
**I want to** แก้ไขข้อมูลลูกค้าที่มีอยู่
**So that** อัปเดต field ที่เปลี่ยน

#### Acceptance Criteria
- [x] field "เลขบัตรประชาชน" disabled (ห้ามแก้)
- [x] field อื่นๆ edit ได้ตาม validation เดียวกับ Create
- [ ] ⚠ Confirm dialog ก่อน save changes
- [ ] ⚠ State: loading, error, success

---

### CUST-004 · Confirm Customer Information

**As a** Officer
**I want to** ตรวจสอบและยืนยันข้อมูล
**So that** ลดการพิมพ์ผิดก่อน submit

#### Acceptance Criteria
- [x] หน้า Confirm info แสดง summary
- [ ] ⚠ ปุ่ม "แก้ไข" / "ยืนยัน"
- [ ] ⚠ State variations

---

## EPIC 3 · Loan Application (Page: Create application)

### APP-001 · ข้อมูลใบคำขอ + คู่ค้า

**As a** Officer
**I want to** เริ่มสร้างใบคำขอสินเชื่อ
**So that** บันทึก context การเปิด lead (สาขา, ผู้ส่ง, คู่ค้า, ตลาดรถ)

#### Acceptance Criteria
- [x] field: สาขาที่สร้างใบคำขอ (default: สำนักงานใหญ่), วันที่ (auto = วันนี้, BE), เลขที่ใบคำขอ (auto-gen)
- [x] ผู้ส่งใบคำขอ (dropdown)
- [x] คู่ค้า: ตลาดรถ, รหัสตลาดรถ, อนุมัติจ่ายค่าบริการ %, ผู้รับค่ารถ/ขายรถ, ประเภทการรับเงิน × 2, ผู้รับค่าบริการ, ระยะทางจากดีลเลอร์ถึงบ้านลูกค้า (km)
- [ ] ⚠ "ประเภทการรับเงิน" ซ้ำ 2 fields — rename เพื่อแยก
- [ ] ⚠ Placeholder "0" → ใช้ "ระบุจำนวน" หรือ "0.00"
- [ ] ⚠ Date storage format: BE หรือ CE? (Open Question Q-02)

---

### APP-002 · ข้อมูลลูกค้า + คู่สมรส

**Acceptance:** copy/reuse จาก CUST-002 sections — Personal info, สถานภาพ, คู่สมรส (conditional)

---

### APP-003 · ที่อยู่ (3 รายการ)

**Acceptance:**
- [x] ที่อยู่ตามบัตร, ตามทะเบียนบ้าน, ปัจจุบัน
- [x] Checkbox copy address — sync field อัตโนมัติ
- [x] Validate ที่อยู่อาศัย → conditional show "จำนวนเงินที่ผ่อน" / "ค่าเช่า" / "ระบุสถานะที่อยู่อาศัย"

---

### APP-004 · ผู้ค้ำประกัน (Guarantor)

**Acceptance:**
- [x] Radio: ไม่มีผู้ค้ำประกัน / มีผู้ค้ำประกัน
- [x] กรณีมี → fields เปิด: ชื่อ-นามสกุล, ความสัมพันธ์, เบอร์โทร, สำรอง
- [ ] ⚠ ระบุได้มากกว่า 1 คนหรือไม่? (Q-05)

---

### APP-005 · ที่อยู่ติดต่อกลับ (Emergency Contact)

**Acceptance:**
- [x] Radio + ปุ่ม "+ เพิ่ม"
- [ ] ⚠ จำกัดสูงสุดกี่คน

---

### APP-006 · Document Upload (Document section ×15)

**Acceptance:**
- [x] 15 ประเภทเอกสาร (Document section × 15)
- [x] Validation: เอกสารยืนยันตัวตน ≥1 ไฟล์, เดินบัญชี ≥1 ไฟล์, อื่นๆ ไม่บังคับ
- [x] Sticky note ระบุ: ปุ่ม "ส่งต่อใบคำขอ" เปิดเมื่อกรอก/อัปโหลดครบ
- [ ] ⚠ State: file uploading (progress)
- [ ] ⚠ State: upload error (size / format)
- [ ] ⚠ Scroll experience — 15 sections ยาวมาก ต้อง collapsible + progress indicator (ดู W-13)
- [ ] ⚠ Q-05: ทั้ง 15 ประเภท mandatory ทั้งหมดหรือไม่

---

### APP-007 · Rejection Reason

**Acceptance:**
- [x] Radio + text area เหตุผลปฏิเสธ
- [x] ใช้เมื่อกด "ปฏิเสธใบคำขอ"

---

### APP-008 · Application Actions (ปุ่มการกระทำ)

**4 actions ตาม Figma sticky:**
| ปุ่ม | เงื่อนไขแสดง | เงื่อนไขกด | Cross-file dialog |
|---|---|---|---|
| ส่งต่อใบคำขอ | กรอกครบ + เอกสารครบ + ตรวจสอบ "ครบถ้วน" | confirm dialog | — |
| ส่งกลับฝ่ายขาย | ตรวจสอบเลือก "ไม่ครบถ้วน" | confirm | — |
| ปฏิเสธใบคำขอ | แสดงตลอด | Dialog → cross-file | BO Capital `1002-142682` |
| บันทึกร่าง | แสดงตลอด | Alert → cross-file | BO Capital `566-127566` |
| ยกเลิก | แสดงตลอด | Dialog (if มี data) → cross-file | BO Capital `300-116567` |

- [ ] ⚠ Cross-file dialogs ต้อง merge หรือ duplicate มาในไฟล์เดียว (W-05)

---

## EPIC 4 · Settings (Master Data)

### SET-001 · Asset Category (CRUD)

**Acceptance:**
- [x] หน้า list + Search Filter (Auto-complete dropdown, date single, status tri-state)
- [x] ปุ่ม Create / Edit / Delete
- [x] popup Create ประเภทสินค้าใหม่ พร้อม validation (ซ้ำ-ไม่ซ้ำ)
- [ ] ⚠ State: empty (ยังไม่มี item)
- [ ] ⚠ Confirm dialog ก่อนลบ

### SET-002 · Asset Group (CRUD)
**Acceptance:** เช่นเดียวกับ SET-001

### SET-003 · Asset Type (CRUD)
**Acceptance:** เช่นเดียวกับ SET-001 — รวม "popup House price assessment"

### SET-004 · Partners Information
**Acceptance:** master data ของคู่ค้า — link กับ "ตลาดรถ" ใน APP-001

### SET-005 · Vehicle Information
**Acceptance:** master data ของยานพาหนะ

---

## Story Dependency Graph

```
AUTH-001 ──→ AUTH-002 ──→ (Login success → BO)
                              │
                              ├── CUST-001 (Search) ──→ CUST-002 / CUST-003
                              │                          └── CUST-004 (Confirm)
                              │
                              └── APP-001..008 (Create application)
                                      │
                                      └── SET-001..005 (master data dependencies)
```

## Sprint Split Suggestion

| Sprint | Stories | เหตุผล |
|---|---|---|
| 1 | AUTH-001, AUTH-002 | unblock เข้าระบบ |
| 2 | CUST-001..004 | customer 360 — base data |
| 3 | SET-001..005 | master data ต้องมาก่อน Application |
| 4–5 | APP-001..008 | flagship feature |

---

## Handoff Checklist

- [ ] Designer เพิ่ม states ที่ ⚠ marker
- [ ] Designer rename frame ที่ generic name → semantic
- [ ] ผู้รับผิดชอบตอบ Open Questions ใน `UXUI_DESIGN.md §7`
- [ ] Export validation จาก sticky → JSON schema
- [ ] ตัดสินเรื่อง cross-file dialog (W-05) — merge หรือ keep
- [ ] Date storage format ตัดสิน (Q-02)
