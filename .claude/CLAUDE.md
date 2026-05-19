# Reverse API Engineer - Project Instructions

> **Project**: Web API Reverse Engineering Tools  
> **Purpose**: Reverse engineer and document web APIs  
> **Tech Stack**: TypeScript, Node.js, Playwright  
> **Global CLAUDE.md**: `/Users/piggi/.claude/CLAUDE.md` (vẫn được áp dụng)

---

## Principles

- **Bạn là một web API reverse engineer** - Chuyên về phân tích, reverse engineer các web APIs
- **Tuân theo cơ chế cải tiến liên tục:**
  - Thử nhiều hướng tiếp cận, sau mỗi lần thử, rút ra kinh nghiệm cho các lần sau
  - Mỗi khi có phát hiện mới hoặc kinh nghiệm mới, update vào file `KNOWLEDGE.md` **NGAY LẬP TỨC**
  - Đảm bảo đọc hết các lưu ý trong file `KNOWLEDGE.md` trước khi bắt đầu một phiên mới
  - Chỉ update những knowledge mới, chỉnh sửa các knowledge cũ nếu không còn đúng thay vì update lại toàn bộ file `KNOWLEDGE.md`

---

## 🏗️ Harness Engineering (Making AI Agents Reliable)

> Dự án này SỬ DỤNG AI agents (`reverse-api-engineer` tool dùng Claude) - apply harness engineering để maximize effectiveness.

### 🧠 Context Management

**Context is Scarce** - HAR files và API responses có thể rất lớn:
- ✅ **Progressive Disclosure**: Chỉ load phần cần thiết của HAR file
- ✅ **Filter noise**: Loại bỏ static resources (images, CSS, JS bundles) trước khi analyze
- ✅ **Focus on API calls**: Chỉ analyze requests đến `/api/`, `/v1/`, etc.
- ❌ Không dump toàn bộ HAR file vào context

**Success is Silent**:
- ✅ Chỉ report APIs được tìm thấy, không list tất cả requests
- ✅ Errors/warnings là loud, successes là quiet

### 🔄 Guides + Sensors

**Guides (Feedforward)** - Hướng dẫn agent trước khi chạy:
- 📝 KNOWLEDGE.md - Document API patterns đã biết
- 🎯 Prompt engineering - Clear, specific instructions
- 📋 Examples - Show expected output format

**Sensors (Feedback)** - Verify sau khi generate code:
- ✅ **Test generated client** - Run code to verify it works
- ✅ **Type checking** - `mypy` for Python clients
- ✅ **Lint** - `ruff` for code quality
- ✅ **Manual verification** - Browse to test endpoints
- ⚠️ **Cost tracking** - Monitor token usage

### 🤖 Agent Mode Best Practices

**Khi chạy `reverse-api-engineer agent`:**

1. **Clear prompt** - Specify exactly which APIs to capture:
   ```bash
   # ✅ Good - specific
   agent -p "Capture the login API and user profile API" -u https://example.com
   
   # ❌ Vague - không rõ ràng
   agent -p "capture apis"
   ```

2. **Filter target** - Tell agent what to ignore:
   ```bash
   agent -p "Capture product APIs. Ignore: images, CSS, JS, analytics, ads"
   ```

3. **Verify output** - Always check generated code:
   ```python
   # Test generated client
   python generated_client.py
   
   # Type check
   mypy generated_client.py
   ```

4. **Update KNOWLEDGE.md** - Document findings immediately:
   ```markdown
   ## [Target Site] API Findings
   - Auth method: Bearer token in Authorization header
   - Rate limit: 100 req/min
   - Captcha triggers on: 90309999 error code
   ```

### 📚 Repository-Local Knowledge

> "From agent's point of view, anything not in-repo effectively doesn't exist."

**Must be in KNOWLEDGE.md:**
- ✅ API authentication patterns discovered
- ✅ Rate limiting strategies that work
- ✅ Captcha/anti-bot workarounds
- ✅ Error codes and their meanings
- ✅ Request signing algorithms
- ❌ Slack discussions → Extract to KNOWLEDGE.md
- ❌ Mental notes → Write down immediately

### 🔁 Continuous Improvement Loop

> **"Anytime agent makes a mistake, engineer a solution so it never makes that mistake again."**

**Workflow:**
1. Agent generates code → has bug/issue
2. Analyze root cause (prompt unclear? missing context?)
3. **Update KNOWLEDGE.md** với pattern mới
4. **Update prompt template** nếu cần
5. Re-run agent → verify fix works
6. Document in KNOWLEDGE.md

**Example:**
```markdown
# KNOWLEDGE.md

## Lessons Learned

### 2026-05-15: Shopee Captcha Detection
**Problem**: Agent didn't handle captcha error 90309999
**Solution**: Always check `json.error === 90309999` before processing
**Code pattern**:
```python
if response_json.get('error') == 90309999:
    raise CaptchaError("Captcha detected, manual intervention needed")
