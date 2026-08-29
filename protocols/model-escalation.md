# Harness & Model Escalation

> Hard gates, structured output, harness loop, model escalation và ví dụ thực thi.

# 41. Machine-Enforceable Harness Contract

Prompt rule một mình không đủ cho unreliable model. Khi agent harness xung quanh có thể enforce behavior, phải implement hard gate sau **bên ngoài model**.

## 41.1 Workflow được enforce bằng state

Represent agent state rõ ràng:

```text
INTAKE
RECON
PLAN
EDIT
VERIFY
DIAGNOSE
REVIEW
DONE
BLOCKED
```

Không cho phép illegal transition như:

```text
EDIT → DONE
DIAGNOSE → DONE
INTAKE → EDIT    # trừ trivial change đã được classify rõ
```

## 41.2 Evidence object

Yêu cầu tool output tạo machine-readable evidence record:

```json
{
  "kind": "test",
  "command": "...",
  "exit_code": 0,
  "timestamp": "...",
  "workspace_revision": "...",
  "summary": "..."
}
```

Success assertion phải reference evidence từ current workspace revision.

## 41.3 Dirty-state invalidation

Bất kỳ code edit nào sau successful verification phải invalidate verification evidence trong affected scope.

Ví dụ:

```text
TEST PASS tại revision R1
EDIT → revision R2
=> evidence test của R1 không còn chứng minh R2
```

Harness phải yêu cầu re-verification sau edit.

## 41.4 Done gate

Reject `DONE` trừ khi agent cung cấp:

- acceptance criteria đã xử lý;
- final diff review evidence;
- ít nhất một verification evidence item cho behavioral change, hoặc structured reason vì sao verification không khả dụng;
- danh sách unresolved issue.

## 41.5 Loop detector

Theo dõi:

- repeated command;
- repeated error fingerprint;
- edit cùng file/range;
- reversion về content cũ;
- số speculative attempt.

Nếu cùng failure fingerprint xuất hiện hai lần sau edit, force `DIAGNOSE` và tạm cấm code modification cho tới khi có evidence mới.

## 41.6 Command allow/deny policy

Phân loại command thành:

- read-only;
- normal write;
- dependency-changing;
- destructive;
- external/production-impacting.

Yêu cầu authorization mạnh hơn cho hai nhóm cuối.

## 41.7 Persistence cho Context Ledger

Nếu có thể, lưu Constraint Ledger bên ngoài model context. Reinject compact ledger ở mỗi major agent turn.

Việc này giảm mất constraint khi context compression.

## 41.8 Grounding tool result

Khi agent nhắc tới test/build result, harness nên attach tool result liên quan hoặc trusted summary được tạo từ nó.

Không cho phép free-form self-reported pass/fail status là source of truth duy nhất.

## 41.9 Patch-size guard

Nếu task nhỏ đột nhiên đổi nhiều file hoặc line, trigger review trước khi cho edit tiếp.

Heuristic gợi ý, có thể điều chỉnh theo repository:

```text
small bug task + >5 files changed        → warning
small bug task + >300 changed lines      → warning
unexpected lockfile/generated churn      → warning
```

Đây là heuristic, không phải universal limit.

## 41.10 Test weakening detector

Flag suspicious change như:

- removed assertion;
- new skip;
- lower coverage threshold;
- disabled lint rule;
- widened exception expectation;
- large snapshot update.

Yêu cầu explicit justification trước completion.

---

# 42. Recommended Harness Loop

Một outer loop robust có thể dùng algorithm sau:

```text
receive task
load repository instructions
initialize constraint ledger

while not done:
    choose state

    if state == RECON:
        allow read/search tools
        update verified facts

    if state == PLAN:
        require concrete next change + verification plan

    if state == EDIT:
        allow bounded edits
        mark affected verification stale

    if state == VERIFY:
        execute real command
        save evidence
        if failure:
            fingerprint failure
            transition DIAGNOSE

    if state == DIAGNOSE:
        prohibit speculative patching until new evidence exists
        require hypothesis + evidence
        then allow transition EDIT

    if loop_detected:
        force context compression
        show ledger + recent diff + failure history
        require fresh evidence

    if state == REVIEW:
        require final diff inspection
        check test weakening / secret leakage / scope growth

    if state == DONE:
        validate completion gate
        reject if evidence stale or missing
```

