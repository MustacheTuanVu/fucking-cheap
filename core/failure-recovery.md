# Failure Recovery

> Chẩn đoán lỗi, root-cause analysis và decision tree phục hồi.

# 13. Failure-Diagnosis Protocol

Khi verification fail, chuyển sang state `DIAGNOSE`.

## 13.1 Failure record bắt buộc

Theo dõi:

```text
FAILED COMMAND:
OBSERVED ERROR:
FIRST RELEVANT FRAME/LOCATION:
EXPECTED BEHAVIOR:
CURRENT HYPOTHESIS:
EVIDENCE FOR HYPOTHESIS:
EVIDENCE AGAINST:
NEXT INFORMATION TO GATHER:
```

## 13.2 Thứ tự chẩn đoán

Dùng sequence:

1. đọc toàn bộ error liên quan;
2. xác định failure có ý nghĩa sớm nhất, không chỉ downstream noise;
3. inspect code/config bị liên đới;
4. xác định failure có deterministic hay không;
5. hình thành một hoặc vài hypothesis;
6. thu bằng chứng giúp phân biệt các hypothesis;
7. chỉ edit sau khi evidence ủng hộ một likely cause;
8. chạy lại command fail hẹp nhất.

## 13.3 Root cause thay vì triệu chứng

Các symptom patch phải tránh:

- nuốt exception thay vì sửa invalid state;
- thêm `try/except` quanh deterministic programming error;
- tăng timeout mà không kiểm tra vì sao operation treo;
- thêm null check khắp nơi thay vì tìm lý do invariant bị phá;
- đổi expected test output mà chưa xác nhận intended behavior;
- thêm retry cho request fail vĩnh viễn;
- disable linter rule toàn cục chỉ để im một violation.

## 13.4 Nguyên tắc một biến

Khi debug khó, ưu tiên thay đổi một yếu tố có ý nghĩa tại một thời điểm để kết quả tiếp theo mang thông tin chẩn đoán.

Thay nhiều thứ cùng lúc làm mất diagnostic signal.

---

# 46. Debugging Decision Tree

Dùng decision tree gọn sau:

```text
Failure có reproduce không?
├─ Không → investigate flakiness/environment trước khi patch
└─ Có
   ↓
Earliest meaningful failure nằm trong changed code?
├─ Có → inspect invariant/input/caller
└─ Không
   ↓
Change có alter upstream contract/state không?
├─ Có → trace propagation
└─ Không → investigate unrelated/pre-existing/environment failure

Trước patch:
Hypothesis có giải thích TẤT CẢ key observation không?
├─ Không → gather more evidence
└─ Có → make smallest discriminating fix → rerun narrow reproduction
```

Nếu một hypothesis chỉ giải thích một phần symptom nhưng mâu thuẫn với observation quan trọng khác, không được patch chỉ vì nó “có vẻ hợp lý”.

---

# 47. Root-Cause Checklist

Với bug khó, inspect các category có liên quan:

- wrong input assumption;
- wrong type/shape;
- stale state;
- race/order issue;
- null/empty boundary;
- off-by-one/indexing;
- timezone/date behavior;
- encoding/Unicode;
- mutation/aliasing;
- caching;
- transaction behavior;
- dependency-version mismatch;
- environment/config mismatch;
- permission/auth boundary;
- retry/timeout behavior;
- resource cleanup;
- serialization/deserialization;
- numeric precision/overflow;
- path/platform difference.

Không check cơ học mọi category. Dùng observed evidence để ưu tiên.

Khi root cause nằm ở boundary giữa hai subsystem, phải inspect cả hai phía của contract thay vì chỉ sửa side đang throw error.

---
