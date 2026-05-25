# POST /auth/refresh-token

ต่ออายุ access token ด้วย refresh token ใช้ **token rotation** — refresh token ตัวเก่าจะถูก revoke ทันทีหลังออกตัวใหม่ (ป้องกัน token replay)

---

## 1. Combined Flow — Client Trigger + Server Rotation

แสดงทั้ง **เงื่อนไขที่ client เรียก `/auth/refresh`** + **server processing** + **client handling ผลลัพธ์**

> **TTL:** Access 15 นาที, Refresh **8 ชั่วโมง (sliding)** — ทุกครั้งที่ refresh สำเร็จ → ได้ pair ใหม่ refresh TTL reset 8h เต็มจาก now (idle 8h+ = auto logout)

```mermaid
flowchart TD
    %% ===== Client Trigger =====
    Start([User action ใน BO<br/>e.g. open /users, click button]) --> CAcc{Client:<br/>มี accessToken<br/>ใน storage?}
    CAcc -- ไม่มี --> ToLogin[/"redirect → /login"/]
    CAcc -- มี --> Send[Send API request<br/>Authorization: Bearer]

    Send --> Resp{Server response}
    Resp -- "200 / non-401" --> ReturnOK[/"return ให้ caller"/]:::ok
    Resp -- "401 (1003)<br/>access_token_expired" --> CheckRef{มี refreshToken<br/>ใน storage?}
    Resp -- "401 (1001/1002)<br/>missing / invalid" --> Logout

    CheckRef -- ไม่มี --> Logout
    CheckRef -- มี --> CallRefresh[/"POST /auth/refresh<br/>Body: refreshToken"/]

    %% ============================================================
    %% TODO: Single-flight lock — ป้องกัน parallel refresh ที่จะโดน
    %% server ตี replay → revoke ทุก session (false-positive logout)
    %% Implement ทีหลังเมื่อ FE พร้อม (Axios interceptor + Promise queue)
    %% ------------------------------------------------------------
    %% CheckRef -- มี --> SingleFlight{มี /auth/refresh<br/>กำลังรันอยู่?<br/>(single-flight lock)}
    %% SingleFlight -- ใช่ --> Wait[Wait current refresh<br/>+ reuse new tokens จาก store]
    %% Wait --> Retry
    %% SingleFlight -- ไม่ --> Acquire[Acquire refresh lock]
    %% Acquire --> CallRefresh
    %% ============================================================

    %% ===== Server Side =====
    CallRefresh --> SValid{Server: validate body<br/>มี refreshToken?}
    SValid -- ไม่ --> S1008[/"400 (1008)<br/>validation_failed"/]:::err

    SValid -- มี --> SParse{Parse JWT<br/>+ verify signature<br/>+ type == refresh}
    SParse -- "signature/format/type ผิด" --> S1004[/"401 (1004)<br/>refresh_token_invalid"/]:::err

    SParse -- "signature OK" --> SExp{exp ≥ NOW?}
    SExp -- "ไม่ (expired)<br/>= idle 8h+" --> S1005[/"401 (1005)<br/>refresh_token_expired"/]:::warn

    SExp -- "ใช่ (valid)" --> SGet[("Redis GET<br/>refresh_token:userId:jti")]
    SGet --> SExists{พบใน Redis?}
    SExists -- "ไม่พบ<br/>(JWT ยัง valid แต่ key หาย<br/>= ถูก rotate ไปแล้ว)" --> SReplay[("Redis SCAN+DEL<br/>refresh_token:userId:*<br/>(revoke ทุก session)")]
    SReplay --> SAudit1[Audit:<br/>SUSPICIOUS_REFRESH_REPLAY]
    SAudit1 --> S2009[/"401 (2009)<br/>token_replay"/]:::err

    SExists -- พบ --> SUser[("MongoDB findOne<br/>users { _id }")]
    SUser --> SActive{status<br/>= ACTIVE?}
    SActive -- ไม่ --> SDelOnInactive[("Redis DEL<br/>refresh_token:userId:jti")]
    SDelOnInactive --> S1007[/"403 (1007)<br/>inactive_user"/]:::err

    SActive -- ใช่ --> SGen[Generate JWT pair ใหม่:<br/>access 15m scope=FULL<br/>+ refresh 8h<br/>jti ใหม่]
    SGen --> SRotate[("Redis atomic Lua:<br/>DEL old jti<br/>SET new jti EX 28800<br/>= 8h sliding")]
    SRotate --> SAudit2[Audit: TOKEN_REFRESH]
    SAudit2 --> S200[/"200 OK<br/>accessToken + refreshToken<br/>+ expiresIn 900"/]:::ok

    %% ===== Client Post-Refresh =====
    S200 --> SaveTok[Client: save tokens<br/>ทับของเก่า]
    SaveTok --> Retry[Retry original request<br/>ด้วย access ใหม่]
    Retry --> ReturnOK

    S1004 --> ClearLogout[Clear tokens]
    S1005 --> ClearIdle[Clear tokens]
    S1007 --> ClearInactive[Clear tokens]
    S1008 --> ClearLogout
    S2009 --> ClearReplay[Clear tokens<br/>+ broadcast logout ทุก tab]

    ClearLogout --> RedirInvalid[/"→ /login<br/>'กรุณา login ใหม่'"/]
    ClearIdle --> RedirIdle[/"→ /login<br/>'Session หมดอายุ ไม่มี activity 8 ชม.<br/>กรุณา login ใหม่'"/]:::warn
    ClearInactive --> RedirInactive[/"→ /login<br/>'บัญชีถูกปิดการใช้งาน<br/>กรุณาติดต่อ Admin'"/]
    ClearReplay --> RedirReplay[/"→ /login<br/>'⚠️ ตรวจพบความผิดปกติ<br/>session ถูก revoke ทั้งหมด'"/]

    ToLogin --> End([END])
    ReturnOK --> End
    RedirInvalid --> End
    RedirIdle --> End
    RedirInactive --> End
    RedirReplay --> End

    classDef err fill:#FADBD8,stroke:#C0392B,color:#641E16
    classDef warn fill:#FCF3CF,stroke:#E67E22,color:#7E5109
    classDef ok fill:#D5F5E3,stroke:#27AE60,color:#1E8449
    classDef note fill:#FFF9C4,stroke:#F0C000,color:#7E5109
```

