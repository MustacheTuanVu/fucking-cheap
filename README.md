# fucking-cheap

> Đấm chết con mẹ nó các agent đắt đỏ

Modular reliability skill cho Hermes Agent — biến model rẻ thành coding agent đáng tin cậy bằng kỷ luật workflow.

---

## What is this?

A modular skill system that wraps cheap LLM models with hard rules:

- **Plan before code** — never jump straight into implementation
- **Test before commit** — TDD strict, test first
- **Stop when stuck** — max 2 fix attempts, then report
- **Lazy-load modules** — only load what you need, save tokens

## Quick Setup

### Prerequisites

- [Hermes Agent](https://github.com/NousResearch/hermes-agent) installed
- A coding model (mimo-v2.5, DeepSeek, etc.)

### Install

```bash
# Clone the skill
git clone https://github.com/MustacheTuanVu/fucking-cheap.git ~/.hermes/skills/fucking-cheap

# Verify
hermes skills list | grep fucking-cheap
```

### Usage

Just give a coding task — the skill loads automatically:

```
You: "Add margin import to Lagtuz"

Agent: 
→ INTAKE     Scoping: Architectural (multi-file)
→ RECON      Reading codebase... found 3 related files
→ PLAN       Decomposed into 5 subtasks
✓ APPROVED   Waiting for your approval...
```

### Manual Load

If the skill doesn't auto-trigger:

```
/load fucking-cheap
```

---

# fucking-cheap

> Đấm chết con mẹ nó các agent đắt đỏ

Modular reliability skill cho Hermes Agent — biến model rẻ thành coding agent đáng tin cậy bằng kỷ luật workflow.

---

## Đây là gì?

Hệ thống skill modular bọc model LLM rẻ bằng rules cứng:

- **Plan trước khi code** — không bao giờ nhảy thẳng vào implementation
- **Test trước khi commit** — TDD strict, test trước code sau
- **Dừng khi stuck** — max 2 lần fix, nếu vẫn fail thì report
- **Lazy-load modules** — chỉ load module khi cần, tiết kiệm token

## Cài đặt nhanh

### Yêu cầu

- [Hermes Agent](https://github.com/NousResearch/hermes-agent) đã cài
- Model coding (mimo-v2.5, DeepSeek, etc.)

### Cài

```bash
# Clone skill
git clone https://github.com/MustacheTuanVu/fucking-cheap.git ~/.hermes/skills/fucking-cheap

# Verify
hermes skills list | grep fucking-cheap
```

### Sử dụng

Chỉ cần đưa task — skill tự load:

```
Bạn: "Thêm margin import vào Lagtuz"

Agent: 
→ INTAKE     Phân loại: Architectural (nhiều file)
→ RECON      Đọc codebase... tìm thấy 3 file liên quan
→ PLAN       Chia thành 5 subtasks
✓ APPROVED   Chờ bạn approve...
```

### Load thủ công

Nếu skill không tự trigger:

```
/load fucking-cheap
```

---

## Architecture

```
fucking-cheap/
├── SKILL.md                    # Router — 10 luật sống còn + state machine
├── core/
│   ├── workflow.md             # State machine + planning
│   ├── constraints.md          # Constraint Ledger, chống drift
│   ├── evidence.md             # Verify bằng evidence, không guess
│   ├── verification.md         # Test, build, lint, typecheck
│   ├── completion-gate.md      # Điều kiện DONE
│   ├── failure-recovery.md     # Debug flow khi fail
│   └── vibecoding.md           # Plan → Decompose → TDD → Delegate ⭐
├── domains/
│   ├── frontend.md             # React/Vue/CSS/Browser
│   ├── backend.md              # API/Server/Network
│   ├── database.md             # SQL/Schema/Migration
│   ├── security.md             # Auth/Permission/Crypto
│   ├── concurrency.md          # Async/Thread/Race
│   ├── performance.md          # Latency/Memory/CPU
│   └── cli.md                  # CLI/Flags/Env
└── protocols/
    ├── anti-loop.md            # Dừng khi fix loop
    ├── context-compression.md  # Compact context
    ├── git-safety.md           # Git workspace safety
    ├── regression-guard.md     # Diff review, regression
    └── model-escalation.md     # Weak model handling
```

## Kiến trúc

```
fucking-cheap/
├── SKILL.md                    # Router — 10 luật sống còn + state machine
├── core/
│   ├── workflow.md             # State machine + planning
│   ├── constraints.md          # Constraint Ledger, chống drift
│   ├── evidence.md             # Verify bằng evidence, không guess
│   ├── verification.md         # Test, build, lint, typecheck
│   ├── completion-gate.md      # Điều kiện DONE
│   ├── failure-recovery.md     # Debug flow khi fail
│   └── vibecoding.md           # Plan → Decompose → TDD → Delegate ⭐
├── domains/
│   ├── frontend.md             # React/Vue/CSS/Browser
│   ├── backend.md              # API/Server/Network
│   ├── database.md             # SQL/Schema/Migration
│   ├── security.md             # Auth/Permission/Crypto
│   ├── concurrency.md          # Async/Thread/Race
│   ├── performance.md          # Latency/Memory/CPU
│   └── cli.md                  # CLI/Flags/Env
└── protocols/
    ├── anti-loop.md            # Dừng khi fix loop
    ├── context-compression.md  # Compact context
    ├── git-safety.md           # Git workspace safety
    ├── regression-guard.md     # Diff review, regression
    └── model-escalation.md     # Weak model handling
```

## State Machine

```
INTAKE → RECON → PLAN → IMPLEMENT → VERIFY → REVIEW → DONE
                        ↑           |
                        └───────────┘ (DIAGNOSE)
```

No shortcut from IMPLEMENT to DONE. Verification is mandatory.

## Stop Conditions

| Condition | Action |
|-----------|--------|
| Fix loop > 2 times | STOP, report to user |
| > 3 subtasks fail | STOP, reassess plan |
| Task estimate > 2h | Decompose more or reject |
| Test coverage < 80% | WARN, suggest more tests |

## License

MIT

## Built by

[ocoderkiemcom.com](https://coderkiemcom.com)
