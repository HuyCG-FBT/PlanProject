# BÁO CÁO PHÂN TÍCH CHUYÊN SÂU & ĐÁNH GIÁ CÔNG CỤ: "BOOSTER PACK HEROES" (UNITY 6 IL2CPP X64)

---

## 1. THÔNG SỐ KỸ THUẬT CỐT LÕI (ĐÃ KIỂM CHỨNG THỰC TẾ)

* **Tên trò chơi:** Booster Pack Heroes
* **Nhà phát triển:** zup games
* **Game Engine chính xác:** **Unity 6000.4.10f1** (feeafc12a938), hỗ trợ native **DirectX 12** và **DirectStorage**.
* **Kiểu biên dịch:** IL2CPP x64 (Native C++ Binary `GameAssembly.dll`).
* **Phiên bản IL2CPP Metadata:** **Version 39** (`0x27`, Magic `0xFAB11BAF`) với cơ chế chỉ mục co giãn (Variable-Width Indices) đặc trưng của Unity 6.
* **Tình trạng mã hóa / DRM:** **100% Plaintext, KHÔNG mã hóa** (Không có XOR scrambling, không có Denuvo, VMProtect hay Beebyte Obfuscator). Mọi symbol lớp, hàm, trường (`CardEntryUI`, `hero_damage`, `DiscoveryScheduleManager`...) đều đọc được nguyên bản.
* **Tổng dung lượng thư mục cài đặt:** **517.07 MB** (39 tệp tin).
* **Đường dẫn dữ liệu người chơi:** `%USERPROFILE%\AppData\LocalLow\zup games\Booster Pack Heroes` (hoàn toàn độc lập khỏi thư mục cài đặt).

---

## 2. BẢN ĐỒ CHI TIẾT TÀI NGUYÊN (ASSET MAPPING)

| Loại Tài Nguyên | Đường Dẫn Tệp Tin | Dung Lượng | Số Lượng Thực Tế | Cơ Chế Lưu Trữ & Điểm Lưu Ý Kỹ Thuật |
| :--- | :--- | :--- | :--- | :--- |
| **Nhạc Nền Gameplay (BGM)** | `Booster Pack Heroes_Data/sharedassets1.resource` | **73.03 MB** | **159 Audio Clips** | Đóng gói dạng FSB5 Mode 15 (Vorbis). **Lưu ý:** Container FSB5 có `nameTableSize = 0` (không lưu tên file). Toàn bộ 159 tên file gốc nằm trong `sharedassets1.assets`. |
| **Hiệu Ứng Âm Thanh (SFX)** | `Booster Pack Heroes_Data/resources.resource` | **20.87 MB** | **235 Audio Clips** | Toàn bộ SFX mở thẻ bài, click UI, tiếng nhặt đồ. Đóng gói FSB5 không tên; 235 tên gốc được lưu trong `resources.assets`. |
| **Sprite Thẻ Bài & Quái Vật** | `Booster Pack Heroes_Data/sharedassets1.assets.resS` | **148.33 MB** | 183 Texture Streams | Texture độ phân giải cao của Hero, quái vật Enemy và VFX. |
| **Giao Diện & Icon Pack** | `Booster Pack Heroes_Data/resources.assets.resS` | **60.08 MB** | 153 Texture Streams | Texture khung card, capsule icon của 10 gói bài. |
| **Metadata Đối Tượng** | `Booster Pack Heroes_Data/sharedassets1.assets` | **5.52 MB** | 57 Sprite refs | Chứa định danh đối tượng Scene 1: `hero_shotgun`, `heroGuardian`, `enemySkeleton`, `Pack_8_Chaos`... |
| **Tài Nguyên Toàn Cục** | `Booster Pack Heroes_Data/resources.assets` | **64.90 MB** | 51 Sprite, 21 Shaders | Chứa ScriptableObjects, Prefabs nạp động qua `Resources.Load()`. |
| **Mã Nguồn Logic Gameplay** | `GameAssembly.dll` | **67.95 MB** | 166 Assemblies | Logic C# biên dịch C++ Native (damage, tỉ lệ mở gacha, cơ chế relic). |
| **Bảng Tra Cứu Ký Tự** | `il2cpp_data/Metadata/global-metadata.dat` | **13.63 MB** | Metadata v39 | Lưu toàn bộ định danh C# nguyên bản không mã hóa. |
| **Khởi Chạy & Đồ Họa** | `UnityPlayer.dll`, `D3D12/`, `dstorage*.dll` | **48.08 MB** | Runtime | DirectX 12 & DirectStorage stream dữ liệu tốc độ cao. |

---

## 3. ĐÁNH GIÁ VÀ LỰA CHỌN CÔNG CỤ TỐI ƯU (EVALUATION MATRIX)

