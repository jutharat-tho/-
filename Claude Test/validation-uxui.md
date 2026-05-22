# validation-uxui.md — Leasing BO: Authentication & User Management

**Feature scope:** Authentication + User Management
**Design spec:** `./UXUI_DESIGN.md` v0.1
**Stories:** `./STORIES.md` (AUTH-001..003, USER-001..003, EMAIL-001)
**Status:** `draft` → `in_review` → `approved` → `final`
**Current status:** `draft`

---

## 1. Pre-Review Checklist (UX self-check)

- [x] 6 states ต่อหน้าจอ (default · loading · empty · error · success · disabled)
- [x] Responsive notes ระบุครบ
- [x] Tokens ใน docs ตรงกับ Figma (เมื่อมี Figma)
- [x] Accessibility notes ครบ (label, aria-live, focus, contrast)
- [x] Edge cases ของ data (empty user list, long names, INACTIVE rows)
- [ ] Interactive prototype พร้อม click-through *(ต้องทำใน Figma)*

## 2. Pre-Handoff Checklist (UX → Dev)

- [ ] Dev Mode เปิดใน Figma
- [ ] Component name ใน Figma ตรงกับชื่อใน `STORIES.md`
- [ ] Tokens export → `design-tokens.json` / `design-tokens.css` ✅
- [ ] Figma node link ใน `STORIES.md` แทนที่ `<FIGMA_NODE_URL>` ครบ
- [ ] Interaction spec (timing, easing) ระบุใน Figma prototype

---

## 3. Stakeholder Approval

| Role | Name | Approved | Date | Comments |
|---|---|:-:|---|---|
| **BA** | _______________ | ☐ | __________ |  |
| **PM** | _______________ | ☐ | __________ |  |
| **Frontend Dev Lead** | _______________ | ☐ | __________ |  |
| **Security Lead** *(แนะนำเพิ่ม — เพราะ C-01/C-02)* | _______________ | ☐ | __________ |  |
| **UX (author)** | _______________ | ☐ | __________ |  |

---

## 4. Open Questions (จาก UXUI_DESIGN.md §10)

| # | Question | Owner | Answer | Date |
|---|---|---|---|---|
| Q-01 | Admin role เห็นหน้า User Management ไหม? | BA |  |  |
| Q-02 | รองรับมือถือ scope ไหน? | PM |  |  |
| Q-03 | Email language: ไทยอย่างเดียว / bilingual? | BA |  |  |
| Q-04 | Login URL ใน email ใช้ deep link prefill? | Frontend Lead |  |  |
| Q-05 | Backend rate limit / CAPTCHA (C-02 mitigation)? | Backend Lead |  |  |

---

## 5. BRD Conflict Decisions (UX flagged)

| # | Decision | Risk | Stakeholder Confirmed | Mitigation |
|---|---|---|---|---|
| C-01 | ไม่มี Auto Logout Warning Modal | data loss กลาง form ยาว | ✅ PO confirmed | autosave draft (recommend dev) |
| C-02 | ไม่มี Failed Login warning, ไม่ lock | brute force | ✅ PO confirmed | ต้องมี backend rate limit + CAPTCHA (Q-05) |
| C-03 | ไม่มี Forgot Password link บน Login | UX ไม่ชัดว่าทำยังไง | ✅ PO confirmed | hint text / FAQ link (optional) |
| C-04 | Set Password → กลับ Login ไม่ auto-login | extra login step | ✅ PO confirmed | toast feedback ก่อน redirect (UX 2s) |

---

## 6. Review Meeting Log

| Date | Attendees | Decisions / Action items |
|---|---|---|
|  |  |  |

---

## 7. Sign-off

> เมื่อทุก row ใน §3 ✅ ครบ → เปลี่ยน status เป็น `final` แล้วเริ่ม dev handoff
