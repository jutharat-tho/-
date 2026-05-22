# UXUI_DESIGN.md — Leasing Design BO (Pentor Leasing)

**Status:** `draft`
**Version:** 0.1 (analyze-only — based on existing Figma `OthuQyTNoG9V5s9Z94L5O3`)
**Owner:** UX/UI
**Date:** 2026-05-19
**Approvers needed:** BA, PM, Frontend Dev Lead
**Related:** `./PROS_CONS_ANALYSIS.md`, `./STORIES.md`, `./design-tokens.json`, `./figma-links.md`, `./validation-uxui.md`

---

## 1. Scope

วิเคราะห์ + จัดทำเอกสาร workflow สำหรับ **Pentor Leasing Back Office**
รวม 4 flow หลัก:
1. **Login & Logout** (auth)
2. **Customer information** (CRUD ข้อมูลลูกค้า)
3. **Create application** (สร้างใบคำขอสินเชื่อ)
4. **Settings** (master data: Asset Category / Group / Type / Partners / Vehicle Info)

> เอกสารนี้สรุปจากไฟล์ Figma ที่มีอยู่แล้ว — UX/UI **ไม่ได้แก้ Figma** เพียงแค่ analyze + จัดเอกสาร

---

## 2. Findings Summary

| ด้าน | สถานะ | รายละเอียด |
|---|---|---|
| **Design Tokens** | ✅ มี | brand `#d82329`, font Pentor Corporate, spacing 4-grid |
| **Component library** | 🟡 พอใช้ | มี `Buttons/Basic`, `Text field`, `Radio`, `h2` แต่ frame name ปะปน |
| **Validation specs** | ✅ ครบ (ใน sticky) | ทุก field มี Required/Optional, length, regex, error msg |
| **Conditional logic** | ✅ ระบุ | "เมื่อเลือก X แสดง Y" คลุมทุก field พิเศษ |
| **7-states coverage** | 🔴 ไม่ครบ | เห็นแค่ default state เป็นส่วนใหญ่ |
| **Accessibility notes** | 🔴 ไม่มี | ไม่เห็น aria-*, focus order, contrast notes |
| **Responsive** | ❓ ไม่ชัด | desktop 1440×1024 เท่านั้น — ยังไม่มี mobile |
| **Documentation hygiene** | 🟡 พอใช้ | mix Figma sticky + cross-file reference — ควรย้ายไป Confluence |

> รายละเอียด pros/cons เต็มดูได้ที่ `./PROS_CONS_ANALYSIS.md`

---

## 3. Screen Inventory (จาก Figma)

| # | Screen | Page ใน Figma | Type |
|---|---|---|---|
| 1 | Login (3 variations) | Login & Logout | page |
| 2 | New Password (3 variations) | Login & Logout | page |
| 3 | Customer information — Create | Customer information | page |
| 4 | Customer information — Edit | Customer information | page |
| 5 | Customer information — Search | Customer information | page |
| 6 | Customer information — Confirm info | Customer information | page |
| 7 | Customer information — popup (results, etc.) | Customer information | modals |
| 8 | Create application — ข้อมูลใบคำขอ | Create application | section |
| 9 | Create application — ข้อมูลลูกค้า / คู่สมรส | Create application | section |
| 10 | Create application — ที่อยู่ | Create application | section |
| 11 | Create application — รายละเอียดผู้ค้ำประกัน | Create application | section |
| 12 | Create application — ที่อยู่ติดต่อกลับ | Create application | section |
| 13 | Create application — Document upload (×15 sections) | Create application | section |
| 14 | Create application — Rejection reason | Create application | section |
| 15 | Create application — popup | Create application | modal |
| 16 | Settings — Asset Category (CRUD + Search Filter) | Settings | page |
| 17 | Settings — Asset Group | Settings | page |
| 18 | Settings — Asset Type | Settings | page |
| 19 | Settings — Partners Information | Settings | page |
| 20 | Settings — Vehicle Information | Settings | page |
| 21 | Settings — popups (Add document, Add ประเภทสินค้า, House price) | Settings | modals |

**Total:** ~21 distinct screen contexts + several popups

---

## 4. 7-States Gap Analysis

ที่ Figma มี vs ที่ workflow ต้องการ:

