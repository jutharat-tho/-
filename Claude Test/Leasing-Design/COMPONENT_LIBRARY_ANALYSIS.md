# Component Library Analysis — `❖ Components - Leasing Design BO`

**Figma file:** `IgbC5dmSjDDUmJgTHCr7v2`
**Analyzed by:** UX/UI (Figma MCP, read-only)
**Date:** 2026-05-20
**Related:** `./PROS_CONS_ANALYSIS.md` · `./UXUI_DESIGN.md`

---

## 1. ภาพรวม

ไฟล์ DS แยกต่างหากจาก screen file — มี **Style Guide + Assets + Components + Page Examples** ครบเซ็ต

### Structure ที่อ่านได้

```
🏷️ Cover
---
📊 Style Guide
   ↳ Color
   ↳ Typography
   ↳ Grid
   ↳ Shadows & Blurs
---
〽️ Assets
   ↳ Icon
   ↳ Logo
---
✨ PAGES EXAMPLES
   ↳ Page : Login
   ↳ Page : Customer information
   ↳ Page : Create application
---
💠 Components (19 ตัว)
   ↳ Avatars
   ↳ Breadcrumbsb     ← typo (ควรเป็น "Breadcrumbs")
   ↳ Button
   ↳ Checkbox, Radio, Switch
   ↳ Sidebar
   ↳ Steps
   ↳ Header
   ↳ Pagination
   ↳ Inputs
   ↳ Navbar           ← mobile pattern? (Home/My Tickets/My Profile)
   ↳ Tab
   ↳ Table
   ↳ Text area
   ↳ Step             ← duplicate? (มี "Steps" และ "Step")
   ↳ Heading
   ↳ Box Detail
   ↳ Dialog / Error
   ↳ Dropdown list
   ↳ Search history
```

---

## 2. ที่พบจริง (samples)

### 🎨 Color Palette (full)

| Family | Shades |
|---|---|
| **Base** | White, Black |
| **Primary** (Pentor red) | Basic + 100, 200, 300, 400, 500, 600, 700, 800, 900 |
| **Secondary** (green/teal) | Basic + 100–900 |
| **Grey** | Basic + 100–900 |
| **Orange** | Basic + 100–900 |
| **Pink** | Basic + 100–900 |
| **Purple** | Basic + 100–900 |
| **Red** | Basic + 100–900 (separate from Primary) |

> ⚠️ ทำไมมี **Primary** กับ **Red** แยกกัน? — Primary น่าจะเป็น brand red (Pentor), Red อาจสำหรับ semantic error/destructive — แต่ไม่มีการระบุชัด → ต้อง confirm

### 🔤 Typography (Pentor Corporate)

| Category | Scale |
|---|---|
| Heading | H1 60 · H2 48 · H3 24 (SemiBold) |
| Body | L 16 · M 14 · S 12 · XS 10 (SemiBold + Regular variants) |
| Button text | XL 24 · L 18 · M 16 · S 14 · XS 12 (SemiBold) |
| Link | L 14 · M 12 · S 10 (Regular) |

> ⚠️ Typo ใน label หลายจุด: **"Regula"** ควรเป็น Regular""

### 🧩 Sidebar Component (BO context)

4 variants ที่ดู:
- Default (gray)
- Selected/Active (red badge + red text)
- Collapsed default
- Collapsed selected

มีระบบ index number (1, 1.1, 1.2) สำหรับ nested menu — เหมาะกับ BO ที่มีหลายระดับ

### 📋 Input Component

มี states ครบมาก (matrix):
- default · hover · focused · filled · disabled · success (green border)
- 2 sizes (regular + with helper text/suffix)
- empty vs filled
- ✅ **Input component coverage ดีมาก**

### 📱 Navbar Component

⚠️ **คือ mobile bottom navigation** — ไม่ใช่ BO navbar
- Tabs: Home, My Tickets, My Profile, Menu, Label
- Active state = green color + filled icon
- 4 variants (Home/Tickets/Profile/Menu active)

**Implication:** DS file นี้น่าจะ shared กับ **customer mobile app** ด้วย ไม่ใช่ BO อย่างเดียว
- เป็น **ข้อดี** ถ้าตั้งใจให้ shared
- เป็น **ความเสี่ยง** ถ้า BO อยากแยกเองภายหลัง

---

## 3. ผลกระทบต่อ PROS_CONS_ANALYSIS.md เดิม

### ✅ Updates: items ที่เคย flag ว่าเป็น "ข้อเสีย" → จริงๆ ไม่ใช่

| Old issue | สถานะใหม่ | เหตุผล |
|---|---|---|
| W-06 ไม่มี "00 Design System" page | ❌ **ไม่ใช่ปัญหาแล้ว** | มี DS file แยกครบ — แต่ screen file ไม่ link หา DS ชัด |
| Token gap (ไม่มี semantic, neutral scale) | ❌ **ปัญหาน้อยลง** | DS มี Grey, Secondary, Red ครบ scale — แต่ screen file ใช้ไม่ครบ |
| Component library พื้นฐาน | ✅ **ดียิ่งกว่าที่คาด** | มี 19 components ครบเซ็ต |

