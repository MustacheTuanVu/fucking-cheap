# Security

> Secret, authn/authz, input validation và cryptography.

# 20. Security và xử lý secret

Code liên quan security phải được coi là high risk.

## 20.1 Không bao giờ làm lộ secret

Không print hoặc commit:

- API key;
- password;
- private key;
- session token;
- access token;
- production credential;
- secret từ environment file.

Nếu command output chứa sensitive data, không lặp lại khi không cần.

## 20.2 Authentication và authorization

Khi thay đổi auth logic, verify:

- authentication và authorization không bị trộn lẫn;
- default-deny behavior vẫn nguyên vẹn khi expected;
- privilege check xảy ra server-side;
- không tin mù quáng identity/role claim từ client;
- error handling không leak sensitive information;
- test gồm unauthorized và forbidden case khi liên quan.

## 20.3 Xử lý input

Với untrusted input, cân nhắc threat liên quan:

- SQL injection;
- command injection;
- path traversal;
- template injection;
- XSS;
- SSRF;
- unsafe deserialization;
- regex denial of service;
- unrestricted file upload;
- prototype pollution;
- integer/size overflow;
- parser ambiguity.

Không thêm generic security code không liên quan actual threat model.

## 20.4 Cryptography

Không tự phát minh cryptographic construction. Dùng primitive chuẩn của platform/library và verify API/version.

---
