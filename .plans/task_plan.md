# KẾ HOẠCH: Sửa lỗi bộ setup Architect–Executor

- **Trạng thái:** Ready
- **Architect:** Claude Opus (lập kế hoạch, 2026-11-11 13:56)
- **Executor:** Gemini 3.8 Flash
- **Thư mục gốc (ROOT):** `d:\FPT\Harness\SteamBP`. Mọi đường dẫn bên dưới đều tính từ ROOT.
- **Shell:** PowerShell. Lệnh CLI của npm/pip phải bọc trong `cmd /c "..."` (PowerShell đang chặn script `.ps1`).

## QUY TẮC CHUNG CHO EXECUTOR (đọc trước khi làm)
1. Làm **tuần tự** T1 → T7. Task nào FAIL ở Verification Gate thì **dừng**, ghi lỗi vào `.plans/progress.md`, rồi báo lại. Không tự nghĩ cách vòng.
2. **CẤM ĐỘNG VÀO:** `Booster Pack Heroes/`, `Booster Pack Heroes.zip`, `.plans/findings.md` (trừ T6), các skill toàn cục khác trong `~/.gemini/config/skills/` (trừ `agent-reach` ở T2).
3. Không cài thêm gói nào ngoài gói nêu trong task. Không cài `gh`, `firecrawl`, `mcporter`.
4. File nào có nội dung "GHI ĐÈ TOÀN BỘ" thì dùng `write_file` với `Overwrite: true`, chép **nguyên văn** khối nội dung, không thêm bớt.
5. Xong mỗi task: đánh `[x]` vào task đó trong file này, rồi ghi ≤3 dòng vào `.plans/progress.md`.

---

## T1. Khởi tạo git + .gitignore
**Lý do:** Quy trình nghiệm thu dựa vào `git diff`, nhưng ROOT chưa phải repo git.

**Bước:**
1. Tạo file `.gitignore` (GHI ĐÈ TOÀN BỘ):
```gitignore
# Game build & archive (chỉ đọc, không versioning)
Booster Pack Heroes/
*.zip

# Output phân tích/tra cứu thô
analysis_output/
.plans/scratch/
.firecrawl/

# OS
Thumbs.db
desktop.ini
```
2. Chạy: `cd d:\FPT\Harness\SteamBP; git init; git add -A; git commit -m "chore: baseline agent setup before fixes"`

**Verification Gate:**
- `git -C d:\FPT\Harness\SteamBP status --short` → output rỗng.
- `git -C d:\FPT\Harness\SteamBP ls-files | Select-String "Booster Pack Heroes"` → **không** có dòng nào.

**Nếu FAIL:** `git commit` báo thiếu user.name/email thì DỪNG, báo người dùng tự cấu hình. Không tự đặt tên/email giả.

- [x] T1 xong

---

## T2. Cập nhật Agent Reach + cài lại skill đúng cách
**Lý do:** CLI đang ở v1.5.0, trong khi SKILL.md là bản template mới hơn và còn placeholder chưa điền (`{{VERSION}}` dòng 13, `{{CHANNEL_TABLE}}` dòng 69). Bản mới nhất là v1.6.1.

**Bước:**
1. `cmd /c "pip install --upgrade https://github.com/Panniantong/agent-reach/archive/main.zip"`
2. `cmd /c "agent-reach --version"` → ghi lại số phiên bản.
3. `cmd /c "agent-reach skill --install"` (lệnh này render lại SKILL.md vào các thư mục skill).
4. `cmd /c "agent-reach doctor"` → **không** dán toàn bộ output vào chat, chỉ ghi dòng `状态：x/16` vào progress.

**Verification Gate (cả 3 phải đạt):**
- `agent-reach --version` ≥ `1.6.1`.
- `Select-String -Path "$env:USERPROFILE\.gemini\config\skills\agent-reach\SKILL.md","$env:USERPROFILE\.agents\skills\agent-reach\SKILL.md" -Pattern "\{\{"` → không có kết quả. File nào không tồn tại thì bỏ qua file đó.
- `cmd /c "agent-reach channels"` → exit code 0.

**Nếu FAIL:**
- pip lỗi: DỪNG, ghi lỗi (≤5 dòng cuối), không thử cách cài khác.
- Vẫn còn `{{` sau bước 3: **KHÔNG sửa tay** SKILL.md toàn cục. Ghi vào progress là "skill render failed", rồi chuyển sang T3 nhưng dùng nhánh FALLBACK của T3.
- `agent-reach channels` không tồn tại: ghi lại, dùng nhánh FALLBACK của T3.

- [x] T2 xong (FALLBACK)

---

