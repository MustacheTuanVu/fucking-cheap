# Regression Guard

> Diff review, refactor discipline, documentation và boundary cases.

# 15. Regression Guard

Trước completion, chủ động tìm thứ patch có thể làm hỏng.

## 15.1 Review final diff

Inspect toàn bộ relevant diff và tự hỏi:

- Tôi có sửa file không liên quan task không?
- Formatting có chạm vùng lớn không liên quan không?
- Import có vô tình biến mất không?
- Public signature có đổi không?
- Error semantics có đổi không?
- Serialization/output shape có đổi không?
- Có để lại debug log hoặc temporary code không?
- Validation có bị yếu đi không?
- Có thêm dependency mới không?
- Generated file có đổi ngoài dự kiến không?
- Test có bị nới lỏng thay vì behavior được sửa không?

## 15.2 Tìm caller bị ảnh hưởng

Nếu exported symbol, shared type, config key, route, schema hoặc interface thay đổi, search mọi usage liên quan và xác minh compatibility.

## 15.3 Negative testing

Khi risk yêu cầu, verify không chỉ intended case hoạt động mà invalid/unintended behavior vẫn bị reject.

---

# 28. Refactoring Protocol

Refactor phải giữ behavior trừ khi task rõ ràng yêu cầu khác.

Trước refactor:

- hiểu boundary đang thay;
- identify public/shared interface;
- verify test coverage hoặc thêm characterization test cho risky behavior;
- tách semantic change khỏi structural change khi có thể.

Trong refactor:

- di chuyển theo coherent step;
- giữ intermediate state dễ hiểu;
- tránh unrelated formatting churn;
- chạy targeted check tại milestone hữu ích.

Sau refactor:

- chạy relevant test;
- inspect diff để tìm accidental semantic change;
- search stale reference/dead import.

---

# 32. Quy tắc documentation

Update documentation khi behavior, configuration, setup, API usage hoặc operational procedure thay đổi đáng kể.

Không cần update docs cho internal change vô hình với user/developer trừ khi convention repository yêu cầu.

Documentation phải phản ánh behavior đã verify, không phải behavior mới chỉ nằm trong plan.

---

# 38. Forbidden Behaviors — Hành vi bị cấm

Các hành vi sau bị cấm trừ khi chỉ dẫn ưu tiên cao hơn rõ ràng yêu cầu:

- tuyên bố test pass mà chưa chạy;
- bịa file, symbol, API, command hoặc output;
- patch lặp đi lặp lại mà không có evidence mới;
- thay code không liên quan vì thẩm mỹ;
- delete/reset work của user;
- disable test/lint để đạt green status mà không có justification;
- âm thầm thay public contract;
- che giấu failed check;
- edit generated artifact nhưng bỏ qua source generator;
- thêm dependency chỉ vì agent nhớ có API dễ hơn;
- dùng destructive command làm debugging shortcut;
- catch-all rồi nuốt error;
- commit secret;
- giả vờ unresolved failure “probably fine”.

---

# 48. Boundary-Case Checklist

Khi liên quan, cân nhắc:

- empty input;
- một item;
- maximum/minimum value;
- null/undefined/None;
- duplicate value;
- invalid format;
- Unicode;
- zero/negative number;
- very large input;
- missing configuration;
- network failure;
- partial response;
- cancellation;
- repeated operation;
- concurrent operation;
- timezone/daylight-saving boundary;
- pagination boundary;
- permission denied;
- stale data;
- backward-compatible old data.

Chỉ dùng case có liên quan behavior đang thay. Không tạo hàng loạt test vô nghĩa chỉ để “cover checklist”.

---
