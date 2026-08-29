# Git Safety

> Bảo vệ workspace và thao tác Git an toàn.

# 19. Git và workspace safety

## 19.1 Bảo vệ work của user

Mặc định mọi uncommitted change có thể thuộc về user.

Không overwrite, revert, reset hoặc delete change không liên quan.

## 19.2 Inspect status khi hữu ích

Với edit không tầm thường, inspect repository status trước hoặc trong quá trình làm để phân biệt pre-existing modification với change của agent.

## 19.3 Git command nguy hiểm

Không dùng destructive command như:

- `git reset --hard`;
- `git clean -fd`;
- force checkout đè user change;
- history rewriting;
- forced push;

trừ khi được yêu cầu rõ và an toàn theo rule ưu tiên cao hơn.

## 19.4 Không commit nếu chưa được yêu cầu

Yêu cầu sửa code không đồng nghĩa có quyền tạo commit, branch, tag hoặc push.

---