## T3. Viết lại skill `deep-search-router`
**Target:** `.agents/skills/deep-search-router/SKILL.md` (GHI ĐÈ TOÀN BỘ)

**Nhánh:** Nếu T2 = OK thì dùng nguyên văn khối dưới. Nếu T2 = FALLBACK thì dùng khối dưới nhưng **xóa cả hàng `agent-reach`** trong bảng Tầng 2.

```markdown
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
| Công cụ | Điều kiện | Cách làm |
|---|---|---|
| `agent-reach` | `agent-reach doctor` báo kênh cần dùng ✅ | Chạy `agent-reach channels` để lấy đúng cú pháp của phiên bản đang cài, rồi mới gọi. Không đoán lệnh. |

## Tầng 3 — Tùy chọn (hiện CHƯA cài, chỉ dùng nếu người dùng tự cài)
`gh` (GitHub CLI), `firecrawl`, `mcporter` + Exa.

## Quy tắc cách ly dữ liệu
1. Output thô (HTML/JSON/Markdown dài) → ghi vào `.plans/scratch/` (đã gitignore). Không in ra chat.
2. Đọc file scratch theo đoạn (`read_file` có StartLine/EndLine) hoặc lọc bằng `Select-String`.
3. Ghi kết luận ≤30 dòng vào `.plans/findings.md`, **mỗi ý kèm URL nguồn**.
4. Architect chỉ đọc `findings.md`.
```

**Verification Gate:**
- `Select-String -Path .agents\skills\deep-search-router\SKILL.md -Pattern "firecrawl search|gh search code|agent-reach <kênh>"` → không có kết quả.
- Tạo thư mục `.plans/scratch/`, rồi chạy `curl.exe -s "https://r.jina.ai/https://example.com" -o .plans/scratch/test.md; (Get-Item .plans/scratch/test.md).Length -gt 0` → `True`. Xong thì xóa `test.md`.

- [x] T3 xong

---

## T4. Gộp rules vào AGENTS.md, xóa `.agents/rules/`
**Lý do:** Theo docs Antigravity, `AGENTS.md` luôn được nạp. Trong phiên này, chỉ `AGENTS.md` xuất hiện trong rules của agent; hai file trong `.agents/rules/` không thấy được nạp. Nội dung của chúng đã trùng với `AGENTS.md`.

**Bước:**
1. `AGENTS.md` (GHI ĐÈ TOÀN BỘ):
```markdown
# WORKSPACE DIRECTIVE: ARCHITECT - EXECUTOR

1. **Architect (model mạnh):** lập kế hoạch theo skill `architect-micro-planner` vào `.plans/task_plan.md`; giao task cho subagent `invoke_subagent(Model: "flash")` hoặc để người dùng đổi sang Flash; nghiệm thu bằng `git diff` + Verification Gate.
2. **Executor (Flash):** theo skill `flash-executor-runbook`; chỉ đọc Context Slice; chạy Verification Gate; ghi ≤3 dòng vào `.plans/progress.md`.
3. **Tra cứu:** theo skill `deep-search-router`; dữ liệu thô vào `.plans/scratch/`, kết luận có nguồn vào `.plans/findings.md`.
4. **Context:** lọc log bằng `Select-String`; không dán output dài vào chat; không ghi số liệu chưa đo.
5. **Không động vào** `Booster Pack Heroes/` và file `.zip` trừ khi task yêu cầu đọc.
```
2. Xóa: `Remove-Item -Recurse .agents\rules`

**Verification Gate:**
- `Test-Path .agents\rules` → `False`
- `(Get-Content AGENTS.md).Count` ≤ 12

- [x] T4 xong

---

## T5. Sửa template kế hoạch (bỏ đường dẫn tuyệt đối, bỏ giả định Python)
**Target:** `.plans/template_task_plan.md` (GHI ĐÈ TOÀN BỘ)

```markdown
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
```

**Verification Gate:**
- `Select-String -Path .plans\template_task_plan.md -Pattern "file:///|pytest tests/test_feature"` → không có kết quả.

- [x] T5 xong

---

## T6. Sửa đường dẫn tuyệt đối & lỗi chữ ở các file còn lại
**Bước (dùng `edit_file`, chỉ thay đúng chuỗi):**
1. `.agents/skills/architect-micro-planner/SKILL.md`:
   - Thay `` `Đọc file:///d:/FPT/Harness/SteamBP/src/models/card.py (chỉ đọc các dòng 10-45)` ``
     bằng `` `Đọc src/models/card.py#L10-L45 (đường dẫn tương đối từ ROOT)` ``
