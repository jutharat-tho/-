# Leasing Design — Pros / Cons Analysis (Screens file)

**Figma file:** `OthuQyTNoG9V5s9Z94L5O3` — Leasing Design
**Analyzed by:** UX/UI (via Figma MCP, read-only)
**Date:** 2026-05-19 (updated 2026-05-20 หลังพบ DS file แยก)
**Scope:** ตรวจ structure, tokens, component library, screen samples, validation specs

> 📌 **Update 2026-05-20**: หลังอ่าน DS file (`IgbC5dmSjDDUmJgTHCr7v2`) บางข้อใน list นี้ **resolved** หรือ **severity เปลี่ยน** — ดู `COMPONENT_LIBRARY_ANALYSIS.md §3` สำหรับ delta

---

## 1. ที่อ่านได้จาก Figma (read-only sampling)

### Pages
| Page | สถานะ | สิ่งที่มี |
|---|---|---|
| 🏷️ Cover | active | หน้าปก "Leasing Design BO" สีแดงพื้น hexagon + chevron pattern |
| ------------- | placeholder | page ว่างใช้เป็น divider |
| BA Flow | active | flow diagram จากฝั่ง business analyst |
| ↳ Login & Logout | active | 3 Login frames + 3 New Password frames พร้อม connector |
| ↳ Customer information | active | Create/Edit customer (Personal, Address, Work, Bank, Contact, Sending docs) + validation specs |
| ↳ Create application | active | ฟอร์มใบคำขอสินเชื่อ (ข้อมูลใบคำขอ, คู่ค้า, ผู้ค้ำประกัน, Document section ×15) |
| ↳ Settings | active | Master data: Asset Category / Asset Group / Asset Type / Partners / Vehicle Info |

### Design Tokens (จาก node sample)
| Token | Value |
|---|---|
| Brand color | `#d82329` (Pentor red) |
| Text primary | `#18181b` |
| Icon secondary | `#71717a` |
| Background primary | `#ffffff` |
| Font family | `Pentor Corporate` (Regular, SemiBold) |
| Body M | 16px / line-height 1.3 |
| Spacing | 0, 4, 16, 24, 32 |
| Stroke | 4 |
| Border radius | 0 (sharp corners) |

### Visual sample
- **Login**: centered card, logo on top, language toggle (ไทย ▾), 2 fields, red primary button, version footer `V. 0.1.4`
- **Create Application**: collapsible red header "ข้อมูลใบคำขอ", 3-column form layout, section indicator (red vertical bar), Thai Buddhist Era date (2569)

---

## 2. ✅ ข้อดี (Strengths)

| # | จุดเด่น | ทำไมเป็นข้อดี |
|---|---|---|
| S-01 | **Token-driven** | สี/font/spacing/typography มี named token → rebrand ทีเดียว |
| S-02 | **Brand identity ชัด** | สีแดง #d82329 + Pentor Corporate font + hexagon pattern → จดจำได้ทันที |
| S-03 | **Component library มี** | `h2`, `Buttons/Basic`, `Text field`, `Radio`, `Trailing icon` ใช้ซ้ำ |
| S-04 | **Validation specs embedded** | sticky note บอก rule ทุก field (Required/Optional, length, regex, error msg, cross-field) |
| S-05 | **Reusable layouts** | "Address Container", "Search customer history", "h2" ใช้ซ้ำหลาย flow |
| S-06 | **Conditional logic ระบุ** | "เมื่อเลือก X แสดง Y" → dev อ่านแล้ว implement ได้ |
| S-07 | **Flow diagram มี** | BA Flow + Login & Logout flow → state transition ชัด |
| S-08 | **Thai-first + bilingual ready** | label ภาษาไทย, BE date, dropdown toggle เป็น EN |
| S-09 | **Required indicator** | `*` สีแดงทุก field ที่บังคับ |
| S-10 | **Domain-rich fields** | "ระยะทางจากดีลเลอร์ถึงบ้านลูกค้า (km)", "อนุมัติจ่ายค่าบริการ %", "ผู้ค้ำประกัน" — สะท้อน business จริง |
| S-11 | **Multiple variations** | Login มี 3 variations (Component / Component 2 / Component 3) แสดงว่าออกแบบเผื่อ state |
| S-12 | **Color contrast หลัก OK** | ตัวอักษรดำบนพื้นขาว ผ่าน WCAG AA |

---

## 3. ⚠️ ข้อเสีย (Weaknesses) + ข้อเสนอแก้

### 🔴 ระดับโครงสร้างไฟล์ (Critical)

