# BẢN ĐỒ KẾ HOẠCH MICRO-SPEC: [Tên Tính Năng]

- **Trạng thái:** [Draft / In Progress / Completed]
- **Architect Model:** [Gemini Pro / Claude Opus / Gemini Argon]
- **Executor Model:** Gemini 3.8 Flash
- **Thời gian khởi tạo:** YYYY-MM-DD HH:MM
- **Tài liệu tham chiếu:** Xem findings tại [findings.md](file:///d:/FPT/Harness/SteamBP/.plans/findings.md)

---

## 1. MỤC TIÊU & RÀNG BUỘC KIẾN TRÚC
- [ ] Mục tiêu nghiệp vụ: ...
- **Negative Constraints (Ràng buộc cấm chạm):**
  - Cấm: Không cài thêm dependencies nếu chưa có trong requirements.
  - Cấm: Không sửa đổi cấu trúc thư mục ngoài phạm vi chỉ định.

---

## 2. DANH SÁCH MICRO-TASKS CHO FLASH EXECUTOR

### Task 1.1: [Tên Nhiệm Vụ 1]
- **Context Slice (Chỉ đọc các dòng sau):**
  - `file:///d:/FPT/Harness/SteamBP/src/file_a.py#L1-L30`
- **Target File (Tập tin đích):**
  - `src/file_b.py` (Mới / Sửa)
- **Code Contract & Skeleton:**
  ```python
  class FeatureHandler:
      def execute(self, payload: dict) -> bool:
          # Thuật toán: Parse payload -> Validate schema -> Trả về kết quả
          pass
  ```
- **Verification Gate (Lệnh kiểm thử tự động):**
  - Lệnh: `pytest tests/test_feature.py -v` (Exit Code == 0)
- **Trạng thái:** [ ] Chưa bắt đầu | [ ] Đang làm | [ ] Hoàn thành

---

### Task 1.2: [Tên Nhiệm Vụ 2]
- **Context Slice:** `file:///d:/FPT/Harness/SteamBP/src/file_b.py#L10-L40`
- **Target File:** `tests/test_feature.py`
- **Code Contract:** ...
- **Verification Gate:** `pytest tests/test_feature.py`
- **Trạng thái:** [ ] Chưa bắt đầu | [ ] Đang làm | [ ] Hoàn thành

---

## 3. NGHIỆM THU TỔNG THỂ (ARCHITECT REVIEW GATE)
- [ ] Review diff toàn dự án: `git diff` sạch sẽ, không có code thừa.
- [ ] Toàn bộ unit tests pass: `pytest tests/`
- [ ] Cập nhật nhật ký bàn giao tại [progress.md](file:///d:/FPT/Harness/SteamBP/.plans/progress.md)
