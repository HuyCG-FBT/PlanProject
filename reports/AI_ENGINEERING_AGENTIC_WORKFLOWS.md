# BÁO CÁO NGHIÊN CỨU: QUY TRÌNH LÀM VIỆC CỦA KỸ SƯ AI CHUYÊN NGHIỆP & MÔ HÌNH KIẾN TRÚC TẦNG BẬC (ARCHITECT - EXECUTOR)

---

## 1. TỔNG QUAN XU HƯỚNG KỸ THUẬT AI HIỆN ĐẠI
Trong các phòng thí nghiệm AI hàng đầu (Google DeepMind, Anthropic, OpenAI) và các đội ngũ công nghệ tiên tiến (Vercel, Cursor, Factory), cách thức làm việc giữa con người và AI đã chuyển dịch mạnh mẽ từ **Single-prompting (Ra lệnh đơn lẻ)** sang **Agentic Tiered Workflows (Quy trình tác tử phân tầng)**.

### Vấn đề của phương pháp truyền thống:
* **Lãng phí chi phí & Quota:** Dùng mô hình siêu lớn (Opus, Pro, o1/o3) cho mọi thao tác nhỏ nhặt (gõ boilerplate, đổi tên biến, đọc log dài) làm bùng nổ chi phí API và nhanh chóng cạn kiệt giới hạn token (Rate Limits / Context Quota).
* **Suy thoái ngữ cảnh (Context Degradation):** Khi một cuộc hội thoại kéo dài hàng chục lượt, mô hình bắt đầu bị "quên" (lost in the middle), nhầm lẫn logic và tự suy diễn (hallucination).
* **Mô hình nhỏ thiếu tầm nhìn bao quát:** Nếu chỉ dùng mô hình nhỏ (Flash, Haiku, Mini) để code trực tiếp, chúng thường thiếu khả năng trừu tượng hóa, dễ viết mã chắp vá, phá vỡ kiến trúc tổng thể của dự án.

---

## 2. CÁC PATTERN CHUẨN CỦA KỸ SƯ AI CHUYÊN NGHIỆP

### Pattern 1: Architect - Executor Pattern (Kiến trúc sư & Người thực thi)
Đây là pattern cốt lõi của các hệ thống tự động hóa lập trình hiện đại:
```
                ┌──────────────────────────────────────┐
                │        ARCHITECT (Model Lớn)         │
                │  (Gemini Pro, Claude Opus, Argon)    │
                │                                      │
                │  - Phân tích yêu cầu nghiệp vụ        │
                │  - Thiết kế kiến trúc, API Interface  │
                │  - Viết Micro-Spec & Test Gate       │
                │  - Chia nhỏ thành các Task độc lập   │
                └──────────────────┬───────────────────┘
                                   │
              Giao việc qua file   │ (Micro-Spec / Contract)
                                   ▼
                ┌──────────────────────────────────────┐
                │         EXECUTOR (Model Nhỏ)         │
                │     (Gemini 3.8 Flash, Mini, Haiku)  │
                │                                      │
                │  - Đọc nhiệm vụ cụ thể từ file Spec   │
                │  - Viết mã nguồn (chỉ trong scope)   │
                │  - Chạy test, tự sửa lỗi cục bộ      │
                │  - Báo cáo trạng thái hoàn thành     │
                └──────────────────┬───────────────────┘
                                   │
                Nghiệm thu kết quả │ (Code Review & Diff Check)
                                   ▼
                ┌──────────────────────────────────────┐
                │        ARCHITECT (Model Lớn)         │
                │  - Review mã nguồn qua git diff      │
                │  - Đảm bảo toàn vẹn hệ thống        │
                │  - Chuyển sang Task tiếp theo        │
                └──────────────────────────────────────┘
```

### Pattern 2: Planning-with-Files (Bộ 3 Tệp Trạng Thái Bền Vững)
* Các kỹ sư AI không bao giờ để kế hoạch chỉ nằm trôi nổi trong bộ nhớ ngữ cảnh (Chat Context) của một phiên làm việc.
* Kế hoạch luôn được vật chất hóa thành bộ 3 tệp tin Markdown (chuẩn Manus / `OthmanAdi/planning-with-files`):
  1. **`task_plan.md`**: Bản đồ đường đi và từng Micro-task độc lập.
  2. **`findings.md`**: Bộ nhớ đệm tra cứu kiến trúc, tài liệu API — tìm kiếm 1 lần, ghi nhớ vĩnh viễn, tránh lãng phí token tìm đi tìm lại.
  3. **`progress.md`**: Nhật ký bàn giao siêu ngắn giữa các Subagent.

### Pattern 3: Principle "Guard the Context Window" & Progressive Disclosure
* Không nạp toàn bộ mã nguồn cả nghìn dòng vào context.
* Đẩy toàn bộ tài liệu hướng dẫn công cụ vào `.agents/skills/<name>/SKILL.md` (chỉ nạp theo nhu cầu) thay vì viết rule `always_on` dài dòng làm tiêu tốn token đầu vào mỗi lượt chat.

### Pattern 4: Spec-Driven & Test-Driven Development (TDD)
* Trước khi yêu cầu agent viết code, Architect phải xác định rõ:
  - Input / Output của hàm hoặc module.
  - Viết trước file kiểm thử (Unit Test) hoặc kịch bản kiểm thử tự động.
* Subagent thực thi (Flash) sẽ chạy vòng lặp lặp lại (test -> fix -> test) cho đến khi pass 100% thì dừng.

---

## 3. CƠ CHẾ TRIỂN KHAI TRÊN HỆ THỐNG ANTIGRAVITY