### 🆕 New findings (เพิ่มจากที่อ่าน DS file)

| # | Finding | Severity | Action |
|---|---|---|---|
| W-19 | **Typos ใน DS file** — "Breadcrumbsb", "Regula" | 🟡 | rename ใน Figma |
| W-20 | **Duplicate Component** — "Steps" + "Step" | 🟡 | merge หรือชัดเจนว่า 2 ตัวต่างกันยังไง |
| W-21 | **Navbar เป็น mobile pattern** ใน DS ของ BO | 🔴 | confirm ว่า BO จะใช้หรือไม่ — ถ้า BO ใช้แค่ Sidebar/Header ให้ลบ Navbar หรือย้าย DS แยก mobile/BO |
| W-22 | **"Primary" + "Red" 2 palette** ไม่ clear semantic | 🟡 | rename — เช่น `Brand` (Primary) + `Error` (Red) |
| W-23 | **DS file ไม่ published เป็น Figma Library** (ตรวจไม่ได้ทาง MCP แต่สมมุติจากการที่ screen file ไม่เห็น variable cross-file) | 🔴 | Publish เป็น Figma library + connect ใน screen file |
| W-24 | **PAGES EXAMPLES** ใน DS มี Login/Customer/Application — ซ้ำกับ screen file | 🟡 | confirm: example หรือ source of truth? — ถ้า example ให้ระบุ label "Example only — see Leasing-Design.fig" |
| W-25 | **2 ไฟล์ Figma** (Screen + DS) แต่**ไม่มี Code Connect** | 🟡 | setup Code Connect mapping ระหว่าง 2 ไฟล์ + code |

---

## 4. ✅ ข้อดีของ DS file ที่อ่านได้

| # | จุดเด่น | ทำไมเป็นข้อดี |
|---|---|---|
| DS-S-01 | **Organized hierarchy** | Cover / Style Guide / Assets / Examples / Components — section ชัด |
| DS-S-02 | **Full color palette** 8 hue × 10 shades | rebrand ง่าย, ทุก context มีสีรองรับ |
| DS-S-03 | **Typography ครบ** | Heading/Body/Button/Link แยกชัด หลายขนาด |
| DS-S-04 | **Input component complete** | states ครบใน matrix (default/hover/focused/filled/disabled/success) |
| DS-S-05 | **Sidebar + Stepper สำหรับ BO** | ตอบโจทย์ navigation ของ BO |
| DS-S-06 | **PAGES EXAMPLES** | ทำให้ developer/PM เห็น component ที่ assemble แล้ว |
| DS-S-07 | **Dialog/Error + Dropdown list** | ครอบคลุม edge case ที่ screen file มักลืม |
| DS-S-08 | **Heading + Box Detail** | reusable layout primitive |

---

## 5. 🚀 Recommendations (เพิ่มจาก existing)

### High priority
1. **W-23 Publish เป็น Figma Library** — เพื่อให้ screen file (Leasing-Design.fig) เห็น component cross-file
2. **W-21 Navbar** — ตัดสินว่า DS ใช้สำหรับ BO only หรือ shared multi-product
3. **W-25 Code Connect** — setup mapping จาก Figma component → React/Vue component
4. **W-22 Rename color palette** — `Primary` → `Brand`, `Red` → `Error/Destructive`

### Medium priority
5. **W-19 Fix typos** — Breadcrumbsb, Regula
6. **W-20 Resolve duplicate** — Steps vs Step
7. **W-24 Label PAGES EXAMPLES** — ระบุชัดว่าเป็น example ไม่ใช่ source of truth

### Low priority
8. เพิ่ม **Shadows & Blurs** doc ใน design-tokens.json/.css (ตอนนี้มี page ใน DS แต่ผมยังไม่ได้อ่าน)
9. เพิ่ม **Grid** spec ใน design tokens
10. **Icon page** export เป็น SVG sprite

---

## 6. ที่ผมไม่ได้อ่าน (transparency)

ใน DS file นี้:
- ❌ Avatars (44:699)
- ❌ Breadcrumbsb (44:696)
- ❌ Checkbox, Radio, Switch (40:839)
- ❌ Steps (2469:251) + Step (65:9364)
- ❌ Header (40:758)
- ❌ Pagination (40:841)
- ❌ Tab (40:840)
- ❌ Text area (311:52737)
- ❌ Heading (81:7773)
- ❌ Box Detail (106:22671)
- ❌ Dialog / Error (106:22670)
- ❌ Dropdown list (106:22673)
- ❌ Search history (1225:180076)
- ❌ Grid page
- ❌ Shadows & Blurs page
- ❌ Icon page + Logo page
- ❌ PAGES EXAMPLES (Login / Customer / Application)

อ่านแล้ว:
- ✅ Cover (สรุปจาก top-level pages)
- ✅ Color page
- ✅ Typography page
- ✅ Button (เห็น screenshot — ไม่อ่าน detail)
- ✅ Inputs (matrix)
- ✅ Sidebar
- ✅ Table (เห็น screenshot — ไม่อ่าน detail)
- ✅ Navbar (mobile pattern)

ถ้าต้องการ deep dive component ไหน → ส่ง Dev Mode URL ของ component นั้นมา