Điểm quan trọng: outer loop không nên chỉ “nhắc” model tuân thủ. Nó phải **khóa capability theo state** khi có thể. Ví dụ, trong `DIAGNOSE`, harness có thể tạm thời chỉ cho phép read/search/test tool và chặn write tool cho tới khi model cung cấp hypothesis cùng evidence mới.

---

# 43. Structured Agent Output gợi ý cho Harness

Khi harness parse được structured output, yêu cầu model emit các field tương tự:

```json
{
  "state": "VERIFY",
  "goal": "...",
  "constraints": ["..."],
  "verified_facts": ["..."],
  "hypothesis": null,
  "next_action": "Run targeted parser test",
  "tool_request": {
    "type": "shell",
    "command": "..."
  },
  "expected_evidence": "Test should reproduce the trailing-field failure"
}
```

Không expose chain-of-thought. Structure chỉ nên chứa decision, fact, action và evidence requirement cần cho orchestration.

Harness NÊN validate schema trước khi thực thi tool. Nếu model trả field thiếu, state transition bất hợp lệ hoặc yêu cầu command không phù hợp state, reject output và yêu cầu model sửa structured action thay vì tự đoán intent.

---

# 50. Compact Runtime Reminder

Với model không thể giữ toàn bộ skill này ổn định trong context, inject condensed reminder sau trước mỗi major reasoning cycle:

```text
RELIABILITY RULES
1. Đọc lại goal + constraints.
2. Verify repository facts; không bao giờ bịa API/file/result.
3. Inspect relevant code trước khi edit.
4. Tạo root-cause patch nhỏ nhất.
5. Sau failure, phải có evidence mới trước edit tiếp theo.
6. Chạy targeted verification thật sau mỗi meaningful final edit.
7. Re-check caller/contract đối với shared change.
8. Inspect final diff tìm regression/unrelated change.
9. Không bao giờ nói test/build pass nếu chưa pass thật trên current code.
10. Nếu blocked, nói exact blocker và strongest evidence đã thu được.
```

Nếu context budget rất hạn chế, giữ ít nhất các rule 1, 2, 4, 5, 6, 8 và 9. Đây là những rule có tỷ lệ giảm hallucination và false completion cao nhất.

---

# 51. Recommended Agent-System Pairing

Skill mạnh nhất khi đi cùng external harness cung cấp:

- persistent task/constraint memory;
- explicit workflow state;
- shell/tool execution;
- repository search;
- patch application;
- git diff/status inspection;
- command result capture;
- test-evidence invalidation sau edit;
- loop detection;
- destructive-command policy;
- secret redaction;
- optional model switching/escalation sau repeated failure.

Model mạnh hơn có thể follow rule tự nhiên hơn. Model yếu hưởng lợi không cân xứng từ hard enforcement vì harness ngăn nó skip step khi attention hoặc instruction adherence degrade.

Thiết kế lý tưởng là chia trách nhiệm:

```text
MODEL
- hiểu task
- hình thành hypothesis
- chọn bước tiếp theo
- đề xuất patch
- giải thích evidence

HARNESS
- giữ state
- giữ ledger
- enforce transition
- chạy tool thật
- capture evidence
- invalidate stale verification
- detect loop
- chặn destructive action
- kiểm soát completion
```

Không giao cho model trách nhiệm tự chứng nhận rằng chính nó đã tuân thủ workflow nếu harness có thể verify điều đó bằng máy.

---

# 52. Escalation Policy cho Weak Model

Nếu harness hỗ trợ model escalation, cân nhắc escalate khi xảy ra bất kỳ điều nào:

- cùng failure fingerprint tồn tại sau hai evidence-based fix attempt;
- agent không thể giải thích current call path một cách coherent;
- constraint violation tái diễn;
- required API không verify được sau repository/doc search;
- patch scope tăng nhanh mà không convergence;
- security/concurrency/data-loss risk vượt demonstrated reliability của model;
- context đã compress nhiều lần nhưng key state vẫn bị mất.

Trước escalation, generate handoff template ở Section 45 để stronger model nhận clean state thay vì toàn noisy transcript.

Escalation không nên diễn ra chỉ vì model gặp một test failure bình thường. Nó dành cho dấu hiệu **không hội tụ**, mất constraint, hoặc risk vượt capability đáng tin cậy đã thể hiện.

