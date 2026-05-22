# Design Tokens — Leasing Design BO

**Last sync:** 2026-05-23
**Source of truth:** Figma file `❖ Components : Leasing Design BO` ([open](https://www.figma.com/design/IgbC5dmSjDDUmJgTHCr7v2/%E2%9D%96-Components---Leasing-Design-BO?node-id=23-154))
**Companion file:** `design-tokens.html` (visual reference for non-tech stakeholders — open in any browser)

> Tokens were extracted from Figma Variables surfaced via `get_design_context` on Login screens (`139:28609`, `139:28611`, `239:74548`). The `get_variable_defs` MCP tool requires an active selection in the Figma desktop app and could not be called headlessly during this session — refresh by reopening the file in desktop Figma and re-running it if you need the full variable tree.

---

## 1. Colors

### 1.0 Primitives — Full color ramps

Extracted from Figma section `Primitives` (node `4095:130`). These are the **raw color values** — semantic tokens (§1.1–1.3) reference these primitives.

#### Base
| Token | Value |
|---|---|
| `Base/White` | `#FFFFFF` |
| `Base/Black` | `#000000` |

#### Primary (Brand red)
| Step | Value | Step | Value |
|---|---|---|---|
| `Primary/100` | `#FFC6C8` | `Primary/500` | `#E04F54` |
| `Primary/200` | `#EC9194` | `Primary/600` | `#CE4246` |
| `Primary/300` | `#E87B7F` | `Primary/700` | `#BC3439` |
| `Primary/400` | `#E46569` | `Primary/800` | `#A9272B` |
| `Primary/Basic` | `#D82329` | `Primary/900` | `#97191D` |

#### Secondary (Green — success / positive)
| Step | Value | Step | Value |
|---|---|---|---|
| `Secondary/100` | `#BFEDDF` | `Secondary/500` | `#019267` |
| `Secondary/200` | `#99D3C2` | `Secondary/600` | `#01875F` |
| `Secondary/300` | `#73C3AC` | `Secondary/700` | `#017C58` |
| `Secondary/400` | `#4DB395` | `Secondary/800` | `#017150` |
| `Secondary/Basic` | `#019267` | `Secondary/900` | `#016648` |

#### Grey (neutral)
| Step | Value | Step | Value |
|---|---|---|---|
| `Grey/100` | `#F4F4F5` | `Grey/500` | `#71717A` |
| `Grey/200` | `#E4E4E7` | `Grey/600` | `#52525B` |
| `Grey/300` | `#D4D4D8` | `Grey/700` | `#3F3F46` |
| `Grey/400` | `#A1A1AA` | `Grey/800` | `#27272A` |
| `Grey/Basic` | `#9E9E9E` | `Grey/900` | `#18181B` |

#### Orange (warning / pending)
| Step | Value | Step | Value |
|---|---|---|---|
| `Orange/100` | `#FFFBF2` | `Orange/500` | `#D08600` |
| `Orange/200` | `#FFDD9F` | `Orange/600` | `#AF7000` |
| `Orange/300` | `#FFBC42` | `Orange/700` | `#8F5C00` |
| `Orange/400` | `#F39C00` | `Orange/800` | `#6F4800` |
| | | `Orange/900` | `#523500` |

#### Pink
| Step | Value | Step | Value |
|---|---|---|---|
| `Pink/100` | `#FFEDFF` | `Pink/500` | `#CB5BCD` |
| `Pink/200` | `#FEC3FF` | `Pink/600` | `#A64AA8` |
| `Pink/300` | `#FD97FF` | `Pink/700` | `#843B86` |
| `Pink/400` | `#F06CF3` | `Pink/800` | `#612C63` |
| `Pink/Basic` | `#FC71FF` | `Pink/900` | `#421E43` |

#### Purple
| Step | Value | Step | Value |
|---|---|---|---|
| `Purple/100` | `#FEFDFF` | `Purple/500` | `#B47AFF` |
| `Purple/200` | `#ECDDFF` | `Purple/600` | `#9E54FF` |
| `Purple/300` | `#D9BCFF` | `Purple/700` | `#833EDE` |
| `Purple/400` | `#C79BFF` | `Purple/800` | `#6731AF` |
| `Purple/Basic` | `#9747FF` | `Purple/900` | `#4C2480` |

#### Red (critical / error — separate from Primary/Brand)
| Step | Value | Step | Value |
|---|---|---|---|
| `Red/100` | `#FFEBEB` | `Red/500` | `#FF3333` |
| `Red/200` | `#FFD0D0` | `Red/600` | `#D32222` |
| `Red/300` | `#FF9999` | `Red/700` | `#A71111` |
| `Red/400` | `#FF5555` | `Red/800` | `#7A0000` |
| `Red/Basic` | `#FF4545` | `Red/900` | `#3D0000` |

#### Semantic → Primitive mapping (for reference)
The semantic tokens in §1.1–1.3 below resolve to primitives as follows:

| Semantic | = | Primitive |
|---|---|---|
| `background/button/brand` | = | `Primary/Basic` `#D82329` |
| `text/button/critical` | = | `Red/Basic` `#FF4545` |
| `border/button/critical` | = | `Red/500` `#FF3333` |
| `border/primary` | = | `Grey/Basic` `#9E9E9E` |
| `border/secondary` | = | `Grey/200` `#E4E4E7` |
| `text/primary` | ≈ | `Grey/900` `#18181B` |
| `text/secondary` | = | `Grey/500` `#71717A` |
| `text/placeholder` | = | `Grey/300` `#D4D4D8` |
| `background/secondary-hover` | ≈ | `Grey/100` `#F4F4F5` (note: docs say `#F5F5F5` — 1 unit drift) |

> **Note:** ตัว `background/secondary-hover` ใน semantic token เก่าเป็น `#F5F5F5` ส่วน `Grey/100` ใน primitive เป็น `#F4F4F5` — ต่างกัน 1 unit. ควรเลือกใช้อันใดอันหนึ่งให้ตรงกัน. แนะนำ migrate ไป `Grey/100` เป็น single source.

---

### 1.1 Semantic — Background

| Token | Value | Usage |
|---|---|---|
| `background/primary` | `#FFFFFF` | Cards, modal/dialog body, input background |
| `background/secondary-hover` | `#F5F5F5` | Page background, subtle hover surfaces |
| `background/input/primary` | `#FFFFFF` | Form input fields (default) |
| `background/button/brand` | `#D82329` | Primary action button (brand red) |

### 1.2 Semantic — Text

| Token | Value | Usage |
|---|---|---|
| `text/primary` | `#18181B` | Body text, input value, labels |
| `text/secondary` | `#71717A` | Version label, secondary helper copy |
| `text/placeholder` | `#D4D4D8` | Input placeholder |
| `text/button/primary` | `#FFFFFF` | Text inside primary brand button |
| `text/button/critical` | `#FF4545` | Error helper text below input |

### 1.3 Semantic — Border

| Token | Value | Usage |
|---|---|---|
| `border/primary` | `#9E9E9E` | Default input/button border |
| `border/secondary` | `#E4E4E7` | Subtle dividers, pill borders (language pill) |
| `border/button/critical` | `#FF3333` | Input border in error state |

### 1.4 Brand palette (primitive — read from logo + button)

| Token | Value | Usage |
|---|---|---|
| `brand/red-500` | `#D82329` | Pentor Leasing brand red |

> Additional primitive ramps (gray, semantic green/orange/blue) are present in the component library but were not surfaced in the Login screen design context. Re-extract when the file is open in Figma desktop and update this section.

---

## 2. Typography

### 2.1 Font Family

| Token | Value |
|---|---|
| `font-families/primary` | `Pentor Corporate, sans-serif` |

### 2.2 Font Weights

| Token | Value (CSS weight) |
|---|---|
| `font-families/regular` | 400 |
| `font-families/semibold` | 600 |

### 2.3 Size Scale

| Token | Value | Used in |
|---|---|---|
| `sizes/boay/xSmall` | 12px | Body S, helper/version text |
| `sizes/boay/small` | 14px | Body M, input label |
| `sizes/boay/medium` | 16px | Body L, input value |
| `sizes/button-text/small` | 14px | Small button text |
| `sizes/button-text/medium` | 16px | Medium button text |

> Naming note: the Figma collection uses `boay` (likely "body" mis-typed). Code Connect mapping should rename to `body` when feasible — flag for the design system review.

### 2.4 Text styles (composed)

| Style | Family | Size | Weight | Line height | Letter spacing |
|---|---|---|---|---|---|
| Body/S/Regular 12 | Primary | 12 | 400 | 1.3 | 0 |
| Body/M/SemiBold 14 | Primary | 14 | 600 | 1.3 | 0 |
| Body/L/Regular 16 | Primary | 16 | 400 | 1.3 | 0 |
| Button/S | Primary | 14 | 600 | 1.2 | 0.5px |
| Button/M | Primary | 16 | 600 | 1.2 | 0 |

---

## 3. Spacing (`gap/space-*`)

Numeric scale doubles roughly per step.

| Token | Value |
|---|---|
| `gap/space-0` | 0px |
| `gap/space-50` | 2px |
| `gap/space-100` | 4px |
| `gap/space-200` | 8px |
| `gap/space-400` | 16px |
| `gap/space-600` | 24px |
| `gap/space-800` | 32px |
| `gap/space-1000` | 40px |

> Steps `space-300`, `space-500`, `space-700`, `space-900` were not observed in the surfaced design context. They may exist in the full Variable collection — confirm during a desktop Figma session.

---

## 4. Border Radius (`border/radius-*`)

| Token | Value | Usage |
|---|---|---|
| `border/radius-200` | 8px | Inputs, primary button |
| `border/radius-400` | 16px | Login card / modal container |
| (literal) `100px` | 100px | Pill (language selector) |

---

## 5. Effects / Elevation

### 5.1 Popup / Card shadow

Five-layer drop shadow stack on `#170E39` (dark purple tint) — used by Login card and modal dialogs.

| Layer | Offset | Blur | Color |
|---|---|---|---|
| 1 | 0px 3px | 7px | `#170E391C` (alpha 11%) |
| 2 | 0px 12px | 12px | `#170E3917` (alpha 9%) |
| 3 | 0px 28px | 17px | `#170E390D` (alpha 5%) |
| 4 | 0px 49px | 20px | `#170E3905` (alpha 2%) |
| 5 | 0px 76px | 21px | `#170E3900` (alpha 0%) |

CSS:
```css
box-shadow:
  0 3px 7px rgba(23, 14, 57, 0.11),
  0 12px 12px rgba(23, 14, 57, 0.09),
  0 28px 17px rgba(23, 14, 57, 0.05),
  0 49px 20px rgba(23, 14, 57, 0.02),
  0 76px 21px rgba(23, 14, 57, 0.00);
```

### 5.2 Input error glow

| Property | Value |
|---|---|
| Offset | 0px 2px |
| Blur | 3px |
| Spread | 2px |
| Color | `#FF000040` (red 25%) |

CSS:
```css
box-shadow: 0 2px 3px 2px rgba(255, 0, 0, 0.25);
```

---

## 6. Sizing (component-level)

These are not Figma Variables but recurring concrete values observed in Login + form components.

| Token (proposed) | Value | Where it appears |
|---|---|---|
| `input/height` | 40px | Text fields |
| `button/height/medium` | 40px | Primary button (Login) |
| `icon/size/medium` | 24px | Input leading/trailing icons |
| `icon/size/small` | 14px | Language pill flag |
| `card/login/min-width` | 372px (form field width) | Login form column |

---

## 7. Export targets

When code generation is wired up:

1. **CSS variables** — emit at `:root` from this file (see `design-tokens.html` `<style>` block for the canonical names)
2. **Style Dictionary** — recommended for cross-platform export (web + future mobile)
3. **Code Connect** — once components are mapped, see `figma-code-connect` skill

---

## 8. Change Log

| Date | Change | Reason |
|---|---|---|
| 2026-05-23 | Initial extraction of color/typography/spacing/radius/effect tokens from Login screens | First sync after kick-off |
| 2026-05-23 | Added Primitives section §1.0 — full 7-ramp palette (Primary, Secondary, Grey, Orange, Pink, Purple, Red) × 9 shades + Base White/Black, extracted from Figma node `4095:130` via `get_variable_defs` | Required for full color system + Code Connect mapping |
