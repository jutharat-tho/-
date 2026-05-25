# Authentication Module — Pentor Leasing Back-Office

> **Scope:** Web Back-Office สำหรับใช้งานภายในองค์กร — ไม่ใช่ระบบสำหรับลูกค้าทั่วไป
> **Stack:** Go (Gin/Echo) + MongoDB + Redis + Vue 3 (frontend)

---

## 1. Endpoints

| # | Method | Path | Auth Required | Description |
|---|--------|------|---------------|-------------|
| 1 | POST | `/auth/login` | ❌ | ล็อกอินด้วย username + password (รองรับ temp password และ password ปกติ) |
| 2 | POST | `/auth/logout` | ✅ Bearer | ออกจากระบบ + blacklist token ปัจจุบัน + ลบ session ใน Redis |
| 3 | POST | `/auth/refresh-token` | ❌ (ใช้ refresh token) | ต่ออายุ access token |
| 4 | POST | `/auth/change-password` | ✅ Bearer (full / change-password) | เปลี่ยนรหัสผ่าน (รองรับ first_time / expired) |

> 🔀 **Moved 2026-05-25:** `GET /auth/me` → [`GET /profile/me`](../profile/get-me.md) (ย้าย module ไป [`spec/profile/`](../profile/))

> **Forgot password:** ไม่มี self-service — User ที่ลืมรหัสต้องติดต่อ Super Admin เพื่อ reset → ระบบ gen temp password ใหม่ + ส่ง email

### Login Entry Points — 3 Cases

| Case | สถานการณ์ | Entry | Endpoint | Token ที่ได้ |
|------|----------|-------|----------|-------------|
| **1** | First-time login (Super Admin สร้าง user) | กรอก username + **temp password** ที่ได้จาก email | `POST /auth/login` | JWT scope `CHANGE_PASSWORD` (TTL 15 นาที, trigger `first_time`) |
| **2** | Normal login | กรอก username + password | `POST /auth/login` | Full tokens (access 15m + refresh **24h**) |
| **3** | Password expired (90 วัน) | กรอก username + password | `POST /auth/login` | JWT scope `CHANGE_PASSWORD` (TTL 15 นาที, trigger `expired`) |

> **Case 1 + Case 3 ลงเอยที่หน้า Change Password** → หลัง set password → กลับ login → Case 2

---

## 2. Design Decisions

### 2.1 Session Strategy: JWT + Redis Blacklist + Single-Session

| Token | Algorithm | TTL | Storage | หมายเหตุ |
|-------|-----------|-----|---------|---------|
| Access Token | JWT HS256 | **15 นาที** | stateless (client-side) | ใช้ใน `Authorization: Bearer` |
| Refresh Token | JWT HS256 | **24 ชั่วโมง** | Redis (`refresh_token:{userId}:{jti}`) | one-time use — rotate ทุกครั้งที่ refresh |
| Token Blacklist | — | จนกว่า access token จะหมดอายุ | Redis (`token_blacklist:{jti}`) | สำหรับ logout / change-password / single-session kick |

#### Single-Session Enforcement (Soft Kick)

**Business rule:** หากมีคนอื่น login ด้วย user เดียวกัน → คนก่อนหน้าโดนเตะออก

**Implementation:**
- ทุก refresh token เก็บใน Redis ภายใต้ key pattern `refresh_token:{userId}:{jti}`
- ตอน **login สำเร็จ:**
  1. `SCAN refresh_token:{userId}:*` → DEL ทุกตัวที่เจอ (revoke session เก่า)
  2. SET `refresh_token:{userId}:{newJti}` ใหม่
- ผลลัพธ์:
  - **Session เก่า:** access token ใช้ได้จนหมดอายุตามธรรมชาติ (≤ 15 นาที) แต่ refresh ไม่ได้ → ภายใน 15 นาที session เก่าโดน logout
  - **Session ใหม่:** ใช้ปกติ

### 2.2 Token Scopes

- `FULL` — เข้าได้ทุก endpoint ของ user role นั้น (ใช้หลัง login สำเร็จและรหัสไม่ใช่ temp/expired)
- `CHANGE_PASSWORD` — เข้าได้เฉพาะ `/auth/change-password` (ใช้กรณี Case 1 / Case 3)

**JWT claim `trigger`** (เฉพาะ scope `CHANGE_PASSWORD`) ระบุที่มา:
- `first_time` — มาจาก login ด้วย temp password (Case 1)
- `expired` — มาจาก login + password หมดอายุ 3 เดือน (Case 3)

### 2.3 Password Policy

- ความยาวอย่างน้อย **8 ตัวอักษร** สูงสุด 128
- ต้องมี: ตัวพิมพ์ใหญ่ + ตัวพิมพ์เล็ก + ตัวเลข + อักขระพิเศษ (`@$!%*?&`)
- **ห้ามซ้ำกับรหัสผ่านชั่วคราว** (ตรวจสอบจาก `tempPasswordHash` ถ้ามี — code `2012`)
- ~~ห้ามซ้ำกับรหัสผ่านล่าสุด~~ _(เลิกใช้ 2026-05-25 — code `2011` comment ไว้, ห้าม recycle)_
- ห้ามตรงกับรหัสปัจจุบัน (trigger=`expired`)
- หมดอายุทุก **90 วัน** (`passwordExpiresAt = passwordChangedAt + 90 days`)
- Hash ด้วย **bcrypt** cost 12

