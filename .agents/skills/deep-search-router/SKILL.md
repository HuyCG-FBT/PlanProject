---
name: deep-search-router
description: Định tuyến tra cứu web/tài liệu/repo sao cho dữ liệu thô không đi vào context chính. Dùng khi cần đọc URL, tra docs, tìm code mẫu, hoặc khảo sát cộng đồng.
---

# Deep Search Router

## Bước 0 — Kiểm tra công cụ trước khi dùng
Chỉ dùng công cụ đã xác minh có trên máy. Chưa chắc thì chạy `cmd /c "where <tên>"`. Không có thì xuống tầng thấp hơn, **không tự cài**.

## Tầng 1 — Luôn có sẵn (ưu tiên)
| Nhu cầu | Cách làm |
|---|---|
| Tìm kiếm tổng quan | tool `search_web` |
| Đọc 1 URL thành Markdown | `curl.exe -s "https://r.jina.ai/<URL>" -o .plans/scratch/<slug>.md` |

## Tầng 2 — Có điều kiện
*(Hiện chưa có công cụ khả dụng ở tầng này)*

## Tầng 3 — Tùy chọn (hiện CHƯA cài, chỉ dùng nếu người dùng tự cài)
`gh` (GitHub CLI), `firecrawl`, `mcporter` + Exa.

## Quy tắc cách ly dữ liệu
1. Output thô (HTML/JSON/Markdown dài) → ghi vào `.plans/scratch/` (đã gitignore). Không in ra chat.
2. Đọc file scratch theo đoạn (`read_file` có StartLine/EndLine) hoặc lọc bằng `Select-String`.
3. Ghi kết luận ≤30 dòng vào `.plans/findings.md`, **mỗi ý kèm URL nguồn**.
4. Architect chỉ đọc `findings.md`.
