# Backend & Network

> API/backend, network/external service, error handling và observability.

# 23. Thay đổi network và external service

Với network call:

- verify request/response contract;
- xử lý timeout có chủ đích;
- phân biệt retryable failure với permanent failure;
- tránh unbounded retry;
- giữ idempotency nếu có thể retry;
- validate external data tại trust boundary;
- tránh log secret hoặc sensitive payload;
- cân nhắc cancellation và resource cleanup.

Không phát minh third-party API từ trí nhớ khi repository hoặc official evidence có thể xác minh nó.

---

# 25. Backend và API change

Với API behavior, kiểm tra:

- input validation;
- auth boundary;
- status/error semantics;
- response compatibility;
- serialization;
- pagination;
- idempotency;
- transaction scope;
- logging/observability;
- timeout;
- retry;
- downstream failure behavior.

Khi đổi endpoint contract, locate client và test trước khi finalize.

---

# 30. Quy tắc error handling

Không catch error bừa bãi.

Khi thêm error handling, phải quyết định:

- error nào expected;
- error nào phải propagate;
- caller cần thông tin gì;
- logging có duplicate upstream log không;
- retry có phù hợp không;
- cleanup có được đảm bảo không.

Khi wrap error, giữ causal information.

Tránh empty catch và generic behavior kiểu “mọi lỗi đều return null”, trừ khi contract rõ ràng yêu cầu.

---

# 31. Quy tắc observability

Khi thay đổi log, metric hoặc trace:

- tránh secret/PII;
- giữ correlation identifier hữu ích;
- dùng logging convention hiện có;
- tránh noisy log trên hot path;
- đảm bảo error giữ đủ context để diagnose;
- không tùy tiện đổi log level nếu operations phụ thuộc vào nó.

Không thêm logging chỉ để trông “thorough”; chỉ thêm khi nó cải thiện một diagnostic boundary thật sự.

---