### 2.4 Temporary Password (Super Admin Create User)

ฝั่ง User Management module:
- Super Admin สร้าง user → ระบบ **gen temp password** (random strong 12 chars) + ส่งไปยัง email ของ user
- บันทึก:
  - `tempPasswordHash`: bcrypt hash ของ temp password
  - `tempPasswordExpiresAt`: NOW + 7 วัน
  - `isTempPassword`: true
  - `passwordHash`: null (ยังไม่มี real password)

User ใช้ temp password login:
- ระบบเช็คตามลำดับ:
  1. Match กับ `passwordHash` (real password) ก่อน — ถ้าตรง = Case 2 หรือ 3
  2. ถ้าไม่ตรง → ลอง match กับ `tempPasswordHash` (ถ้า `isTempPassword=true` + ไม่หมดอายุ) — ถ้าตรง = Case 1
  3. ถ้าไม่ตรงทั้งคู่ → 401 Invalid credentials

**Reusability:** Temp password ใช้ซ้ำได้จนกว่า user จะ set รหัสใหม่สำเร็จ
- เผื่อ user login ด้วย temp password แล้วปิด browser / logout ก่อน set password → กลับมา login ด้วย temp password เดิมได้
- หลัง set password สำเร็จ → `tempPasswordHash = null`, `isTempPassword = false`

**Expiry:** Temp password หมดอายุ 7 วัน → user ต้องติดต่อ Super Admin เพื่อ reset (gen ใหม่)

### 2.5 Security Hardening

- **Rate limiting:** 5 attempts / นาที / IP ต่อ `/auth/login` (HTTP 500 code 1014)
- **User enumeration protection:** "user not found" และ "wrong password" ใช้ HTTP 401 + message เดียวกัน (`Invalid credentials`)
- **Audit logging:** ทุก login/logout/change-password บันทึกลง collection `audit_logs`
- **HTTPS only** ในทุก environment (รวม dev)
- **ไม่มี Account Lockout** — กรอกผิดกี่ครั้งก็ไม่ lock (ตาม business rule ใหม่)

---

## 3. MongoDB Schema

### 3.1 `users` Collection

> หมายเหตุ: collection นี้จัดการโดย User Management module — Auth module **อ่านอย่างเดียว** + อัปเดต fields ที่เกี่ยวกับ password เท่านั้น

```json
{
  "_id": "ObjectId('6650abc...')",
  "username": "700",
  "employeeCode": "700",
  "email": "suwimol.rak@example.com",
  "tel": "812345678",
  "firstName": "สุวิมล",
  "lastName": "รักษาสัตย์",
  "roleCode": "sale_mng",
  "status": "ACTIVE",

  "passwordHash": "$2a$12$...",
  "passwordChangedAt": "2026-02-12T08:00:00Z",
  "passwordExpiresAt": "2026-05-13T08:00:00Z",

  "tempPasswordHash": null,
  "tempPasswordExpiresAt": null,
  "isTempPassword": false,

  "lastLoginAt": "2026-05-12T10:30:00Z",
  "lastLoginIP": "10.0.1.42",

  "createdAt": "2026-01-01T00:00:00Z",
  "updatedAt": "2026-05-12T10:30:00Z",
  "createdBy": "ObjectId('...')",
  "updatedBy": "ObjectId('...')"
}
```

#### Field details

| Field | Type | Description |
|-------|------|-------------|
| `username` | string | Unique. ใช้ login (ตามตัวอย่าง business = `"700"` รหัสพนักงาน) |
| `employeeCode` | string | รหัสพนักงาน (อาจเหมือนกับ username) |
| `email` | string | Email ที่รับ temp password |
| `tel` | string | เบอร์โทร (ไม่มี country code prefix) |
| `firstName` / `lastName` | string | **บังคับ** — ภาษาไทย |
| `roleCode` | string (snake_case) | เช่น `super_admin`, `sale_mng`, `marketing`, `support`, `credit_ops`, `analyst`, `contract`, `verify`, `account` |
| `status` | enum | `ACTIVE` / `INACTIVE` / `SUSPENDED` (uppercase) |
| `passwordHash` | string\|null | bcrypt ของรหัสปัจจุบัน. null = ยังไม่เคย set (ใช้ temp password เท่านั้น) |
| `passwordChangedAt` | Date | วันที่ตั้ง/เปลี่ยน password ล่าสุด |
| `passwordExpiresAt` | Date | `passwordChangedAt + 90 days` |
| `tempPasswordHash` | string\|null | bcrypt ของ temp password (null หลัง user set password สำเร็จ) |
| `tempPasswordExpiresAt` | Date\|null | TTL 7 วันหลัง gen |
| `isTempPassword` | boolean | true = บังคับเปลี่ยน password ครั้งหน้าที่ login (สำหรับ first-time + admin reset) |

#### Lifecycle scenarios

