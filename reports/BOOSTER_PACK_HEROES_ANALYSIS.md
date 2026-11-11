# BÁO CÁO PHÂN TÍCH TOÀN DIỆN: CẤU TRÚC TÀI NGUYÊN VÀ KIẾN TRÚC GAME "BOOSTER PACK HEROES"

---

## 1. THÔNG TIN TỔNG QUAN HỆ THỐNG
* **Tên sản phẩm:** Booster Pack Heroes
* **Nhà phát triển (Developer):** zup games
* **Game Engine:** Unity (Phiên bản hỗ trợ DirectX 12, DirectStorage)
* **Kiểu biên dịch (Scripting Backend):** IL2CPP x64 (Native C++ Assembly)
* **Tổng dung lượng thư mục cài đặt:** **517.07 MB**
* **Tổng số lượng tệp tin:** **39 tệp** (phân bổ trong 9 thư mục con)
* **Vị trí lưu trữ dữ liệu người chơi (Save Game):** `%USERPROFILE%\AppData\LocalLow\zup games\Booster Pack Heroes` (tách biệt hoàn toàn khỏi thư mục game)

---

## 2. BẢNG PHÂN LOẠI & ĐỊNH VỊ TÀI NGUYÊN (ASSET MAPPING)

| Nhóm Tài Nguyên | Tên Tệp Tin / Đường Dẫn | Dung Lượng | Số Lượng Đo Được | Vai Trò & Nội Dung Cụ Thể |
| :--- | :--- | :--- | :--- | :--- |
| **Nhạc Nền (BGM)** | `Booster Pack Heroes_Data/sharedassets1.resource` | **73.03 MB** | **159 Audio Clips** | Chứa toàn bộ nhạc nền gameplay (Battle BGM), âm thanh chiến đấu theo từng wave, âm thanh quái vật. Đóng gói chuẩn **FSB5 (FMOD SoundBank 5)**. |
| **Hiệu Ứng Âm Thanh (SFX)** | `Booster Pack Heroes_Data/resources.resource` | **20.87 MB** | **235 Audio Clips** | Chứa âm thanh toàn cục (Global SFX): Tiếng xé/mở gói bài (*Pack Ripping*), click UI, tiếng nhặt coin/gem, hiệu ứng âm thanh kỹ năng. |
| **Đồ Họa & Sprite Thẻ Bài** | `Booster Pack Heroes_Data/sharedassets1.assets.resS` | **148.33 MB** | ~ Hàng trăm texture | Tệp dung lượng lớn nhất game. Chứa dữ liệu streaming thô: Sprite Atlas thẻ bài nhân vật Hero, quái vật Enemy, hiệu ứng hạt (VFX), hoạt ảnh. |
| **Giao Diện & Icon Pack** | `Booster Pack Heroes_Data/resources.assets.resS` | **60.08 MB** | ~ Hàng chục texture | Dữ liệu texture khung thẻ bài (Card frame), capsule icon các gói bài, biểu tượng UI dùng chung. |
| **Cấu Trúc Đối Tượng & Meta** | `Booster Pack Heroes_Data/sharedassets1.assets` | **5.52 MB** | 57 Sprite refs | Chứa cấu trúc phân cấp GameObject, liên kết Material, Shader (DirectX 12) và định danh thẻ bài của Scene 1. |
| **Tài Nguyên Toàn Cục** | `Booster Pack Heroes_Data/resources.assets` | **64.90 MB** | 51 Sprite, 21 Shader refs | Chứa dữ liệu các prefab, ScriptableObject định nghĩa chỉ số ban đầu được gọi qua `Resources.Load()`. |
| **Mã Nguồn Logic Gameplay** | `GameAssembly.dll` | **67.95 MB** | Toàn bộ mã C# | Mã C# đã biên dịch sang mã máy C++ x64. Chứa logic mở thẻ gacha, tính damage, AI di chuyển của quái, hệ thống relic/upgrade. |
| **Metadata Phản Chiếu** | `Booster Pack Heroes_Data/il2cpp_data/Metadata/global-metadata.dat` | **13.63 MB** | Toàn bộ định danh | Chứa bảng tra cứu tên Class, tên hàm (Method), trường dữ liệu (Field) và chuỗi ký tự (Strings) của C#. |
| **Unity Engine Core** | `UnityPlayer.dll` + `Booster Pack Heroes.exe` | **35.84 MB** | Engine Runner | Bộ nạp thực thi và điều phối vòng lặp render, âm thanh và bộ nhớ. |
| **DirectX 12 & Storage** | `D3D12/D3D12Core.dll`, `dstoragecore.dll`, `dstorage.dll` | **6.12 MB** | Driver / API | Hỗ trợ DirectStorage giúp load texture và audio streaming từ ổ cứng với tốc độ cực cao. |
| **Bản Quyền & DRM** | `Booster Pack Heroes_Data/Plugins/x86_64/steam_api64.dll` | **0.30 MB** | Steam SDK | Giao tiếp với Steam Client (License Check, Cloud Save, Thành tựu). |

