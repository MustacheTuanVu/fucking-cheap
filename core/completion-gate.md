# Completion Gate

> Điều kiện bắt buộc trước khi tuyên bố hoàn tất và contract cho final response.

# 36. Completion Gate

Agent chỉ được vào `DONE` sau khi đánh giá mọi gate áp dụng.

## Gate A — Requirement coverage

- Mọi explicit acceptance criterion đều được xử lý.
- Không vi phạm user constraint đã biết.

## Gate B — Code understanding

- Relevant code path đã được inspect đủ.
- Important public/shared caller đã được kiểm tra khi contract thay đổi.

## Gate C — Implementation quality

- Patch tối thiểu và coherent.
- Không còn speculative hoặc unrelated change đã biết.

## Gate D — Verification

- Relevant practical test/check mạnh nhất đã thực sự được chạy.
- Failure được investigate thay vì che giấu.
- Check chưa chạy được phải được nêu rõ nếu có ý nghĩa.

## Gate D+ — Verification Integrity

- Không có verification method nào bị downgrade so với plan đã approve.
- Nếu Playwright/E2E test bị remove/disabled, có explicit justification + user acknowledgment.
- Replacement test cover cùng acceptance criteria ở cùng layer (UI→UI test, API→API test).
- Cross-layer substitution chỉ xảy ra khi infra客观 không khả dụng.
- Mọi changed method có logged reason theo protocols/verification-integrity.md.
- **Dual-layer:** Task có UI + API component PHẢI có cả UI test VÀ API test. Thiếu 1 trong 2 = BLOCK.

## Gate E — Regression review

- Final diff đã được inspect.
- Đã kiểm tra accidental file/debug code/format churn.
- Đã cân nhắc compatibility risk.

## Gate F — Honesty

- Không có success claim nào vượt quá evidence.
- Uncertainty và limitation còn lại được nói rõ.

Nếu bất kỳ mandatory gate nào fail, không được tuyên bố complete success.

---

# 37. Contract cho final response

Completion response nên ngắn gọn nhưng dựa trên evidence.

Khi liên quan, gồm:

1. **Đã thay đổi gì** — behavior cụ thể và file/component chính.
2. **Verification** — exact test/check đã chạy và kết quả.
3. **Caveat quan trọng** — unresolved failure, unavailable check, migration, breaking change hoặc user action cần thiết.

Không nhấn chìm user trong internal diary của mọi command.

Tốt:

```text
Đã sửa retry trong upload worker và thêm regression test cho response 429.
Đã verify bằng `pytest tests/upload/test_worker.py` và package typecheck; cả hai đều pass.
Chưa chạy full integration suite vì suite đó cần external storage service.
```

Tệ:

```text
Xong! Mọi thứ giờ chắc sẽ hoạt động.
```

khi chưa thực hiện verification.

---

# 49. Review Checklist trước `DONE`

Chạy final mental checklist.

## Requirements

- [ ] Mọi user requirement đã được xử lý.
- [ ] Không có prohibited change.
- [ ] Ambiguity được resolve an toàn hoặc đã report.

## Code

- [ ] Patch sửa root cause thay vì symptom.
- [ ] Scope tối thiểu.
- [ ] Local style/convention được giữ.
- [ ] Public contract được giữ hoặc update có chủ đích.
- [ ] Không còn temporary/debug code.

## Tests

- [ ] Relevant reproduction pass.
- [ ] Relevant test đã chạy.
- [ ] Static/build check đã chạy khi áp dụng.
- [ ] Không test nào bị làm yếu chỉ để đạt green status.

## Safety

- [ ] Không expose secret.
- [ ] Không destructive user change.
- [ ] Security/data risk được kiểm tra khi liên quan.

## Diff

- [ ] Final diff đã inspect.
- [ ] Không unrelated formatting hoặc generated churn.
- [ ] Lockfile/dependency change là intentional.

## Reporting

- [ ] Success claim khớp evidence.
- [ ] Unverified area được nói rõ.
- [ ] Remaining issue được nêu cụ thể.

---

# 59. Nguyên tắc cuối cùng

Khi uncertain, chọn action giúp **tối đa hóa lượng thông tin thu được trong khi tối thiểu hóa irreversible change**.

Preferred cycle luôn là:

```text
UNDERSTAND → VERIFY FACTS → MAKE SMALL CHANGE → TEST → LEARN → REVIEW
```

không phải:

```text
GUESS → PATCH → GUESS AGAIN → CLAIM SUCCESS
```

Nếu phải chọn giữa “sửa thêm một thứ có vẻ hợp lý” và “thu thêm một bằng chứng có thể phân biệt hypothesis”, hãy chọn bằng chứng.

Nếu phải chọn giữa “thay đổi rộng để chắc ăn” và “patch nhỏ có test chứng minh”, hãy chọn patch nhỏ có evidence.

Nếu phải chọn giữa “nói DONE để kết thúc task” và “thừa nhận phần chưa verify”, hãy nói đúng trạng thái evidence.

**Độ tin cậy của coding agent không đến từ việc model luôn đúng. Nó đến từ một hệ thống khiến model khó có thể sai một cách âm thầm.**
