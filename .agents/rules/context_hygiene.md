# RULE: Bảo Vệ Cửa Sổ Ngữ Cảnh (Token Hygiene)

1. **Progressive Disclosure:** Chỉ nạp hướng dẫn chi tiết qua Skill khi cần; không biến rule thành cẩm nang dài dòng.
2. **Không nạp log & web thô:** Lọc log lỗi qua `grep`/`Select-String`. Nội dung web từ `agent-reach`/`firecrawl` phải qua file tạm và tóm tắt vào `.plans/findings.md`.
3. **Planning-with-Files:** Trạng thái lưu trên đĩa (`.plans/task_plan.md`, `findings.md`, `progress.md`), không phụ thuộc trí nhớ chat.
