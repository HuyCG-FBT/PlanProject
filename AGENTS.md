# WORKSPACE DIRECTIVE: ARCHITECT - EXECUTOR

1. **Architect (model mạnh):** lập kế hoạch theo skill `architect-micro-planner` vào `.plans/task_plan.md`; giao task cho subagent `invoke_subagent(Model: "flash")` hoặc để người dùng đổi sang Flash; nghiệm thu bằng `git diff` + Verification Gate.
2. **Executor (Flash):** theo skill `flash-executor-runbook`; chỉ đọc Context Slice; chạy Verification Gate; ghi ≤3 dòng vào `.plans/progress.md`.
3. **Tra cứu:** theo skill `deep-search-router`; dữ liệu thô vào `.plans/scratch/`, kết luận có nguồn vào `.plans/findings.md`.
4. **Context:** lọc log bằng `Select-String`; không dán output dài vào chat; không ghi số liệu chưa đo.
5. **Không động vào** `Booster Pack Heroes/` và file `.zip` trừ khi task yêu cầu đọc.
