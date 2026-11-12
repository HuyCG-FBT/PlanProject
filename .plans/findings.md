# KẾT QUẢ TRA CỨU & PHÁT HIỆN KỸ THUẬT (FINDINGS CACHE)

Tài liệu này lưu trữ toàn bộ các phát hiện kỹ thuật, tài liệu API, đường dẫn và kết quả tra cứu từ `agent-reach`, `Jina Reader`, `Firecrawl` hoặc khảo sát thực nghiệm mã nhị phân.
*Mục đích: Tra cứu 1 lần, ghi nhớ vĩnh viễn trên đĩa, tránh việc mỗi agent phải tìm lại nhiều lần gây lãng phí token.*

---

## 1. Mục lục tra cứu
- [2026-11-12] Khảo sát chuyên sâu mã nhị phân Booster Pack Heroes (Unity 6 IL2CPP Metadata v39)
- [2026-11-12] Đánh giá ma trận công cụ: AssetStudio.CLI (Razviar) vs AssetRipper vs Il2CppDumper (Rodroid)
- [2026-11-11] Cấu hình kênh tra cứu sâu Agent Reach & Deep Search Router

---

## 2. Chi tiết các phát hiện

### [2026-11-12] Khảo sát Chuyên Sâu Game Booster Pack Heroes
- **Engine Version:** `Unity 6000.4.10f1` (DirectX 12, DirectStorage). Dung lượng game: **517.07 MB** (39 file).
- **IL2CPP Metadata Version:** **Version 39** (`0x27`, Magic `0xFAB11BAF`). Sử dụng variable-width index của Unity 6.
- **Tình trạng mã hóa / Obfuscation:** **100% Plaintext, hoàn toàn không mã hóa**. Mọi định danh lớp (`CardEntryUI`, `hero_damage`, `DiscoveryScheduleManager`...) đều nguyên bản.
- **Vị trí file Nhạc & Âm thanh (394 audio clips FSB5 Vorbis):**
  - `sharedassets1.resource` (73.03 MB): Chứa 159 audio clips (BGM gameplay & combat SFX).
  - `resources.resource` (20.87 MB): Chứa 235 audio clips (Global SFX: mở thẻ, tiếng click UI, hiệu ứng âm thanh).
  - **Lưu ý kỹ thuật then chốt:** Container FSB5 có `nameTableSize = 0` (không lưu tên file). Toàn bộ 394 tên clip nằm trong các file `.assets` (`sharedassets1.assets` và `resources.assets`). Nếu dùng carver FSB độc lập sẽ bị mất tên file; **bắt buộc dùng AssetStudio.CLI hoặc AssetRipper** để giữ nguyên tên gốc (`BloodMage_Shard_Impact`, `03_Ice_Explosion`, `Sketchbook 2024-03-30_02`...).
- **Vị trí file Đồ họa & Sprite Thẻ bài:**
  - `sharedassets1.assets.resS` (148.33 MB): Dữ liệu Texture stream lớn nhất (Hero cards, quái, VFX).
  - `resources.assets.resS` (60.08 MB): Dữ liệu Texture UI, khung card, icon pack.
  - `sharedassets1.assets` (5.52 MB): Chứa định danh sprite (`hero_shotgun`, `Pack_8_Chaos`, `enemySkeleton`, `heroGuardian`...).
- **Khảo sát Toolchain:**
  - Perfare Il2CppDumper gốc dừng ở Metadata v31 -> Ném lỗi Unsupported trên Unity 6.
  - Rodroid Il2CppDumper v6.1 (Rust/Tauri) và AssetRipper v2.0.0 hỗ trợ native Metadata v39.
  - Máy chủ đã cài sẵn .NET 8.0 và .NET 10.0 SDK.
