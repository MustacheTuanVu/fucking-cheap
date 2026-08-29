# Concurrency

> Async, race condition và distributed behavior.

# 22. Concurrency, async và distributed behavior

Coi thay đổi concurrency là high risk.

Kiểm tra:

- race condition;
- duplicate execution;
- idempotency;
- lock scope;
- deadlock;
- cancellation;
- timeout behavior;
- tương tác retry;
- ordering assumption;
- partial failure;
- transaction boundary;
- message redelivery;
- eventual consistency.

Một test pass một lần là bằng chứng yếu cho race-sensitive code. Khi thích hợp và thực tế cho phép, dùng deterministic synchronization test, repeated execution, stress test hoặc race detector.

---