2. `.plans/findings.md`:
   - Thay `# TẬP KHOA HỌC & KẾT QUẢ TRA CỨU (FINDINGS CACHE)` bằng `# KẾT QUẢ TRA CỨU (FINDINGS CACHE)`
   - Thay dòng `- CLI: \`agent-reach\` v1.5.0 đã cài đặt toàn cục.` bằng `- CLI: \`agent-reach\` <phiên bản ghi được ở T2>.`

**Verification Gate:**
- `Select-String -Path .agents\skills\*\SKILL.md,.plans\*.md -Pattern "file:///d:"` → không có kết quả. Riêng `task_plan.md` (file này) được loại trừ.

- [x] T6 xong

---

## T7. Sửa báo cáo: bỏ khẳng định không có nguồn, thêm nguồn
**Target:** `reports/AI_ENGINEERING_AGENTIC_WORKFLOWS.md` (dùng `edit_file`)

1. Thay nguyên đoạn:
   `Trong các phòng thí nghiệm AI hàng đầu (Google DeepMind, Anthropic, OpenAI) và các đội ngũ công nghệ tiên tiến (Vercel, Cursor, Factory), cách thức làm việc giữa con người và AI đã chuyển dịch mạnh mẽ từ **Single-prompting (Ra lệnh đơn lẻ)** sang **Agentic Tiered Workflows (Quy trình tác tử phân tầng)**.`
   bằng:
   `Các bộ skill mã nguồn mở phổ biến cho coding agent (xem mục Nguồn) cho thấy xu hướng chuyển từ ra lệnh đơn lẻ sang quy trình phân tầng: lập kế hoạch ra file, chia task nhỏ, thực thi có kiểm thử. Báo cáo này tổng hợp các pattern đó; các nhận định chưa có nguồn được ghi rõ là "kinh nghiệm thực hành".`
2. Ở mục 2, ngay dưới tiêu đề `### Pattern 1: ...`, thêm dòng: `> Kinh nghiệm thực hành (chưa có nguồn chính thức).`
3. Thêm vào **cuối file**:
```markdown

---

## 7. HẠN CHẾ ĐÃ BIẾT CỦA SETUP HIỆN TẠI
- Chưa đo mức tiết kiệm token; mọi nhận định về "tiết kiệm" là định tính.
- `gh`, `firecrawl`, `mcporter`/Exa chưa cài; tra cứu dựa vào `search_web`, Jina Reader và `agent-reach` (nếu T2 OK).
- Ranh giới Architect/Executor là quy ước trong `AGENTS.md`, không có cơ chế cưỡng chế. Model nào được chọn lúc bấm Proceed sẽ là model thực thi.

## 8. NGUỒN
- planning-with-files: https://github.com/OthmanAdi/planning-with-files
- planning-and-task-breakdown: https://github.com/addyosmani/agent-skills
- executing-plans: https://github.com/obra/superpowers
- Agent Reach: https://github.com/Panniantong/Agent-Reach
- Antigravity Skills: https://antigravity.google/docs/skills
- Antigravity Rules: https://antigravity.google/docs/rules-workflows
```
4. Trong mục 6, thay `- **Tệp lưu trữ trạng thái:** [task_plan.md](file:///d:/FPT/Harness/SteamBP/.plans/template_task_plan.md), [findings.md](file:///d:/FPT/Harness/SteamBP/.plans/findings.md), [progress.md](file:///d:/FPT/Harness/SteamBP/.plans/progress.md).` bằng `- **Tệp lưu trữ trạng thái:** \`.plans/task_plan.md\`, \`.plans/findings.md\`, \`.plans/progress.md\`.`
   và thay `- **Tập tin chỉ thị:** [AGENTS.md](file:///d:/FPT/Harness/SteamBP/AGENTS.md)` bằng `- **Tập tin chỉ thị:** \`AGENTS.md\``

**Verification Gate:**
- `Select-String -Path reports\AI_ENGINEERING_AGENTIC_WORKFLOWS.md -Pattern "DeepMind|file:///|60%"` → không có kết quả.
- `Select-String -Path reports\AI_ENGINEERING_AGENTIC_WORKFLOWS.md -Pattern "## 8. NGUỒN"` → có 1 kết quả.

- [x] T7 xong

---

## Bàn giao cuối
1. `git add -A; git commit -m "fix: agent setup corrections (T1-T7)"`
2. Báo lại cho người dùng **≤6 dòng**: trạng thái từng task (OK/FALLBACK/FAIL) và phiên bản agent-reach.

## Nghiệm thu (Architect — Opus, sau khi Flash xong)
- [ ] `git show --stat HEAD` chỉ chạm: `.gitignore`, `AGENTS.md`, `.agents/`, `.plans/`, `reports/`
- [ ] Chạy lại toàn bộ Verification Gate T1–T7
