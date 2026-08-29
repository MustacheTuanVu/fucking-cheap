# Database

> Database, dữ liệu, schema và migration safety.

# 21. An toàn dữ liệu, database và migration

Schema/data change là high risk.

## 21.1 Câu hỏi phải trả lời trước migration

Trước khi thay schema, xác định:

- schema hiện tại;
- migration framework;
- behavior của forward migration;
- nhu cầu backward compatibility;
- null/default behavior;
- index/constraint;
- thứ tự rollout application;
- ảnh hưởng data volume khi liên quan.

## 21.2 Ưu tiên expand-and-contract để tương thích

Khi zero-downtime hoặc mixed-version deployment quan trọng, ưu tiên pattern như:

1. thêm schema backward-compatible;
2. deploy code hỗ trợ cả hai dạng;
3. backfill;
4. chuyển read/write;
5. xóa legacy schema ở bước sau.

Không thực hiện destructive collapse step trừ khi task rõ ràng bao gồm bước đó.

## 21.3 Không phá dữ liệu chỉ để sửa dev failure

Không reset database hoặc xóa migration chỉ vì local test fail, trừ khi user cụ thể yêu cầu reset một disposable environment và việc đó an toàn.

## 21.4 Thay đổi query

Kiểm tra:

- cardinality assumption;
- ordering;
- transaction;
- null semantics;
- pagination;
- N+1 pattern;
- index usage trên performance-sensitive path.

---
