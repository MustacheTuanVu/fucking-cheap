# Verification Integrity

> Ngăn substitute verification method, bảo vệ test layer coupling.

# 60. Verification Layer Integrity

Mỗi acceptance criterion gắn liền với **verification layer**. Layer không được thay thế một cách tự do.

## 60.1 Layer coupling

```
UI behavior      → UI test (Playwright, screenshot, rendering check)
API contract     → API test (curl, httpie, supertest)
Data integrity   → DB query/assertion
Business logic   → Unit test
Integration flow → Integration/E2E test
```

Một test ở layer A **không thể chứng minh** behavior ở layer B. API test pass không chứng minh UI render đúng.

## 60.2 Verification method lock

Trong plan decomposition, mỗi subtask gắn verification method:

```
Subtask 1: Login flow
- Verification method: UI (Playwright)
- Layer: UI
```

Sau khi plan được approve, verification method = **locked contract**. Đổi contract = requirement change = PHẢI có lý do + user approval.

## 60.3 Phân loại substitution

| Thay đổi | Verdict |
|---|---|
| Playwright → API test cho cùng UI behavior | **CẤM** |
| Thêm API test BÊN CẠNH Playtest hiện có | **ĐƯỢC** |
| Playwright → simpler Playwright test (cùng layer) | **XEM XÉT** |
| Replace assertion trong test hiện có | **CẤM** (test weakening) |
| Playwright fail vì infra (no browser) → báo blocker | **ĐÚNG** |

## 60.4 Khi Playwright test fail

Phiên bản đúng:

1. **Diagnostic:** Phân tích WHY fail — selector sai? timing? stale state? render issue?
2. **Fix root cause** trong test hoặc trong production code.
3. **Thu evidence mới** trước khi retry.
4. Nếu infra không hỗ trợ → **REPORT BLOCKER**, không thay method.

Phiên bản sai:

```
- Xóa Playwright test
- Viết API test thay thế
- Claim "verified" vì API test pass
```

## 60.5 Failure reason logging

Khi test method thay đổi trong quá trình thực thi, PHẢI log:

```text
CHANGED METHOD FROM: Playwright (UI)
CHANGED METHOD TO: API test
REASON: [exact reason — infra? repeated failure? user request?]
ATTEMPTED FIXES: [N times, evidence per attempt]
ACCEPTANCE CRITERIA COVERED: [same? partial? different layer?]
USER APPROVAL: [yes/no/pending]
```

Không được log chung chung kiểu "test đã được cập nhật."

## 60.6 Completion gate — Verification Integrity

Trước `DONE`, kiểm tra:

- [ ] Không có verification method nào bị downgrade so với plan approve.
- [ ] Nếu Playwright test bị remove/disabled, có explicit justification + user acknowledgment.
- [ ] Replacement test cover **cùng acceptance criteria** ở **cùng layer**.
- [ ] Cross-layer substitution chỉ xảy ra khi infra客观 không khả dụng.
- [ ] Mọi changed method có logged reason như Section 60.5.

Nếu bất kỳ criterion fail → không được tuyên bố DONE cho phần verification đó.

## 60.7 Dual-layer completion requirement

Để tuyên bố `DONE` cho task có cả UI và API component, PHẢI có:

1. Ít nhất 1 **UI-layer test** (Playwright, screenshot, rendering check).
2. Ít nhất 1 **API-layer test** (curl, httpie, supertest).
3. Cả hai cover acceptance criteria của task.

**Thiếu 1 trong 2 = KHÔNG ĐƯỢC tuyên bố DONE.** Không có ngoại lệ trừ khi task thật sự chỉ có 1 layer (pure API backend không có UI, hoặc pure frontend không có API call).

```text
Task có UI + API component:
  ✅ UI test pass + API test pass → DONE được
  ⚠️ UI test pass, API test missing → BLOCK, chưa đủ
  ⚠️ API test pass, UI test missing → BLOCK, chưa đủ
  ❌ Cả hai missing → BLOCK, chưa đủ
```

Trường hợp chỉ có 1 layer:
- Pure backend API task → API test đủ
- Pure frontend rendering (không gọi API) → UI test đủ
- Task nào quyết định cần explicit justification trong plan

## 60.8 Lazy-load trigger

Load module này khi:

- Task có frontend/UI component.
- Task involve Playwright/E2E test.
- Model đang trong VERIFY phase với UI tests.
- Failure fingerprint cho thấy test method changed giữa attempts.
