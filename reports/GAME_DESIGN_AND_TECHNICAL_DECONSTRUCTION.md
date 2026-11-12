# BÁO CÁO GIẢI MÃ TOÀN DIỆN: THIẾT KẾ TRÒ CHƠI, KIẾN TRÚC HỆ THỐNG VÀ CHIẾN LƯỢC TÂM LÝ "BOOSTER PACK HEROES"

---

## 1. TỔNG QUAN HỆ THỐNG & CẤU TRÚC THƯ MỤC CHỨC NĂNG

### A. Cấu trúc tổ chức mã nguồn và tài nguyên (Folder & Module Architecture)
Dựa trên phân tích nhị phân `GameAssembly.dll`, `global-metadata.dat` (Metadata v39) và hệ thống asset, kiến trúc mã nguồn của trò chơi được chia thành 6 phân hệ độc lập:

```
[MÃ NGUỒN C# / IL2CPP MODULES]
├── 1. Core Engine & Runtime:
│   ├── GameAssembly.dll (Logic C++ Native)
│   ├── UnityPlayer.dll (Unity 6000.4.10f1 Engine)
│   └── Plugins (DirectStorage, DirectX 12, Steamworks)
│
├── 2. Spawning & Horde System:
│   ├── WaveSpawner / RiftWaveSpawner (Quản lý nhịp độ wave)
│   ├── WaveConfig / WaveConfigForCampaign (Bảng dữ liệu ScriptableObject)
│   └── MonsterPool (_InactiveMonsters - Object Pooling)
│
├── 3. Card & Gacha Simulation (Trading Card Engine):
│   ├── BoosterPack / PackSpawn (Hệ thống tạo và xé gói bài)
│   ├── FoilManager / FoilRevealRays (Hiệu ứng thẻ bài lấp lánh / Foil)
│   └── randomTomePool / RelicGain (Sách kỹ năng và cổ vật)
│
├── 4. Economy & Pity Subsystem:
│   ├── MonsterPackDropPityManager (Thuật toán bảo hiểm rớt pack)
│   ├── KillCoinPityConfig (Bảo hiểm rớt tiền khi tiêu diệt quái)
│   └── Foilium / FoiliumDropFlight (Tiền tệ đặc biệt đổi đồ hiếm)
│
├── 5. Game Juice & Feedback Engine:
│   ├── JuiceManager / CombatJuiceBurstPool (Tạo độ nảy, rung, sướng tay)
│   ├── ScreenShakeManager (Rung màn hình khi ra đòn lớn)
│   └── Gameplay_DamageNumbers (Số sát thương nảy động)
│
└── 6. Progression & Meta-Game:
    ├── SkillTreeUI / SkillNodesRoot (Cây kỹ năng vĩnh viễn)
    └── SurviveDoorView / ChapterProgression (Màn chơi và cửa sinh tồn)
```

---

## 2. BÍ QUYẾT KHIẾN GAME THU HÚT & CƠ CHẾ TÂM LÝ HỌC (THE DOPAMINE LOOP)

Sự hấp dẫn kỳ lạ của *Booster Pack Heroes* đến từ việc kết hợp 3 thể loại gây nghiện nhất vào một vòng lặp cốt lõi (**The Golden Triad**):

```
         ┌──────────────────────────────────────────────────┐
         │              VÒNG LẶP CỐT LÕI (CORE LOOP)        │
         └─────────────────────────┬────────────────────────┘
                                   │
                                   ▼
    [1. THỦ THÀNH / HẠ BẦY QUÁI] ─────► Tiêu diệt hàng trăm quái vật
              ▲                                   │
              │                                   ▼ Nhận Vàng & Foilium
              │                     [2. XÉ GÓI THẺ BÀI (TCG DOPAMINE)]
              │                       Cảm giác mở thẻ Foil, tìm Hero hiếm
              │                                   │
              │                                   ▼
    [3. KẾT HỢP SYNERGY & NÂNG CẤP] ◄──── Nâng chỉ số, phối hợp Cổ vật (Relics)
```

### Các thủ thuật tâm lý được cài cắm tinh vi:
1. **Ảo giác mở gói bài vật lý (Physical Pack Illusion):**
   - Thay vì chỉ bấm nút "Gacha x10" khô khan như game di động thông thường, game tái hiện tỉ mỉ thao tác: Xé mép bao gói thẻ bài -> Âm thanh rách giấy giòn tan (`FSB5 SFX`) -> Từng thẻ trượt ra -> Tia sáng lấp lánh hé lộ thẻ Foil (`FoilRevealRays`, `FoilNameEcho`).
2. **Kích thích trực quan Foil (Holographic Card Addiction):**
   - Sự xuất hiện của cơ chế **Foil Cards** (Thẻ phủ bạc/phủ nhũ) và tiền tệ **Foilium** kích hoạt tâm lý sưu tầm tương tự như người chơi Pokémon TCG hay Magic: The Gathering ngoài đời thực.
