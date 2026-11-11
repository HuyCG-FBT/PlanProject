# KẾT QUẢ TRA CỨU (FINDINGS CACHE)

Tài liệu này lưu trữ toàn bộ các phát hiện kỹ thuật, tài liệu API, đường dẫn và kết quả tra cứu từ `agent-reach`, `Jina Reader`, `Firecrawl` hoặc tài liệu bên ngoài.
*Mục đích: Tra cứu 1 lần, ghi nhớ vĩnh viễn trên đĩa, tránh việc mỗi agent phải tìm lại nhiều lần gây lãng phí token.*

---

## 1. Mục lục tra cứu
- [2026-11-11] Khảo sát cơ chế game Booster Pack Heroes & Engine Unity
- [2026-11-11] Cấu hình kênh tra cứu sâu Agent Reach

---

## 2. Chi tiết các phát hiện

### [2026-11-11] Khảo sát Game Booster Pack Heroes
- **Engine:** Unity IL2CPP x64 (DirectX 12). Tổng dung lượng: 517.07 MB (39 file).
- **Vị trí file Nhạc & Âm thanh (394 audio clips FSB5):**
  - `sharedassets1.resource` (73.03 MB): Chứa 159 audio clips (BGM gameplay & combat SFX).
  - `resources.resource` (20.87 MB): Chứa 235 audio clips (Global SFX: mở thẻ, tiếng click UI, hiệu ứng âm thanh).
- **Vị trí file Đồ họa, Sprite & Thẻ bài:**
  - `sharedassets1.assets.resS` (148.33 MB): Dữ liệu Texture stream lớn nhất (Hero cards, quái, VFX).
  - `resources.assets.resS` (60.08 MB): Dữ liệu Texture UI, khung card, icon pack.
  - `sharedassets1.assets` (5.52 MB): Chứa định danh sprite (`hero_shotgun`, `Pack_8_Chaos`, `enemySkeleton`, `heroGuardian`...).
- **Core DLL & Logic:** `GameAssembly.dll` (67.95 MB), `UnityPlayer.dll` (35.2 MB), `global-metadata.dat` (13.63 MB).
- **DRM & Steamworks:** Có `steam_api64.dll` (0.3 MB).
- **Dữ liệu người chơi:** Không lưu trong thư mục game, tách biệt tại `%USERPROFILE%\AppData\LocalLow\zup games\Booster Pack Heroes`.

### [2026-11-11] Cấu hình kênh tra cứu sâu Agent Reach
- CLI: `agent-reach` v1.5.0.
- Kênh trực tiếp: Jina Reader (`curl -s "https://r.jina.ai/<URL>"`), V2EX API, RSS/Atom, Bilibili search.
- Kỹ năng tích hợp: `Panniantong/Agent-Reach@agent-reach` tại `~/.gemini/config/skills/agent-reach`.
