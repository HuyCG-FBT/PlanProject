# KẾ HOẠCH: <Tên>

- **Trạng thái:** Draft | Ready | In Progress | Done
- **Architect:** <model> — **Executor:** Gemini 3.8 Flash
- **ROOT:** <đường dẫn gốc>. Mọi path bên dưới tính từ ROOT.
- **Lệnh kiểm thử của dự án (VERIFY_CMD):** <vd: `dotnet test`, `npm test`, `pytest`, hoặc lệnh kiểm tra file cụ thể>

## Quy tắc chung
- Làm tuần tự; FAIL ở Gate thì dừng và ghi `progress.md`.
- CẤM ĐỘNG VÀO: <danh sách file/thư mục>
- Không cài thêm gói ngoài danh sách: <...>

---

## T1. <Tên task>
**Lý do:** <1 câu>
**Context Slice (chỉ đọc):** `<path>#L<a>-L<b>`
**Target:** `<path>` (Mới | Sửa | GHI ĐÈ TOÀN BỘ)
**Contract / Nội dung:**
<code skeleton hoặc nội dung nguyên văn>
**Verification Gate:** <lệnh cụ thể> → <kết quả mong đợi>
**Nếu FAIL:** <hành động cụ thể>
- [ ] T1 xong

---

## Nghiệm thu (Architect)
- [ ] `git diff` chỉ chạm các Target đã khai báo
- [ ] Mọi Gate đạt
- [ ] `progress.md` đã cập nhật