---

# 54. Ví dụ: Bug Fix đúng quy trình

User report: “Submit optional nickname rỗng trả về 500.”

Workflow đúng:

```text
INTAKE
- Preserve existing API response schema.

RECON
- Find endpoint.
- Find validation/model code.
- Find existing endpoint tests.
- Reproduce request.

PLAN
- Confirm empty string phải normalize thành null hay được accept trực tiếp dựa trên existing convention.
- Patch tại input-normalization boundary hẹp nhất.
- Add regression test.

IMPLEMENT
- Make minimal change.

VERIFY
- Run new focused test.
- Run endpoint test module.
- Run typecheck nếu relevant.

REVIEW
- Inspect diff.
- Verify không đổi global validator semantics.

DONE
- Report change và exact commands/results.
```

Workflow sai:

```text
- Đoán ORM không thích empty string.
- Add catch-all exception quanh database save.
- Đổi 500 thành 200.
- Không chạy test.
- Nói “fixed”.
```

Lý do sai: patch không chứng minh root cause, có thể che database/programming error, thay đổi error semantics và claim completion không có evidence.

---

# 55. Ví dụ: Dependency/API Behavior đúng

Task cần dùng method của một library chưa quen.

Đúng:

```text
1. Check declared/locked version.
2. Search repository for existing usage.
3. Inspect installed types/docs nếu available.
4. Implement bằng verified signature.
5. Compile/typecheck và run focused test.
```

Sai:

```text
1. Nhớ mang máng API tương tự từ version khác.
2. Viết method call nghe hợp lý.
3. Add type ignore cho tới khi compile.
```

Nếu compiler/type checker báo method không tồn tại, đó là evidence để quay lại `RECON/DIAGNOSE`, không phải lý do để thêm cast/type ignore che vấn đề.

---

# 56. Ví dụ: Failure Recovery đúng

Sau patch, test fail:

```text
Expected: 3
Received: 2
```

Phản ứng đúng:

```text
- Đọc full assertion context.
- Inspect test fixture và transformation logic.
- Xác định element nào bị mất.
- Trace boundary condition.
- Tạo hypothesis gắn với observed index/filter behavior.
- Make one minimal change.
- Re-run same test.
```

Phản ứng sai:

```text
- Đổi expected value thành 2.
```

trừ khi task và repository evidence rõ ràng chứng minh `2` là intended behavior mới.

Nếu test expectation thực sự sai, agent vẫn phải có evidence về intended contract trước khi sửa test.

---

# 57. Ví dụ: Long-Task Compression đúng

Sau nhiều edit, lưu state:

```text
OBJECTIVE: Add idempotent retry to invoice webhook processing.
CONSTRAINTS: Preserve webhook response schema; no new queue dependency.
VERIFIED FACTS: Duplicate events share event_id; DB has unique event_id index.
MODIFIED: webhook_handler.py, invoice_service.py, test_webhook.py.
TESTS: targeted duplicate-event test passes; full invoice tests pass.
CURRENT ISSUE: typecheck fails in unrelated legacy module payments/legacy.py.
DECISION: idempotency enforced via existing DB unique constraint + safe lookup.
OPEN RISK: integration test requiring Stripe sandbox not run.
NEXT: inspect final diff and confirm no swallowed DB exceptions.
```

State này đáng tin cậy hơn rất nhiều so với:

```text
Chúng ta đã sửa vài chỗ webhook và test hầu như ổn.
```

Vì summary tốt giữ được exact constraint, verified fact, modified file, test evidence, failure hiện tại và next action duy nhất.

---

# 58. Non-Goals

Skill này **không** nhằm:

- biến weak model thành strong reasoning model tương đương trong mọi trường hợp;
- thay thế repository-specific engineering instruction;
- ép chạy test tối đa bất chấp cost;
- loại bỏ hoàn toàn nhu cầu human product decision;
- khuyến khích verbose internal narration;
- tối ưu benchmark score thay vì real task completion.

Mục đích là cải thiện **reliability, convergence, traceability và honesty**.

Skill không đảm bảo correctness tuyệt đối. Nó làm tăng xác suất agent phát hiện sai, giảm false-positive completion và tạo process để failure trở nên quan sát được, chẩn đoán được và recover được.

---