---

## 3. DANH SÁCH CHỦ THỂ NỘI DUNG ĐÃ NHẬN DIỆN (CONTENT AUDIT)

Qua quét chuỗi dữ liệu trong các tệp `.assets`, các nhóm thực thể chính trong game gồm có:

### A. Danh sách Nhân vật (Heroes):
* `hero_shotgun` (Xạ thủ súng săn)
* `hero_beastmaster` (Bậc thầy huấn luyện thú)
* `heroGuardian` (Hộ vệ phòng thủ)
* `heroMonk` (Tu sĩ)
* `hero_bloodmage` (Pháp sư máu)
* `hero_dinoRider` / `hero_dino` / `hero_rider` (Kỵ sĩ khủng long)
* `hero_icebourne` (Băng nhân)
* `hero_skelewhee` (Chiến binh xương xe)
* `heroSquire3` (Cận vệ)
* `hero_warlord` (Lãnh chúa chiến tranh)
* `hero_fisher` (Ngư phủ)
* `hero_treant` (Người cây)

### B. Danh sách Gói thẻ bài (Booster Packs):
* `Pack_8_Chaos` (Gói Hỗn Mang)
* `Pack_9_Tome` (Gói Cổ Thư)
* `Pack_7_Frost` (Gói Băng Giá)
* Các định dạng ảnh đóng gói: `Pack_small_462x174`, `Pack_LibraryCapsule_600x900`, `Card_Elements`, `card_Item`.

### C. Kẻ địch (Enemies & Waves):
* `enemySkeleton` (Bộ xương) và các biến thể quái vật theo wave.

---

## 4. HƯỚNG DẪN QUY TRÌNH KỸ THUẬT XỬ LÝ VỀ SAU (OPERATIONAL RUNBOOK)

Khi cần thực hiện các tác vụ xử lý chuyên sâu, sử dụng các công cụ và quy trình sau:

### Tác Vụ 1: Trích Xuất File Nhạc & Âm Thanh Ra `.wav` hoặc `.ogg`
* **Nguồn tệp:** `sharedassets1.resource` và `resources.resource`.
* **Công cụ:**
  - **AssetStudioGUI:** Bấm `File > Load Folder` > chọn `Booster Pack Heroes_Data` > sang tab `Asset List` > Lọc `Type: AudioClip` > Chuột phải chọn `Export Selected Assets`. Tool sẽ tự động giải mã từ FSB5 thành file `.wav` chuẩn.
  - **vgmstream / fsb_extractor:** Công cụ CLI giải mã hàng loạt file FSB5.

### Tác Vụ 2: Trích Xuất Hình Ảnh Thẻ Bài & Sheet Quái Ra `.png`
* **Nguồn tệp:** `sharedassets1.assets.resS` và `resources.assets.resS`.
* **Công cụ:**
  - **AssetStudioGUI:** Lọc `Type: Sprite` hoặc `Texture2D` > Tìm theo từ khóa `hero_`, `enemy`, `Pack_` > Chuột phải chọn `Export > Selected assets`.
  - **AssetRipper:** Tạo lại toàn bộ cấu trúc Sprite Atlas và Texture nguyên bản.

### Tác Vụ 3: Đọc Logic Code & Bảng Chỉ Số Game (Reverse Engineering)
* **Nguồn tệp:** `GameAssembly.dll` và `global-metadata.dat`.
* **Công cụ:**
  - Sử dụng **Il2CppDumper**:
    ```bash
    Il2CppDumper.exe "<path_to_GameAssembly.dll>" "<path_to_global-metadata.dat>" "<output_directory>"
    ```
  - **Kết quả sinh ra:**
    - `dump.cs`: Chứa toàn bộ cấu trúc class C#, hàm tính toán, chỉ số HP, Damage, giá tiền mở pack.
    - `script.json` / `il2cpp.h`: Dùng để nạp vào **Ghidra** hoặc **IDA Pro** nếu cần xem mã assembly chi tiết.
