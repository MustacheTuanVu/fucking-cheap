---
name: fucking-cheap
language: vi
description: >-
  Bộ skill modular tối ưu token cho coding agent. Luôn áp dụng một lõi reliability nhỏ,
  sau đó chỉ đọc các module liên quan đến loại task, failure mode và domain hiện tại.
  Mục tiêu là tăng độ ổn định của model rẻ/nhỏ mà không nhét toàn bộ specification vào context.
---

# fucking-cheap

## Mục tiêu

`fucking-cheap` là router cho một bộ skill coding-agent modular. Không đọc toàn bộ thư mục một cách mặc định.
Chỉ nạp đúng module cần thiết cho phase hiện tại để giảm token, giảm instruction dilution và vẫn giữ hard gates quan trọng.

## Luật sống còn — luôn áp dụng

1. **Không đoán khi có thể kiểm chứng.** File, symbol, API, dependency version, command output và test result phải được xác minh bằng evidence phù hợp.
2. **Đọc trước khi sửa.** Phải hiểu entry point, code liên quan và constraint trước edit có hành vi.
3. **Giữ Constraint Ledger.** Mục tiêu, MUST, MUST NOT, fact đã xác minh, assumption và open question phải không bị thất lạc qua task dài.
4. **Patch nhỏ nhất nhưng đủ.** Không refactor lan man, không đổi public contract hoặc dependency nếu task không yêu cầu.
5. **Mọi edit làm bẩn evidence cũ.** Verification chạy trên revision trước không chứng minh revision sau.
6. **Code nhìn đúng không phải bằng chứng.** Behavioral change phải được verify bằng test/build/typecheck/lint hoặc evidence tương đương phù hợp.
7. **Test/command fail thì chẩn đoán trước khi patch tiếp.** Không blind retry, không đổi nhiều biến cùng lúc.
8. **Không lặp strategy không tạo evidence mới.** Nếu cùng failure fingerprint xuất hiện lặp lại, dừng vòng lặp và đổi phương pháp.
9. **Review diff trước DONE.** Kiểm tra unintended change, regression, caller bị ảnh hưởng, test weakening, generated file và secret.
10. **Không claim success vượt quá evidence.** Nếu chưa verify được, nói rõ phần nào chưa verify và vì sao.

## State machine tối thiểu

```text
INTAKE -> RECON -> PLAN -> IMPLEMENT -> VERIFY -> REVIEW -> DONE
                         ^       |
                         |       v
                         +--- DIAGNOSE
```

Không cho phép `IMPLEMENT -> DONE` đối với behavioral change.
Sau bất kỳ edit nào, verification liên quan phải được chạy lại trước `DONE`.

## Chính sách nạp module

### Nạp mặc định cho coding task có thay đổi code

Đọc các file sau trước hoặc đúng lúc bắt đầu phase tương ứng:

- `core/workflow.md` — workflow, reconnaissance, planning, editing.
- `core/constraints.md` — Constraint Ledger và chống requirement drift.
- `core/completion-gate.md` — điều kiện DONE và final reporting.

### Nạp theo nhu cầu

- `core/vibecoding.md` — khi bắt đầu coding task mới (plan → decompose → TDD → delegate).
- `core/evidence.md` — khi phải dùng tool, xác minh dependency/API, đọc tài liệu, hoặc có fact chưa chắc chắn.
- `core/verification.md` — khi task thay đổi behavior, sửa bug, thêm feature, refactor, build/test/typecheck/lint.
- `core/failure-recovery.md` — ngay khi test/build/runtime/command fail hoặc diagnosis chưa rõ.

### Domain router

| Tín hiệu trong task | Module cần đọc |
|---|---|
| React/Vue/Svelte/UI/browser/CSS/component/state | `domains/frontend.md` |
| HTTP/API/server/service/network/observability | `domains/backend.md` |
| SQL/schema/migration/query/data integrity | `domains/database.md` |
| auth/permission/secret/input validation/crypto | `domains/security.md` |
| async/thread/worker/race/lock/distributed | `domains/concurrency.md` |
| slow/latency/memory/CPU/optimization | `domains/performance.md` |
| CLI/flag/env/config/config file | `domains/cli.md` |

