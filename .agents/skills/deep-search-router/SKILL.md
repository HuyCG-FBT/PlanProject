---
name: deep-search-router
description: Định tuyến tra cứu và tìm kiếm thông tin chuyên sâu đa nền tảng không tốn phí API bằng Agent Reach, Jina Reader, Firecrawl và GitHub CLI. Cách ly hoàn toàn dữ liệu thô khỏi cửa sổ ngữ cảnh chính.
---

# Bộ Định Tuyến Tìm Kiếm Sâu (Deep Search Router)

Kỹ năng này hướng dẫn cách thu thập tài liệu kỹ thuật, nghiên cứu đối thủ, đọc repo GitHub và quét bài viết chuyên sâu mà **không làm nổ token của mô hình chính**.

## 1. Bản Đồ Định Tuyến Nhanh (Search Routing Matrix)

| Nhu cầu thông tin | Công cụ tối ưu | Lệnh thực thi cách ly |
| :--- | :--- | :--- |
| **Đọc bài viết / docs bất kỳ từ URL** | **Jina Reader (Agent Reach)** | `curl -s "https://r.jina.ai/<URL>" -o .plans/scratch_page.md` |
| **Tìm code mẫu / Repo trên GitHub** | **GitHub CLI / Agent Reach** | `gh search code "<query>" --language <lang>` |
| **Tìm kiếm cộng đồng (Reddit, Bilibili, V2EX)**| **Agent Reach CLI** | `agent-reach <kênh> search "<query>"` |
| **Cào web cấu trúc & trích xuất JSON** | **Firecrawl CLI** | `firecrawl search "<query>" -o .firecrawl/res.json --json` |
| **Tìm kiếm web tổng quan** | **Native Search Web** | Dùng tool `search_web` tích hợp sẵn |

---

## 2. Quy Tắc Cách Ly Dữ Liệu Thô (Zero-Pollution Rule)
1. **Không in HTML/JSON thô ra chat:** Mọi kết quả từ `curl`, `firecrawl` hoặc `agent-reach` phải được lưu vào thư mục đệm tạm thời (như `.plans/scratch/` hoặc `.firecrawl/`).
2. **Ủy quyền cho Subagent Flash lọc dữ liệu:**
   - Dùng Subagent Flash để đọc file tạm, chắt lọc ra danh sách nguồn uy tín và các kết luận quan trọng (tối đa 30 dòng).
   - Ghi kết quả cô đọng này vào `.plans/findings.md`.
3. **Architect chỉ đọc `.plans/findings.md`:** Architect không bao giờ đọc dữ liệu thô chưa qua lọc.