### 🚧 TODO: Single-flight lock (comment ไว้ในไดอะแกรม)

> Dev น่าจะรู้อยู่แล้ว — comment ไว้ใน diagram ก่อน implement จริง

ถ้า user คลิกเร็วๆ ตอน access หมดอายุ → หลาย requests ได้ 401 ขนานกัน → call `/auth/refresh` หลายตัวพร้อมกัน → ตัวที่ 2+ จะเจอ "old jti ไม่อยู่ใน Redis" → server ตีความว่า **REPLAY** → revoke ทุก session

**แก้:** FE ต้องมี in-memory mutex / promise queue ให้มี `/auth/refresh` เพียงตัวเดียวรันต่อเวลา ตัวอื่นรอผลลัพธ์แล้วใช้ token ใหม่ที่ rotate มา

### ลำดับการ check (สำคัญ!)

```
1. Verify signature + type   → 1004 ถ้าผิด
2. Check exp claim ≥ NOW     → 1005 ถ้าหมดอายุ (= idle 8h+)
3. Check Redis exists        → 2009 ถ้าไม่พบ (= replay)
4. MongoDB user status       → 1007 ถ้า inactive
```

> **ทำไม signature ก่อน exp?** — security best practice: ป้องกัน info leakage. ถ้าเช็ค exp ก่อน → attacker forge token ปลอม + ใส่ exp พ้น → server ตอบ "expired" → leak ว่าระบบไว้ใจ payload โดยไม่ verify

### 1004 / 1005 / 2009 — แยกกันยังไง?

| สาเหตุ | Signature | JWT exp | Redis key | Code |
|--------|-----------|---------|-----------|------|
| **Token ปลอม / format ผิด** | ผิด | (ไม่ได้เช็ค) | (ไม่ได้เช็ค) | **1004** `refresh_token_invalid` |
| **Idle 8h+** (BA requirement) | OK | < NOW (expired) | TTL หมด | **1005** `refresh_token_expired` |
| **Replay attack** | OK | ≥ NOW (valid) | ถูก DEL ตอน rotate | **2009** `token_replay` |

---

## 2. Combined Sequence Diagram — End-to-End