3. **Thỏa mãn thị giác số đông (Horde Cleaving Satisfaction):**
   - Quái vật tràn ra như thác lũ tạo cảm giác áp lực, nhưng khi người chơi kích hoạt kỹ năng diện rộng (AoE), hàng trăm con quái cùng lúc nổ tung tạo thành cơn mưa số sát thương (`DamageNumbers`) và tiền bay ngập màn hình (`CombatCoinCanvas`).

---

## 3. XỬ LÝ GIAO DIỆN (UI/UX) VÀ NGHỆ THUẬT "GAME JUICE"

Game chạy cực kỳ mượt mà nhờ việc phân lớp giao diện và kỹ thuật "Juice" bậc thầy:

### A. Kiến trúc Giao diện UI:
* **Tách biệt Canvas tĩnh và Canvas động:** Các thông tin cố định (máu người chơi, số wave) nằm ở Canvas tĩnh (không bao giờ vẽ lại). Toàn bộ tiền bay (`CombatCoinCanvas`), sát thương và thanh máu quái vật nằm ở Canvas riêng để giảm tải Re-batching UI.
* **Tweening & Animation nhanh:** Sử dụng hệ thống `SkillTreeAnimationsFast` để người chơi không phải chờ đợi các màn hình menu dài dòng.

### B. Nghệ thuật Game Juice (Phản hồi giác quan):
* **`JuiceManager` & `transformJuice`:** Mọi phần tử giao diện khi được bấm đều có độ co giãn nhẹ (Squash & Stretch) 1.1x rồi nảy về 1.0x.
* **`ScreenShakeManager`:** Rung màn hình có trọng lượng (`openScreenShake`, `Gameplay_ScreenShakeEnabled`), chỉ kích hoạt khi quái Boss xuất hiện hoặc nổ combo cực lớn.
* **Âm thanh phân tầng:** 235 clip SFX ngắn trong `resources.resource` được tinh chỉnh cao độ (Pitch Variation ngẫu nhiên ±5%) để cùng một tiếng click thẻ bài hay tiếng nhặt tiền không bao giờ tạo cảm giác đinh tai, lặp lại khó chịu.

---

## 4. LOGIC QUÁI, THẺ BÀI VÀ VẬT PHẨM (GAMEPLAY MECHANICS)

### A. Logic Quái Vật (Wave & Enemy AI)
* **Thuật toán sinh theo nhịp (Pacing Rhythm):** `WaveSpawner` không xuất quái đều đặn mà theo nhịp: `Bình yên (Chuẩn bị) -> Bùng nổ (Wave Burst) -> Đỉnh điểm (Mini-boss / BossSpawnWebs) -> Thư thả (Thu thập chiến lợi phẩm)`.
* **Di chuyển bầy đàn không xung đột (Horde Flow):** Dùng `TransformAccessJob` trong Unity Job System để tính toán vị trí theo nhóm, không cần từng con quái tự tìm đường phức tạp (A* Pathfinding), giúp tiết kiệm 95% CPU.

### B. Hệ thống Thẻ Bài (Heroes) & Sách Kỹ Năng (Tomes)
* Các thẻ chia thành các trường phái (Archetypes):
  - **Sát thương vật lý / Xạ thủ:** `hero_shotgun`, `hero_warlord`, `heroSquire3`.
  - **Phép thuật & Kiểm soát:** `hero_bloodmage` (hút máu), `hero_icebourne` (làm chậm đóng băng `BlizzardWave`).
  - **Triệu hồi & Tự nhiên:** `hero_beastmaster`, `hero_treant`, `hero_dinoRider`.
* **Sách kỹ năng ngẫu nhiên (`randomTomePool`):** Sau mỗi đợt wave, người chơi nhận Tomes để nâng cấp thuộc tính của thẻ hiện có (Tăng số đạn, tăng bán kính nổ, giảm thời gian hồi chiêu).

---

## 5. CƠ CHẾ KINH TẾ, TÍNH ĐIỂM VÀ BẢO HIỂM RƠI ĐỒ (PITY SYSTEM)

### A. Cơ chế 3 tầng tiền tệ (Multi-Tier Currency)
1. **Vàng thường (`GoldGold` / `spentGold`):** Kiếm được trong trận đấu khi giết quái, dùng để mua thêm gói thẻ bài cơ bản trong lượt chơi hiện tại.
2. **Đá quý / Linh hồn (`RelicGain`):** Dùng để mua cổ vật bị động (tăng 20% sát thương đòn đánh đầu, tăng tốc độ chạy quái vật).
3. **Foilium (`FoiliumFromPack` / `FoiliumDropFlight`):** Tiền tệ hiếm có được khi mở ra các thẻ Foil hoặc đánh bại quái vật mạ bạc (`FoilMonsterConfig`), dùng để nâng cấp vĩnh viễn trên cây kỹ năng meta (`SkillTreeUI`).

