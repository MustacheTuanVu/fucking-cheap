# Context Compression

> Nén state và handoff khi context dài hoặc đổi model.

# 16. Bảo toàn và nén context

Task dài cần explicit state compression.

## 16.1 Khi nào nén

Tạo/cập nhật state summary gọn sau một trong các tình huống:

- discovery lớn;
- hoàn tất implementation phase;
- sau nhiều tool call có ý nghĩa;
- context trở nên lớn;
- user đổi requirement;
- hướng debugging thay đổi;
- trước khi chuyển sang subsystem khác.

## 16.2 Format nén

Dùng internal record gọn:

```text
OBJECTIVE:
CONSTRAINTS:
VERIFIED FACTS:
FILES READ:
FILES MODIFIED:
TESTS RUN + RESULTS:
CURRENT FAILURE:
DECISIONS:
OPEN RISKS:
NEXT STEP:
```

## 16.3 Quy tắc nén

- giữ nguyên chính xác tên symbol, file, command và error message quan trọng;
- giữ nguyên user constraint nếu wording tinh tế;
- bỏ hypothesis lỗi thời sau khi đánh dấu là disproven;
- phân biệt `verified` với `assumed`;
- không tóm tắt failure thành “tests fail” nếu exact failure có ý nghĩa.

---

# 45. Template xoay context / handoff

Khi context cần compress hoặc handoff sang model/agent khác, dùng:

```text
TASK
- Goal:
- Acceptance criteria:

CONSTRAINTS
- Must preserve:
- Must not change:

REPOSITORY FACTS
- Stack/toolchain:
- Relevant files:
- Relevant symbols:

WORK COMPLETED
- Changes made:
- Files modified:

VERIFICATION
- Commands run:
- Passes:
- Failures:

DEBUGGING STATE
- Current error:
- Root-cause hypothesis:
- Evidence:
- Disproven hypotheses:

RISKS / OPEN ITEMS
- ...

NEXT ACTION
- exactly one best next step
```

Agent tiếp theo phải có thể tiếp tục mà không cần reconstruct toàn bộ conversation.

Handoff phải ưu tiên **fact có evidence**, không mang theo speculative narrative không còn giá trị. Hypothesis đã bị bác bỏ phải được ghi rõ `DISPROVEN` hoặc loại bỏ khỏi active state.

---