| # | ปัญหา | ผลกระทบ | แนะนำ |
|---|---|---|---|
| W-01 | Page `-------------` เป็น divider ปลอม | `get_metadata` default แสดงแค่ Cover → คนใหม่หา content ไม่เจอ | ใช้ Figma **Section** แทน + ลบ divider page |
| W-02 | Page ใช้ `↳` prefix ไม่มี emoji | ระบุ hierarchy ทาง text เท่านั้น | เพิ่ม emoji เช่น 🔐 Login, 👤 Customer, 📋 Application, ⚙️ Settings |
| W-03 | Frame name ปะปน — "Frame", "Frame 25", "Frame 31" ปนกับ "Address Container" | dev import แล้วชื่อสุ่ม | rename frame ให้ semantic หรือซ่อนใน group |
| W-04 | Sticky note บรรจุ conditions ยาวมาก | คนหา detail ไม่เจอ + แก้ลำบาก | ย้ายไป Notion/Confluence + ใส่ลิงก์ใน sticky สั้นๆ |
| W-05 | Cross-file reference ไป Figma อื่น (BO Capital) | dependency แตกถ้าไฟล์นั้นย้าย/ลบ → broken link | ย้าย component ที่ใช้ซ้ำมาไฟล์เดียว หรือใช้ Figma published library |
| W-06 | ไม่มี dedicated "00 Design System" page | tokens กระจาย ไม่มี single source of truth | สร้าง page รวม color/typography/spacing/components |

### 🟡 ระดับ UX/UI (Should fix)

| # | ปัญหา | ผลกระทบ | แนะนำ |
|---|---|---|---|
| W-07 | **States ขาด** — เห็นแต่ default | ไม่มี loading/empty/error/disabled per screen | เพิ่ม **7-states matrix** ทุกหน้า |
| W-08 | "ประเภทการรับเงิน" ซ้ำชื่อ 2 fields ในหน้าเดียว | user งง 2 fields ต่างกันยังไง | rename เป็น "(ค่ารถ)" / "(ค่าบริการ)" |
| W-09 | Placeholder `0` ในช่องจำนวนเงิน | สับสนกับค่าจริง | ใช้ `0.00` หรือ `ระบุจำนวนเงิน` + thousands separator หลัง blur |
| W-10 | `*` สีแดง = สีเดียวกับ error message | a11y — แยกจาก color contrast ไม่ออก | เพิ่ม icon ⚠ ที่ error / ใช้ `*` + tooltip "จำเป็น" |
| W-11 | Buddhist Era date 2569 | ระบบ integrate ภายนอกพังถ้าส่งดิบ | แสดง BE ใน UI, store เป็น ISO 8601 ใน backend |
| W-12 | Border radius = 0 (sharp corners) | ดูเก่ากว่ายุค modern UI ปกติใช้ 4-8px | พิจารณาเพิ่ม radius-sm = 4px สำหรับ input/button |
| W-13 | Document section มี 15 instances ซ้อนกัน | scroll ยาว, ไม่มี progress indicator | เพิ่ม sticky header + progress bar / collapsible group |
| W-14 | ไม่เห็น mobile / responsive frames | BO desktop only? ถ้า sale ใช้ tablet/มือถือ — มีปัญหา | confirm requirement + ออกแบบ responsive ถ้าจำเป็น |

### 🟢 ระดับ Handoff / Docs (Nice to fix)

| # | ปัญหา | ผลกระทบ | แนะนำ |
|---|---|---|---|
| W-15 | Validation อยู่ใน Figma แต่ไม่ export | dev copy ผิด typo ลำบาก | export เป็น **JSON schema / Zod / Yup** |
| W-16 | ไม่มี changelog | ไม่รู้ revision | ใส่ version + change log ใน Cover sticky |
| W-17 | ไม่มี a11y note | dev อาจลืม aria-label, focus order | เพิ่ม Accessibility section per screen |
| W-18 | Component naming ไม่ map กับ code | dev ต้องเดาว่าตัวไหน = ตัวไหน | ใช้ **Code Connect** หรือบอกชื่อ code component ใต้ instance |

---

## 4. 🎯 Quick Wins (ทำก่อน, impact สูง)

อันดับสิ่งที่ควรปรับ**ก่อน** sprint หน้า:

1. **W-06** สร้าง "00 Design System" page + รวม tokens ในที่เดียว
2. **W-07** เติม 7-states ให้ Login + Create Application ก่อน (ใช้บ่อยสุด)
3. **W-08, W-09** แก้ field naming + placeholder ใน Create Application
4. **W-15** export validation specs เป็น JSON
5. **W-04** ย้าย sticky condition ใหญ่ไป Confluence/Notion

## 5. 🚧 Long-term

- **W-01–W-03** ปรับ file structure (ลบ divider page, ใช้ Section, rename frames)
- **W-13** Document section UX rework (collapsible/progress)
- **W-14** confirm responsive scope
- **W-18** setup Code Connect

---

## 6. ผมไม่ได้อ่านอะไรบ้าง (transparency)

- ❌ ไม่ได้เปิดทุก frame ใน "Customer information" (page ใหญ่เกิน 120k chars metadata)
- ❌ ไม่ได้ดู "BA Flow" page (สันนิษฐานว่าเป็น flow diagram, ไม่ใช่ screen)
- ❌ ไม่ได้ดูทุก variation ของ Login (เปิดแค่ Component แรก)
- ❌ ไม่ได้ open popup frames เต็มทุกตัว
- ❌ ไม่ได้อ่าน validation sticky note ทุกตัวแบบเต็ม (ยาวมาก)

ถ้าต้องการ deep dive specific frame → ส่ง Dev Mode link ของ frame นั้นมาเฉพาะ