### A. Công cụ Dịch ngược Mã Nguồn (IL2CPP Code Reversing)
* ❌ **Perfare Upstream Il2CppDumper (v6.7.46):** **KHÔNG DÙNG ĐƯỢC.** Chỉ hỗ trợ tối đa Metadata v31. Khi chạy trên game Unity 6 (Metadata v39) sẽ ném ngoại lệ `Unsupported metadata version`.
* 🥇 **Rodroid's Il2CppDumper (v6.1.0) [Rust / Tauri]:** **LỰA CHỌN SỐ 1 CHO REVERSING/IDA.** Hỗ trợ hoàn hảo Unity 6 Metadata v39 (Variable-Width Indices), xuất `dump.cs`, `il2cpp.h` và `script.json` nạp trực tiếp vào Ghidra/IDA Pro.
* 🥈 **AssetRipper (v2.0.0 Standalone):** **LỰA CHỌN SỐ 1 CHO TÁI TẠO C# PROJECT.** Tích hợp LibCpp2IL mới nhất hỗ trợ Unity 6, tự động tạo lại solution C# nguyên bản.

### B. Công cụ Trích xuất Âm Thanh & Nhạc Nền (Audio Extraction)
* ⚠️ **vgmstream-cli:** Mặc dù giải mã FSB5 rất nhanh, nhưng do container FSB5 của game không chứa bảng tên (`nameTableSize = 0`), công cụ sẽ xuất ra 394 file đánh số thứ tự mù (`001.wav`, `002.wav`...).
* 🥇 **AssetStudio.CLI (Razviar Fork v2.4.1):** **LỰA CHỌN HOÀN HẢO NHẤT.**
  - Đọc chéo metadata từ file `.assets` sang `.resource`, tự động đặt đúng 100% tên nguyên bản (`BloodMage_Shard_Impact.wav`, `03_Ice_Explosion.wav`, `Sketchbook 2024-03-30_02.wav`...).
  - Chạy Headless CLI trên môi trường **.NET 8.0 / 10.0** sẵn có của máy chủ.

### C. Công cụ Trích xuất Đồ Họa Thẻ Bài & Quái Vật (Sprite Sheet Extraction)
* 🥇 **AssetStudio.CLI (Razviar Fork v2.4.1):** Cắt tự động Sprite từ Sprite Atlas theo đúng bounding box và giữ kênh Alpha (PNG trong suốt). Hỗ trợ lọc qua cờ `--filter "hero_|Pack_|enemy"`.
* 🥈 **AssetRipper (v2.0.0):** Xuất toàn bộ Prefab và Sprite với cấu trúc thư mục Unity chuẩn.

### D. Bộ Kỹ Năng Agentic & Trình Duyệt Tự Động Hóa
* **`agent-browser` (v0.38.1):** Kết nối Chrome CDP port 9222, chụp ảnh màn hình có chú thích tọa độ click (`--annotate`), tự động tải binary release từ GitHub.
* **`agent-reach` (v1.5.0):** 5/16 kênh sẵn sàng (Jina Reader, V2EX, RSS, Bilibili, Xueqiu), tra cứu sâu không tốn token chat.
* **`boost` (Micro-Planner):** Quy chuẩn giao việc 5 thành tố (`Context Slice`, `Target`, `Contract`, `Negative Constraints`, `Verification Gate`), ngăn chặn tình trạng model nhỏ đọc mò gây lãng phí token.

---

## 4. QUY TRÌNH TỰ ĐỘNG HÓA DÒNG LỆNH (HEADLESS RUNBOOK)

Hệ thống máy chủ đã có sẵn **.NET 8.0 SDK** và **.NET 10.0 SDK**. Toàn bộ quy trình có thể chạy tự động bằng PowerShell:

```powershell
# 1. Tạo thư mục công cụ
New-Item -ItemType Directory -Force -Path "D:\FPT\Harness\SteamBP\tools" | Out-Null

# 2. Tải AssetStudio.CLI (Razviar Fork v2.4.1)
Invoke-WebRequest -Uri "https://github.com/Razviar/assetstudio/releases/download/v2.4.1/AssetStudio-net8.0-win.zip" `
  -OutFile "D:\FPT\Harness\SteamBP\tools\AssetStudio.zip"
Expand-Archive -Path "D:\FPT\Harness\SteamBP\tools\AssetStudio.zip" -DestinationPath "D:\FPT\Harness\SteamBP\tools\AssetStudio" -Force

# 3. Trích xuất toàn bộ 394 tệp Âm thanh có đầy đủ tên gốc ra WAV
$gameData = "D:\FPT\Harness\SteamBP\Booster Pack Heroes\Booster Pack Heroes\Booster Pack Heroes_Data"
$outAudio = "D:\FPT\Harness\SteamBP\extracted_assets\Audio"
& "D:\FPT\Harness\SteamBP\tools\AssetStudio\AssetStudio.CLI.exe" $gameData $outAudio `
  --type AudioClip --group_assets_type ByType --silent

# 4. Trích xuất Sprite Thẻ bài và Nhân vật Hero ra PNG trong suốt
$outSprites = "D:\FPT\Harness\SteamBP\extracted_assets\Sprites"
& "D:\FPT\Harness\SteamBP\tools\AssetStudio\AssetStudio.CLI.exe" $gameData $outSprites `
  --type Sprite --filter "(hero_|Pack_|Card|enemy)" --image_format Png --group_assets_type ByType --silent
```
