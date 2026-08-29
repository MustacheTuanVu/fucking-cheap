# Vibecoding — Plan → Decompose → TDD → Delegate

> Workflow cho coding task: plan trước, decompose nhỏ, TDD strict, delegate background.

## Khi nào dùng

- Task mới bắt đầu (feature, refactor, page mới)
- Task estimate > 15 phút
- Task có frontend component
- Lê Vũ muốn auto từ A-Z

## Flow

### Phase 1: INTAKE — Phân tích Task

1. Đọc task description
2. Xác định scope:
   - **Spike** (< 15 phút): feasibility question, code throwaway
   - **Bounded** (15-60 phút): 1-3 files, existing flow
   - **Architectural** (> 60 phút): new subsystem, multi-file
3. Nếu không rõ → ask clarifying questions

### Phase 2: PLAN — Decompose + Viết Plan

#### Decompose Rules

Mỗi subtask PHẢI có:
```
### Subtask N: [Tên]
- **Objective:** [Làm gì]
- **Files:** [File nào]
- **Test:** [Test case cụ thể]
- **Acceptance:** [Criterion rõ ràng]
```

Quy tắc:
- 1 subtask = 1 file, 1 function tối đa
- Nếu task > 3 subtasks → decompose thêm
- Nếu không decompose được → task quá lớn, reject và ask lại

#### Present Plan

```
## Plan: [Tên task]

### Decomposition
- [ ] Subtask 1: [mô tả] → Test: [criterion]
- [ ] Subtask 2: [mô tả] → Test: [criterion]
...

### Stop Conditions
- Fix loop > 2 lần → STOP
- > 3 subtasks fail → STOP

### Verification
- [ ] Unit test pass
- [ ] Integration test pass
- [ ] UI test pass (Playwright, nếu có frontend)
```

**ĐỢI APPROVE** — KHÔNG bắt đầu code cho đến khi Lê Vũ nói "OK".

### Phase 3: DELEGATE hoặc CHẠY

**Option A — Delegate (preferred cho task > 30 phút):**
```
delegate_task(
  goal="[Task description + plan chi tiết]",
  context="[File paths, DB schema, API patterns, test framework]"
)
```

**Option B — Chạy trong conversation (cho task < 30 phút):**
- Chạy từng subtask theo TDD loop

### Phase 4: TDD LOOP (mỗi subtask)

```
1. Viết test TRƯỚC
2. Viết code để pass test
3. Run test
   - PASS → Next subtask
   - FAIL → Fix (max 2 lần)
     - Fix lần 2 vẫn FAIL → STOP, report problem
```

### Phase 5: VERIFICATION GATE

Sau khi tất cả subtasks done:
```
1. Run full test suite
2. Nếu có frontend → Playwright test
3. Integration test (API call, DB write)
4. Report kết quả
```

### Phase 6: REPORT

```
## Kết quả: [Tên task]

### Subtasks
- [x] Subtask 1: ✅ Test pass
- [x] Subtask 2: ✅ Test pass
- [x] Subtask 3: ✅ Test pass (fix 1 lần)

### Tests
- Unit: ✅ X/Y pass
- Integration: ✅ Pass
- UI: ✅ Pass

### Files changed
- path/to/file1.py
- path/to/file2.py
```

## Stop Conditions (BẮT BUỘC)

| Điều kiện | Hành động |
|---|---|
| Fix loop > 2 lần | STOP, report cho Lê Vũ |
| > 3 subtasks fail | STOP, reassess plan |
| Task > 2 giờ estimate | Decompose thêm hoặc reject |
| Test coverage < 80% | WARN, suggest thêm test |

## Context Template (khi delegate)

```
## Project Context
- Path: [worktree path]
- Framework: [Next.js/Express/etc]
- Test: [pytest/jest/playwright]
- DB: [PostgreSQL/MySQL]

## Key Files
- [list important files]

## Patterns
- [code patterns to follow]

## Constraints
- [don't break X, always Y]
```

## Rules

1. **Plan trước, code sau** — KHÔNG nhảy thẳng vào code
2. **Test trước, code sau** — TDD strict
3. **Decompose nhỏ** — 1 subtask = 1 file/function
4. **Stop khi fail** — không loop vô tận
5. **Report rõ ràng** — Lê Vũ cần biết cái gì done, cái gì fail
6. **Context đầy đủ** — subagent cần đọc codebase trước khi code
