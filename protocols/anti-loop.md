# Anti-loop

> Phát hiện retry mù, failure fingerprinting và phá vòng lặp.

# 14. Anti-Loop Protocol

Agent yếu thường rơi vào vòng `edit → test → edit`. Phải phát hiện và dừng.

## 14.1 Dấu hiệu loop

Xem các dấu hiệu sau là cảnh báo:

- cùng command fail với gần như cùng error hai lần sau các edit khác nhau;
- cùng file bị sửa ba lần mà không có bằng chứng mới;
- fix luân phiên giữa hai state trước đó;
- model liên tục đoán các API khác nhau;
- scope thay đổi ngày càng rộng nhưng hiểu biết không tăng;
- test output không được đọc kỹ;
- failure mới xuất hiện nhanh hơn tốc độ giải quyết failure cũ.

## 14.2 Loop breaker

Khi phát hiện nguy cơ loop:

1. **DỪNG edit.**
2. Tóm tắt các known fact hiện tại.
3. Đọc lại requirement và constraint ban đầu.
4. Inspect diff.
5. Reproduce từ failing case nhỏ nhất.
6. Thu một nguồn evidence mới.
7. Cân nhắc revert speculative change gần nhất.
8. Tạo hypothesis mới.
9. Chỉ tiếp tục khi edit tiếp theo có lý do gắn trực tiếp với evidence.

## 14.3 Hard cap cho blind retry

Không bao giờ thực hiện quá hai attempt mang tính suy đoán tương tự nhau mà không thu evidence mới.

---

# 44. Failure Fingerprinting

Harness có thể fingerprint failure từ các field đã normalize:

```text
command family
exit code
exception/error class
primary file + line
assertion name
normalized first meaningful error line
```

Bỏ qua unstable data như:

- timestamp;
- temporary path;
- random ID;
- memory address;
- request ID không liên quan;
- generated port;
- nondeterministic ordering noise.

Nếu fingerprint vẫn giống nhau qua nhiều attempt, agent có khả năng chưa thay đổi root cause.

Fingerprint không nên quá coarse. Hai failure có cùng exit code nhưng khác exception class hoặc assertion location phải được coi là khác nếu chúng chỉ ra root cause khác nhau.

---
