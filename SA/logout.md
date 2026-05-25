# POST /auth/logout

ออกจากระบบ — เพิ่ม access token JTI ลงใน Redis blacklist และลบ refresh token ที่เกี่ยวข้องออกจาก Redis

---

## 1. Flowchart

```mermaid
flowchart TD
    Start([Client POST /auth/logout]) --> CheckHeader{มี Authorization<br/>Bearer header?}
    CheckHeader -- ไม่มี --> R401N[/"401 Unauthorized"/]:::err

    CheckHeader -- มี --> ParseJWT{Parse & verify<br/>JWT signature}
    ParseJWT -- invalid --> R401I[/"401 Invalid token"/]:::err

    ParseJWT -- valid --> CheckBlacklist[("Redis GET<br/>token_blacklist:jti")]
    CheckBlacklist --> AlreadyBL{อยู่ใน<br/>blacklist?}
    AlreadyBL -- ใช่ --> R401BL[/"401 Token revoked"/]:::err

    AlreadyBL -- ไม่ --> CheckExp{token<br/>หมดอายุแล้ว?}
    CheckExp -- หมด --> R200Expired[/"200 OK<br/>(no-op — token หมดอายุอยู่แล้ว)"/]:::ok

    CheckExp -- ยัง --> AddBlacklist[(Redis SET<br/>token_blacklist:jti = 1<br/>EXPIRE = ttl_remaining)]
    AddBlacklist --> HasRefresh{Body มี<br/>refreshToken?}

    HasRefresh -- มี --> DelRefresh[(Redis DEL<br/>refresh_token:refresh_jti)]
    HasRefresh -- ไม่มี --> Audit
    DelRefresh --> Audit[Audit: LOGOUT]
    Audit --> R200[/"200 OK<br/>Logged out successfully"/]:::ok

    classDef err fill:#FADBD8,stroke:#C0392B,color:#641E16
    classDef ok fill:#D5F5E3,stroke:#27AE60,color:#1E8449
```

---

## 2. Sequence Diagram

```mermaid
sequenceDiagram
    autonumber
    actor Client as 👤 Client
    participant API as API<br/>/auth/logout
    participant MW as JWT Middleware
    participant SVC as Auth Service
    participant Redis as Redis
    participant DB as MongoDB

    Client->>+API: POST /auth/logout<br/>Authorization: Bearer {accessToken}<br/>Body: { refreshToken? }

    rect rgb(240, 244, 255)
    note right of API: 1. Authenticate Token
    API->>+MW: validate access token
    MW->>MW: parse JWT & verify signature
    alt invalid signature / malformed
        MW-->>API: ERR_INVALID_TOKEN
        API-->>Client: 401 Unauthorized
    end
    MW->>Redis: GET token_blacklist:{jti}
    Redis-->>MW: nil | "1"
    alt already blacklisted
        MW-->>API: ERR_TOKEN_REVOKED
        API-->>Client: 401 Token revoked
    end
    MW-->>-API: claims { userId, jti, exp }
    end

    rect rgb(232, 245, 233)
    note right of SVC: 2. Revoke Tokens
    API->>+SVC: logout(userId, jti, exp, refreshToken?)

    SVC->>SVC: ttlRemaining = exp - NOW()
    alt ttlRemaining <= 0
        SVC-->>API: SUCCESS (no-op)
        API-->>Client: 200 OK
    end

    SVC->>Redis: SET token_blacklist:{jti} = 1<br/>EX ttlRemaining

    opt body มี refreshToken
        SVC->>SVC: parse refreshToken → refreshJti
        SVC->>Redis: DEL refresh_token:{refreshJti}
    end

    SVC->>DB: insert audit_log (LOGOUT)
    SVC-->>-API: SUCCESS
    API-->>-Client: 200 OK
    end
```

---

## 3. API Specification

### 3.1 Request

```http
POST /auth/logout HTTP/1.1
Authorization: Bearer eyJhbGciOiJIUzI1NiI...
Content-Type: application/json
```

```json
{
  "refreshToken": "eyJhbGciOiJIUzI1NiI..."
}
```

**Headers:**
| Header | Required | Description |
|--------|----------|-------------|
| `Authorization` | ✅ | `Bearer {accessToken}` |
| `Content-Type` | ⚪ | `application/json` (จำเป็นถ้าส่ง body) |

**Body fields:**
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `refreshToken` | string | ⚪ | ถ้าส่งมาด้วย backend จะลบ refresh token ออกจาก Redis ด้วย (แนะนำให้ส่งเสมอ) |

---

### 3.2 Response — 200 Logout Success

```json
{
  "statusCode": "200",
  "code": "0000",
  "message": "Logged out successfully",
  "traceID": "5a132ae0-...",
  "responseTime": "2026-05-12T10:35:00.000+07:00",
  "data": {
    "loggedOut": true
  }
}
```

---

### 3.3 Error Responses

#### 401 Unauthorized — ไม่มี Token / Token Invalid
```json
{
  "statusCode": "401",
  "code": "1001",
  "error": "unauthorized",
  "message": "Authentication required",
  "traceID": "...",
  "responseTime": "2026-05-12T10:35:00.000+07:00",
  "data": null
}
```

#### 401 Token Revoked — token อยู่ใน blacklist
```json
{
  "statusCode": "401",
  "code": "1002",
  "error": "access_token_invalid",
  "message": "Token has been revoked",
  "traceID": "...",
  "responseTime": "2026-05-12T10:35:00.000+07:00",
  "data": null
}
```

#### 500 Internal Server Error
```json
{
  "statusCode": "500",
  "code": "1013",
  "error": "internal_server_error",
  "message": "An unexpected error occurred",
  "traceID": "...",
  "responseTime": "2026-05-12T10:35:00.000+07:00",
  "data": null
}
```

---

## 4. Implementation Notes

### 4.1 Blacklist TTL คำนวณจาก `exp` ของ token

ไม่ต้อง blacklist forever — แค่จนกว่า access token จะหมดอายุตามธรรมชาติ Redis จะลบเองด้วย TTL:

```go
ttlRemaining := time.Until(time.Unix(claims.ExpiresAt, 0))
if ttlRemaining > 0 {
    redis.Set(ctx, "token_blacklist:"+claims.JTI, "1", ttlRemaining)
}
```

### 4.2 Idempotent Behavior

- เรียก logout ซ้ำด้วย token เดิม → 401 Token Revoked (ครั้งแรกสำเร็จ ครั้งที่สอง token อยู่ใน blacklist แล้ว)
- เรียก logout ด้วย token ที่หมดอายุ → 200 OK (no-op) เพื่อหลีกเลี่ยง edge case ที่ frontend แจ้งไม่ตรงสถานะ

### 4.3 "Logout from all devices" (Future)

ในรอบนี้ไม่ทำ — แต่ schema รองรับโดย:
- เก็บ `user_sessions:{userId}` เป็น Redis SET ของ JTI ทั้งหมด
- Logout-all → loop blacklist ทุก JTI + DEL refresh tokens ทุกตัวของ user นั้น
