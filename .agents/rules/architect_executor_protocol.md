# RULE: Chu Trình Phối Hợp Architect - Executor

1. **Quy hoạch (Architect):** Soạn Micro-Spec vào `.plans/task_plan.md` theo skill `architect-micro-planner`. Xác định rõ `Context Slice`, `Code Contract` và `Verification Gate`.
2. **Giao việc:** Gọi `invoke_subagent(Model: "flash")` truyền đường dẫn task plan.
3. **Thực thi (Flash):** Đọc skill `flash-executor-runbook`, viết mã đúng phạm vi, chạy lệnh test, ghi 3 dòng vào `.plans/progress.md`.
4. **Nghiệm thu (Architect):** Kiểm tra `git diff`, xác nhận test pass và thông báo cho người dùng.
