# validation-uxui.md — Leasing Design BO

**Feature scope:** Authentication + Customer + Application + Settings
**Source Figma:** `OthuQyTNoG9V5s9Z94L5O3` Leasing-Design (read-only analyze)
**Related:** `./UXUI_DESIGN.md` · `./PROS_CONS_ANALYSIS.md` · `./STORIES.md`
**Status:** `draft` → `in_review` → `approved` → `final`
**Current status:** `draft`

---

## 1. Document Type

> นี่คือ **analysis + workflow consolidation** จาก Figma ที่มีอยู่
> ไม่ใช่การออกแบบใหม่ — UX/UI ไม่ได้แก้ Figma
> เอกสารชุดนี้เพื่อให้ทีมเห็น gap + จัดลำดับ Fix

---

## 2. Pre-Review Checklist (UX self-check)

ที่ทำเสร็จจาก Figma analysis:
- [x] อ่าน file structure ทั้งหมด (top-level pages)
- [x] Sample tokens จาก Figma ผ่าน get_variable_defs
- [x] Sample screenshot — Cover, Login, Create Application (sections)
- [x] Sample metadata — Login & Logout, Create application, Customer information, Settings
- [x] บันทึก validation specs ที่เจอใน Figma sticky
- [x] บันทึก cross-file references
- [x] สรุปข้อดี/ข้อเสีย (12 จุดดี / 18 จุดควรปรับ)
- [x] เขียน Stories ครบ 4 EPICs

ที่ยังไม่ได้ตรวจสอบ (transparency — ผมบอก user ใน PROS_CONS_ANALYSIS §6):
- [ ] ทุก frame ใน Customer information page (>120k chars)
- [ ] BA Flow page
- [ ] ทุก variation ของ Login (เปิดแค่ Component 1)
- [ ] popup frames เต็มทุกตัว
- [ ] validation sticky note แบบเต็ม

## 3. Pre-Handoff Checklist (Designer ต้องทำ)

- [ ] เพิ่ม **states ที่ขาด** (loading/error/empty/responsive) ใน Figma — ทุก ⚠ ใน STORIES.md
- [ ] **Rename frames** generic ("Frame", "Frame 25") → semantic
- [ ] สร้าง **"00 Design System"** page + รวม tokens
- [ ] เพิ่ม **emoji prefix** หน้า page name
- [ ] **ลบ** divider page "-------------"
- [ ] **Merge** cross-file dialog references หรือใช้ published library
- [ ] เปิด **Dev Mode** + Copy link to selection สำหรับทุก story
- [ ] **Code Connect mapping** (optional แต่ช่วย dev มาก)

---

## 4. Stakeholder Approval

| Role | Name | Approved | Date | Comments |
|---|---|:-:|---|---|
| **BA** | _______________ | ☐ | __________ |  |
| **PM** | _______________ | ☐ | __________ |  |
| **Frontend Dev Lead** | _______________ | ☐ | __________ |  |
| **Design Lead** (เจ้าของไฟล์ Figma) | _______________ | ☐ | __________ |  |
| **Security Lead** *(แนะนำเพิ่ม — auth/policy related)* | _______________ | ☐ | __________ |  |
| **UX (author)** | _______________ | ☐ | __________ |  |

---

## 5. Open Questions

| # | Question | Owner | Answer | Date |
|---|---|---|---|---|
| Q-01 | รองรับ mobile/tablet มั้ย? | PM |  |  |
| Q-02 | Date storage: BE หรือ CE? UI แสดง BE ตลอด? | Backend Lead |  |  |
| Q-03 | "ประเภทการรับเงิน" 2 fields ต่างกันยังไง? | BA |  |  |
| Q-04 | Cross-file Figma (BO Capital) merge หรือ keep แยก? | Design Lead |  |  |
| Q-05 | Document section 15 ประเภท mandatory ทั้งหมด? | BA |  |  |
| Q-06 | Failed Login Behavior (lock / warning / unlimited)? | BA |  |  |
| Q-07 | Login 3 variations ใช้กรณีไหน (default/error/disabled)? | Design Lead |  |  |
| Q-08 | บุคคลอ้างอิงสามารถมีมากกว่า 2 คน? (จาก sticky Q&A) | BA |  |  |

---

## 6. Quick Wins Recommendation (ทำก่อน sprint หน้า)

จาก `PROS_CONS_ANALYSIS.md §4`:

1. **W-06** สร้าง "00 Design System" page + รวม tokens
2. **W-07** เติม 7-states ให้ Login + Create Application ก่อน (ใช้บ่อยสุด)
3. **W-08, W-09** แก้ field naming + placeholder ใน Create Application
4. **W-15** export validation specs เป็น JSON schema
5. **W-04** ย้าย sticky condition ใหญ่ไป Confluence/Notion

---

## 7. Review Meeting Log

| Date | Attendees | Decisions / Action items |
|---|---|---|
|  |  |  |

---

## 8. Sign-off

> เมื่อทุก row ใน §4 ✅ ครบ → เปลี่ยน status เป็น `final` + Designer เริ่ม implement fix list
