# Architect-Executor Agentic Framework

Hệ thống khung làm việc (Framework) phân tầng dành cho AI Agent trong môi trường phát triển phần mềm, tối ưu hóa sự phối hợp giữa **Mô hình lập kế hoạch cao cấp (Architect)** và **Mô hình thực thi tốc độ cao (Executor - Gemini 3.8 Flash)**.

---

## 🌟 Tính Năng Nổi Bật

1. **Phân Tầng Vai Trò (Tiered Agent Architecture):**
   - **Architect (Model lớn: Pro / Opus / Argon):** Tiếp nhận bài toán, thiết kế kiến trúc, tạo Micro-Spec và Verification Gate.
   - **Executor (Model nhỏ: Gemini 3.8 Flash):** Đọc đúng lát cắt mã nguồn (Context Slice), viết mã, tự động chạy test và bàn giao ngắn gọn.

2. **Quản Lý Trạng Thái Qua Tệp (Planning-with-Files):**
   - Bộ 3 tệp trạng thái bền vững trong `.plans/`: `task_plan.md`, `findings.md`, `progress.md`.
   - Tránh thất thoát ngữ cảnh (Context Rot) và tiết kiệm token tối đa khi hội thoại kéo dài.

3. **Tối Ưu Token Nền (Token Hygiene & Progressive Disclosure):**
   - Quy tắc cốt lõi trong `AGENTS.md` cực kỳ cô đọng (< 20 dòng).
   - Chi tiết nghiệp vụ được nạp qua các Skill theo nhu cầu (`.agents/skills/`), giảm hơn 60% lượng token tiêu thụ mỗi lượt chat.

4. **Tầng Tra Cứu Cách Ly (Isolated Deep Search):**
   - Định tuyến tìm kiếm qua `agent-reach` và `Jina Reader`.
   - Mọi dữ liệu web/tài liệu thô được cách ly tại `.plans/scratch/`, chắt lọc kết luận cô đọng có nguồn vào `.plans/findings.md`.

---

## 📁 Cấu Trúc Thư Mục

```
.
├── .agents/
│   └── skills/
│       ├── architect-micro-planner/ # Kỹ năng lập kế hoạch Micro-Spec cho Model lớn
│       ├── flash-executor-runbook/  # Sổ tay thực thi tiết kiệm token cho Flash 3.8
│       └── deep-search-router/      # Bộ định tuyến tìm kiếm sâu cách ly dữ liệu
├── .plans/
│   ├── findings.md                  # Bộ nhớ đệm lưu kết quả nghiên cứu (tra cứu 1 lần, dùng mãi mãi)
│   ├── progress.md                  # Nhật ký bàn giao giữa các Subagent
│   ├── task_plan.md                 # Kế hoạch thực thi hiện tại
│   └── template_task_plan.md        # Mẫu Micro-Spec chuẩn công nghiệp
├── docs/                            # Tài liệu kỹ thuật dự án
├── reports/
│   └── AI_ENGINEERING_AGENTIC_WORKFLOWS.md # Báo cáo nghiên cứu quy trình kỹ sư AI
├── src/                             # Mã nguồn chính của dự án
├── tests/                           # Bộ kiểm thử tự động
├── .gitignore                       # Chặn các dữ liệu sản phẩm, file lớn, thư mục tạm
└── AGENTS.md                        # Chỉ thị gốc tự động phát hiện bởi Antigravity
```

---

## 🚀 Cách Sử Dụng

1. **Giai đoạn 1 (Lập kế hoạch):**
   - Chọn model lớn (Claude Opus, Gemini Pro, Gemini Argon).
   - Yêu cầu model kích hoạt skill `architect-micro-planner` để soạn kế hoạch chi tiết vào `.plans/task_plan.md`.
2. **Giai đoạn 2 (Thực thi):**
   - Chuyển sang **Gemini 3.8 Flash** (hoặc để model lớn gọi subagent `Model: "flash"`).
   - Flash kích hoạt skill `flash-executor-runbook`, thực hiện từng task, chạy kiểm tra và ghi nhật ký vào `.plans/progress.md`.
3. **Giai đoạn 3 (Nghiệm thu):**
   - Model lớn kiểm tra lại `git diff`, đảm bảo mọi Verification Gate đạt yêu cầu trước khi bàn giao cho người dùng.
