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
- **Engine:** Unity IL2CPP x64 (DirectX 12).
- **Core DLL:** `GameAssembly.dll` (Chứa logic C# đã biên dịch sang C++ Native), `UnityPlayer.dll` (Engine core).
- **Metadata:** `Booster Pack Heroes_Data/il2cpp_data/Metadata/global-metadata.dat`.
- **DRM & Steamworks:** Có sự hiện diện của `steam_api64.dll` và `Steamworks.NET.txt`.
- **Dữ liệu người chơi:** Không lưu trong thư mục game, tách biệt tại `%USERPROFILE%\AppData\LocalLow\zup games\Booster Pack Heroes`.

### [2026-11-11] Cấu hình kênh tra cứu sâu Agent Reach
- CLI: `agent-reach` v1.5.0.
- Kênh trực tiếp: Jina Reader (`curl -s "https://r.jina.ai/<URL>"`), V2EX API, RSS/Atom, Bilibili search.
- Kỹ năng tích hợp: `Panniantong/Agent-Reach@agent-reach` tại `~/.gemini/config/skills/agent-reach`.