Một task có thể nạp nhiều domain module nếu thật sự giao nhau.

### Protocol router

| Tình huống | Module cần đọc |
|---|---|
| Coding task mới, plan → decompose → TDD → delegate | `core/vibecoding.md` |
| Cùng lỗi/cùng patch pattern lặp lại | `protocols/anti-loop.md` |
| Context dài, nhiều phase, handoff/đổi model | `protocols/context-compression.md` |
| Git, dirty workspace, reset/revert/commit | `protocols/git-safety.md` |
| Refactor, diff lớn, nguy cơ regression/boundary case | `protocols/regression-guard.md` |
| Model yếu, harness, hard gate, escalation | `protocols/model-escalation.md` |

## Lazy-loading bắt buộc

Không đọc tất cả module "để cho chắc". Chỉ đọc module khi một trong các điều kiện sau đúng:

1. Task thuộc domain của module.
2. Phase hiện tại yêu cầu protocol đó.
3. Một failure mode kích hoạt module.
4. Evidence mới làm thay đổi classification ban đầu.

Nếu module đã được đọc và context vẫn còn đầy đủ, không đọc lại nguyên file; chỉ quay lại phần cần thiết.

## Minimum Runtime Ledger

Duy trì state ngắn gọn sau đây trong quá trình làm việc:

```text
OBJECTIVE:
MUST:
MUST_NOT:
VERIFIED_FACTS:
ASSUMPTIONS:
FILES_TOUCHED:
CURRENT_FAILURE:
LAST_EVIDENCE:
NEXT_ACTION:
VERIFICATION_REQUIRED:
```

Không cần in ledger cho user trừ khi hữu ích; mục đích chính là chống drift.

## Routing examples

### Coding task mới (vibecoding)

Đọc:

```text
core/workflow.md
core/constraints.md
core/vibecoding.md
core/completion-gate.md
```

### Bug React đơn giản

Đọc:

```text
core/workflow.md
core/constraints.md
core/verification.md
domains/frontend.md
core/completion-gate.md
```

Nếu test fail hai lần với cùng fingerprint, đọc thêm `core/failure-recovery.md` và `protocols/anti-loop.md`.

### Race condition trong worker

Đọc:

```text
core/workflow.md
core/constraints.md
core/evidence.md
core/verification.md
domains/concurrency.md
core/completion-gate.md
```

Khi failure không hội tụ, thêm `core/failure-recovery.md` và `protocols/anti-loop.md`.

### Migration database production

Đọc:

```text
core/workflow.md
core/constraints.md
core/evidence.md
core/verification.md
domains/database.md
domains/security.md
protocols/git-safety.md
protocols/regression-guard.md
core/completion-gate.md
```

### Model rẻ bị loop trong repo lớn

Đọc thêm:

```text
protocols/anti-loop.md
protocols/context-compression.md
protocols/model-escalation.md
```

## Điều kiện kết thúc

Chỉ chuyển sang `DONE` khi:

- requirement chính đã được đáp ứng hoặc phần chưa làm được được nêu rõ;
- code hiện tại đã được review sau edit cuối;
- verification phù hợp đã chạy trên revision hiện tại hoặc limitation được báo rõ;
- không dùng test result cũ để chứng minh code mới;
- không có failure chưa được giải thích bị che giấu;
- final response phân biệt rõ `VERIFIED`, `SUPPORTED`, `ASSUMED`, `UNKNOWN` khi cần.

Nếu có xung đột giữa router này và module chi tiết, quy tắc an toàn/nghiêm ngặt hơn được ưu tiên, trừ khi instruction cấp cao hơn của user/system yêu cầu khác.
