# Verification

> Chiến lược test, static checks, reproducibility và ma trận xác minh thích nghi.

# 12. Chiến lược kiểm thử

Testing phải tương xứng với rủi ro, có phân tầng và dựa trên bằng chứng.

## 12.1 Verification ladder

Ưu tiên thứ tự:

```text
reproduction → focused test → related package tests → static checks → broader suite/build
```

Không phải task nào cũng cần đủ mọi tầng, nhưng high-risk change thường cần coverage rộng hơn.

## 12.2 Quy tắc bug fix

Đối với bug fix, khi thực tế cho phép:

1. reproduce bug hoặc failing test trước khi sửa;
2. thực hiện thay đổi;
3. chứng minh reproduction đã pass;
4. chạy regression test liên quan.

NÊN thêm regression test khi bug có thể được capture ổn định và repository thường test loại behavior đó.

## 12.3 Quy tắc feature

Với behavior mới:

- test happy path;
- test boundary case liên quan;
- test failure behavior khi có ý nghĩa;
- xác minh compatibility với caller hiện có.

Không tạo test vô nghĩa chỉ chạy qua line mà không assert behavior.

## 12.4 Quy tắc refactor

Với refactor nhằm giữ nguyên behavior:

- test hiện có là bằng chứng chính;
- chỉ thêm test khi coverage hiện có không đủ cho behavior rủi ro;
- không sửa test chỉ để thích nghi với behavior change vô tình, trừ khi task thật sự đổi semantics.

## 12.5 Static checks

Chạy check phù hợp như:

- type checker;
- compiler;
- linter;
- formatter check;
- schema validator;
- generated-code consistency check.

Dùng command do repository định nghĩa thay vì tự phát minh command mới.

## 12.6 Quy tắc full suite

Chạy full suite khi thực tế cho phép nếu:

- thay đổi ảnh hưởng shared infrastructure;
- public API thay đổi;
- common utility thay đổi;
- rủi ro cao;
- test suite có kích thước hợp lý;
- repository instruction yêu cầu.

Không lãng phí thời gian chạy full suite đắt đỏ sau mọi chỉnh sửa nhỏ. Bắt đầu hẹp rồi mở rộng gần completion.

## 12.7 Test fail không liên quan patch

Nếu test fail và có vẻ pre-existing hoặc unrelated:

1. inspect đủ bằng chứng để biện minh kết luận;
2. không sửa code không liên quan chỉ để suite xanh;
3. báo chính xác check nào fail và vì sao có vẻ unrelated;
4. vẫn verify vùng đã thay đổi mạnh nhất có thể.

---

# 29. Quy tắc sửa test

Test là specification, không phải obstacle.

Không làm failing test pass bằng cách làm yếu test trừ khi intended behavior thật sự thay đổi.

Các test modification đáng ngờ gồm:

- xóa assertion;
- nới rộng expected error type không có lý do;
- tăng arbitrary sleep/timeout;
- skip test;
- đánh dấu test flaky;
- thay deterministic assertion bằng truthiness;
- update snapshot mà không inspect semantic change.

Khi snapshot/golden output thay đổi, inspect diff và xác nhận mọi material change đều có chủ đích.

---

# 33. Environment và reproducibility

Nhận thức rằng local environment failure có thể trông giống code failure.

Phân biệt:

- code defect;
- missing dependency;
- unsupported runtime version;
- absent service;
- missing environment variable;
- network restriction;
- permission issue;
- platform-specific behavior;
- flaky infrastructure.

Không “sửa” application code để bù cho local environment rõ ràng bị hỏng, trừ khi task cụ thể là làm environment robust hơn.

---

# 35. Xử lý repository thiếu test hoặc tooling bị hỏng

Nếu repository không có test hữu ích hoặc normal test command không chạy được:

1. inspect local test convention;
2. thử verification hẹp nhất hợp lý;
3. dùng type/build/static check;
4. tạo minimal reproduction khi phù hợp;
5. thực hiện source review và diff review cẩn thận;
6. nói chính xác phần nào không thể verify.

Không bịa successful test result.

---

# 40. Adaptive Verification Matrix

Dùng bảng như guidance, không dùng nó làm lý do để chạy command không liên quan.

| Loại thay đổi | Evidence tối thiểu nên có | Evidence bổ sung khi rủi ro cao hơn |
|---|---|---|
| Documentation | inspect diff | validate link/command/example |
| Pure formatting | formatter/check + diff | relevant build nếu formatter có thể ảnh hưởng syntax |
| Unit-level bug | failing reproduction + focused test | package suite/typecheck |
| Feature | focused behavior tests | integration/build/full relevant suite |
| Refactor | existing tests + static checks | broader regression suite |
| Public API | API tests + callers | compatibility/integration tests |
| Dependency change | build + targeted tests | full suite, lockfile review |
| DB query | focused tests | integration DB test + query/perf review |
| Migration | migration validation | forward/backward/rollout checks |
| Auth/security | positive + negative tests | integration/security review |
| Concurrency | deterministic test | stress/repeat/race detector |
| Performance | baseline + post-change measurement | profiling/regression suite |
| CI/build | reproduce failing job local khi có thể | validate matrix/platform assumptions |

---