```mermaid
sequenceDiagram
    autonumber
    actor User as 👤 User
    participant FE as Frontend<br/>(Vue 3 + Axios interceptor)
    %% participant Lock as 🔒 Refresh Lock<br/>(in-memory mutex)  %% TODO: single-flight — implement ทีหลัง
    participant API as API
    participant SVC as Auth Service<br/>(Go)
    participant Redis as Redis
    participant DB as MongoDB

    User->>FE: trigger action<br/>(e.g. open /users page)

    rect rgb(240, 244, 255)
    note right of FE: 1. Pre-flight check
    FE->>FE: read accessToken from storage
    alt no token
        FE-->>User: redirect → /login
    end
    end

    rect rgb(232, 245, 233)
    note right of API: 2. Original request
    FE->>+API: GET /users<br/>Authorization: Bearer {accessToken}
    API-->>-FE: response

    alt response != 401
        FE-->>User: render result
    end
    end

    note over FE, API: ⬇️ ถ้าได้ 401 (1003) access_token_expired ⬇️

    %% ============================================================
    %% TODO: Single-flight lock — comment ไว้ก่อน implement ทีหลัง
    %% Dev น่าจะรู้อยู่แล้ว — ป้องกัน parallel refresh เจอ replay
    %% ------------------------------------------------------------
    %% rect rgb(255, 248, 220)
    %% note right of FE: 3. Single-flight refresh
    %% FE->>+Lock: tryAcquire(refreshLock)
    %% alt lock held by another request
    %%     Lock-->>FE: wait...
    %%     Lock-->>-FE: ✅ unlocked (with new tokens in store)
    %%     FE->>FE: read latest tokens
    %%     FE->>API: retry GET /users (new Bearer)
    %%     API-->>FE: 200 OK
    %%     FE-->>User: render result 🎉
    %% else lock acquired
    %%     Lock-->>FE: ✅ acquired
    %% end
    %% end
    %% ============================================================

    rect rgb(232, 244, 253)
    note right of SVC: 3. Server processing — /auth/refresh
    FE->>+API: POST /auth/refresh<br/>{ refreshToken }
    API->>+SVC: refresh(refreshToken, ip)

    SVC->>SVC: 1. verify signature + type==refresh
    alt signature/format/type invalid
        SVC-->>API: ERR_REFRESH_INVALID
        API-->>FE: 401 (1004) refresh_token_invalid
    end

    SVC->>SVC: 2. check exp claim ≥ NOW
    alt exp < NOW (idle 8h+)
        SVC-->>API: ERR_REFRESH_EXPIRED
        API-->>FE: 401 (1005) refresh_token_expired
    end

    SVC->>Redis: GET refresh_token:{userId}:{jti}
    Redis-->>SVC: stored_data | nil

    alt nil — REPLAY suspected<br/>(JWT ยัง valid แต่ Redis key หาย = ถูก rotate ไปแล้ว)
        SVC->>Redis: SCAN+DEL refresh_token:{userId}:*<br/>(revoke ทุก session)
        SVC->>DB: audit_log (SUSPICIOUS_REFRESH_REPLAY)
        SVC-->>API: ERR_TOKEN_REPLAY
        API-->>FE: 401 (2009) token_replay
    end

    SVC->>DB: db.users.findOne({ _id: userId })
    DB-->>SVC: user

    alt status != ACTIVE
        SVC->>Redis: DEL refresh_token:{userId}:{jti}
        SVC-->>API: ERR_INACTIVE
        API-->>FE: 403 (1007) inactive_user
    end

    SVC->>SVC: generate access (15m, scope=FULL)<br/>+ refresh (8h, new jti)
    SVC->>Redis: Lua atomic:<br/>DEL refresh_token:{userId}:{old_jti}<br/>SET refresh_token:{userId}:{new_jti} EX 28800
    Redis-->>SVC: OK
    SVC->>DB: audit_log (TOKEN_REFRESH)

    SVC-->>-API: { accessToken, refreshToken, expiresIn: 900 }
    API-->>-FE: 200 OK
    end

    rect rgb(232, 245, 233)
    note right of FE: 4. Client post-refresh handling
    FE->>FE: save new tokens<br/>(ทับของเก่า — สำคัญ!)

    FE->>+API: retry GET /users (new Bearer)
    API-->>-FE: 200 OK
    FE-->>User: render result 🎉
    end

    note over User, DB: ⬇️ Error paths — FE behavior ⬇️

    alt 401 (1005) refresh_token_expired (idle 8h+)
        FE->>FE: clear tokens
        FE-->>User: redirect /login<br/>"Session หมดอายุ ไม่มี activity 8 ชม."
    else 401 (1004) refresh_token_invalid
        FE->>FE: clear tokens
        FE-->>User: redirect /login<br/>"กรุณา login ใหม่"
    else 401 (2009) token_replay
        FE->>FE: clear tokens
        FE->>FE: broadcast logout ทุก tab (BroadcastChannel)
        FE-->>User: redirect /login<br/>"⚠️ ตรวจพบความผิดปกติ session ถูก revoke ทั้งหมด"
    else 403 (1007) inactive_user
        FE->>FE: clear tokens
        FE-->>User: redirect /login<br/>"บัญชีถูกปิดการใช้งาน"
    end
```

---

## 3. API Specification

### 3.1 Request

```http
POST /auth/refresh-token HTTP/1.1
Content-Type: application/json
```

```json
{
  "refreshToken": "eyJhbGciOiJIUzI1NiI..."
}
```

**Body fields:**
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `refreshToken` | string | ✅ | JWT refresh token ที่ได้จาก `/auth/login` หรือจาก refresh ครั้งก่อน |

> **หมายเหตุ:** ไม่ต้องส่ง `Authorization` header — access token อาจหมดอายุไปแล้ว

---

### 3.2 Response — 200 Refresh Success