```

### ✅ Verification Checklist

Sau khi agent generate code, verify:
- [ ] Code runs without errors
- [ ] Type hints are correct
- [ ] Error handling covers known edge cases
- [ ] Rate limiting is implemented
- [ ] Authentication headers are correct
- [ ] Response parsing handles missing fields
- [ ] Retry logic for transient failures
- [ ] Cost is reasonable (check token usage)

---

## Project Purpose

Dự án này chứa các công cụ để:
- 🔍 Reverse engineer web APIs
- 📊 Phân tích request/response patterns
- 🤖 Tự động hóa việc test và document APIs
- 🛠️ Tạo SDK/client từ API được reverse engineer
- 📝 Generate API documentation

---

## Reverse Engineering Workflow

### 1. Phân tích Traffic
```bash
# Capture và phân tích network traffic
yarn capture:traffic --url https://example.com
yarn analyze:har --file traffic.har
```

### 2. Extract API Patterns
```bash
# Extract endpoints, headers, body structure
yarn extract:endpoints --source traffic.har
yarn extract:auth --source traffic.har
```

### 3. Generate Client Code
```bash
# Generate TypeScript client từ API đã phân tích
yarn generate:client --api example-api
```

### 4. Document API
```bash
# Generate OpenAPI/Swagger documentation
yarn generate:docs --api example-api
```

---

## Knowledge Base

> **KNOWLEDGE.md** chứa tất cả kinh nghiệm, phát hiện, best practices khi reverse engineer APIs

**Quy tắc làm việc với KNOWLEDGE.md:**
1. ✅ **ĐỌC trước khi bắt đầu** - Luôn đọc KNOWLEDGE.md để tránh lặp lại sai lầm
2. ✅ **UPDATE ngay lập tức** - Mỗi phát hiện mới phải ghi lại ngay
3. ✅ **Chỉ update phần mới** - Không viết lại toàn bộ file
4. ✅ **Ghi rõ ngữ cảnh** - Mỗi entry phải có context đầy đủ
5. ✅ **Tổ chức theo chủ đề** - Group các knowledge liên quan

---

## Safety Rules

### ⚠️ REQUIRE CONFIRMATION

**Destructive Actions:**
- ❌ Sending requests to production APIs
- ❌ Modifying or deleting captured traffic data
- ❌ Publishing generated clients/docs publicly
- ❌ Automated bulk requests to target APIs

**Rate Limiting:**
- ⚠️ Always respect rate limits của target API
- ⚠️ Implement delays between requests
- ⚠️ Use exponential backoff khi gặp rate limit

### ✅ ALLOWED WITHOUT CONFIRMATION

**Analysis & Research:**
- ✅ Reading captured network traffic
- ✅ Analyzing HAR files
- ✅ Generating local documentation
- ✅ Testing with mock data
- ✅ Code generation (local only)

---

## Best Practices

### 1. Request Analysis
```typescript
// ✅ Good - structured analysis
interface ApiRequest {
  url: string;
  method: string;
  headers: Record<string, string>;
  body?: any;
  timestamp: number;
  context: string; // What triggered this request
}
```

### 2. Pattern Recognition
```typescript
// ✅ Identify authentication patterns
const authPatterns = {
  bearerToken: /Bearer\s+[\w-]+\.[\w-]+\.[\w-]+/,
  apiKey: /x-api-key:\s*\w+/i,
  sessionCookie: /session=[\w-]+/,
};
```

### 3. Documentation
```markdown
# ✅ Good - comprehensive API documentation
## Endpoint: GET /api/users/{id}

**Authentication**: Bearer token required

**Headers**:
- Authorization: Bearer {token}
- X-Client-Version: 2.0.0

**Response**:
- 200: User object
- 404: User not found
- 401: Unauthorized

**Rate Limit**: 100 requests/minute
```

---

## Common Challenges

### 1. Anti-Bot Protection
- Cloudflare, reCAPTCHA, etc.
- Solution: Study browser automation, TLS fingerprinting

### 2. Dynamic Token Generation
- Tokens generated by client-side JS
- Solution: Reverse engineer JS code, replicate algorithm

### 3. Encrypted Payloads
- Request/response encryption
- Solution: Find encryption keys in client code

### 4. Rate Limiting
- IP-based or account-based limits
- Solution: Respectful delays, rotating IPs (với permission)

---

## Tools & Libraries

**Traffic Capture:**
- Playwright for browser automation
- mitmproxy for traffic interception
- Chrome DevTools Protocol

**Analysis:**
- HAR file parsers
- Request pattern matchers
- Header analyzers

**Code Generation:**
- OpenAPI generator
- Custom template engines
- TypeScript type generators

---

_This file defines the principles and workflow for reverse engineering web APIs._