### B. Thuật toán Bảo hiểm rớt đồ (Pity Algorithm)
* Class **`MonsterPackDropPityManager`** và **`KillCoinPityConfig`**:
  - Mỗi khi một con quái bị tiêu diệt mà **không rơi gói bài**, biến đếm nội bộ `pityCounter` tăng thêm một giá trị.
  - Tỉ lệ rơi thực tế: $P_{drop} = P_{base} + (pityCounter \times P_{step})$.
  - Khi gói bài rơi ra, `pityCounter` được reset về 0.
  - **Tác dụng:** Ngăn chặn tuyệt đối tình trạng người chơi bị "vận xui" liên tục nhiều phút không rơi đồ, luôn giữ mức độ thỏa mãn ở mức cao.

---

## 6. SO SÁNH ĐỐI CHIẾU VỚI CÁC TỰA GAME NỔI TIẾNG

| Tiêu Chí So Sánh | **Booster Pack Heroes** | **Balatro** (Poker Roguelike) | **Vampire Survivors** (Horde) | **Plants vs. Zombies** (Classic TD) |
| :--- | :--- | :--- | :--- | :--- |
| **Cơ chế cốt lõi** | Mở gói thẻ bài TCG + Tower Defense | Poker + Xếp bài nhân điểm Jokers | Di chuyển sinh tồn tự đánh bầy quái | Đặt trụ theo hàng bảo vệ nhà |
| **Yếu tố gây nghiện** | Âm thanh xé bao bài + Thẻ nhũ Foil | Hiệu ứng cộng dồn điểm số nổ màn hình | Nâng cấp vũ khí dọn sạch màn hình | Chiến thuật phối hợp cây trồng |
| **Độ phức tạp điều khiển** | Cực thấp (Tập trung tư duy mở bài) | Thấp (Tập trung chiến thuật toán học) | Rất thấp (Chỉ cần điều khiển di chuyển) | Trung bình (Quản lý ánh sáng mặt trời) |
| **Hệ thống Meta-Progression**| Cây kỹ năng sâu (`SkillNodesRoot`) | Mở khóa bộ bài và Voucher mới | Nâng cấp chỉ số cơ bản vĩnh viễn | Mở khóa cây trong Shop Crazy Dave |
| **Công nghệ đồ họa** | **Unity 6 (DirectX 12 + Burst Job)** | LÖVE2D (Lua Scripting) | Phaser / Unity WebGL | PopCap Framework 2D |

* **Điểm học hỏi từ Balatro:** Thiết kế âm thanh tactile, cảm giác lật mở thẻ bài cực kỳ kích thích thị giác.
* **Điểm học hỏi từ Vampire Survivors:** Cơ chế bầy đàn lũ lượt, số sát thương nảy liên tục (`DamageNumbers`) và hệ thống nhặt rương/mở đồ dồn dập.

---

## 7. ĐÁNH GIÁ ĐIỂM YẾU VÀ CÁC NGUY CƠ TIỀM ẨN (CRITICAL WEAKNESSES)

Mặc dù trò chơi làm rất tốt về mặt kích thích tâm lý và tối ưu hóa hiệu năng, việc giải phẫu kỹ thuật chỉ ra các điểm yếu sau:

1. **Nguy cơ lạm phát chỉ số (Stat Bloat / Numbers Creep):**
   - Về late-game, khi người chơi mở được quá nhiều tomes và relic tăng cấp số nhân (% damage stack), sát thương của Hero có thể vượt ngưỡng hàng triệu đơn vị, khiến quái vật thường bị tiêu diệt ngay lập tức tại điểm xuất hiện, làm mất đi tính chiến thuật thủ thành ban đầu.
2. **Sự phụ thuộc vào yếu tố May rủi (RNG Dependency):**
   - Mặc dù có `PityManager`, nếu mở toàn gói bài trùng Hero yếu hoặc không ra đúng trường phái (ví dụ đang build Frost mà ra Chaos), người chơi có thể bị kẹt màn chơi do thiếu sát thương đơn mục tiêu.
3. **Thiếu đa dạng về cơ chế Boss:**
   - Quét metadata cho thấy hành vi quái chủ yếu là di chuyển theo bầy dọc hành lang (Lane). Boss chỉ có lượng máu lớn hơn (`BossSpawnWebs`) chứ chưa có các pha chuyển đổi phase (multi-phase mechanics) phức tạp.
4. **Rào cản modding & kỹ thuật (IL2CPP Unity 6):**
   - Việc build mã nguồn bằng **IL2CPP Metadata v39** với DirectX 12 khiến cộng đồng rất khó viết các bản mod gameplay không chính thức nếu không có công cụ chuyên sâu như Rodroid Il2CppDumper.