```json
{
  "statusCode": "200",
  "code": "0000",
  "message": "Token refreshed successfully",
  "traceID": "5a132ae0-...",
  "responseTime": "2026-05-12T10:45:00.000+07:00",
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiI... (new)",
    "refreshToken": "eyJhbGciOiJIUzI1NiI... (new)",
    "tokenScope": "FULL",
    "expiresIn": 900
  }
}
```

> **ข้อสำคัญ:** หลัง refresh สำเร็จ — frontend ต้องเก็บ `refreshToken` ตัวใหม่และทิ้งตัวเก่า เพราะตัวเก่าถูก revoke แล้ว

---

### 3.3 Error Responses

#### 400 Validation Failed
```json
{
  "statusCode": "400",
  "code": "1008",
  "error": "validation_failed",
  "message": "Refresh token is required",
  "traceID": "...",
  "responseTime": "2026-05-12T10:45:00.000+07:00",
  "data": {
    "errors": [{ "field": "refreshToken", "message": "required" }]
  }
}
```

#### 401 Invalid Refresh Token
```json
{
  "statusCode": "401",
  "code": "1004",
  "error": "refresh_token_invalid",
  "message": "Invalid or expired refresh token",
  "traceID": "...",
  "responseTime": "2026-05-12T10:45:00.000+07:00",
  "data": null
}
```

#### 401 Token Replay Detected
```json
{
  "statusCode": "401",
  "code": "2009",
  "error": "token_replay",
  "message": "Refresh token replay detected. All sessions have been revoked.",
  "traceID": "...",
  "responseTime": "2026-05-12T10:45:00.000+07:00",
  "data": null
}
```

> **Frontend behavior:** เมื่อได้ code `2009` (token_replay) → force logout ทุก tab + redirect ไปหน้า login + แสดง message แจ้งเตือนความปลอดภัย

#### 401 Account Inactive
```json
{
  "statusCode": "401",
  "code": "1007",
  "error": "inactive_user",
  "message": "บัญชีนี้ถูกปิดการใช้งาน",
  "traceID": "...",
  "responseTime": "2026-05-12T10:45:00.000+07:00",
  "data": null
}
```

---

## 4. Implementation Notes

### 4.1 Token Rotation = Security Best Practice

**ทำไมต้อง rotate:**
- ถ้า refresh token รั่ว → attacker จะ refresh ได้ครั้งเดียว ก่อนที่เจ้าของ token จะ refresh ครั้งถัดไป
- พอเจ้าของ refresh ครั้งถัดไป → ใช้ token เก่า (ที่ attacker ขโมยไป) จะถูก detect = REPLAY → revoke ทุก session

**Trade-off:** Refresh token หาย/ใช้ผิด session → user ต้อง login ใหม่ (acceptable trade-off สำหรับ BO ภายใน)

### 4.2 Go Service Signature

```go
type RefreshInput struct {
    RefreshToken string
    IP           string
}

type RefreshOutput struct {
    AccessToken  string
    RefreshToken string
    TokenScope   string
    ExpiresIn    int
}

func (s *AuthService) Refresh(ctx context.Context, in RefreshInput) (*RefreshOutput, error)
```

### 4.3 Atomic Rotation (Lua Script)

เพื่อป้องกัน race condition (2 refresh พร้อมกัน) — ใช้ Redis Lua script ให้ DEL old + SET new เป็น atomic:

```lua
-- KEYS[1] = refresh_token:{old_jti}
-- KEYS[2] = refresh_token:{new_jti}
-- ARGV[1] = new_data (JSON)
-- ARGV[2] = TTL

if redis.call('EXISTS', KEYS[1]) == 0 then
  return 'REPLAY'  -- old token ไม่มี = ถูกใช้ไปแล้ว
end

redis.call('DEL', KEYS[1])
redis.call('SET', KEYS[2], ARGV[1], 'EX', ARGV[2])
return 'OK'
```

### 4.4 Refresh = Sliding Window 8 ชั่วโมง

Refresh token ใหม่มี TTL **8 ชั่วโมงเต็ม** จากตอน rotate (sliding window) — ไม่ใช่จาก login เดิม

**ผลลัพธ์ตาม BA rule** ("ไม่ยิง API ใดๆ ภายใน 8 ชม. = auto logout"):
- **Active user** (มี API call ตลอด) → refresh slide ทุกครั้ง → session ไม่หมด
- **Idle user** (ไม่มี API call 8h+) → Redis TTL หมดอายุ + JWT exp ผ่าน → ครั้งต่อไปยิง refresh ได้ `401 (1005) refresh_token_expired` → force re-login

> **ไม่มี absolute max session cap** ในรอบนี้ — ตาม BA spec ตอนนี้  active session = ไม่จำกัด
> ถ้าอนาคตต้องการ "max session lifetime 12-24 ชม." — เก็บ `issuedAtOriginal` ใน JWT claims และเช็คตอน refresh