| Screen | default | loading | empty | error | success | disabled | responsive |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| Login | ✅ (3 variants) | ❓ | — | ❓ | ❓ | ❓ | ❌ |
| New Password | ✅ (3 variants) | ❓ | — | ❓ | ❓ | ❓ | ❌ |
| Customer info — Create | ✅ | ❓ | — | ❓ (รู้ field error) | ❓ | ❓ | ❌ |
| Customer info — Search | ✅ | ❓ | ❓ | ❓ | ✅ (popup-results) | ❓ | ❌ |
| Create application | ✅ | ❓ | — | ❓ | ❓ | ❓ | ❌ |
| Settings — list views | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❌ |

✅ = มีใน Figma · ❓ = ยังไม่ได้ตรวจ/ไม่ชัด · ❌ = ไม่มี · — = N/A

**Action:** ต้องเพิ่ม **error / loading / empty / responsive** อย่างน้อยให้ทุก critical screen

---

## 5. Design Tokens (จาก Figma)

ดูเต็มที่ `./design-tokens.json` + `./design-tokens.css`

### Quick reference
```
Brand:       #d82329 (Pentor red)
Text:        primary #18181b · secondary icon #71717a
Background:  #ffffff
Font:        Pentor Corporate (Regular 400, SemiBold 600)
Body M:      16px / line-height 1.3
Spacing:     4 / 16 / 24 / 32 (sparse — เพิ่ม 8, 12, 20 จะดีขึ้น)
Radius:      0 (sharp) — แนะนำเพิ่ม sm=4 สำหรับ input
Stroke:      4
```

### ⚠ Token gaps ที่แนะนำเพิ่ม
- **Semantic colors**: success, warning, error, info — Figma ยังไม่มี
- **Neutral scale**: ตอนนี้มีแค่ #18181b, #71717a — ขาด 100/200/300 สำหรับ border/background
- **Spacing 8, 12, 20** — เพิ่มความยืดหยุ่น
- **Elevation/Shadow** — ยังไม่มี (modal, dropdown ใช้อะไร?)

---

## 6. Critical Findings ที่ต้อง resolve

### 🔴 ก่อน sprint ถัดไป
1. **States ขาด** — ทุก critical screen ต้องครบ 7 states
2. **"ประเภทการรับเงิน" ซ้ำชื่อ 2 fields** ใน Create application — rename
3. **Required `*` สีเดียวกับ error** — a11y ปัญหา
4. **Date BE 2569 vs CE** — store format ต้องระบุ
5. **Document section ×15** scroll ยาว → ต้องมี collapsible/progress

### 🟡 ภายใน 1 quarter
6. รวม Design System เป็น page เดียว
7. Frame naming cleanup (rename "Frame", "Frame 25", "Frame 31" ให้ semantic)
8. ย้าย sticky note ใหญ่ไป Confluence
9. Code Connect setup
10. Responsive scope confirm

### 🟢 ระยะยาว
11. ทำ component documentation ครบ
12. ทำ a11y guideline ของ project
13. Export validation เป็น JSON schema

---

## 7. Open Questions

| # | Question | Owner | Status |
|---|---|---|---|
| Q-01 | รองรับ mobile/tablet มั้ย? | PM | open |
| Q-02 | Date storage: BE หรือ CE? | Backend Lead | open |
| Q-03 | "ประเภทการรับเงิน" 2 fields ต่างกันยังไง? | BA | open |
| Q-04 | Cross-file Figma reference (BO Capital) จะ merge หรือ keep แยก? | Design Lead | open |
| Q-05 | Document section ทั้ง 15 ประเภทเป็น mandatory ทั้งหมดมั้ย? | BA | open |
| Q-06 | Failed Login Behavior (lock/warning) ในไฟล์นี้คืออะไร? | BA | open |

---

## 8. Validation Sign-off

ดู `./validation-uxui.md`

## 9. Next Steps

1. Review เอกสารชุดนี้กับ Designer ที่เป็นเจ้าของไฟล์ Figma
2. ตอบ Open Questions
3. Designer **เพิ่ม states ที่ขาด** ใน Figma (UX/UI ไม่แก้ Figma แทน)
4. Export validation specs จาก sticky → JSON
5. Setup Code Connect mapping
