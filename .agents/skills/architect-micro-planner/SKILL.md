---
name: architect-micro-planner
description: Dành cho mô hình mạnh (Architect: Pro, Opus, Argon). Hướng dẫn quy hoạch kế hoạch chi tiết dạng Micro-Spec để mô hình nhỏ như Gemini 3.8 Flash thực thi chuẩn xác 100% không bị lạc hướng và không tốn token thừa.
---

# Kỹ Năng Lập Kế Hoạch Micro-Spec (Architect Planner)

Khi bạn là mô hình lớn (Architect) tiếp nhận một bài toán phát triển phần mềm, **tuyệt đối không giao các task mơ hồ** như "viết logic cho file X" hay "tạo game theo yêu cầu". Mô hình nhỏ (Flash) khi nhận task mơ hồ sẽ tự động đọc quét toàn bộ workspace hoặc viết code chắp vá gây tốn hàng chục nghìn token và lỗi kiến trúc.

## 1. Cấu Trúc Bắt Buộc Của Một Task Dành Cho Flash
Mỗi task trong `.plans/task_plan.md` phải cung cấp đủ 5 thành tố sau:

1. **Context Slice (Phạm vi đọc hạn chế):**
   - Chỉ định chính xác đường dẫn file và khoảng dòng mà Flash được phép đọc:
     *Ví dụ:* `Đọc src/models/card.py#L10-L45 (đường dẫn tương đối từ ROOT)`.
   - *Cấm Flash đọc mò các file khác.*

2. **Target File (Tập tin đích):**
   - Đường dẫn chính xác của file cần tạo mới hoặc chỉnh sửa.

3. **Code Contract & Skeleton (Khuôn mẫu mã nguồn):**
   - Cung cấp sẵn tên class, tên hàm, kiểu dữ liệu tham số (Type annotations) và mô tả thuật toán chính dưới dạng mã giả ngắn gọn (pseudo-code).

4. **Negative Constraints (Ràng buộc cấm chạm - Do-Not-Touch):**
   - Nêu rõ những gì Flash **KHÔNG ĐƯỢC PHÉP LÀM**.
   - *Ví dụ:* "Không cài thêm thư viện pip ngoài", "Không sửa đổi database schema", "Không xóa các comment hiện có".

5. **Deterministic Verification Gate (Lệnh kiểm tra tất định):**
   - Đưa ra đúng lệnh terminal mà Flash phải chạy để tự kiểm tra (Ví dụ: `python -m pytest tests/test_card.py -v`).
   - Yêu cầu: Exit code phải bằng 0 trước khi Flash được phép báo cáo hoàn thành.

---

## 2. Quy Trình Giao Việc Qua `invoke_subagent`
Khi giao việc cho Subagent:
* Đặt `Model: "flash"` hoặc `Model: "flash_lite"`.
* Nội dung prompt giao việc: Chỉ gửi đúng đường dẫn tệp `.plans/task_plan.md` và số hiệu task (ví dụ: `Thực thi Task 1.1 trong .plans/task_plan.md`).
* Nhắc Flash: Đọc skill `flash-executor-runbook` để tối ưu token.
