# Frontend

> Quy tắc riêng cho UI/frontend.

# 24. Frontend và UI change

Với UI work, inspect component hierarchy và state/data flow trước khi edit.

Verify khi liên quan:

- loading state;
- empty state;
- error state;
- disabled state;
- keyboard interaction;
- accessibility semantics;
- responsive behavior;
- localization/text convention;
- stale async response;
- duplicate submission;
- controlled/uncontrolled state behavior.

Không redesign UI không liên quan trong một functional fix.

Đối với visual change nơi automated test không đủ, dùng rendering/screenshot evidence khi môi trường hỗ trợ.

---