Antigravity cung cấp sẵn các hạ tầng kỹ thuật hoàn hảo để hiện thực hóa mô hình này:
1. **Rule Engine phân tầng (`AGENTS.md` & `.agents/rules/*.md`):** Tự động phát hiện khi mở workspace và chỉ đạo phong cách phối hợp công việc.
2. **Subagent Delegation (`invoke_subagent`):** Cho phép agent mẹ truyền tham số `Model: "flash"` hoặc `Model: "flash_lite"` để tạo các worker chuyên trách viết code, chạy lệnh terminal hoàn toàn tách biệt.
3. **Artifacts & Planning Files:** Lưu trữ trực tiếp kế hoạch thực thi để mọi mô hình có thể truy cập chung.

---

## 4. PHÂN TÍCH THIẾU SÓT KHI MODEL NHỎ (FLASH 3.8) TỰ LẬP KẾ HOẠCH & GIẢI PHÁP

Khi để một mô hình nhỏ như **Gemini 3.8 Flash** tự lập kế hoạch cho chính nó hoặc cho dự án, các nghiên cứu và thực tế ghi nhận những vấn đề sau:

| Điểm hạn chế của Flash khi tự lập kế hoạch | Nguyên nhân kỹ thuật | Giải pháp khắc phục (Cách Architect phải can thiệp) |
| :--- | :--- | :--- |
| **1. Kế hoạch quá khái quát (Under-specification)** | Model nhỏ có xu hướng tóm tắt bài toán thành các gạch đầu dòng ngắn ngủi (ví dụ: "Viết logic mở thẻ", "Tạo UI"). | **Bắt buộc dùng Micro-Spec:** Architect phải cung cấp sẵn chữ ký hàm (`def open_pack(pack_id: str) -> List[Card]:`), đường dẫn file và cấu trúc dữ liệu mong muốn. |
| **2. Tự suy diễn và sửa file ngoài phạm vi (Scope Creep)** | Khi không biết file cần sửa nằm ở đâu, Flash sẽ dùng lệnh `read_file` đọc hàng loạt file trong repo, hoặc tự ý refactor code người khác. | **Thiết lập `Context Slice` & `Negative Constraints`:** Nêu rõ chỉ được đọc từ dòng X đến Y của file Z, và cấm chạm vào các file còn lại. |
| **3. Không có tiêu chí kiểm thử tất định** | Kế hoạch do Flash tạo thường thiếu bước kiểm thử cụ thể, dẫn đến việc Flash viết code xong nhưng không biết code có chạy được không. | **Bắt buộc có `Verification Gate`:** Mỗi task phải đi kèm một câu lệnh kiểm tra (ví dụ: `pytest tests/test_card.py` hoặc `python -m unittest`). Flash chỉ được báo cáo xong khi lệnh trả về mã thoát 0. |
| **4. Báo cáo dông dài làm phình to Context** | Khi hoàn thành một việc, model nhỏ hay chép lại toàn bộ code vào tin nhắn chat. | **Áp dụng Quy chuẩn bàn giao (Handover Log):** Flash chỉ được ghi tối đa 3 dòng vào `.plans/progress.md` và thông báo ngắn gọn. |

---

## 5. TẦNG TRA CỨU CHUYÊN SÂU CÁCH LY VỚI AGENT-REACH & JINA READER

Trong các dự án AI chuyên nghiệp, việc tìm kiếm tài liệu từ các nguồn mở rộng (GitHub, Reddit, Twitter, Bilibili, Docs web) được thực hiện qua một tầng riêng biệt:
1. **Không tốn phí API chính thức đắt đỏ:** Sử dụng **`agent-reach`** kết hợp **Jina Reader** (`https://r.jina.ai/<URL>`) để chuyển đổi bất kỳ trang web nào thành Markdown sạch không dính quảng cáo hay mã script.
2. **Ngăn chặn ô nhiễm ngữ cảnh (Context Pollution):** Dữ liệu web cào về không bao giờ được đưa trực tiếp vào cuộc trò chuyện của Architect. Dữ liệu được lưu tạm tại `.plans/scratch/`, Subagent Flash tóm tắt lấy các điểm chính (dưới 30 dòng) và ghi vào `.plans/findings.md`.
3. **Tra cứu 1 lần, dùng mãi mãi:** Nhờ lưu vào `findings.md`, mọi subagent sau này đều có thể tái sử dụng dữ liệu nghiên cứu mà không phải chạy lại lệnh tìm kiếm trên mạng.

---

## 6. KẾT LUẬN & QUY TẮC VẬN HÀNH CHUẨN TRONG WORKSPACE
Hệ thống hiện tại đã đạt chuẩn công nghiệp:
- **Tập tin chỉ thị:** [AGENTS.md](file:///d:/FPT/Harness/SteamBP/AGENTS.md)
- **Kỹ năng chuyên biệt:**
  - `architect-micro-planner`: Giúp model mạnh tạo kế hoạch chi tiết không kẽ hở.
  - `flash-executor-runbook`: Giúp Gemini 3.8 Flash thực thi chuẩn xác, tiết kiệm token.
  - `deep-search-router`: Giúp tra cứu sâu đa nền tảng không phát sinh phí API và không ô nhiễm ngữ cảnh.
- **Tệp lưu trữ trạng thái:** [task_plan.md](file:///d:/FPT/Harness/SteamBP/.plans/template_task_plan.md), [findings.md](file:///d:/FPT/Harness/SteamBP/.plans/findings.md), [progress.md](file:///d:/FPT/Harness/SteamBP/.plans/progress.md).
