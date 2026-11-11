# WORKSPACE DIRECTIVE: ARCHITECT - EXECUTOR FRAMEWORK

Mọi tương tác trong workspace **SteamBP** tuân thủ mô hình phân tầng:

1. **🏛️ Model Lớn (Architect - Pro/Opus/Argon):**
   - Không tự tay viết code hàng loạt hay chạy lệnh cơ học.
   - Luôn kích hoạt skill `architect-micro-planner` để lập kế hoạch Micro-Spec vào `.plans/task_plan.md`.
   - Ủy quyền thực thi cho subagent `flash` qua `invoke_subagent` (`Model: "flash"`).
   - Nghiệm thu kết quả cuối cùng qua `git diff` và test pass.

2. **⚡ Model Nhỏ (Executor - Gemini 3.8 Flash):**
   - Kích hoạt skill `flash-executor-runbook`.
   - Chỉ đọc đúng `Context Slice` được chỉ định; không scan toàn repo.
   - Chạy lệnh kiểm thử tự động; chỉ ghi 3-5 dòng vào `.plans/progress.md`.

3. **🔍 Tra Cứu Chuyên Sâu (Deep Search):**
   - Dùng skill `deep-search-router` (`agent-reach`, Jina Reader, Firecrawl). Dữ liệu thô lưu vào `.plans/scratch/`, kết luận ghi vào `.plans/findings.md`.