| Scenario | passwordHash | tempPasswordHash | isTempPassword |
|----------|-------------|------------------|----------------|
| Admin สร้าง user ใหม่ | `null` | `bcrypt(temp)` | `true` |
| User set password สำเร็จ (Case 1) | `bcrypt(new)` | `null` | `false` |
| Admin reset password | (keep) | `bcrypt(new temp)` | `true` |
| User เปลี่ยน password (expired — 90 วัน) | `bcrypt(new)` | `null` | `false` |
| User login ด้วย password ปกติ | (no change) | (no change) | `false` |

**Indexes:**
- `{ username: 1 }` unique
- `{ employeeCode: 1 }` unique
- `{ email: 1 }` unique
- `{ status: 1, roleCode: 1 }`

### 3.2 `audit_logs` Collection

```json
{
  "_id": "ObjectId('...')",
  "userId": "ObjectId('...')",
  "username": "700",
  "action": "LOGIN_SUCCESS",
  "ip": "10.0.1.42",
  "userAgent": "Mozilla/5.0 ...",
  "metadata": { "tokenJti": "...", "trigger": "first_time" },
  "createdAt": "2026-05-12T10:30:00Z"
}
```

**Actions:** `LOGIN_SUCCESS`, `LOGIN_FAILED`, `LOGIN_TEMP_PASSWORD`, `LOGOUT`, `TOKEN_REFRESH`, `PASSWORD_CHANGED`, `PASSWORD_EXPIRED_FORCED_CHANGE`, `SESSION_KICKED`, `TEMP_PASSWORD_RESET`

---

## 4. Redis Keys

| Key Pattern | Value | TTL | Purpose |
|-------------|-------|-----|---------|
| `refresh_token:{userId}:{jti}` | `{ issuedAt, ip }` (JSON) | 24 ชั่วโมง | เก็บ refresh token (one-time use) — pattern ช่วยให้ scan revoke ทั้ง user ได้ |
| `token_blacklist:{jti}` | `1` | จนกว่า access token จะ exp | บล็อก access token ที่ถูก revoke แล้ว |
| `login_attempts:{ip}` | counter | 1 นาที | Rate limit login per IP |

---

## 5. Standard Response Envelope

ทุก response ใช้รูปแบบเดียวกัน:

**Success (2xx):**
```json
{
  "code": "0000",
  "message": "Success",
  "requestId": "req-...",
  "responseTime": "2026-05-20T10:30:00.000+07:00",
  "apiVersion": "1.0.0",
  "data": { /* endpoint-specific */ }
}
```

**Error (4xx/5xx):**
```json
{
  "code": "2001",
  "error": "invalid_credentials",
  "message": "Invalid username or password",
  "requestId": "req-...",
  "responseTime": "2026-05-20T10:30:00.000+07:00",
  "apiVersion": "1.0.0",
  "errorData": []
}
```

---

## 6. ไฟล์ในโฟลเดอร์นี้

### 6.1 API Specification (Markdown + Mermaid)

| File | Description |
|------|-------------|
| [`login.md`](./login.md) | POST /auth/login — รวม 3 cases (first-time / normal / expired) |
| [`logout.md`](./logout.md) | POST /auth/logout |
| [`refresh-token.md`](./refresh-token.md) | POST /auth/refresh-token (token rotation + anti-replay) |
| [`change-password.md`](./change-password.md) | POST /auth/change-password (3 triggers) |

> `get-me.md` ย้ายไป [`../profile/get-me.md`](../profile/get-me.md) (endpoint รีเนมเป็น `GET /profile/me`, 2026-05-25)

### 6.2 PlantUML Diagrams

| File | Description |
|------|-------------|
| [`flow-authMaster.puml`](./flow-authMaster.puml) | 🌟 Master — รวม 3 cases + convergence |
| [`sq-authMaster.puml`](./sq-authMaster.puml) | 🌟 Master sequence diagram |
| [`flow-authLogin.puml`](./flow-authLogin.puml) | Flow: POST /auth/login (3 cases) |
| [`sq-authLogin.puml`](./sq-authLogin.puml) | SQ: POST /auth/login (3 cases) |
| [`flow-authRefreshToken.puml`](./flow-authRefreshToken.puml) | Flow: POST /auth/refresh (client trigger + server rotation, 8h sliding) |
| [`sq-authRefreshToken.puml`](./sq-authRefreshToken.puml) | SQ: POST /auth/refresh (end-to-end with single-flight lock) |
| [`flow-authChangePassword.puml`](./flow-authChangePassword.puml) | Flow: POST /auth/change-password (3 triggers) |
| [`sq-authChangePassword.puml`](./sq-authChangePassword.puml) | SQ: POST /auth/change-password (3 triggers) |
| [`flow-feChangePassword.puml`](./flow-feChangePassword.puml) | Flow: FE-side Change Password (Vue 3 + Vuelidate) |
| [`flow-authLogout.puml`](./flow-authLogout.puml) | Flow: POST /auth/logout (blacklist + refresh cleanup) |
| [`sq-authLogout.puml`](./sq-authLogout.puml) | SQ: POST /auth/logout (end-to-end + FE clear-and-redirect) |
