# Leasing-Design — Analysis Deliverables

UX/UI analysis ของไฟล์ Figma **Leasing-Design** (Pentor Leasing BO)
สรุปจากการอ่าน Figma แบบ **read-only** ผ่าน Figma MCP — ไม่มีการแก้ไข Figma

**Figma sources (2 files):**
- Screens: [OthuQyTNoG9V5s9Z94L5O3](https://www.figma.com/design/OthuQyTNoG9V5s9Z94L5O3/Leasing-Design) — Leasing-Design
- DS: [IgbC5dmSjDDUmJgTHCr7v2](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO) — ❖ Components - Leasing Design BO

**Date:** 2026-05-20

---

## ไฟล์ในโฟลเดอร์นี้

| File | Purpose | อ่านก่อน |
|---|---|---|
| `PROS_CONS_ANALYSIS.md` | Screens file — สรุปข้อดี/ข้อเสีย 12 + 18 จุด | ⭐ **เริ่มที่นี่** |
| `COMPONENT_LIBRARY_ANALYSIS.md` | DS file — findings + 7 new issues + updates เก่า | ⭐ **อ่านต่อ** |
| `UXUI_DESIGN.md` | Master spec: screen inventory, 7-states gap, tokens summary, open questions | |
| `STORIES.md` | 4 EPICs (Auth/Customer/Application/Settings) + acceptance criteria + ⚠ marker ของ state ที่ขาด | |
| `figma-links.md` | Registry ของ Figma URL — **2 ไฟล์: Screens + DS** | |
| `design-tokens.json` | Tokens จาก Figma (verified) + proposed gap fillers | |
| `design-tokens.css` | CSS variables พร้อมใช้ — แยก `*-figma` กับ `*-proposed` | |
| `validation-uxui.md` | Sign-off template + checklist สำหรับ Designer + Open Questions tracking | |

---

## TL;DR สรุปสั้นสุด

### Pentor Leasing BO Figma มี
- ✅ Token-driven (brand red `#d82329`, Pentor Corporate font)
- ✅ 4 flow ครบ: Login, Customer, Application, Settings
- ✅ Validation specs ละเอียดมาก (อยู่ใน sticky note)
- ✅ Conditional logic ระบุครบ
- ✅ Component library พื้นฐาน

### ที่ควรปรับ
- 🔴 **States ขาด** ใน screens file — มีแต่ default
- 🔴 **DS file น่าจะยังไม่ published เป็น Figma Library** — screens file ไม่ link cross-file
- 🔴 **Navbar เป็น mobile pattern** ใน DS ที่ตั้งใจสำหรับ BO — confirm scope
- 🟡 **Typos** ใน DS — "Breadcrumbsb", "Regula"
- 🟡 **Cross-file Figma dependency** (BO Capital) — เสี่ยง broken link
- 🟡 **"ประเภทการรับเงิน" ซ้ำ** field name ใน Create Application
- 🟡 **Required `*` สีเดียวกับ error** — a11y concern

ดู full list (25+ ข้อ) ใน `PROS_CONS_ANALYSIS.md` + `COMPONENT_LIBRARY_ANALYSIS.md`

---

## Next Action

1. Review `PROS_CONS_ANALYSIS.md` กับ Designer (เจ้าของไฟล์ Figma)
2. ตอบ Open Questions ใน `validation-uxui.md §5`
3. Designer เพิ่ม states ที่ขาด ใน Figma
4. Export validation จาก sticky → JSON schema
5. ขอ sign-off จาก 4 roles ใน `validation-uxui.md §4`
